# Vertical Slice 1: User Bootstrap (Completed)

## Implementation Plan

This plan covers the implementation of Vertical Slice 1, which establishes the authenticated user session and backend integration. The goal is to allow a user to sign in via Google, verify the session on the backend, and load server-authoritative settings.

### 1. Database & Prisma (Backend)
- Add the `UserRole` and `Script` enums to `prisma/schema.prisma`.
- Add the `User` and `UserSettings` models to `prisma/schema.prisma` following the design in `database-design.md`.
- Generate the first Prisma migration and update the Prisma client.

### 2. Authentication Foundation (Backend)
- Implement `FirebaseAuthGuard` to verify Firebase ID tokens passed in the `Authorization: Bearer` header using the Firebase Admin SDK.
- Update `UsersController` to expose `GET /api/v1/me`, protected by the `FirebaseAuthGuard`.
- Update `UsersService` to look up the application `User` by `firebaseUid`.
- Implement bootstrap logic in `UsersService`: if the user does not exist, create the `User` record and the default `UserSettings` record.

### 3. Flutter Authentication UI & Networking
- Implement minimal placeholder UI for onboarding screens (SS-001 to SS-002B) that immediately provides a "Sign In with Google" option, executing the 'onboarding-to-auth handoff'.
- Integrate `firebase_auth` and `google_sign_in` to retrieve the Firebase ID token.
- Update the Dio HTTP client to include an interceptor that automatically attaches the Firebase ID token to the `Authorization` header.
- Create a Riverpod auth provider that manages the session state (Startup -> Unauthenticated -> Authenticated) and fetches the `/me` payload upon sign-in.
- Configure GoRouter to transition from the onboarding placeholder UI to an authenticated shell.

### 4. Flutter Backend Integration
- **Models**: Create `User` and `UserSettings` data models in Flutter to match the API response.
- **User Repository (Flutter)**: Implement `UserRepository` using `apiClient` to call `GET /api/v1/me`.
- **Session State**: Implement `sessionStateProvider` that listens to `authStateChanges`. Upon Firebase login, it must call the backend to fetch/create the application user profile.
- **Home Screen Updates**: Modify `HomeScreen` to display the database user's name and `scriptPreference`, proving end-to-end data flow.

---

## Completion Report

**Status: 100% Complete**  
We have successfully proven the end-to-end viability of the Shruti Sadhana architecture. The Flutter web frontend is now fully integrated with the NestJS backend via Firebase Authentication.

### What Was Accomplished

1. **Frontend Authentication Flow (Flutter Web)**
   - Implemented Google Sign-In using Firebase Authentication.
   - Built a robust, reactive state management system using Riverpod (`AppSession`) that listens to Firebase Auth state changes in real-time.
   - Configured `go_router` for route guarding, ensuring unauthenticated users are redirected to the Login Screen, and authenticated users are directed to the Home Screen.

2. **Frontend-Backend Integration (Dio API Client)**
   - Configured a global Dio API Client interceptor that automatically attaches the user's secure Firebase ID token as a `Bearer` header to all backend requests.
   - Resolved Cross-Origin Resource Sharing (CORS) security policies to allow the Flutter web app (`localhost:8080`) to securely query the NestJS backend (`localhost:3000`).

3. **Backend Authentication & Authorization (NestJS)**
   - Initialized the Firebase Admin SDK securely for local development using file system absolute path resolution (`fs.readFileSync`), preventing common Node.js module resolution bugs.
   - Configured `FirebaseAuthGuard` to automatically intercept, decode, and verify incoming Firebase ID tokens using Google's public certificates.
   - Rejected tokens with invalid audience claims (e.g., mismatched environments like `dnji-app`) to enforce strict security boundaries.

4. **User Profile Hydration (Prisma & MySQL)**
   - Created the `GET /api/v1/me` endpoint.
   - Upon successful token verification, the backend automatically provisions a new `User` and default `UserSettings` record in the local MySQL database if they do not exist.
   - Serializes the user profile (safely handling `BigInt` MySQL types) and returns it to the frontend.

### Validation Results

- ✅ **Login Screen**: Renders the "Sign in with Google" button.
- ✅ **Google OAuth Popup**: Successfully opens and redirects back to the Flutter app.
- ✅ **Token Exchange**: Flutter correctly sends the ID token to the NestJS backend.
- ✅ **Token Verification**: NestJS securely verifies the `shruti-sadhana` audience claim.
- ✅ **Database Provisioning**: The user is created in MySQL via Prisma.
- ✅ **Home Shell UI**: The frontend dynamically renders the Home Screen, successfully displaying the user's name retrieved directly from the MySQL database.

### Production Readiness
The backend's Firebase Admin SDK implementation is designed to transition seamlessly to a GCP Compute Engine VM. By omitting the `FIREBASE_SERVICE_ACCOUNT_PATH` environment variable in production, the app will securely use Google's Application Default Credentials (ADC) without requiring any hardcoded `.json` files on the server.

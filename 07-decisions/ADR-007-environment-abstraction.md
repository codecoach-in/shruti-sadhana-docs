# ADR-007: Environment Abstraction

## Status
Approved

## Context
Shruti Sadhana utilizes multiple cloud infrastructure integrations, including private object storage, CDNs, third-party authentication providers, notifications, and payment gateways. During the media security proof of concept (PoC), development in direct cloud-only environments proved painful. Requiring live cloud credentials for local development creates onboarding friction, incurs service costs, prevents offline work, and makes automated testing non-deterministic.

## Decision
Design all core infrastructure integrations around the principle of Environment Abstraction. Every integration (Storage, Authentication, Media delivery, Payments, Notifications) must implement a uniform interface with two distinct drivers:
1. **Local Mode**: Uses mock data, local storage directories, mock JSON Web Tokens, or local static servers. Works completely offline with zero configuration and zero external dependencies.
2. **Production Mode**: Connects to the actual live cloud service (e.g., private cloud storage, Cloud CDN, Firebase Auth).

## Alternatives Considered
- **Cloud-Only Development**: Rejected. Developers would require active cloud accounts, sandboxes, constant internet connectivity, and would risk cloud cost overrun. Testing would also be slower and prone to networking flakiness.

## Consequences
- **Positive**:
  - **Onboarding**: Developers can download the codebase and start running it instantly without external keys.
  - **Testing**: Tests run against mock drivers, ensuring high speed and complete determinism.
  - **Resilience**: The backend is shielded from direct vendor lock-in; switching cloud providers (e.g., from Razorpay to Stripe) requires writing a new driver, leaving application logic untouched.
- **Negative**: Adds a layer of interface abstraction that developers must design and maintain.

## Related Documents
- [ADR-003-secure-media.md](ADR-003-secure-media.md)
- [system-overview.md](../03-architecture/system-overview.md)

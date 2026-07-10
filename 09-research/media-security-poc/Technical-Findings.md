# Media Security POC: Technical Findings

This document details the validated technical findings from the Media Security Proof of Concept (POC) execution.

---

## Secure Streaming
- **Triviality of MP3 Downloads**: Standard file streaming (delivering raw MP3 paths directly to clients) was validated to be highly insecure. Direct download links are easily extracted from browser network logs or native traffic inspector tools, enabling quick copying of proprietary assets.
- **HLS Segment Protection**: Utilizing HTTP Live Streaming (HLS) breaks the raw audio master files into tiny, sequential ts segments (using a 6-second segment chunk size). Storing and delivering these segments separately prevents standard media grabbers from copying a single contiguous file.

## Authentication & Key Protection
- **Decoupled Key Access**: User authentication acts as the gatekeeper for key retrieval. The client player requests the HLS decryption key from an authorized backend API.
- **Key Endpoint Protection**: The HLS key URL is protected by bearer authentication. Non-authenticated requests are blocked.
- **Transparent Token Recovery**: If authorization tokens expire mid-stream, the client-side playback framework must refresh the token transparently, allowing HLS playback to recover without pausing or failing.

## HLS Architecture & Header Limitations
- **Manifest Header Restrictions**: The POC validated a major limitation in browser and native HLS media players: while custom authorization headers (like `Authorization: Bearer <token>`) propagate to the initial manifest request (`.m3u8`), they do *not* propagate to subsequent HLS sub-manifest, segment (`.ts`), or encryption key (`.key`) requests triggered natively by the browser media engine.
- **Signed URLs as the Solution**: To maintain authorization across all HLS sub-requests without custom headers, individual segments and key endpoints must utilize query-string signed parameters (short-lived signed URLs).

## Dynamic Manifest Rewriting
- **Mechanism**: To support HLS streaming while keeping cloud assets private, the application backend acts as a manifest processor.
  1. The client player requests the `.m3u8` manifest file via an authorized API call.
  2. The application server fetches the private master manifest from cloud storage.
  3. The server dynamically rewrites the file: it replaces local/private segment and key paths with short-lived, signed CDN URLs.
  4. The rewritten manifest is returned to the client player, enabling the native browser/mobile player to fetch HLS chunks and decryption keys directly from the CDN without further backend proxying.
- **Decoupling CDN and Backend**: The backend is responsible *only* for manifest parsing, rewriting, and token signature validation. The high-bandwidth media segments themselves are served directly by the CDN, keeping backend server resource utilization minimal.

## AES-128 Key Protection
- **Encryption Implementation**: Segments are encrypted using AES-128 in Cipher Block Chaining (CBC) mode. 
- **Dynamic Decryption**: The rewritten HLS manifest specifies a `#EXT-X-KEY` tag directing the client to request the decryption key. The player retrieves the 16-byte key, decrypts segments in memory, and decodes the stream locally. The raw decrypted audio is never saved to the device disk.

## Flutter Web Findings
- **Renderer Performance**: The POC tested two Flutter Web rendering frameworks: CanvasKit and the HTML Renderer.
  - **HTML Renderer**: Validated to start up significantly faster. It loads standard system and custom Sanskrit web fonts natively, guaranteeing correct typography rendering.
  - **CanvasKit**: Excluded due to slow initial load times (large engine binaries to download) and custom font rendering overhead, which creates text rendering errors on Sanskrit fonts.

## Performance Metrics
- **Startup Latency**: The duration between hitting play and initial audio output ranges between **120–220 ms** (under standard mobile network conditions).
- **Seek Latency**: Jumping to a new segment during active playback takes approximately **80–150 ms**.
- **Buffering Behavior**: Negligible buffering issues were observed. The 6-second segment chunk size represents the optimal balance between startup responsiveness and network request overhead.
- **HTML Renderer Startup**: The HTML renderer delivered a noticeably quicker initial app load time than CanvasKit.

## Security Assessment
- **Asset Reconstruction Difficulty**: Segmenting and encrypting with AES-128 dramatically increases the technical barrier to reconstruction. While not immune to focused memory capture, standard browser extensions and simple scraping scripts cannot download or merge the encrypted segments.
- **Signed URL Expiry**: Rewritten URLs expire within a short window (e.g. 15 minutes), preventing shared links from working outside the active session context.
- **No Proxy Overhead**: Because the backend never proxies the media stream, there is no risk of backend denial of service (DoS) due to high-volume media requests.

## Playback Behavior
- **Native HLS Integration**: The HLS stream integrates cleanly with native Android and iOS/Safari web media pipelines.
- **Interruption Recovery**: The client-side player handles network dropouts smoothly, caching ahead 3–4 segments to survive short connectivity lapses.

## Local Development Experience
- **Environment Abstraction Validation**: Fulfilling the POC on cloud-only integrations proved highly painful. This findings directly validated the necessity of [ADR-007: Environment Abstraction](../../08-decisions/ADR-007-environment-abstraction.md).
- **Local Drivers**: Developers can run the entire HLS playback suite offline by serving local unencrypted/encrypted segments via a local mock static server and mocking the JWT token signer, bypassing GCS, Firebase, and CDN setup hurdles.

## Production Readiness
- **Production Infrastructure**: The validated production path utilizes private Google Cloud Storage (GCS) buckets, Google Cloud CDN, and a NestJS server backend. This combination is fully production-ready and matches the security targets for MVP launch.

---

## Related Documents
- [ADR-003: Secure Media Platform](../../08-decisions/ADR-003-secure-media.md)
- [ADR-007: Environment Abstraction](../../08-decisions/ADR-007-environment-abstraction.md)

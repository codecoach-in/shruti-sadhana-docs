# ADR-003: Secure Media Platform

## Status
Approved

## Context
Shruti Sadhana's Gold Standard recordings represent premium intellectual property. Unsecured media assets are highly vulnerable to scraping, hotlinking, and unauthorized redistribution. We must protect this media without forcing the application backend to proxy high-bandwidth media streams, which would degrade performance and escalate hosting costs.

Note: The current technology stack employs Google Cloud Storage, Cloud CDN, and NestJS, but the architectural decision focuses on decentralized secure delivery via signed access and CDN segment encryption, which can be ported to other providers (e.g., AWS S3/CloudFront) if needed.

## Decision
Secure and distribute media using a decoupled authorization model:
1. **Direct secure CDN streaming**: Media is served directly from cloud storage via a Content Delivery Network using signed URLs/cookies and segment-level encryption (AES-128 HLS).
2. **Backend-only authorization**: The application backend handles authentication, validates subscription status, and issues short-lived credentials (signed keys/URLs) for playback, but *never* proxies actual media data.

## Alternatives Considered
- **Proxying Media Through Application Backend**: Rejected. This creates a severe backend performance bottleneck, limits scaling capacity, and raises server costs.
- **Publicly Accessible Storage Buckets**: Rejected. This presents an unacceptable security risk to premium audio assets and makes scraping trivial.

## Consequences
- **Positive**: High security for premium recordings, low backend resource usage, fast load times via CDN edge locations, and scalability.
- **Negative**: Increased configuration complexity, requiring client-side integration of HLS/AES decryption and backend logic to sign URLs and serve short-lived encryption keys securely.

## Related Documents
- [PRD.md](../01-product/PRD.md)
- [ADR-004-no-offline-downloads.md](ADR-004-no-offline-downloads.md)

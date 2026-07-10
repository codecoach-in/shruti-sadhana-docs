# ADR-006: Curated Content Pipeline

## Status
Approved

## Context
The core value proposition of Shruti Sadhana is teaching correct Sanskrit stotram pronunciation and chanting. In traditional spiritual practice, correct pronunciation is vital, and incorrect pronunciation destroys trust in the platform's authenticity. Crowdsourced or user-generated uploads present a high risk of poor audio quality, incorrect pronunciation, copyright issues, and a heavy moderation burden.

## Decision
Restrict the content inventory to a curated, closed content pipeline. Only professionally recorded, manually mastered audio (such as the "Gold Standard" recordings), and expert-verified text scripts and meanings will be admitted to the platform database. No user-generated content (UGC) or public upload endpoints will be supported.

## Alternatives Considered
- **Community-Uploaded/Crowdsourced Content**: Rejected. Although it would allow faster catalog growth, it introduces a massive quality-assurance and moderation overhead and risks publishing incorrect chanting.

## Consequences
- **Positive**:
  - **Trust**: Guarantees authentic, high-quality, and spiritually reliable chanting.
  - **Consistency**: Audio production, editing, and indexing standards remain uniform.
  - **Premium Value**: Reinforces the premium position of the platform's proprietary learning audio.
- **Negative**: Slower rate of catalog expansion, since every stotram requires manual recording, mastering, and scripting prior to publication.

## Related Documents
- [project-context.md](../00-project/project-context.md)
- [PRD.md](../01-product/PRD.md)

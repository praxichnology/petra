# PeTRA — Performance Tracing Report Assistant

**An analytics engine for serious games, published as Open Documentation.**

[![DOI](https://zenodo.org/badge/1221296838.svg)](https://doi.org/10.5281/zenodo.19782551)

PeTRA is a research-grade analytics engine designed to read player behavior in serious games — not just outcomes (scores, completion), but the *information trails* players leave through the game environment over repeated play. Developed alongside *Nexus* (a serious game on social media risk awareness) as a paired example, PeTRA is intended to grow into a common analytics core that other serious-game researchers can adapt to their own games.

This repository hosts PeTRA's **Open Documentation**: the methodology, design rationale, and architecture. It does NOT host the implementation code.

PeTRA is one of the tools in the [praxichnology](https://github.com/praxichnology) field commons — a worked example of praxichnological methods (reading traces produced through situated activity in designed environments) applied to serious-games analytics. For the foundational architecture of the field, see [`praxichnology/preprint`](https://github.com/praxichnology/preprint).

---

## What is *Open Documentation*?

PeTRA's **Open Documentation** model means:

- **Methodology, design rationale, and architecture** are openly published here for inspection, replication, and scholarly reference.
- **Implementation code** is not openly licensed for unrestricted redistribution.

Academic methodology papers are, in effect, *blueprints* — they describe HOW to build something so that other researchers can implement it in their own context. Open Documentation extends this academic practice into a publication strategy: the blueprint is openly published; the implementation is one worked example.

The choice reflects two realities:

1. **Academic norms** of methodological transparency are met when design rationale and architecture are openly documented for reviewers, replicators, and other researchers.
2. **2026 security realities**: automated AI-assisted vulnerability scanning makes traditional open-source attack surfaces too costly to defend for a small research team.

---

## For researchers wanting to use PeTRA

PeTRA offers three paths, each suited to different research needs:

### Path 1 — Build your own implementation from the documentation

The PeTRA methodology is specifiable enough that you can implement an analytics engine for your own serious game in any tech stack. The documentation here is the blueprint; your implementation is the realization.

The architecture is three-layer:

1. **Adapter** — reads your game's data source and converts it into the format PeTRA expects
2. **Analytics modules** — process information trails and other behavioral indicators
3. **Reporting/visualization layer** — renders results for researchers

**Best for**: research teams with engineering capacity who want full independence over their analytics infrastructure.
**Cost**: free.
**Obligations**: standard academic citation when publishing.

### Path 2 — Hosted analytics with co-publication (active collaborators)

Use Loh's hosted PeTRA instance to analyze your data. Your game and database live on your own infrastructure (e.g., Vercel + Render); a custom adapter — which we'll create together — lets PeTRA read your database for analysis. PeTRA computes metrics in real-time; PeTRA is stateless and stores no data, so your data never leaves your infrastructure for storage purposes.

Because Path 2 is a genuine research collaboration — Loh contributes the engine and analytical expertise; you contribute the game, the data, and the research questions — **co-publication is part of the terms**. We discuss authorship, contribution roles, and publication targets at the start of the collaboration.

**Best for**: research collaborators who want to use PeTRA without engineering their own implementation, and who are open to genuine collaborative research.
**Cost**: free.
**Obligations**: co-publication agreement.

To inquire: email the author with your name, institutional affiliation, a brief description of your serious game and research project, your IRB approval status, and your willingness to co-publish.

### Path 3 — Snapshot license for self-hosting

For researchers who need to run PeTRA on their own infrastructure (typically due to IRB requirements, data residency rules, or institutional policies), a snapshot license is available. You receive a frozen version of PeTRA at the time of license; you run it on your infrastructure; you handle security and maintenance. PeTRA's continued development happens privately — newer versions are not provided as part of the snapshot license.

**Best for**: researchers with self-hosting requirements that prevent using Path 2.
**Cost**: token fee (covers administrative costs).
**Obligations**: non-redistribution agreement; you are responsible for the security and maintenance of your installation.

To inquire: email the author with your name, institutional affiliation, the reason a self-hosted version is required, and your IRB approval status.

---

## Field context

PeTRA is a worked example within the praxichnology field — its analytical methods (information trails, Expert Similarity Index, learning curves, Matthew-effect diagnostics) are concrete instantiations of praxichnological practice. For field-level orientation, see:

- [`praxichnology/preprint`](https://github.com/praxichnology/preprint) — the v1.0 preprint coining the field
- [`praxichnology/docs`](https://github.com/praxichnology/docs) — field-level orientation, citation guidance, FAQ

---

## Citation

If you use or build on PeTRA, please cite the forthcoming book:

> Loh, C. S., Sheng, Y., & Ifenthaler, D. (in press, 2027). *Serious Games Analytics in the Age of AI*. Springer.

To cite PeTRA as a software/methodology artifact, use the Zenodo DOI: [10.5281/zenodo.19782551](https://doi.org/10.5281/zenodo.19782551).

---

## Contact

Christian S. Loh, Ph.D. · csloh@siu.edu
Professor · School of Education · Southern Illinois University Carbondale

For PeTRA-related inquiries — Path 2 collaboration proposals, Path 3 license inquiries, methodology questions, citation queries — please email above.

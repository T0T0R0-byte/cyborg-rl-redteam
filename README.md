# CybORG RL Red-Team Agent (final year project)

![Status: in progress](https://img.shields.io/badge/Status-In%20Progress-orange)
![Field: Reinforcement Learning](https://img.shields.io/badge/Field-Reinforcement%20Learning-blue)
![Environment: CybORG](https://img.shields.io/badge/Environment-CybORG-informational)

My final year project: training a reinforcement-learning agent to act as an autonomous red-team
agent inside **CybORG**, the CAGE-standard cyber-operations simulator.

This repository holds the research design, proposal and literature review as they stand, and will
grow to include the training harness, experiment configs and results as the project progresses.

## What the project is actually claiming

RL for automated penetration testing is an active field: the original proof-of-concept, standard
simulators (CybORG/CAGE, NASim/NASimEmu), a 2024 systematisation of knowledge, and several surveys.
Generalisation to unseen networks is *already* studied elsewhere. So this project does not claim
novelty on any of it.

What it does target: no controlled, reproducible comparison in the standard CybORG environment pits
the training strategies a practitioner would actually reach for (single-scenario training,
curriculum, fine-tuning) against scripted and random baselines under one protocol, with statistical
rigour and a public harness. That gap - an independent systematic comparison plus an evidence-based
training recipe - is the contribution.

## Approach, in outline

- **Environment:** CybORG (the environment used in the CAGE challenge series).
- **Agents compared:** policy trained on a single scenario; curricula; fine-tuned variants.
- **Baselines:** scripted and random agents, so improvements are measured against something.
- **Evaluation:** held-out scenarios, repeated runs, statistical comparison rather than single-run
  score picking.
- **Reproducibility:** harness, configs and raw results published with the write-up.

## Documents

| Document | What it covers |
|---|---|
| [Research design](docs/research-design.md) | Positioning against prior work, the research question, method and evaluation plan |
| [Initial proposal](docs/initial-proposal.md) | The proposal as submitted for approval |
| [Literature review](docs/literature-review.md) | Landscape of RL for automated penetration testing and the generalisation literature |
| [One-page summary](docs/one-page-summary.md) | The short version of the project |

Internal working notes (supervision preparation, submission paperwork, meeting records and the
citation ledger) are deliberately not published here.

## Status

Design and review stage. Written up as of 2026-09; the implementation, experiments and results will
be added as they are produced. Nothing in this repository claims results that do not exist yet.

## Author

**Faraj Farook** (CB012653), BSc (Hons) Cyber Security.

## The reports as submitted

The original submitted documents are in [`reports/`](reports/), kept alongside the write-up above so the artefact can be checked directly.

## License

MIT for the repository contents, see [LICENSE](LICENSE). Cited papers remain the property of their
authors.

# CybORG RL Red-Team — One-Page Research Summary
# Final Year Project Topic Submission Form

## 1. Working Research Title

Do RL Red-Team Agents Learn to Attack Networks or Memorize One? A Study of Generalization and Training Strategies for Autonomous Penetration Agents in CybORG

## 2. Background / Problem Overview

Autonomous penetration testing addresses a real gap - pentesters are scarce - and RL can automate attack planning in standard environments such as CybORG, home of the public CAGE challenges. Its value hinges on whether an agent trained on one network can attack one it has never seen. Evidence shows this is not guaranteed: [NASimEmu](https://arxiv.org/abs/2305.17246) showed agents transfer poorly to novel scenarios, and a [2026 preprint](https://arxiv.org/html/2603.10041v1) confirmed they remain brittle at test time. Yet no controlled study in CybORG compares the training strategies that determine transfer.

## 3. Quick Literature Insights

- [Schwartz and Kurniawati (2019)](https://arxiv.org/abs/1905.05965): model-free RL is viable for automated pentesting.
- [Standen et al. (2021)](https://github.com/cage-challenge/CyBORG): CybORG - the standard environment behind the CAGE challenges.
- [Janisch et al. (2023)](https://arxiv.org/abs/2305.17246): NASimEmu shows agents transfer poorly to novel topologies.
- [Ondrej et al. (2026)](https://arxiv.org/html/2603.10041v1): agents trained on five variants remain brittle on a sixth; meta-learning helps.

## 4. Research Gaps Found

- Gap 1: No controlled comparison in CybORG of practical training strategies (single-scenario, curriculum, fine-tuning) for RL attack agents.
- Gap 2: Whether high-scoring agents learn real attack strategies or game the reward function is unmeasured.
- Gap 3: No public, reproducible benchmark harness with baselines exists for this question in CybORG.

## 5. Proposed Solution / Research Direction

A controlled experiment: train PPO red-team agents in CybORG under three training strategies (single-scenario, multi-topology curriculum, fine-tuning), evaluate them on held-out network topologies against scripted and random baselines, and analyze whether high-scoring agents learn genuine attack strategies or game the reward function.

## 6. Expected Contributions

| Contribution Dimension | Value / Academic and Practical Significance |
|------------------------|---------------------------------------------|
| Research | Reproducible measurement of RL attack-agent generalization. |
| Practical | Guidance for building red-team agents that work on unseen networks. |
| Academic | First controlled strategy comparison, reward-gaming analysis, and a public harness. |

## 7. Next Steps (Before Approval)

- **Candidate Environment:** [CybORG](https://github.com/cage-challenge/CyBORG) (CAGE Challenge 2) + generated topology variants; no real systems.
- **Evaluation Metrics:** attack success rate, steps to success, generalization gap, episode reward.
- **Baselines:** scripted B-line attacker, random policy, in-distribution performance.
- **Scope:** single-agent red team, fixed blue team, simulation only; no MARL or emulation.
- **Feasibility:** 16GB RAM + CPU; free Colab; zero cost; ethics disclaimer tier (no participants).

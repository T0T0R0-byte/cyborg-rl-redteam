# Initial Research Proposal - Generalization of RL Red-Team Agents for Autonomous Penetration Testing in CybORG

> AI-DRAFT for review (2026-09-03). Initial (formative) submission 07 Sep 2026.
> Every citation verified against arXiv/CrossRef/DBLP. Follows the official Proposal
> Template (Ch 1-3). Rewrite in your own words before submission.

# CHAPTER 01: INTRODUCTION

## 1.1 Chapter Overview

This chapter introduces the research project: whether reinforcement-learning (RL)
red-team agents trained to attack networks can generalize to networks they have
never seen. It defines the problem, reviews the most relevant prior work, states
the research gap, and sets out the research questions, aim, and objectives.

## 1.2 Background / Problem Domain

Autonomous penetration testing is proposed as a partial answer to the growing
shortage of skilled cybersecurity professionals (Schwartz and Kurniawati, 2019).
Penetration testing requires highly skilled practitioners, and automating parts of
the process using artificial intelligence is an active research direction. Deep
reinforcement learning has been shown capable of discovering attack paths on real
network setups (Hu, Beuran and Tan, 2020), and the field has since built standard
environments - most notably CybORG, the environment behind the public CAGE
challenges (Standen et al., 2021).

The critical question for real-world use is whether an agent trained on one
network can attack a different network it has never seen. The literature shows this
is not guaranteed: agents often perform poorly when transferred to unseen scenarios
(Zhou et al., 2025), and even a minimal change such as IP reassignment can break
long-horizon attack policies (Lukas et al., 2026).

## 1.3 Problem Definition

### 1.3.1 Problem Statement

RL red-team agents are typically trained and evaluated on the same network, so it
is unknown whether their policies generalize to unseen networks - and the training
strategy that best improves such generalization has not been measured under a
controlled comparison in a standard environment.

## 1.4 Key Related Work (Top 5)

| Citation (Author, Year, link) | Contribution | Limitation | Gap Identified | Relevance to My Research |
|---|---|---|---|---|
| Schwartz & Kurniawati (2019) - https://arxiv.org/abs/1905.05965 | RL for autonomous pentesting | Only practical for smaller networks; scalability untested | Need scalable RL + higher-fidelity testing | Foundation of red-team RL; scalability gap |
| Janisch et al. (2023) - https://arxiv.org/abs/2305.17246 | NASimEmu simulator+emulator | Agents transfer poorly to novel scenarios; reality gap | Generalization across topologies unaddressed by framework alone | Core evidence of the generalization gap |
| Venturi et al. (2024) - https://doi.org/10.1016/j.array.2024.100365 | Assesses DRL generalization to unseen hosts | Confirms failure on hosts not in training | No strategy comparison offered | Direct evidence the gap is real |
| Zhou et al. (2025) - https://doi.org/10.1631/fitee.2500100 | Domain randomization + meta-RL (GAP framework) | Single framework, NASim-style env | No CybORG comparison of practical strategies | Closest work; my project compares strategies in CybORG |
| Lukas et al. (2026) - https://arxiv.org/html/2603.10041v1 | Generalization mechanisms in attack agents | NetSecGame env; limited strategy set | No CybORG-based comparison; reward gaming unmeasured | Most recent evidence; motivates my controlled study |

## 1.5 Research Motivation

Technical motivation: current RL pentest agents score well on their training
network but their performance on unseen networks is largely unknown, and the field
lacks a standardized evaluation (Fernandes et al., 2026). Societal motivation:
organizations rely on penetration testing for security assurance, and tools that
appear effective in the lab but fail in practice create a false sense of security.
Personal motivation: an interest in both offensive security and machine learning,
and a desire to contribute reproducible evaluation practice to an emerging field.

## 1.6 Research Gap

The literature establishes that RL red-team agents fail to generalize (Venturi et
al., 2024; Zhou et al., 2025; Lukas et al., 2026) and that the field is fragmented
with no standard benchmark (Fernandes et al., 2026), yet no verified study compares
practical training strategies - single-scenario, multi-topology curriculum, and
fine-tuning - in the same environment (CybORG) under the same protocol. Whether
high-scoring agents learn genuine attack strategies or game the reward function is
also unmeasured. This project addresses those gaps.

## 1.7 Contribution

To the problem domain: guidance for building red-team agents that work on unseen
networks. To the research domain: a controlled measurement of how training strategy
affects the generalization gap in CybORG, including reward-gaming analysis. To the
body of knowledge: a public, reproducible benchmark harness with documented
baselines for red-agent generalization.

## 1.8 Research Challenges

(1) Reproducing PPO red-team agents in CybORG with stable training requires careful
hyperparameter work (Becker et al., 2024). (2) Designing held-out topologies that
are genuinely unseen and meaningfully different is non-trivial. (3) Distinguishing
genuine attack strategies from reward gaming requires behavioral analysis beyond
raw episode reward.

## 1.9 Research Questions

- RQ1: How does training strategy (single-scenario vs curriculum vs fine-tuning)
  affect red-agent generalization to unseen topologies in CybORG?
- RQ2: How much of a high-scoring agent's performance transfers to unseen networks,
  and which failures dominate?
- RQ3: Do agents that score well on the training network learn real attack
  strategies or game the reward function?
- RQ4: Do simulation-trained agents transfer to a higher-fidelity CybORG variant,
  and does that transfer depend on training strategy?

## 1.10 Research Aim

To measure how training strategy affects the generalization of RL red-team agents
to unseen networks in CybORG, and to determine whether high-scoring agents learn
genuine attack strategies.

## 1.11 Research Objectives

- O1: Reproduce PPO red-team agents in CybORG under three training strategies:
  single-scenario, multi-topology curriculum, and fine-tuning.
- O2: Evaluate agents on held-out topologies (unseen during training) against
  scripted and random baselines, measuring the generalization gap.
- O3: Analyze whether high-scoring agents learn genuine attack strategies or game
  the reward function, using behavioral analysis.
- O4: Release a public, reproducible benchmark harness for red-agent
  generalization in CybORG with documented baselines.

## 1.12 Chapter Summary

This chapter defined the problem of RL red-team generalization, identified the
research gap, and set out four research questions and four objectives. The next
chapter reviews the literature in detail.

# CHAPTER 02: LITERATURE REVIEW

## 2.1 Chapter Overview

This chapter summarizes the literature on autonomous penetration testing with
reinforcement learning, focusing on the generalization of red-team agents to
unseen networks. It covers the problem domain, the technological landscape
(environments, algorithms, training strategies), evaluation practice, and the
most relevant existing work. The review concentrates on the papers most directly
tied to the research gap: the evidence that agents fail to generalize and the
absence of a controlled training-strategy comparison in CybORG.

## 2.2 Concept Map

![Concept Map - RL Red-Team Generalization in CybORG](cyborg-concept-map.png)

*Figure 2.1: Concept map. Center: RL red-team generalization in CybORG. Branches:
problem domain, environments, algorithms, training strategies, evaluation, gaps.*

## 2.3 Thematic Review

### 2.3.1 Problem domain
Autonomous penetration testing is motivated by the cybersecurity skills shortage
(Schwartz and Kurniawati, 2019; Hu, Beuran and Tan, 2020). The field has grown to
include systematic comparisons of agents (Simon and Mees, 2024) and comprehensive
reviews (Liu et al., 2026).

### 2.3.2 Technological review
Environments: CybORG/CAGE (Standen et al., 2021; Kiely et al., 2023; Emerson et al.,
2024), NASimEmu (Janisch et al., 2024), PenGym (Nguyen et al., 2025). Algorithms:
Q-learning, DQN, A3C (Becker et al., 2024), PPO as standard modern choice.
Training strategies: single-scenario (brittle), curriculum/domain randomization
(Zhou et al., 2025), meta-learning (Lukas et al., 2026). Reward design: proxy
gaming is documented in general RL (arXiv:2507.05619) but unmeasured in pentest
agents.

### 2.3.3 Comparative analysis
Generalization evidence is consistent across NASimEmu, Array 2024, Mind the Gap,
and the 2026 preprint: agents fail on unseen scenarios. What differs is the remedy
proposed - none offers a controlled comparison of practical strategies in CybORG.

## 2.4 Existing Work

The reviewed literature can be organized into five categories, grouped by what each
body of work contributes.

**Category 1: Foundations.** Early work established that reinforcement learning can
find attack paths: Schwartz and Kurniawati (2019) on simulated topologies and Hu,
Beuran and Tan (2020) on real networks. Both evaluated in a single setting, leaving
scalability and transfer open.

**Category 2: Environments.** A second group built the training infrastructure:
CybORG/CAGE (Standen et al., 2021; Kiely et al., 2023), NASimEmu (Janisch et al.,
2024), PenGym (Nguyen et al., 2025), and CybORG++ with MiniCAGE (Emerson et al.,
2024). These are tools: none resolves the generalization question.

**Category 3: Generalization evidence.** A third group directly measures transfer.
Venturi et al. (2024) show failure on hosts held out from training; Janisch et al.
(2024) report the same on NASimEmu; Zhou et al. (2025) show small scenario changes
degrade the policy; Lukas et al. (2026) show unseen IP reassignment alone breaks
long-horizon plans. This is the strongest and most consistent evidence.

**Category 4: Methods proposed.** The remedies offered so far are domain
randomization and meta-RL (Zhou et al., 2025), adaptation and meta-learning (Lukas
et al., 2026), and curriculum-style training. Each is tested in isolation, in a
different environment, under a different protocol.

**Category 5: Evaluation and reviews.** Finally, measurement practice and surveys
set the context. Schwartz and Kurniawati (2019), Becker et al. (2024) and Janisch et
al. (2024) show agents are conventionally scored on the network they trained on.
Reviews by Simon and Mees (2024), Moreno et al. (2025), Liu et al. (2026) and
Fernandes et al. (2026) confirm the field is fragmented and lacks a standard
benchmark, and Kong et al. (2025) extend the space toward LLM-based pentesting
agents.

None of the work across these five categories compares practical training
strategies in the same environment under the same protocol.

### 2.4.1 Key papers and the gaps they identify

The following table summarizes the most important papers for this research and
the specific gap each one identifies. This makes the link between the literature

| # | Paper | What it found | Gap it identifies |
|---|---|---|---|
| 1 | Schwartz & Kurniawati (2019) - https://arxiv.org/abs/1905.05965 | RL can automate pentesting | Algorithms "only practical for smaller networks" - scalability untested |
| 2 | Hu, Beuran & Tan (2020) - https://doi.org/10.1109/eurospw51379.2020.00010 | DRL discovers attack paths | Limited to single network scenarios |
| 3 | Standen et al. (CybORG, 2021) - https://arxiv.org/abs/2108.09118 | CybORG env + CAGE challenges | Generalization across topologies not the focus |
| 4 | Janisch et al. (NASimEmu, 2023) - https://arxiv.org/abs/2305.17246 | Simulator + emulator for pentest RL | Agents transfer poorly; reality gap; "unrealistic metric on training data" |
| 5 | Simon & Mees (SoK, 2024) - https://doi.org/10.1145/3664476.3664484 | Comparison of pentest agents | Field fragmented; no standard generalization evaluation |
| 6 | Venturi et al. (2024) - https://doi.org/10.1016/j.array.2024.100365 | DRL generalization to unseen hosts | Agents fail on hosts not in training; no strategy comparison |
| 7 | Zhou et al. (Mind the Gap, 2024) - https://arxiv.org/abs/2412.04078 | Domain randomization + meta-RL | Single framework in NASim-style env; not a CybORG strategy comparison |
| 8 | Lukas et al. (2026) - https://arxiv.org/html/2603.10041v1 | Generalization mechanisms in attack agents | Few strategies compared; NetSecGame env not CybORG |
| 9 | Fernandes et al. (2026) - https://doi.org/10.1016/j.iot.2026.101932 | Review of AI pentesting | "None supports end-to-end training that generalises beyond controlled scenarios" |
| 10 | Vyas/Kiely et al. (CAGE-2, 2023) - https://arxiv.org/abs/2309.07388 | CAGE Challenge 2 approaches | Defence focus; red-agent generalization unstudied |
| 11 | Becker et al. (2024) - https://arxiv.org/abs/2407.15656 | Q-learning, DQN, A3C on NASim | "Rather small scenarios and small state/action spaces" |
| 12 | Liu et al. (review, 2026) - https://doi.org/10.1016/j.eswa.2025.130219 | Review of RL in autonomous pentesting | Generalization remains an open future-work area |

This project addresses the common thread across all 12: no one has compared
training strategies (single-scenario, curriculum, fine-tuning) in the SAME
environment (CybORG) under the SAME evaluation protocol, measuring the
generalization gap against baselines.

## 2.5 Evaluation and Benchmarking

Common metrics: episode reward, attack success rate, steps to success, held-out
performance (generalization gap). Known limitation in the field: evaluation on
training data gives unrealistic results (Janisch et al., 2024). Baselines: scripted
attacker, random policy (Lukas et al., 2026).

## 2.6 Chapter Summary

The literature establishes that autonomous pentesting is motivated by a real
skills shortage, that RL is the natural technical approach, and that environments
such as CybORG make training feasible. Recent work consistently shows that trained
agents fail to generalize to unseen networks. Six gaps were identified, centered
on the absence of a controlled comparison of training strategies and the lack of a
reproducible benchmark for red-agent generalization in CybORG. The next chapter
proposes the methodology.

# CHAPTER 03: METHODOLOGY

## 3.1 Chapter Overview

This chapter describes the research methodology using Saunders' Research Onion and
the development methodology for the CybORG experiments.

## 3.2 Research Methodology (Saunders' Onion)

- Philosophy: Positivism - the research measures objective, observable outcomes
  (agent performance under controlled conditions).
- Approach: Deductive - hypotheses about training strategy effects are tested
  through experiments.
- Methodological choice: Mono-method quantitative - controlled experiments.
- Strategy: Experiment - controlled comparison of training strategies on held-out
  topologies.
- Time horizon: Cross-sectional - experiments run within the project period.
- Data collection and analysis: agent episode rewards, attack success rates,
  generalization gap metrics; analyzed with descriptive statistics, confidence
  intervals, and repeated seeds.

## 3.3 Development Methodology

One-person Scrum/agile prototyping: iterate on the CybORG training pipeline in
short cycles, with regular supervisor check-ins.

### 3.3.1 Solution Methodology

Environment: CybORG (CAGE Challenge 2) + generated topology variants.
Agents: PPO red-team agents.
Training strategies: (a) single-scenario, (b) multi-topology curriculum,
(c) fine-tuning on a target variant.
Evaluation: held-out topologies unseen during training; baselines = scripted
B-line attacker, random policy, in-distribution performance.
Metrics: attack success rate, steps to success, generalization gap, episode reward.

### 3.3.2 Evaluation Methodology

Generalization gap = in-distribution performance minus held-out performance.
Statistical comparison across seeds; behavioral analysis of action distributions
for reward-gaming detection.

## 3.4 Project Management

Scope in: single-agent red team, fixed blue team, simulation; CybORG. Scope out:
MARL, real deployment, LLM agents.
Gantt: Month 1 lit review; Month 2-3 environments + baselines; Month 4 experiments;
Month 5 writing.
Resources: Python, PyTorch, RLlib/Stable-Baselines3, CybORG; 16GB RAM + CPU, free
Colab; zero cost.
Risks: (1) PPO instability - mitigate with hyperparameter search; (2) dataset/environment
setup issues - CybORG is open source; (3) compute time - use MiniCAGE/CybORG++ for
faster runs.

## 3.5 Ethics & Compliance Checklist

No human participants; public simulated environment; no real systems attacked.
Ethics disclaimer tier applies; no personal data collected.

## 3.6 Chapter Summary

This chapter described a positivist, deductive, mono-method quantitative
methodology: controlled experiments with PPO red-team agents in CybORG under three
training strategies, evaluated on held-out topologies against baselines. The
methodology is feasible with public tools, zero cost, and no human participants,
and it directly addresses the gaps identified in the literature review.

# References

Becker, N., Reti, D., Ntagiou, E.V., Wallum, M. and Schotten, H.D. (2024). Evaluation of reinforcement learning for autonomous penetration testing using A3C, Q-learning and DQN. *arXiv preprint*, arXiv:2407.15656.

Emerson, H., Bates, L., Hicks, C. and Mavroudis, V. (2024). CybORG++: An enhanced gym for the development of autonomous cyber agents. *arXiv preprint*, arXiv:2410.16324.

Fernandes, R., Lopes, N., Goncalves, J. and Cosgrove, J. (2026). Autonomous pentesting using artificial intelligence: from the cybersecurity point-of-view. *Internet of Things*. doi:10.1016/j.iot.2026.101932.

Hu, Z., Beuran, R. and Tan, Y. (2020). Automated penetration testing using deep reinforcement learning. In *Proceedings of the 2020 IEEE European Symposium on Security and Privacy Workshops (EuroS&PW)*. doi:10.1109/eurospw51379.2020.00010.

Janisch, J., Pevny, T. and Lisy, V. (2024). NASimEmu: Network attack simulator and emulator for training agents generalizing to novel scenarios. In *Lecture Notes in Computer Science* (ESORICS 2023 International Workshops), Springer. doi:10.1007/978-3-031-54129-2_35.

Kiely, M., Bowman, D., Standen, M. and Moir, C. (2023). On autonomous agents in a cyber defence environment. *arXiv preprint*, arXiv:2309.07388.

Kong, H., Hu, D., Ge, J., Li, L., Li, H. and Li, T. (2025). Pentest-R1: Towards autonomous penetration testing reasoning optimized via two-stage reinforcement learning. *arXiv preprint*, arXiv:2508.07382.

Liu, J., Zhang, Y., Zhou, S., Yang, J., Lu, Y. and Zhong, X. (2026). Autonomous penetration testing using reinforcement learning: a review and perspectives. *Expert Systems with Applications*. doi:10.1016/j.eswa.2025.130219.

Lukas, O., Shin, J., Rivas, E., Forni, D., Rigaki, M., Catania, C., Piplai, A., Kiekintveld, C. and Garcia, S. (2026). Evaluating generalization mechanisms in autonomous cyber attack agents. *arXiv preprint*, arXiv:2603.10041.

Nguyen, H.P.T., Hasegawa, K., Fukushima, K. and Beuran, R. (2025). PenGym: Realistic training environment for reinforcement learning pentesting agents. *Computers & Security*, 148, 104140. doi:10.1016/j.cose.2024.104140.

Schwartz, J. and Kurniawati, H. (2019). Autonomous penetration testing using reinforcement learning. *arXiv preprint*, arXiv:1905.05965.

Simon, R. and Mees, W. (2024). SoK: A comparison of autonomous penetration testing agents. In *Proceedings of the 19th International Conference on Availability, Reliability and Security (ARES 2024)*. doi:10.1145/3664476.3664484.

Standen, M., Lucas, M., Bowman, D., Richer, T.J., Kim, J. and Marriott, D. (2021). CybORG: A gym for the development of autonomous cyber agents. *arXiv preprint*, arXiv:2108.09118.

Venturi, A., Andreolini, M., Marchetti, M. and Colajanni, M. (2024). Assessing generalizability of deep reinforcement learning algorithms for automated vulnerability assessment and penetration testing. *Array*, 24, 100365. doi:10.1016/j.array.2024.100365.

Zhou, S., Liu, J., Lu, Y., Yang, J., Zhang, Y. and Chen, J. (2025). Mind the gap: Towards generalizable autonomous penetration testing via domain randomization and meta-reinforcement learning. *Frontiers of Information Technology & Electronic Engineering*. doi:10.1631/fitee.2500100.

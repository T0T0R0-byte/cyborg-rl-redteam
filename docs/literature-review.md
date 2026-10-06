# CHAPTER 02: LITERATURE REVIEW - Generalization of RL Red-Team Agents for Autonomous Penetration Testing in CybORG

> AI-DRAFT for review (2026-09-03). Every citation verified against arXiv/CrossRef/
> DBLP. Section order follows the module LR guide (2.1-2.7). Rewrite in your own
> words before submission. The ≥20-paper table and concept map are separate workstreams.

## 2.1 Chapter Overview

This chapter reviews the literature on autonomous penetration testing using
reinforcement learning (RL), with a specific focus on whether trained red-team
agents generalize to networks they have not seen during training. The review is
organized around the research pipeline: the problem domain (why autonomous
penetration testing is needed), the technological landscape (simulation
environments, RL algorithms, and training strategies), evaluation and benchmarking
practice, and existing work organized thematically. The chapter draws on studies
published predominantly in the last five years, with foundational works cited where
needed, and concludes by identifying the research gaps this project addresses.

## 2.2 Concept Map

![Concept Map - RL Red-Team Generalization in CybORG](cyborg-concept-map.png)

*Figure 2.1: Concept map of the literature. The centre is the research focus -
RL red-team generalization in CybORG. Branches cover the problem domain, simulation
environments, RL algorithms, training strategies, evaluation metrics, and the
research gaps this project addresses. The map guides the structure of this chapter:
sections 2.3-2.6 follow the branches in order.*

## 2.3 Problem Domain

### 2.3.1 The need for autonomous penetration testing

Penetration testing is a controlled attack on a system to assess its security, and
it requires highly skilled practitioners. Schwartz and Kurniawati (2019) frame the
core motivation: there is a growing shortage of skilled cybersecurity professionals,
and one avenue for alleviating this is automating parts of the penetration-testing
process using artificial intelligence. Hu, Beuran and Tan (2020) demonstrate that
deep reinforcement learning can discover attack paths on real network setups,
establishing feasibility. Liu et al. (2026) provide a comprehensive review of the
field and categorize existing work into attack path planning and autonomous
penetration testing frameworks.

### 2.3.2 Why reinforcement learning

Model-based planning requires maintaining up-to-date models of exploits, which is
hard in a rapidly changing landscape. Model-free RL learns a policy through
interaction with the environment without such a model (Schwartz and Kurniawati,
2019). This makes RL a natural fit for attack planning, where the state is the
known configuration of the network and actions are available scans and exploits.
Simon and Mees (2024) provide a systematic comparison (SoK) of autonomous
penetration testing agents and note that through the rise of deep RL, agents have
emerged with the goal of actively assessing system security.

### 2.3.3 The central problem: generalization

The motivating problem of this project is that RL agents are typically trained and
evaluated on the same network. The evidence for the problem is consistent across several independent groups.
Janisch, Pevny and Lisy (2024) report that existing training setups do not produce
agents that behave reliably when the environment changes. Venturi et al. (2024)
put this to the test by holding out hosts from training and then asking the agents
to work on them; performance dropped noticeably. Zhou et al. (2025) make the
same point in different words: moving an agent to a scenario it never encountered
can degrade its policy sharply, even when the change looks small. Lukas et al.
(2026) go further and show that simply renumbering the IP addresses in a fixed
network is enough to break a trained agent's attack plan.

### 2.3.4 Why this matters now

Fernandes et al. (2026) survey the area from an operational cybersecurity
perspective and draw a blunt conclusion: the tools that exist are built for
particular setups, and as they put it, none supports end-to-end training that
generalises beyond controlled scenarios. That missing piece is precisely what
this project targets.

## 2.4 Technological Review

### 2.4.1 Environments for training RL agents

Three families of training environments dominate the literature. The first is
CybORG (Standen et al., 2021), the simulator behind the publicly released CAGE
exercises: Kiely et al. (2023) document how participants tackled CAGE Challenge 2,
and Emerson et al. (2024) ship CybORG++, whose MiniCAGE variant runs experiments
up to 1000x faster. Second, NASim and its
extension NASimEmu (Janisch et al., 2024) combine a simulator and an emulator with
a shared interface so agents can be trained in simulation and deployed in
emulation. Third, PenGym (Nguyen et al., 2025) provides a realistic training
environment supporting real pentesting actions and full automation of network
creation. Singh et al. (2024) extend the family toward hierarchical multi-agent
settings for cyber network defence. Each environment makes different trade-offs
between fidelity, speed, and scalability.

### 2.4.2 RL algorithms

Early work applied tabular and neural Q-learning (Schwartz and Kurniawati, 2019).
Hu, Beuran and Tan (2020) applied deep RL to automated pentesting. Becker et al.
(2024) systematically evaluate Q-learning, DQN, and A3C on NASim scenarios and find
A3C able to solve all scenarios with fewer actions. Kong et al. (2025) extend the
landscape toward LLM-based agents that learn attack logic from real-world
walkthroughs via two-stage reinforcement learning. Liu et al. (2026) and Moreno et
al. (2025) review the broader algorithm landscape, including hybrid approaches that
combine RL with recommender systems. Lopez-Montero et al. (2025) combine RL with
geometric deep learning to reduce the search space for web-application pentesting.

### 2.4.3 Training strategies relevant to generalization

The literature points to three candidate strategies for improving transfer.
Single-scenario training is the default but produces brittle policies (Lukas et
al., 2026). Domain randomization and curriculum approaches train on multiple
variants; Zhou et al. (2025) show that training on diverse domains improves
transfer to unseen scenarios, and Lukas et al. (2026) find that meta-learning can
adapt at test time. Fine-tuning on target variants is a third option. Critically,
no verified study compares these strategies against each other in the SAME
environment under a SAME protocol - this is Gap 1.

### 2.4.4 Reward design

Reward design is central to RL training. General RL literature (Shihab, Akter and Sharma, 2025)
documents proxy gaming, where agents exploit evaluator weaknesses rather than
improve intended objectives. Whether pentest agents learn genuine attack strategies
or game the reward function is unmeasured in the verified literature - this is Gap 3.

## 2.5 Evaluation and Benchmarking

The usual way researchers judge these agents is by episode reward, whether the
attack succeeded, and how many steps were needed, all measured on the very
network used for training (Schwartz and Kurniawati, 2019; Becker et al., 2024). Janisch et al. (2024) make the same criticism of the field's default metric:
scoring an agent on the data it trained on is not a realistic measure of
capability. Lukas et al. (2026) evaluate on a held-out unseen variant with baselines
including random policies, and Zhou et al. (2025) evaluate zero-shot transfer
and rapid adaptation.

Limitations in current evaluation practice: (a) most studies report only
in-distribution performance; (b) no standard benchmark exists for red-agent
generalization in CybORG (Gap 2); (c) baselines are inconsistent across studies
(Gap 2). This project addresses these by measuring a generalization gap (difference
between in-distribution and held-out performance) against scripted and random
baselines.

## 2.6 Existing Work (Thematic)

The literature can be organized into five categories, grouping the reviewed papers
by what they contribute.

### Category 1: Foundations

Early work established that reinforcement learning can find attack paths. Schwartz
and Kurniawati (2019) demonstrated model-free RL on simulated topologies, and Hu,
Beuran and Tan (2020) applied deep RL to real networks. These studies motivated the
field, but they evaluated on a single setting each, so scalability and transfer were
left open.

### Category 2: Environments

A second group supplied the training infrastructure: CybORG and the CAGE challenges
(Standen et al., 2021; Kiely et al., 2023), CyberBattleSim, used by Walter,
Ferguson-Walter and Ridley (2021) to study deception, NASimEmu (Janisch et al.,
2024), PenGym (Nguyen et al., 2025), and CybORG++ with MiniCAGE (Emerson et al.,
2024). Together they made training practical and progressively more realistic, but
they are tools: none of them resolves the generalization question.

### Category 3: Generalization evidence

A third group directly measures transfer. Venturi et al. (2024) show that
generalization to hosts held out from training fails in practice; Janisch et al.
(2024) report the same on NASimEmu; Zhou et al. (2025) show that even small scenario
changes degrade the policy; and Lukas et al. (2026) show that unseen IP reassignment
alone breaks long-horizon attack plans. This is the strongest and most consistent
evidence in the literature.

### Category 4: Methods proposed

Against that evidence, the methods proposed so far are domain randomization and
meta-RL (Zhou et al., 2025), adaptation and meta-learning (Lukas et al., 2026), and
curriculum-style training. Each is tested in isolation, in a different environment,
under a different protocol. None is compared against the alternatives in one
controlled setting.

### Category 5: Evaluation and reviews

Finally, practice and surveys set the measurement context. Schwartz and Kurniawati
(2019), Becker et al. (2024) and Janisch et al. (2024) show that agents are
conventionally scored on the network they trained on, which Janisch et al. call an
unrealistic measure. Reviews by Simon and Mees (2024), Moreno et al. (2025),
Liu et al. (2026) and Fernandes et al. (2026) confirm that the field is fragmented
and lacks a standard benchmark for end-to-end generalization.

### Identified gaps

From this synthesis, the following gaps emerge (each supported by multiple papers):
Gap 1 - no controlled comparison of training strategies; Gap 2 - no reproducible
benchmark harness with baselines for CybORG red-agent generalization; Gap 3 -
reward-gaming versus genuine strategy unmeasured; Gap 4 - CybORG red (attack) side
under-studied; Gap 5 - reality gap between simulation and emulation unverified;
Gap 6 - scalability beyond small networks untested.

## 2.7 Chapter Summary

This chapter reviewed the literature on RL-based autonomous penetration testing.
It established that (1) autonomous pentesting is motivated by a real skills
shortage, (2) RL is the natural technical approach, (3) environments such as CybORG,
NASimEmu, and PenGym make training feasible, and (4) a growing body of recent work
shows that trained agents fail to generalize to unseen networks. Six research gaps
were identified, centered on the absence of a controlled comparison of training
strategies and the lack of a reproducible benchmark for red-agent generalization in
CybORG. The next chapter proposes the methodology to address these gaps.

## 2.8 References

Kong, H., Hu, D., Ge, J., Li, L., Li, H. and Li, T. (2025). Pentest-R1: Towards autonomous penetration testing reasoning optimized via two-stage reinforcement learning. *arXiv preprint*, arXiv:2508.07382.

Emerson, H., Bates, L., Hicks, C. and Mavroudis, V. (2024). CybORG++: An enhanced gym for the development of autonomous cyber agents. *arXiv preprint*, arXiv:2410.16324.

Fernandes, R., Lopes, N., Goncalves, J. and Cosgrove, J. (2026). Autonomous pentesting using artificial intelligence: from the cybersecurity point-of-view. *Internet of Things*. doi:10.1016/j.iot.2026.101932.

Becker, N., Reti, D., Ntagiou, E.V., Wallum, M. and Schotten, H.D. (2024). Evaluation of reinforcement learning for autonomous penetration testing using A3C, Q-learning and DQN. *arXiv preprint*, arXiv:2407.15656.

Singh, A.V., Rathbun, E., Graham, E., Oakley, L., Boboila, S., Oprea, A. and Chin, P. (2024). Hierarchical multi-agent reinforcement learning for cyber network defense. *arXiv preprint*, arXiv:2410.17351.

Hu, Z., Beuran, R. and Tan, Y. (2020). Automated penetration testing using deep reinforcement learning. In *Proceedings of the 2020 IEEE European Symposium on Security and Privacy Workshops (EuroS&PW)*. doi:10.1109/eurospw51379.2020.00010.

Janisch, J., Pevny, T. and Lisy, V. (2024). NASimEmu: Network attack simulator and emulator for training agents generalizing to novel scenarios. In *Lecture Notes in Computer Science* (ESORICS 2023 International Workshops), Springer. doi:10.1007/978-3-031-54129-2_35.

Liu, J., Zhang, Y., Zhou, S., Yang, J., Lu, Y. and Zhong, X. (2026). Autonomous penetration testing using reinforcement learning: a review and perspectives. *Expert Systems with Applications*. doi:10.1016/j.eswa.2025.130219.

Moreno, A.C., Hernandez-Suarez, A., Sanchez-Perez, G., Toscano-Medina, L.K., Perez-Meana, H., Portillo-Portillo, J., Olivares-Mercado, J. and Garcia Villalba, L.J. (2025). Analysis of autonomous penetration testing through reinforcement learning and recommender systems. *Sensors*, 25(1), 211. doi:10.3390/s25010211.

Nguyen, H.P.T., Hasegawa, K., Fukushima, K. and Beuran, R. (2025). PenGym: Realistic training environment for reinforcement learning pentesting agents. *Computers & Security*, 148, 104140. doi:10.1016/j.cose.2024.104140.

Lukas, O., Shin, J., Rivas, E., Forni, D., Rigaki, M., Catania, C., Piplai, A., Kiekintveld, C. and Garcia, S. (2026). Evaluating generalization mechanisms in autonomous cyber attack agents. *arXiv preprint*, arXiv:2603.10041.

Schwartz, J. and Kurniawati, H. (2019). Autonomous penetration testing using reinforcement learning. *arXiv preprint*, arXiv:1905.05965.

Simon, R. and Mees, W. (2024). SoK: A comparison of autonomous penetration testing agents. In *Proceedings of the 19th International Conference on Availability, Reliability and Security (ARES 2024)*. doi:10.1145/3664476.3664484.

Standen, M., Lucas, M., Bowman, D., Richer, T.J., Kim, J. and Marriott, D. (2021). CybORG: A gym for the development of autonomous cyber agents. *arXiv preprint*, arXiv:2108.09118.

Lopez-Montero, D., Alvarez-Aldana, J.L., Morales-Martinez, A., Gil-Lopez, M. and Garcia, J.M.A. (2025). Reinforcement learning for automated cybersecurity penetration testing. *arXiv preprint*, arXiv:2507.02969.

Venturi, A., Andreolini, M., Marchetti, M. and Colajanni, M. (2024). Assessing generalizability of deep reinforcement learning algorithms for automated vulnerability assessment and penetration testing. *Array*, 24, 100365. doi:10.1016/j.array.2024.100365.

Kiely, M., Bowman, D., Standen, M. and Moir, C. (2023). On autonomous agents in a cyber defence environment. *arXiv preprint*, arXiv:2309.07388.

Zhou, S., Liu, J., Lu, Y., Yang, J., Zhang, Y. and Chen, J. (2025). Mind the gap: Towards generalizable autonomous penetration testing via domain randomization and meta-reinforcement learning. *Frontiers of Information Technology & Electronic Engineering*. doi:10.1631/fitee.2500100.

Li, Y. and Dai, H. (2024). Knowledge-informed auto-penetration testing based on reinforcement learning with reward machine. *arXiv preprint*, arXiv:2405.15908.

Shihab, I.F., Akter, S. and Sharma, A. (2025). Detecting proxy gaming in RL and LLM alignment via evaluator stress tests. *arXiv preprint*, arXiv:2507.05619.

Walter, E., Ferguson-Walter, K. and Ridley, A. (2021). Incorporating deception into CyberBattleSim for autonomous defense. *arXiv preprint*, arXiv:2108.13980.

## Appendix A: Literature Summary Table

The full literature summary table of the 20 verified papers reviewed in this chapter, with what each studied, key findings, and the gaps identified.

| # | Paper (verified link) | Year | What it studied | Key finding | Identified gap |
|---|---|---|---|---|---|
| 1 | Schwartz & Kurniawati - https://arxiv.org/abs/1905.05965 | 2019 | RL for autonomous pentesting (model-free) | RL can automate pentesting; addresses skills shortage | "Only practical for smaller networks"; scalability untested |
| 2 | Hu, Beuran & Tan - https://doi.org/10.1109/eurospw51379.2020.00010 | 2020 | DRL applied to automated pentesting | DRL discovers attack paths on real network | Limited/single network scenarios only |
| 3 | Standen et al. (CybORG/CAGE) - https://github.com/cage-challenge/CyBORG | 2021 | CybORG environment + CAGE challenges | Standard env for cyber RL training/eval | Generalization across topologies not the focus |
| 4 | Janisch et al. (NASimEmu) - https://arxiv.org/abs/2305.17246 | 2023 | Simulator+emulator for pentest RL | Agents transfer poorly to novel topologies/sizes | Reality gap + scalability; "unrealistic metric on training data" |
| 5 | Kiely et al. (CAGE-2) - https://arxiv.org/abs/2309.07388 | 2023 | CAGE Challenge 2 participant approaches | CAGE-2 standard single-blue-agent env | Defence focus; red-agent generalization unstudied |
| 6 | Simon & Mees (SoK) - https://doi.org/10.1145/3664476.3664484 | 2024 | Systematic comparison of autonomous pentest agents | Agent types/tools compared | Field fragmented; no standard generalization eval |
| 7 | Nguyen et al. (PenGym) - https://doi.org/10.1016/j.cose.2024.104140 | 2025 | Realistic env for pentest RL agents | Real actions + full automation improve training | Environment work; no cross-network generalization measure |
| 8 | Becker et al. - https://arxiv.org/abs/2407.15656 | 2024 | Q-learning, DQN, A3C agents on NASim | Algorithm comparison; A3C generalizes in small scenarios | "Rather small scenarios and small state/action spaces" |
| 9 | Venturi et al. - https://doi.org/10.1016/j.array.2024.100365 | 2024 | DRL generalization to unseen hosts (VAPT) | Explicitly: agents fail on hosts not in training | Confirms generalization gap; no strategy comparison |
| 10 | Zhou et al. (Mind the Gap / GAP) - https://arxiv.org/abs/2412.04078 | 2024/25 | Domain randomization + meta-RL for generalization | Training on diverse domains improves transfer | Two challenges named: realism + generalization; single framework |
| 11 | Lukas et al. - https://arxiv.org/html/2603.10041v1 | 2026 | Agent generalization on unseen IP-range variant | Agents brittle; meta-learning adapts; LLM agents trade compute for transparency | Only a few strategies; NetSecGame env not CybORG |
| 12 | Emerson et al. (CybORG++) - https://arxiv.org/abs/2410.16324 | 2024 | CybORG++ env, MiniCAGE 1000x faster | Tooling makes experiments feasible | Tooling paper; not a generalization study |
| 13 | Singh et al. - https://arxiv.org/abs/2410.17351 | 2024 | Hierarchical MARL for cyber defence | MARL handles complex defence tasks | Defence agents; not red-team generalization |
| 14 | Kong et al. (Pentest-R1) - https://arxiv.org/abs/2508.07382 | 2025 | LLM-based pentesting via two-stage RL | LLMs learn attack logic from walkthroughs | LLM angle; not classical RL policy generalization |
| 15 | Moreno et al. - https://doi.org/10.3390/s25010211 | 2025 | RL + recommender systems for autonomous pentest | Hybrid approach analysis | Future work: RL in incremental, unpredictable environments |
| 16 | Liu et al. (review) - https://doi.org/10.1016/j.eswa.2025.130219 | 2026 | Review of RL in autonomous pentesting | Categorizes attack path planning vs frameworks | Future work: generalization remains open |
| 17 | Fernandes et al. - https://doi.org/10.1016/j.iot.2026.101932 | 2026 | Review of AI pentesting from cybersecurity view | Field fragmented; "none supports end-to-end training that generalises beyond controlled scenarios" | DIRECT gap quote: generalization unsupported |
| 18 | Lopez-Montero et al. - https://arxiv.org/abs/2507.02969 | 2025 | RL + geometric DL to reduce search space | Priors improve convergence on simulated web app | Single simulated webpage; no cross-network test |
| 19 | Li, Yan & Dai (DRLRM-PT) - https://arxiv.org/abs/2405.15908 | 2024 | Knowledge-informed auto-pentest with reward machines | Domain knowledge as reward machines addresses sampling efficiency, reward specification | Names "intricate reward specification" as a challenge - supports Gap 3; single-env eval |
| 20 | Walter et al. (CyberBattleSim) - https://arxiv.org/abs/2108.13980 | 2021 | Deceptive elements (honeypots) in CyberBattleSim | Attacker progress depends on number/location of decoys | Defense-side deception; red-agent generalization unmeasured |

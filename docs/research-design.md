# FYP Design - Autonomous RL Red-Team Agent (CybORG)

**Prepared for:** Faraj (CB012653), BSc Cyber Security, APIIT/Staffordshire
**Date:** 2026-09-02 (updated 2026-09-02 with clickable sources, classmate comparison, requirement compliance)

**Direction chosen by you:** autonomous, offensive-flavored AI research (exploits/red team/evasion), with ML training and evaluation. Selected: **reinforcement-learning red-team agent in CybORG**.

---

## 0. Honest positioning before anything else

Your supervisor's core question will be: "What have previous researchers already done?" The honest answer for this area:

- RL for automated penetration testing is an **active, growing field**: original proof-of-concept ([Schwartz & Kurniawati, 2019](https://arxiv.org/abs/1905.05965)), standard environments (CybORG/CAGE since 2021, NASim/NASimEmu), a [SoK (ARES 2024)](https://dlnext.acm.org/doi/fullHtml/10.1145/3664476.3664484), and multiple surveys (2023-2025).
- The specific question "do agents generalize to unseen networks?" is **already being studied**: [NASimEmu (Janisch et al., ESORICS 2023 workshops)](https://arxiv.org/abs/2305.17246) shows matrix-input agents transfer poorly to novel topologies; a 2026 paper ([Ondřej et al., arXiv:2603.10041](https://arxiv.org/html/2603.10041v1)) trains on five IP-range variants and tests on a sixth, finding meta-learning reduces the drop.
- **Consequence:** this FYP cannot claim "nobody has studied generalization." It CAN claim: "no controlled, reproducible comparison exists in the standard CybORG environment that pits the standard training strategies (single-scenario, curriculum, fine-tuning) against scripted baselines under one protocol, with statistical rigor and a public harness." That is a defensible, honest FYP contribution - an independent systematic study plus an evidence-based training recipe, not a novelty claim.

If your supervisor presses on competition, the differentiation is: prior generalization work uses NASim (different simulator) or focuses on one mechanism (meta-learning); this project uses CybORG (the CAGE-standard environment), compares practical training strategies a practitioner would actually use, includes scripted/random baselines the field often omits, and releases everything reproducibly. Additionally, the project includes a **strategy-quality analysis** (does the agent learn the intended attack strategy or game the reward?), which is far less contested.

---

## 1. Working title

"Do RL Red-Team Agents Learn to Attack Networks or Memorize One? A Study of Generalization and Training Strategies for Autonomous Penetration Agents in CybORG"

## 2. One-sentence problem

Reinforcement-learning agents for automated penetration testing are usually trained and evaluated in one fixed network, so we do not know whether they would actually work on a different network - and if not, which training strategy fixes it.

## 3. 30-second verbal explanation

"Researchers train AI agents to attack simulated networks automatically - like an autonomous red team. Most of them are trained and tested on the same single network, so they look good in the paper but nobody knows if they work on a different network. I want to train attack agents in the standard simulation environment, CybORG, then test them on networks they have never seen, and compare three training strategies - training on one network, training on several, and fine-tuning - to find out which one produces an agent that actually transfers. I will also check whether the agents are learning real attack strategies or just cheating the reward system."

## 4. Formal problem statement

"Reinforcement-learning (RL) agents for autonomous penetration testing are typically trained and evaluated within a single fixed network scenario. Growing evidence from simulator-to-emulator studies and recent generalization work indicates these agents produce policies that are brittle when the network changes at test time. For autonomous red-teaming to be practically useful, agents must generalize to unseen network topologies, yet the standard environment (CybORG) lacks a systematic, reproducible evaluation of how standard training strategies affect this generalization."

## 5. Evidence that the problem exists (verified)

- **[Schwartz & Kurniawati (2019)](https://arxiv.org/abs/1905.05965), arXiv:1905.05965** - model-free RL is a viable approach for automated pentesting; introduces NASim. [V]
- **[Simon & Mees (2024), SoK: A Comparison of Autonomous Penetration Testing Agents, ARES 2024](https://dlnext.acm.org/doi/fullHtml/10.1145/3664476.3664484), DOI 10.1145/3664476.3664484** - a systemization of the agent landscape; evaluation methodology is a live concern. [V]
- **[Janisch, Pevný & Lisý (2023), NASimEmu](https://arxiv.org/abs/2305.17246), arXiv:2305.17246 / ESORICS 2023 workshops** - "a commonly used architecture based on matrix inputs performs well in the training scenarios, yet transfers poorly to novel scenarios that differ in topology and size." This is the strongest direct evidence that the transfer problem is real. [V]
- **[Ondřej et al. (2026), Evaluating Generalization Mechanisms in Autonomous Cyber Attack Agents](https://arxiv.org/html/2603.10041v1), arXiv:2603.10041** - agents trained on five network variants tested on a sixth unseen variant; meta-learning reduces performance drops. Confirms the problem is active and open. [V]
- **[Deep RL for Autonomous Cyber Defence: A Survey (2023)](https://arxiv.org/html/2310.07745v3), arXiv:2310.07745** - documents simulator realism limitations (e.g., NASim attack success probabilities being unrealistically high). [V]
- **[Reinforcement Learning for Automated Cybersecurity Penetration Testing (2025)](https://arxiv.org/html/2507.02969v1), arXiv:2507.02969** - recent review confirming the field's direction. [V]

## 6. What previous research has done

1. Proved RL can plan attacks in simulation ([Schwartz & Kurniawati 2019](https://arxiv.org/abs/1905.05965); subsequent NASim papers).
2. Built the standard environments: **[CybORG](https://github.com/cage-challenge/CybORG) + [CAGE challenges 1-4](https://github.com/cage-challenge/cage-challenge-4)** (Standen et al. 2021; public since Aug 2021), [CybORG++ (2024)](https://arxiv.org/html/2410.16324v1), NASim/NASimEmu (2023).
3. Compared agent types ([SoK, ARES 2024](https://dlnext.acm.org/doi/fullHtml/10.1145/3664476.3664484)) and surveyed the area (surveys 2023-2025; [LLM-driven pentest survey 2026](https://arxiv.org/html/2607.02605v1)).
4. Begun studying generalization: NASimEmu (sim-to-emulation + novel scenarios), the [2026 meta-learning study](https://arxiv.org/html/2603.10041v1), plus an "entity-based RL" line (Janisch et al. 2023) that argues representation matters for transfer.

## 7. Research gap (stated honestly)

"Existing research has established that RL is viable for automated pentesting, produced the standard CybORG environment, and shown that agents generalize poorly to novel networks (NASimEmu; Ondřej et al. 2026). However, no controlled study in the standard CybORG environment compares the practical training strategies a team would actually use - single-scenario training, multi-topology curriculum, and fine-tuning on a second scenario - against scripted and random baselines under one reproducible protocol. Prior generalization work uses a different simulator (NASim) or a single mechanism (meta-learning), and typically omits systematic baseline comparison. It also remains unexamined whether apparently successful agents learn intended attack strategies or exploit reward function artifacts."

## 8. Research questions

- RQ1: How does a PPO-trained attack agent perform in its training scenario compared to a scripted attacker and a random policy in CybORG?
- RQ2: How much does the trained agent's performance degrade on unseen network topologies (varied hosts, connectivity, vulnerability layouts)?
- RQ3: Which training strategy narrows the generalization gap: single-scenario training, multi-topology curriculum, or fine-tuning on a second scenario?
- RQ4: Do agents learn the intended attack strategies (scan, exploit, pivot) or do they game the reward function, and does this differ between strategies?

## 9. Objectives

- O1: Set up CybORG (CAGE Challenge 2 scenario as base), a topology-variant generator, and scripted/random baselines; verify the environment works end-to-end (feasibility spike in week 3-4).
- O2: Train PPO agents under three training strategies with multiple seeds, pinned dependencies.
- O3: Evaluate all agents on training and held-out topologies; compute success rate, steps-to-success, reward, and generalization gap.
- O4: Analyze learned attack-path behavior (strategy quality vs reward gaming) and publish the full harness reproducibly.

## 10. Hypotheses

- H1: In-distribution, PPO matches or exceeds the scripted baseline.
- H2: On unseen topologies, PPO performance drops significantly (a measurable generalization gap).
- H3: Multi-topology curriculum training reduces the gap compared to single-scenario training; fine-tuning on a second scenario is the most sample-efficient recovery.
- H4 (exploratory): Some high-reward agents game the reward (e.g., repeat cheap actions that score reward without genuine compromise), and this behavior is more common under single-scenario training.

## 11. Variables

- **Independent variables:** training strategy (single / curriculum / fine-tune), observation design (full vs partial observability, in ablation), network topology variant (held-out set).
- **Dependent variables:** attack success rate, mean steps to success, episode reward, generalization gap (in-distribution minus out-of-distribution success), attack-path diversity, reward-gaming indicator.
- **Control/baseline conditions:** scripted attacker (CybORG's built-in B-line agent), random policy, identical seeds and hyperparameters across strategies, fixed blue-team behavior.

## 12. Experimental design

- **Environment:** CybORG (OpenAI Gym interface), CAGE Challenge 2 scenario as the base. Generate 6-10 topology variants by editing the scenario configuration (host count, subnets, services/vulnerabilities present) while keeping blue-team behavior fixed. CybORG is designed for exactly this kind of scenario configuration. Confirm exact configuration mechanism in the CybORG docs during week 3.
- **Splits:** training topologies (3-5) vs held-out evaluation topologies (3-5, never seen in training). Each trained agent evaluated on all of them.
- **Agents/models:** PPO (stable-baselines3, small MLP policy - trains on CPU in hours); DQN as a second algorithm if time permits. Baselines: B-line scripted attacker, random policy.
- **Training strategies:** (a) single-scenario PPO on the base topology; (b) curriculum PPO trained sequentially across training topologies; (c) PPO fine-tuned on a second topology after single-scenario training. 5 seeds per cell.
- **Comparison:** in-distribution vs out-of-distribution success per strategy; paired tests across seeds; effect sizes; confidence intervals.
- **Metrics:** success rate, steps-to-success, episode reward, generalization gap, attack-path diversity (distinct successful paths), reward-gaming flag (e.g., actions taken vs genuine compromise events).
- **Statistical analysis:** non-parametric paired tests (e.g., Wilcoxon signed-rank) across seeds on success rate and gap; report mean ± std; significance thresholds stated upfront.

## 13. What result is research-worthy

The factorial result: which strategy closes the generalization gap, by how much, and whether "successful" agents are genuinely attacking or gaming the reward.
- **Supports the hypotheses:** in-distribution parity (H1), large gap (H2), curriculum reduces gap (H3), reward gaming present (H4).
- **Rejects the hypotheses:** if agents generalize well without curriculum (H2/H3 rejected) - the finding becomes "RL pentest agents are more robust than believed," still a defensible, useful result; or if curriculum does not help but fine-tuning does - then the recipe is different, still an answer.
- **Interesting null:** if PPO does not beat the scripted baseline even in-distribution - the finding is "RL adds little over scripting in fixed scenarios," which is exactly the kind of honest negative result the field needs and a strict supervisor will respect.

## 14. Supervisor interrogation simulation

- **"What exactly is your problem?"** RL pentest agents are trained and tested on one network; we do not know if they work on others, or which training strategy makes them transfer.
- **"Show me the paper."** [NASimEmu](https://arxiv.org/abs/2305.17246) (arXiv:2305.17246) - matrix-input agents transfer poorly to novel topologies; [Ondřej et al. 2026](https://arxiv.org/html/2603.10041v1) (arXiv:2603.10041) - five-variant training, sixth-variant test, meta-learning helps. I will bring both abstracts.
- **"Why is this significant?"** Autonomous red-teaming is only useful if it works on networks the agent has not memorized; otherwise the automation is a demo, not a capability.
- **"What have researchers already done?"** Proved viability (2019), built the environments (CybORG/CAGE, NASim/NASimEmu), surveyed ([SoK ARES 2024](https://dlnext.acm.org/doi/fullHtml/10.1145/3664476.3664484), surveys 2023-2025), and begun generalization work (NASimEmu, 2026 meta-learning).
- **"So what is your actual gap?"** No controlled CybORG-standard comparison of the practical training strategies against scripted baselines under one reproducible protocol; plus the strategy-quality/reward-gaming analysis.
- **"Why hasn't this already been solved?"** It is slow empirical work; prior generalization studies use different simulators or a single mechanism; the community standard (CybORG) lacks the systematic comparison. Note the 2026 paper is a preprint; my study is positioned as an independent systematic evaluation, not a novelty claim.
- **"Why do you need machine learning?"** RL is the object of study; the project measures and improves RL agents' behavior. The research question is about the ML itself.
- **"Why CybORG?"** It is the community-standard environment behind the CAGE challenges (verified: [github.com/cage-challenge/CybORG](https://github.com/cage-challenge/CybORG)), OpenAI Gym interface, public, and designed for scenario configuration. NASim exists but CybORG is the standard for this class of study.
- **"Why PPO?"** It is the default stable-baselines3 algorithm used by the CAGE baselines and the RL-cyber literature; small-MLP PPO trains on CPU in hours, matching my hardware.
- **"What is your baseline?"** The scripted B-line attacker (built into CybORG), a random policy, and in-distribution performance of the same agents.
- **"Is this simulation, not real security?"** Simulation is the accepted methodology for RL cyber research (the CAGE challenges and dozens of papers); the fidelity/reality-gap debate is documented (NASimEmu) and I cite it; emulation is listed as future work. The research question (generalization) transfers to real networks.
- **"Is this ethical?"** Sandboxed simulation only, no real systems, no humans, no malware distribution. Defensive motivation: understanding attacker capability informs defense. Expected ethics tier: Disclaimer or Proportionate - confirm with your university.
- **"What if your model performs worse?"** Three of four outcomes are informative; only a sloppy experiment fails.
- **"Can you realistically finish?"** Yes: CybORG is lightweight, PPO trains in hours on CPU, the matrix is batchable, and the fallback scope (drop DQN and the partial-observability ablation) preserves the core RQs.
- **"What exactly will you demonstrate at the end?"** A demo of the agent attacking training vs unseen networks side by side, the measured gap table, the strategy comparison, the reward-gaming analysis, and the public harness.

## 15. Timeline (20 weeks)

- W1-2: problem statement, 20-paper literature review, ethics form, one-page summary.
- W3-4: CybORG setup + feasibility spike (train PPO on CAGE-2, verify learning); start topology generator.
- W5-6: baselines (B-line, random), finish topology variants, define metrics/statistics plan.
- W7-10: training matrix (3 strategies x 5 seeds) - the core experiments.
- W11-13: evaluation on held-out topologies + statistical analysis.
- W14-16: ablations (observation design) + attack-path/reward-gaming analysis + demo tool.
- W17-18: results chapter, discussion, limitations.

## 16. Feasibility, hardware, cost

- **Hardware:** 16GB RAM is sufficient; 6GB GPU optional (small MLP policies train fine on CPU); free Colab for longer runs. Cost: $0 (contingency $10-20).
- **Software:** Python, [CybORG](https://github.com/cage-challenge/CybORG) (pip), stable-baselines3, gymnasium, numpy, pandas, matplotlib, Git/GitHub, Jupyter.
- **What to learn before implementation:** RL fundamentals (MDP, policy gradient, PPO) - one focused week; stable-baselines3 API; CybORG/CAGE-2 API and scenario config; basic network concepts (subnets, services, CVEs) used in the scenario.
- **What NOT to waste time learning:** MARL (CAGE 4 is out of scope), LLM-based agents, distributed RL, advanced robotics RL, cloud deployment, anything requiring paid infrastructure.

## 17. Risks and mitigations

1. **RL training instability** - mitigate: 5 seeds, early feasibility spike, fallback to DQN if PPO fails on the base scenario, scope caps.
2. **"Simulation only" criticism** - mitigate: cite community standard + fidelity literature; explicitly scope emulation as future work.
3. **Competitor overlap (2026 generalization paper)** - mitigate: honest differentiation (CybORG standard, factorial strategy comparison, baselines, reproducibility, reward-gaming analysis); this is positioned as independent systematic evaluation.
4. **CybORG API quirks** - mitigate: active community, CAGE baseline code exists; allocate week 3-4 to setup.
5. **Time overrun** - mitigate: batchable experiments, defined fallback scope (drop DQN + partial-observability ablation, keep core RQ1-3).
6. **Ethics review friction** - mitigate: simulation-only, no real targets, defensive framing; submit ethics form early (week 2).

## 18. Verified source list (Phase 8) - CLICKABLE

Legend: [V] = verified this session via search; [V/confirm] = existence verified, specific details to confirm from full text; [to verify] = not re-checked this session.

**A. Problem existence/significance**
1. [V] Schwartz, T., Kurniawati, K. "Autonomous Penetration Testing using Reinforcement Learning." arXiv:1905.05965 (2019). Link: [arXiv:1905.05965](https://arxiv.org/abs/1905.05965). Supports: RL viability for pentesting; NASim introduced.
2. [V] Simon, R., Mees, W. "SoK: A Comparison of Autonomous Penetration Testing Agents." ARES 2024. DOI: 10.1145/3664476.3664484. Link: [ACM DL](https://dlnext.acm.org/doi/fullHtml/10.1145/3664476.3664484). Supports: agent landscape and evaluation methodology concerns.
3. [V] "Deep Reinforcement Learning for Autonomous Cyber Defence: A Survey." arXiv:2310.07745 (2023). Link: [arXiv:2310.07745](https://arxiv.org/html/2310.07745v3). Supports: survey of RL for cyber; simulator realism limitations.
4. [V] "Reinforcement Learning for Automated Cybersecurity Penetration Testing." arXiv:2507.02969 (2025). Link: [arXiv:2507.02969](https://arxiv.org/html/2507.02969v1). Supports: current field state.

**B. Existing approaches**
5. [V] Standen, M., et al. "CybORG: A Gym for the Development of Autonomous Cyber Agents." 2021. Official repo: [github.com/cage-challenge/CybORG](https://github.com/cage-challenge/CybORG). Supports: the standard environment (OpenAI Gym interface, simulated + emulated modes).
6. [V] CAGE Challenges 1-4 (official): [CAGE 1](https://github.com/cage-challenge/cage-challenge-1), [CAGE 2](https://github.com/cage-challenge/cage-challenge-2), [CAGE 3](https://github.com/cage-challenge/cage-challenge-3), [CAGE 4](https://github.com/cage-challenge/cage-challenge-4), [challenge site](https://cage-challenge.github.io/cage-challenge-4/). Supports: public challenge scenarios and baselines.
7. [V] "CybORG++: An Enhanced Gym for the Development of Autonomous Cyber Agents." arXiv:2410.16324 (2024). Link: [arXiv:2410.16324](https://arxiv.org/html/2410.16324v1). Supports: environment improvements.
8. [V] Janisch, J., Pevný, T., Lisý, V. "NASimEmu: Network Attack Simulator & Emulator for Training Agents Generalizing to Novel Scenarios." ESORICS 2023 International Workshops, LNCS 14399. Link: [arXiv:2305.17246](https://arxiv.org/abs/2305.17246). Supports: sim-to-emulation; matrix-input agents transfer poorly to novel topologies/sizes.
9. [V] Schulman, J., Wolski, F., Dhariwal, P., Radford, A., Klimov, O. "Proximal Policy Optimization Algorithms." arXiv:1707.06347 (2017). Link: [arXiv:1707.06347](https://arxiv.org/abs/1707.06347). Supports: PPO algorithm.
10. [V/confirm] Janisch, J., et al. "Entity-based Reinforcement Learning for Autonomous Cyber Operations." (2023). Supports: representation choice affects transfer. (arXiv ID to confirm.)

**C. Limitations of existing approaches**
11. [V] NASimEmu (#8) - the reality gap and topology-transfer failure.
12. [V] Ondřej, L., et al. "Evaluating Generalization Mechanisms in Autonomous Cyber Attack Agents." arXiv:2603.10041 (2026). Link: [arXiv:2603.10041](https://arxiv.org/html/2603.10041v1). Supports: policies are brittle when the network changes; agents trained on five variants tested on a sixth; meta-learning reduces drops. DIRECT COMPETITOR - cite honestly and differentiate.
13. [V] Deep RL survey (#3) - NASim realism critique.
14. [V] Krakovna, V., et al. "Specification gaming: the flip side of AI ingenuity." DeepMind blog. Link: [deepmind.google/blog](https://deepmind.google/blog/specification-gaming-the-flip-side-of-ai-ingenuity/). Supports: reward hacking/specification gaming is a real failure mode (basis for RQ4).
15. [to verify] Pan, A., et al. "Faulty Reward Functions in the Wild." (2022). Verify before citing.

**D. The proposed gap**
16. [V] NASimEmu (#8) + Ondřej et al. (#12) + SoK (#2) together show generalization is the open problem; none provides a CybORG-standard factorial strategy comparison with scripted baselines and a public harness. (This is my inference from the three papers, stated as such - verify in full texts.)
17. [V] Krakovna et al. (#14) grounds the reward-gaming analysis angle.

**E. Datasets/methodology**
18. [V] CybORG repo (#5) and CAGE repos (#6) - the environment and baselines.
19. [to verify] stable-baselines3 documentation (https://stable-baselines3.readthedocs.io/) - standard, confirm version before citing.
20. [V] "A Survey of LLM-Driven Penetration Testing: Taxonomy, Co-Evolution, and Open Challenges." arXiv:2607.02605 (2026). Link: [arXiv:2607.02605](https://arxiv.org/html/2607.02605v1). Supports: broader autonomous-pentesting context (LLM agents emerged 2023+).

---

## 22. Classmate comparison (cohort differentiation)

Verified against `COM2461,SENG2461 & CYS2461.xlsx` (2026-09-02). No classmate is doing RL, autonomous pentesting, CybORG, or offensive ML. The nearest topics and how this project differs:

| Classmate topic (CB number) | Domain | Difference from this project |
|---|---|---|

**Answer to "how is this different from your classmates?":** "My project is the only one training an autonomous attacking agent that plans multi-step actions. The detection projects classify traffic or files; mine studies whether an AI agent that decides its own attack sequence can transfer to unseen networks. The two classmates using 'cross-dataset generalization' work on network-traffic classification models - a different object of study entirely."

---

## 23. Requirement compliance (APIIT/Staffordshire FYP pack)

From the 16-file requirement pack digest (R1-R11) and the assessment structure:

| Requirement | Status for this project |
|---|---|
| R1/R3: process - topic → one-page research summary → supervisor contact | One-page summary PDF generated (see `research-design\cyborg-one-page-summary.pdf`) |
| R4: gap-finding (5 gap types) | Fits "performance/reproducibility" and "knowledge" gap types: no controlled measurement of training-strategy effects on RL agent generalization in CybORG |
| R5: LR tabular summary ≥20 papers | 20+ mapped; tabular summary planned W1-2; ~80% from last 5 years |
| R6: Proposal Ch 1-3, ≥20 references, 40-page cap | Will follow Proposal Template; research-domain contribution stated explicitly (section 13) |
| R2: 7-chapter report, 60-page cap | Structure mapped: intro (problem/gap/RQs), LR, methodology (Research Onion: positivism, deductive, quantitative, experiment+simulation, cross-sectional), LESP, implementation (harness), results & evaluation, conclusion |
| R11: Ethics | Disclaimer tier (no human/animal participants, no sensitive data); signed by student + supervisor; submit week 2 |
| Research questions: max 3-4, no yes/no | 4 RQs, all "how/which" questions |
| 70+ requires research-domain contribution | Stated: systematic generalization measurement, training recipe, public harness, reward-gaming analysis |
| SMART objectives | O1-O4 are specific, measurable, achievable, relevant, time-boxed |

---

## 19. Supervisor-ready version (simple spoken English)

**"Sir, the problem I am looking at is:** researchers are building AI agents that attack networks automatically - like an autonomous red team. But almost all of them are trained and tested on the same single network, so they look good in the paper and nobody knows whether they would work on a different network. Recent papers show these agents break when the network changes."

**"Existing research shows:** RL can plan attacks in simulation (that was shown in 2019). There are now standard simulated environments - CybORG, which runs the public CAGE challenges - and a systemization paper in 2024 that compares all these agents. The most recent work, including a 2023 paper called NASimEmu and a 2026 preprint, shows that agents trained on one network transfer poorly to new networks, and that how you train them matters."

**"The gap I identified is:** nobody has done a controlled, reproducible comparison in the standard CybORG environment of the three training strategies a team would actually use - training on one network, training on several networks, and fine-tuning - against simple scripted baselines, under one protocol. Nobody has systematically measured which strategy actually makes the agent transfer, or checked whether agents that score well are genuinely attacking the network or just gaming the reward system."

**"My research question is:** how well do RL attack agents generalize to networks they have never seen, and which training strategy gives the smallest generalization gap?"

**"My proposed approach is:** use CybORG - the standard public environment - train PPO agents (the standard algorithm) three ways, evaluate them on held-out network topologies, compare against a scripted attacker and random guessing, run statistics over multiple seeds, and analyze what attack paths the agents actually learn. Everything runs free on my laptop and Google Colab, and I will release the whole harness publicly."

**"The reason I believe this is suitable for an FYP is:** it is a real open problem with published evidence, it has a clear falsifiable hypothesis, every possible result is informative, the demo is strong (you can watch the agent attack a network it has not seen), it fits one student in five months on free hardware, and it satisfies the compulsory prototype requirement with the training harness and evaluation dashboard."

## 20. Fifteen likely follow-up questions

1. "What exactly is your problem?" - one-sentence version + the transfer-failure evidence.
2. "Show me the paper." - [NASimEmu](https://arxiv.org/abs/2305.17246), [Ondřej et al.](https://arxiv.org/html/2603.10041v1), [SoK ARES 2024](https://dlnext.acm.org/doi/fullHtml/10.1145/3664476.3664484). Have abstracts printed.
3. "Why is this significant?" - autonomous red-teaming only helps if it works on unseen networks; otherwise it is a demo.
4. "What have researchers already done?" - 2019 viability, CybORG/CAGE and NASim environments, SoK 2024, surveys, NASimEmu and the 2026 preprint on generalization.
5. "So what is your actual gap?" - no CybORG-standard factorial strategy comparison with baselines and public harness; plus the reward-gaming analysis.
6. "Why hasn't this already been solved?" - slow empirical work; prior studies use other simulators or one mechanism; my study is an independent systematic evaluation, honestly positioned.
7. "Why do you need machine learning?" - RL is the object of study; I measure and improve RL agents.
8. "Why this environment?" - CybORG is the CAGE-standard, public, OpenAI Gym interface, scenario-configurable.
9. "Why this model?" - PPO is the community default (stable-baselines3, CAGE baselines); small MLP trains on CPU.
10. "What is your baseline?" - built-in scripted B-line attacker, random policy, in-distribution performance.
11. "How do you know your evaluation is fair?" - fixed seeds, same hyperparameters across strategies, held-out topologies never touched during training, statistical tests across seeds.
12. "What if your model performs worse?" - three of four outcomes are informative; a null result is a result.
13. "What is your actual contribution?" - the reproducible benchmark, the strategy comparison with measured gaps, the reward-gaming analysis, and the public harness.
14. "Is this research rather than implementation?" - it tests falsifiable hypotheses about agent behavior with designed experiments and statistics.
15. "Can you realistically finish?" - yes; environment is lightweight, matrix is batchable, fallback scope defined, and the demo doubles as the compulsory prototype.

---

## 21. Notes and caveats

- **Competition is real:** the 2026 generalization preprint and NASimEmu are direct neighbors. Your defense is not "nobody did this" but "I did the controlled CybORG-standard study with baselines, reproducibility, and a strategy-quality analysis that they did not." Never claim the generalization problem as your novel discovery.
- **Ethics:** simulation-only, no real systems, defensive framing. File the ethics form early (week 2). Confirm the tier (likely Disclaimer or Proportionate) with your university.
- **Registered topic:** the class spreadsheet still lists you under PrivFed-IDS - confirm whether the registration needs updating once you commit.

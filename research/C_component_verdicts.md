# Step C — Component-by-component novelty & validity verdicts on YOUR original architecture

Legend: **[J]** peer-reviewed reputed journal/conference · **[J-low]** peer-reviewed lower-tier · **[P]** preprint/workshop (report only, not proof). "Verified via" = search-engine abstract/summary (full text blocked in this environment) unless noted.

## N1 — "5-way decomposition of the gap has never been done; prior work splits at most two causes"
**Verdict: FALSE as a method claim. Application-level novelty only.**
- Zhang, Singh, Ghassemi, Joshi, *"Why did the Model Fail?": Attributing Model Performance Changes to Distribution Shifts*, **ICML 2023**, PMLR 202:41550–41578 **[J]** — performance change is a cooperative game whose players are **any set of distributions** (causal mechanisms); importance weighting + **Shapley values**; code github.com/MLforHealth/expl_perf_drop. This is exactly your Stage-1 machinery, for any number of mechanisms.
- Cai, Namkoong, Yadlowsky, *Diagnosing Model Performance Under Distribution Shift* (DISDE), **Operations Research**, online 18 Dec 2025, doi 10.1287/opre.2023.0217 **[J]**.
- Hierarchical decomposition (HDPD), **NeurIPS 2024** (arXiv 2402.14254) **[J]**.
- Imaging: Roschewitz et al. 2024 (2411.07940) **[P]**; Gordaliza et al. 2025 (2512.09094, MedEurIPS workshop) **[P]**.
- Your IEEE Table I compares only against Roschewitz and Gordaliza and claims ">2 causes" is new. A reviewer who knows Zhang et al. ICML 2023 will reject that claim immediately.
- **Validity problem (independent of novelty):** your Component 3 ("benign morphology") is estimated as the FPR gap on confirmed-benign ECGs and Component 4 as the TPR gap on confirmed-disease ECGs. These are *metrics on strata*, not counterfactual "switches", so they are not players in a Shapley game whose values sum to the gap. As written, the "five shares sum exactly to the full gap" claim does not hold for components 3–4.

## N2 — "No one has looked inside an ECG model; SAEs never pointed at an ECG foundation model"
**Verdict: FALSE.**
- Peer-reviewed ECG representation interpretability predates this: van de Leur et al., FactorECG (β-VAE, 21 explainable generative ECG factors, 1.1M ECGs), *Eur Heart J – Digital Health* 3(3):390–404 (2022) **[J]**; follow-ups in EHJ-DH 2022 (doi 10.1093/ehjdh/ztac063) **[J]**; unsupervised ECG representation learning for disease profiling, *npj Digit Med* 2024 (doi 10.1038/s41746-024-01418-9) **[J]**.
- SAEs on ECG foundation models: **ECG-InterpBench** (Duan & Qiu, Rice; arXiv 2607.27404, 29 Jul 2026) — BatchTopK SAEs on 6 frozen ECG FMs × 5 depths × 5 widths × 3 seeds; 49 clinical measurements; MIMIC-IV-ECG replication **[P]**. **CADENCE** (arXiv 2607.25244) — 8,192 "cardiac atoms" from >9M ECG tokens; **targeted ablation of atoms causes selective output changes** **[P]**.
- What is still open: nobody (peer-reviewed or preprint) has used SAEs/causal patching on ECG FMs for **race/ancestry/population**. That is a *question*, not a tool novelty.

## N3 — "No inference-time, label-free correction on any medical time-series model; LEACE never applied to a medical time-series encoder; every existing fix needs retraining"
**Verdict: Mostly FALSE / weak.**
- LEACE itself: Belrose et al., **NeurIPS 2023** **[J]**.
- LEACE on physiological FM embeddings: *The Identity Trap in EEG Foundation Models* (arXiv 2606.06647, June 2026) — LEACE erases a subject-identity axis from EEG FM embeddings and improves clinical decoding **[P]**.
- Post-hoc (no retraining) removal of protected attributes from medical embeddings: *Post-hoc Orthogonalization for Mitigation of Protected Feature Bias in CXR Embeddings* (arXiv 2311.01349; Springer LNCS chapter 2025) **[J-low]**; adversarial debiasing of frozen CT FM embeddings (2502.04386) **[P]**.
- "Every fix needs retraining" is false even in ECG: Kaur et al. *Circ Heart Fail* 2024 **[J]** fixed much of the gap with **group-specific thresholds** (no retraining); Lotter *Nat Commun* 2024 **[J]** cut CXR underdiagnosis bias 46–67% with view-specific thresholds; Kina & Petersen 2026 (MLMI@MICCAI) **[P]** post-hoc prevalence recalibration.
- **Validity problems with the LEACE step (these matter more than novelty):**
  1. LEACE is only **linear** guardedness. An RBF-SVM still recovers LEACE-erased concepts at 70–95% accuracy (reported in follow-up work); kernel erasure protecting against one non-linear adversary leaves the concept readable by another (Ravfogel et al., *EMNLP 2022* "Adversarial Concept Erasure in Kernel Space") **[J]**.
  2. **Erasing demographic information can create new bias.** Petersen, Ferrante, Ganz, Feragen (arXiv 2305.01397, 2023) **[P]**; Parikh, Petersen, … Feragen, *Is invariance all you need for algorithmic fairness? Removing demographic information can create new bias* (arXiv 2609.32004, 25 Sep 2026) **[P]** — mathematically and empirically: when demographics correlate with labels, some encoding is *necessary*; enforcing invariance can hamper mitigation and create new bias.
  3. Precedent that erasure does not close gaps: Yang et al., *Nature Medicine* 2024 **[J]** (shortcut removal gives "locally optimal" fairness that does not transfer); Kim et al., *Diagnostics* 2026 **[J-low]** (race encoded at AUROC 0.83 in CXR FMs but linear *use* ≈ 0.0015; residualisation did not narrow gaps).
  4. In ECG the population signal sits in the **same waveform components that diagnose disease** (QRS voltage; Bollepalli 2025 found QRS is the key race feature) **[J]**, so "remove population, keep pathology" may be physically impossible for exactly the tasks where gaps exist (e.g., LVH). Your own White Sheet recognised this risk ("wrecking-ball entanglement"); the literature now makes it the *expected* outcome, not an edge case.

# Technical Companion & Pre-Registration
### The runnable protocol layer — so the research can be executed and published without further guidance

**This is companion document #3.** Document #1 = *White Sheet v2* (why this matters, verified state of the art). Document #2 = *Execution Playbook* (the branching architecture, data, tools, work breakdown). **This document** = the *runnable detail*: the exact causal-decomposition math, the exact mechanistic and surgical protocols, the statistical plan, and a **ready-to-file pre-registration**. If you have only these three documents and no way to reach me, you have everything needed to finish and publish.

**New confirming evidence (July 2026 verification pass):** A June-2026 ECG-fairness paper (DA-GAT-v2, *Scientific Reports* 10.1038/s41598-026-54206-8) explicitly names the gap this project fills — it states that race/ethnicity-annotated evaluation *"such as the MIMIC-IV-ECG database) is required before cross-ethnic fairness claims can be substantiated."* Cite it as motivation: the field has publicly flagged that nobody has done the cross-ancestry ECG analysis you begin with. Also confirmed: SAEs have been applied to LLMs, protein LMs (PNAS/Nature Methods), pathology and **EEG** foundation models (arXiv 2605.13930) — **but not ECG**; and a directly useful editing tool exists — **Sparse Feature Circuits** (Marks et al., ICLR 2025: "discovering and editing interpretable causal graphs").

---

## PART A — The formal causal-decomposition methodology (Stage 1)

This is the technical heart of the "what is the gap made of?" study and the part most likely to trip up an executor. Follow it literally.

### A.1 Notation and the causal model

Variables:
- **Y** — the true clinical state / diagnostic target (ideally gold-standard, e.g., CMR-confirmed or adjudicated).
- **E** — population/ancestry ("environment"), e.g., E ∈ {source, target} (or multi-valued across ancestry groups).
- **A** — acquisition context: device, site, sampling, and digital-vs-paper rendering.
- **X** — the observed ECG signal.
- **L** — the *recorded* label (annotation), which may differ from Y across sites/ontologies.
- **f** — the *frozen* foundation model trained on the source distribution; **f(X)** its prediction.

The data-generating process factorizes as:

```
P(Y, E, A, X, L) = P(E) · P(A | E) · P(Y | E) · P(X | Y, E, A) · P(L | Y, E)
```

### A.2 The quantity to decompose

Primary harm = **excess false-positive rate on genuinely benign ECGs** (the "benign variant misread as pathology" failure):

```
EFPR(e) = P( f(X) flags disease  |  Y = benign, E = e )
Gap      = ΔEFPR = EFPR(target) − EFPR(source)      (≥ 0 expected)
```

(Also decompose the AUROC gap and the sensitivity gap in parallel, using the same machinery.)

### A.3 The five components as formal estimands

The source→target gap is driven by five mechanism shifts:

| Component | The shift | Plain meaning |
|---|---|---|
| **1. Prevalence** | `P(Y \| E)` | disease base rates differ by population |
| **2. Acquisition** | `P(A \| E)` | device / paper-vs-digital differs by population |
| **3. Benign-morphology** | the *diagnostically irrelevant* part of `P(X \| Y=benign, E)` | benign regional waveform differences |
| **4. Pathology-manifestation** | the *diagnostically relevant* part of `P(X \| Y=disease, E)` | disease genuinely looks different |
| **5. Labeling** | `P(L \| Y, E)` | annotation thresholds/ontologies differ |

### A.4 Identification — the design that makes this possible

The field's blocker (per the shift-ID literature) is that cross-country comparisons confound population with acquisition. **We resolve it with the within-site ancestry design:**

- **Within MIMIC-IV-ECG** (and within UK Biobank), `A` is ≈ constant across `E` (same devices/site). → comparing ancestry groups *within* a cohort gives the effect of E **with acquisition held fixed** → isolates {prevalence, `P(X|Y,E)`, labeling}, no acquisition confound.
- **Across cohorts** (e.g., PTB-XL vs SPH), `A` varies. → the *contrast* between within-site and across-site gaps identifies the acquisition component.

This contrast is precisely what nobody has exploited for ECG, and it is what makes the decomposition identifiable.

### A.5 Estimators — the exact "do this" for each component

Each component is estimated as the change in the metric M when you "switch" that one mechanism from its target value to its source value (a counterfactual intervention), holding others fixed.

**Component 1 — Prevalence (label-shift correction).**
1. Estimate `P(Y|source)` and `P(Y|target)`.
2. Importance-weight the target evaluation set so its label distribution matches source: weight `w(y) = P(y|source) / P(y|target)` (or resample to match).
3. Recompute M. `ΔM_prevalence = M(target reweighted to source prevalence) − M(target)`.
   *(Individual predictions don't change; only the aggregate metric — this isolates how much of the gap is "just" different base rates.)*

**Component 2 — Acquisition.**
1. Use **ECG-Image-Kit** to render source-population ECGs in the target's acquisition style (e.g., digital → paper-photo, or apply target device characteristics), holding Y and E fixed.
2. `ΔM_acquisition = M(source X in target acquisition) − M(source X in source acquisition)`.
3. **Validation cross-check:** within UK Biobank, A is fixed across ancestry, so the acquisition component there must be ≈ 0 — a built-in falsification test of your acquisition estimate.

**Components 3 & 4 — Benign-morphology vs Pathology-manifestation (the novel split).**
Use **matched sub-cohorts** as the anchor, **generative counterfactuals** as corroboration.
1. *Matched real cohorts (anchor):* within a cohort, stratify by gold-standard Y.
   - **Confirmed-benign stratum (Y = no disease):** any model "disease" flag here is *by definition* benign morphology misread. → `Component 3 (benign-morphology) ≈ ancestry gap in FPR on the confirmed-benign stratum (within-site)`.
   - **Confirmed-disease stratum (Y = disease):** ancestry gap in sensitivity/TPR here reflects genuinely different disease manifestation. → `Component 4 (pathology-manifestation) ≈ ancestry gap in TPR on the confirmed-disease stratum (within-site)`.
2. *Generative counterfactuals (corroboration):* with SSSD-ECG / DiffuSETS, synthesize matched pairs holding the disease label fixed while moving an ancestry-correlated style latent; measure the model's prediction change. Confirms (1) and lets you probe specific morphologies (early-repol, high voltage, TWI).
   - *Caveat:* validate synthetic fidelity (a real-vs-synthetic classifier + cardiologist spot-check) and **anchor conclusions on the real matched cohorts**; use generation only to corroborate.

**Component 5 — Labeling.**
1. Re-derive labels with a *single consistent criterion* across cohorts (e.g., apply one LVH voltage threshold everywhere, ignoring native site thresholds).
2. `ΔM_labeling = M(target, harmonized labels) − M(target, native labels)`.
3. Corroborate with **SGShift** concept-shift feature attribution.

### A.6 Fair aggregation — Shapley over mechanisms

The five "switch" operations interact (order matters). Attribute the total gap fairly:
1. Define the switch set `S = {prevalence, acquisition, benign-morph, pathology-manifest, labeling}`.
2. For each subset of switches applied (from target toward source), evaluate M. (2⁵ = 32 combinations; if any switch is expensive, Monte-Carlo sample orderings.)
3. Each mechanism's **Shapley value** = its average marginal contribution to closing the gap over all orderings. These sum to the total gap → a clean, order-independent partition with bootstrap CIs.
   *(This extends the medical-imaging method arXiv 2512.09094 — which did only acquisition vs annotation — to the full five-way clinical taxonomy, the genuinely new part.)*

### A.7 Assumptions, checks, and honesty guards

- **DAG correctness / no unmeasured confounding:** run a sensitivity analysis varying the DAG (e.g., add a socioeconomic confounder path); report how attribution moves.
- **Positivity/overlap:** each ancestry × disease cell needs adequate n. Pre-specify which diseases have enough representation; **exclude** underpowered cells from confirmatory claims (analyze them as exploratory).
- **Gold-standard labels:** prefer independently confirmed Y (UK Biobank CMR-confirmed subsets; adjudicated MIMIC labels) for Components 3/4.
- **UK Biobank selection (healthy-volunteer) bias:** do **not** use UKB for absolute prevalence estimates; use it for the *within-ancestry morphology* comparison. State as a limitation.
- **Negative control:** a mechanism that should have no effect (e.g., a random re-labeling) must get ≈ 0 Shapley value — if it doesn't, your pipeline is leaking; debug before trusting results.

---

## PART B — Stage 2 protocol (mechanistic localization), runnable detail

**Goal restated:** test whether an internal `ancestry-features → prevalence/disease-logit` pathway exists and *causally* drives the benign-case false positives.

**B.1 Sparse Autoencoder (SAE) training.**
- Architecture: **TopK SAE** (as used successfully on EEG foundation models). Train on cached activations from ECG-FM (primary; fully open) and ECGFounder (replication).
- Where: the final-layer embedding **and** 2–3 intermediate layers (shortcuts can live mid-network).
- Hyperparameters: sweep dictionary size (≈ 8×–64× activation dim) and sparsity K. Select via an **intrinsic dictionary-health audit** (fraction of dead features, reconstruction R², and downstream-loss recovery when substituting the reconstruction) — a single procedure that transferred across three EEG architectures, so it should transfer across ECG encoders too.
- **Seed robustness:** SAE features are seed-sensitive; train ≥3 seeds and report **reproducible subspaces** (features that recur across seeds), not individual latents.

**B.2 Probing (linear + non-linear — this determines Stage 3).**
- Train probes on SAE features *and* raw activations to predict: (i) ancestry E; (ii) the model's disease-logit shift; (iii) a **control concept** (heart rate).
- Use both a **linear** probe (logistic regression) and a **non-linear** probe (small MLP).
- **Decision-relevant read-out:** if non-linear ≫ linear accuracy for ancestry, the concept is *non-linearly* encoded → Stage 3 must use RLACE/TaCo or feature ablation, not LEACE alone.

**B.3 Causal mediation via activation patching (the core test).**
- Estimand: the **proportion of the total E→prediction effect mediated by the ancestry-features**.
- Procedure: (1) run a target-ancestry benign ECG, cache its ancestry-feature activations; (2) run a source-ancestry benign ECG but **patch in** the cached target ancestry-features; (3) measure the change in P(disease). If patching shifts the prediction toward the target's false-positive pattern → the pathway is causal.
- Report the **mediated proportion** (how much of the ancestry effect flows through these features).
- Use **Sparse Feature Circuits** (Marks et al. 2025) to isolate the *minimal* feature set + connections forming the pathway (and to set up editing in Stage 3).

**B.4 Physiological decoding.**
- For each implicated feature: collect max-activating ECG exemplars; localize which leads/segments drive it; have a cardiologist name the correlate (e.g., "J-point elevation in V2–V4"). This turns an abstract direction into a clinically legible finding.

**B.5 Negative controls.**
- Patch **random** features → should produce no systematic FPR shift.
- Patch the **heart-rate** control feature → should not reproduce the ancestry effect.
- If controls fire, the effect is an artifact — stop and debug.

**B.6 Decision point D2 (see Playbook §5):** clean causal circuit (2A) / encoded-but-entangled (2B) / no-shortcut-it's-OOD (2C) / different-driver (2D). Each is publishable.

---

## PART C — Stage 3 protocol (surgical fix), runnable detail

**Goal restated:** remove the pathway at inference, weights frozen, using only *unlabeled* target ECGs, and beat training-time debiasing without harming accuracy.

**C.1 Primary method — LEACE (linear, closed-form).**
- Fit the LEACE erasure transform to remove all linearly-accessible information about the ancestry/prevalence direction from the target layer(s); apply at inference ("concept scrubbing" across layers if single-layer is insufficient).
- **Label-free:** estimate the erasure direction from *unlabeled* target-ancestry ECGs only. (Baselines below need target labels; you don't — that's the headline.)

**C.2 Physiological-preservation constraint (operationalized).**
- After erasure, verify: (i) predictions on a held-out set of unambiguous diagnostic ECGs (e.g., clear STEMI, AF) are unchanged → accuracy preserved; (ii) the *diagnostic* SAE features identified in Stage 2 are **not** removed — only the shortcut direction is.
- If erasure damages diagnostic features: constrain the erased subspace to be **orthogonal to the diagnostic subspace**, or switch to **targeted ablation of only the Stage-2 shortcut features** (via the Sparse Feature Circuit) rather than a broad directional erasure.

**C.3 Escalation ladder (if C.1 is insufficient — you'll know from B.2's non-linear probe).**
- → **RLACE** (adversarial, handles some non-linearity) or **TaCo** (defeats non-linear probes) or **direct SAE-feature ablation** (zero / mean-patch the specific shortcut features).
- → If inference-time still insufficient: **minimal targeted fine-tuning** — a small LoRA/adapter on *only* the identified subspace, using a *small* labeled target set (label-efficient). This is Branch 3C.

**C.4 Baselines (exact head-to-head).**
Report the equity gap (ΔEFPR) **and** accuracy for each, on held-out target data:
1. No intervention.
2. Full fine-tuning on target labels *(reference upper bound; needs labels)*.
3. Group-adversarial demographic suppression *(training-time)*.
4. Reweighting *(training-time)*.
5. FiLM demographic conditioning *(training-time)*.
6. **Your method** *(inference-time, label-free)*.

**C.5 Decision point D3 (see Playbook §5):** closes-gap-no-cost (3A) / closes-gap-with-tradeoff (3B) / needs-minimal-fine-tune (3C) / irreducible-without-data (3D). Each is publishable, with the target venues in Playbook §7.

---

## PART D — Statistical analysis plan

- **Primary outcome:** ΔEFPR (excess false-positive-rate gap on benign-variant cases) across ancestry, and its reduction after intervention. **Secondary:** AUROC gap, sensitivity gap, ECE (calibration) per subgroup, Equalized-Odds gap.
- **Power:** pre-compute n per subgroup to detect a clinically meaningful ΔEFPR (target 5 percentage points) at 80% power, α = 0.05. Major MIMIC groups (White, Black, Hispanic, Asian) and UKB groups (European, South Asian, African) should be adequately powered; **pre-specify which groups you are powered for** and treat small groups as exploratory. *(An adequately-powered null is the difference between a publishable and an unpublishable negative result.)*
- **Multiplicity:** many diagnoses × subgroups → **Benjamini-Hochberg FDR** control; report q-values.
- **Uncertainty:** all effect sizes with **bootstrap 95% CIs** (patient-level resampling).
- **Replication:** no headline claim is reported until reproduced on **≥2 encoders** and (for SAE features) across **≥3 seeds**.
- **Confirmatory vs exploratory:** everything in the pre-registration (Part E) is confirmatory; anything else is labeled exploratory in the paper.

---

## PART E — PRE-REGISTRATION (ready to file on OSF / AsPredicted)

> Copy this into OSF Registrations or AsPredicted and timestamp it **before** running confirmatory analyses. Filing this is the single strongest guarantee of "publishable regardless of outcome," because reviewers judge a pre-registered severe test on its design, and (where offered) a **Registered Report** is accepted *before results exist*. Consider submitting the Stage-1 study as a Registered Report to a venue that offers the format.

**Title:** Decomposing and mechanistically localizing the cross-ancestry generalization gap in ECG foundation models, and testing a label-free surgical correction.

**Authors / date / version:** [fill] / [fill] / v1.

**1. Study type.** Secondary analysis of public ECG datasets + controlled mechanistic and representational-editing experiments on frozen foundation models. Confirmatory, pre-registered.

**2. Background & rationale (one paragraph).** ECG foundation models are trained largely on high-income-country data and are known to encode demographic attributes from the signal. Whether their cross-population failure is (a) benign-variant misperception, (b) a learned population→prevalence shortcut, (c) genuine biological difference, or (d) measurement/label artifact is **untested**; a June-2026 fairness paper explicitly notes the required cross-ethnic analysis (on MIMIC-IV-ECG) has not been done. We decompose the gap causally, test for a localizable causal shortcut mechanism, and test a label-free surgical correction.

**3. Hypotheses (with pre-specified alternatives — every outcome is interpretable).**
- **H1 (gap exists):** frozen ECG foundation models show a non-zero cross-ancestry equity gap (ΔEFPR > 0) on benign-variant cases, within a single site (acquisition-controlled). *Alt (H1₀): no meaningful gap → "modern FMs generalize better than feared; here are the exceptions" (Branch 1D).*
- **H2 (composition):** the decomposition attributes a substantial share of the gap to {prevalence + labeling + benign-morphology-as-shortcut}, i.e., largely non-(true-biology). *Alt: true-pathology-manifestation dominates → real-biology atlas (Branch 1C); or artifact dominates (Branch 1A). All publishable.*
- **H3 (mechanism):** there exists a localizable, causally-validated `ancestry-features → disease-logit` pathway (mediated proportion significantly > 0). *Alt: entangled/non-separable (Branch 2B); or no shortcut, genuine OOD (Branch 2C, contradicting the imaging literature); or a different driver (Branch 2D).*
- **H4 (fix):** a label-free, inference-time surgical edit reduces ΔEFPR significantly more than no intervention, and by a margin competitive with training-time baselines, **without** a significant accuracy drop. *Alt: closes-gap-with-tradeoff (Branch 3B, Pareto frontier); needs minimal fine-tune (Branch 3C); irreducible without data (Branch 3D — a field-redirecting negative result).*
- **H5 (generality):** the mechanism and the fix replicate across ≥3 encoders and ≥2 ancestry axes. *Alt: model/population-specific → a taxonomy of when it appears.*

**4. Datasets.** MIMIC-IV-ECG (+ MIMIC-IV clinical for ancestry; credentialed); UK Biobank ECG subset (application; ancestry incl. South Asian); PTB-XL, CODE-15, SPH/Chapman/CPSC, SaMi-Trop, Georgia (open); ECG-Image-Kit + ECG-Image-Database for the acquisition/paper axis. Models: ECG-FM, ECGFounder, CSFM.

**5. Variables.** Outcome: ΔEFPR (primary), AUROC/sensitivity/calibration/Equalized-Odds gaps (secondary). Grouping: ancestry (pre-specified adequately-powered groups). Mechanisms: prevalence, acquisition, benign-morphology, pathology-manifestation, labeling. Mediator: SAE ancestry-features. Controls: heart rate; random directions.

**6. Analysis plan.** Exactly Parts A–D above (decomposition estimators + Shapley; SAE + probing + activation-patching mediation; LEACE→escalation with physiological-preservation; FDR-corrected, bootstrap-CI, ≥2-encoder replication).

**7. Sample size / power.** Per Part D; pre-specify powered groups; small groups exploratory.

**8. Decision rules → conclusions (the outcome-independence map).**
| Result | Pre-registered conclusion | Branch |
|---|---|---|
| No gap (H1₀) | FMs generalize across ancestry better than feared; characterized exceptions | 1D |
| Gap; artifact/prior dominates | The cross-population ECG gap is largely non-biological measurement/prior | 1A |
| Gap; benign-shortcut dominates + causal circuit found | Cross-population failure is a localizable representational shortcut | 1B→2A |
| Gap; true biology dominates | Atlas of population-specific ECG disease manifestation + data-efficiency frontier | 1C |
| Encoded but entangled | Demographic/diagnostic info is fundamentally entangled — why debiasing fails | 2B |
| No shortcut; genuine OOD | Unlike imaging, ECG failure is not a demographic shortcut but OOD | 2C |
| Fix works free | Label-free surgical cross-population correction | 3A |
| Fix works with tradeoff | Fundamental accuracy-equity tradeoff; the Pareto frontier | 3B |
| Needs minimal fine-tune | Label-efficient targeted correction | 3C |
| Irreducible without data | Post-hoc fixes inadequate; redirect field toward efficient data collection | 3D |

**9. Outcome-neutral validity conditions (must hold for the test to count).** SAE reconstruction R² and downstream-loss-recovery above pre-set thresholds; positivity satisfied for analyzed cells; negative controls (random-direction patching; random-relabeling) return ≈ 0; generative-counterfactual fidelity validated. If a condition fails, that analysis is inconclusive (not a result) and is repaired before interpretation.

**10. Exclusions / exploratory split.** Underpowered ancestry × disease cells → exploratory. Anything beyond H1–H5 → labeled exploratory in the manuscript.

**11. Timeline.** Per Playbook §8.

---

### The through-line
With Parts A–D you can *run* every stage; with Part E you *lock in* publishability before seeing a single result. The three documents together are self-sufficient: the *why* (White Sheet), the *what/branches* (Playbook), and the *how/proof-of-rigor* (this Companion). Whatever the data says — shortcut confirmed, refuted, entangled, or irreducible — the pre-registered decision map in §E.8 routes it to a stated, significant, publishable conclusion. That is the property your professor demanded, engineered in by design rather than hoped for.

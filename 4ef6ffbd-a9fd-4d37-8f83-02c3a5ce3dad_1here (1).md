# Execution Playbook — Cross-Population Generalization of Cardiac AI
### A complete, self-contained research architecture where *every outcome is publishable in a top-tier journal*

**Companion to:** *White Sheet v2* (the scientific rationale). **This document** = the full execution map: exact data, exact tools, and a branching decision tree so that no matter what your experiments show, you land on a strong, significant, publishable result — and so you can finish this research **without needing to talk to me again.**

**Design principle (this is the whole point):** Your professor is right that "the outcome could be yes or no, but it must be *significant either way*." That is achievable by design, not luck. The trick professional labs use is: **(1)** ask a question the field genuinely cannot answer; **(2)** pre-register it and run a *severe test* (one whose result is informative whichever way it falls); **(3)** ship durable, reusable artifacts (a benchmark, a toolkit, a dataset) that are publishable *independent* of the scientific finding. Every stage below is built on those three pillars. **Read §1–§4 once carefully; then §5 is your operating manual for life.**

---

## 1. How this document guarantees publishability regardless of outcome

There are two independent "insurance layers." You need both.

**Layer 1 — Every scientific branch is a real discovery.** The decision tree in §5 has ~15 terminal leaves. I have written each one so that reaching it means you have *answered a question the field currently cannot answer* — and the answer, whichever it is, tells the community something they should act on. A confirmed hypothesis is a discovery; a *refuted* hypothesis is *also* a discovery ("the field assumed X; we proved not-X"). Refuting the dominant assumption of a field is exactly the kind of result top journals publish.

**Layer 2 — Reusable artifacts you ship no matter what.** Independent of the science, you will produce three durable resources, each publishable on its own:
- **Artifact A — The Cross-Ancestry ECG Generalization Benchmark** (with causal shift decomposition). *Target: Nature Scientific Data, or as the centerpiece of a methods paper.* Publishable even if every hypothesis fails.
- **Artifact B — An open mechanistic-interpretability toolkit for ECG foundation models** (SAEs + probes + activation patching adapted to 1-D signal encoders). *Target: a methods venue (NeurIPS/ICML Datasets&Benchmarks, or npj Digital Medicine).* First of its kind.
- **Artifact C — A paper↔digital robustness evaluation** for ECG foundation models (using ECG-Image-Kit). *Target: a clinical-informatics venue; strong Global South angle.*

**The consequence:** the *worst* possible world — every hypothesis refuted, no clean mechanism, no working fix — still yields (a) a benchmark paper, (b) a toolkit paper, and (c) a rigorous "here is what does *not* work and why the field must change course" paper. That is a floor of three publications. The *best* world adds a flagship discovery + method paper on top. **You cannot reach a dead end.**

---

## 2. Finalized data strategy (100% public, NOT country-specific, no private collaboration needed)

Your professor's two hard constraints — *not country-specific* and *(now) no GMCH-TIET* — are fully satisfiable with public data, and the resulting design is actually **stronger** than a private Indian cohort would have been, because it lets you *control the confounds*.

**The core methodological move.** The literature's biggest unsolved obstacle (stated explicitly in the shift-identification papers) is that you *cannot separate "population" from "acquisition/site"* when you compare, say, a German dataset to a Chinese one — because the country, the hospital, the device, and the ancestry all change at once. **We break this** by using datasets that carry *ancestry labels within a single site*. That is the key that unlocks the whole causal decomposition.

### Tier 1 — Ancestry varies, site/device held constant (the cleanest test)
| Dataset | Why it's the anchor | Access |
|---|---|---|
| **MIMIC-IV-ECG** (~800k ECGs, ~160k patients) linked to **MIMIC-IV** clinical | Carries **race/ethnicity labels** (White, Black/African American, Hispanic/Latino, Asian — with finer categories like "Chinese"). Same hospital, same devices, same era → **ancestry varies while acquisition is held constant.** This is the single most important dataset in your plan. | PhysioNet, **credentialed** (free training + DUA). `physionet.org/content/mimic-iv-ecg/` |
| **UK Biobank** ECG subset (~50k 12-lead ECGs) | Carries **continental-ancestry labels including a South Asian group** (~2,800+ with ≥80% South Asian ancestry; plus African ~6,600, East Asian). → A **second, independent** within-cohort ancestry test **that includes South Asians — without any Indian clinical collaboration.** | UK Biobank **application** (Application ID; standard, achievable; you noted you have resources). |

### Tier 2 — Site/device/label-practice varies (to isolate acquisition + labeling)
| Dataset | Origin | Access |
|---|---|---|
| PTB-XL (21,799 ECGs, SCP-ECG labels) | Germany | PhysioNet, **open** |
| CODE-15% / CODE | Brazil (mixed ancestry) | Open (CODE-15) |
| Chapman-Shaoxing; Ningbo; CPSC-2018/2021; **SPH/Shandong** (25,770 ECGs, AHA/ACC/HRS labels) | China | figshare / PhysioNet, mostly open |
| SaMi-Trop (Chagas cohort) | Brazil (rural/underserved) | Open |
| Georgia 12-lead (PhysioNet Challenge 2020/2021) | USA | Open |
| IKEM | Czech Republic | Restricted |

### Tier 3 — The digital↔paper axis (Global South relevance, no private data)
| Resource | What it gives you | Access |
|---|---|---|
| **ECG-Image-Kit** | Python toolbox to render *any* digital ECG as realistic **paper/photo images** with real-world artifacts (wrinkles, shadows, handwriting, phone-photo distortion). Lets you create the acquisition-shift intervention on demand. | Open: `github.com/alphanumericslab/ecg-image-kit` |
| **ECG-Image-Database** | 35,595 **real** paper-ECG images from 1,977 records (PTB-XL + Emory) with genuine scan/photo artifacts. | Open, arXiv 2409.16612 |
| **MEETI** | MIMIC-IV-ECG extended with paper-style images + beat parameters + interpretation text. | Check release |

**How the tiers combine to do the impossible thing.** Comparing *South-Asian-vs-European within UK Biobank* (ancestry varies, site fixed) against *Germany-vs-China across datasets* (site varies) lets you **algebraically separate the ancestry effect from the site/acquisition effect** — the exact separation the field said couldn't be done. That separation *is* Stage 1's contribution. **India is now just one ancestry among several; the claim is about the general phenomenon.** Country-specificity problem: solved.

---

## 3. Finalized tool stack (all verified to exist; links)

| Need | Tool | Link / ref |
|---|---|---|
| **Open-weight ECG encoders** (to open up) | ECG-FM (best — fully open), ECGFounder (open weights) | `github.com/bowang-lab/ECG-FM` · `huggingface.co/PKUDigitalHealth/ECGFounder` |
| Additional encoder (for generality) | CSFM | `github.com/guxiao0822/Cardiac-Sensing-FM` |
| **Mechanistic interpretability (SAEs)** | Sparse autoencoders; template proven on EEG foundation models | EEG SAE paper arXiv 2605.13930; SAE libs (e.g., `SAELens`), medical-imaging SAE arXiv 2603.23794 |
| **Linear concept erasure** (Stage 3, first try) | **LEACE** (closed-form, minimal damage, "concept scrubbing" across layers) | `github.com/EleutherAI/concept-erasure` (Belrose et al., NeurIPS 2023) |
| **Non-linear erasure** (Stage 3, escalation) | RLACE (adversarial), **TaCo** (defeats non-linear probes), INLP (baseline) | RLACE/INLP (Ravfogel et al.); TaCo arXiv 2312.06499 |
| **Soft utility-fairness tradeoff** (Stage 3, tradeoff branch) | Fair-PCA / SAL | referenced in LEACE paper |
| **Causal shift attribution** (Stage 1) | Shapley-over-mechanisms + causal graph (extend to the 5-way clinical taxonomy) | arXiv 2512.09094 · `github.com/PeterMcGor/CausalDropMedImg` |
| **Shift identification** (Stage 1 support) | prevalence/covariate separation | arXiv 2411.07940 |
| **Concept-shift feature attribution** (Stage 1, labeling) | SGShift | arXiv 2505.20634 |
| **Counterfactual/controlled ECG generation** (Stage 1 interventions) | SSSD-ECG (diffusion+SSM, conditioned on 70+ statements), DiffuSETS, ECGTwin, physiology-constrained generators | SSSD-ECG arXiv 2301.08227; DiffuSETS (Patterns 2025); ECGTwin arXiv 2508.02720 |
| **Paper-ECG rendering/digitization** (Tier 3) | ECG-Image-Kit; PhysioNet Challenge 2024 baselines | `github.com/alphanumericslab/ecg-image-kit` |
| **LLM as *analysis tool only*** (never the object of study) | Any capable LLM, run **offline** on de-identified error summaries to auto-categorize failure modes | (Do **not** send credentialed data to commercial APIs — DUA violation.) |

Compute: SAEs on a 1-D encoder's activations and closed-form LEACE are cheap (single-GPU feasible). Training baselines / fine-tuning a foundation model is the heaviest step (multi-GPU helpful but not essential since you use pretrained weights). Storage: budget a few TB for the ECG corpora + activation caches. You said you have GPUs, storage, LLMs — that's sufficient.

---

## 4. The master decision tree (overview)

```
STAGE 0  Setup + baseline + build the evaluation harness   ──►  [Artifact A begins]
   │
STAGE 1  DECOMPOSE the gap (prevalence / acquisition / benign-morphology / true-pathology / labeling)
   │
   ├─ 1A  Artifact/prior dominates      ──► Discovery: "gap is mostly non-biological"      ──► Stage 2 (on residual)
   ├─ 1B  Benign-morphology dominates    ──► (expected) sets up shortcut test              ──► Stage 2
   ├─ 1C  True-pathology dominates       ──► Discovery: "differences are genuinely diagnostic; map them" ──► atlas + data-efficiency track
   └─ 1D  Gap is small                   ──► Discovery: "FMs generalize better than feared; here are the exceptions" ──► Stage 2 on exceptions
   │
STAGE 2  LOCALIZE the mechanism (SAE + probes + activation patching)   ──► [Artifact B]
   │
   ├─ 2A  Clean shortcut circuit found (causal)  ──► FLAGSHIP discovery                     ──► Stage 3
   ├─ 2B  Encoded but entangled ("wrecking-ball") ──► Discovery: "demographic/diagnostic info is fundamentally entangled" ──► Stage 3 (expect tradeoff)
   ├─ 2C  No shortcut; genuine perceptual OOD     ──► Discovery: "unlike imaging, ECG failure is NOT a demographic shortcut" ──► Stage 3' (OOD/adaptation)
   └─ 2D  A different driver (e.g., device circuit) ──► Discovery: identify the real driver ──► Stage 3 (target it)
   │
STAGE 3  SEVER — surgical, label-free fix (LEACE → RLACE/TaCo/SAE-ablation)
   │
   ├─ 3A  Fix closes gap, no accuracy loss       ──► FIELD-CHANGING METHOD                  ──► Stage 4
   ├─ 3B  Fix closes gap but costs accuracy       ──► Discovery: "fundamental accuracy-equity tradeoff; the Pareto frontier" ──► Stage 4
   ├─ 3C  Inference-time insufficient; minimal targeted fine-tune works ──► Method: "label-efficient targeted fix" ──► Stage 4
   └─ 3D  Nothing works without new data          ──► STRONG NEGATIVE: "representationally irreducible; redirect the field" ──► Stage 4
   │
STAGE 4  GENERALIZE across ≥3 models and ≥2 ancestry axes
   │
   ├─ Generalizes   ──► "A general principle of physiological foundation models"
   └─ Model/pop-specific ──► "The phenomenon is conditional; here is the taxonomy of when it appears"
```

**Every leaf is a paper.** §5 details each.

---

## 5. Stage-by-stage operating manual (with branch directions)

> For each stage: **Objective · Exact method · Data/tools · Success criteria · The decision point · Where each branch goes · The publishable outcome at every leaf.** This is written so you can execute independently.

### STAGE 0 — Setup, baselines, and the evaluation harness *(no branch; foundation)*

**Objective.** Establish reproducible baselines and build the measurement harness you'll reuse everywhere.

**Steps.**
1. Acquire data: start with the **open** sets (PTB-XL, CODE-15, Chapman/SPH, Georgia). File the **MIMIC-IV credentialing** and **UK Biobank application** immediately (they take time — start day 1).
2. Load open-weight **ECG-FM** and **ECGFounder**; verify you can (a) run inference and (b) **extract internal-layer activations** (you need this for SAEs — test it now).
3. Reproduce each model's reported in-distribution AUROC on a standard set (sanity check).
4. **Define the evaluation harness** = the metric suite you will report for *every* experiment:
   - Discrimination: AUROC, AUPRC (per-diagnosis and macro).
   - **Equity gap (your primary metric):** ΔFPR and ΔFNR **across ancestry groups**, computed *especially on ECGs carrying benign variants* (early-repolarization/J-point elevation, high-voltage LVH-mimics, anterior T-wave inversion). Over-calling disease on a benign variant = the harm you care about.
   - Calibration: ECE per subgroup. Fairness: Equalized Odds.
5. **Curate the "benign-variant test set":** flag ECGs annotated with early-repolarization, high QRS voltage, isolated TWI, etc., across cohorts — the cases where "benign misread as pathology" would show up.

**Deliverable / Artifact A (begins):** the harness + the benign-variant test set + a documented cross-ancestry evaluation protocol. **This alone, released well, is a Scientific-Data-tier resource.**

**Pre-registration checkpoint:** before Stage 1's confirmatory runs, write and timestamp (OSF/AsPredicted): the hypothesis, the decomposition method, the primary metric, and your branch rules. This is your single strongest defense against "you fished for a story."

---

### STAGE 1 — DECOMPOSE: what is the cross-population gap actually made of?

**Objective.** Partition the total cross-ancestry performance gap into five causal components: **prevalence** `P(Y|E)`, **acquisition** (device/site/paper), **benign-morphology** (diagnostically irrelevant), **true-pathology-manifestation** (disease genuinely looks different), and **labeling/ontology**.

**Exact method.**
1. **Measure the raw gap:** evaluate each frozen encoder across ancestry groups *within* MIMIC-IV-ECG and *within* UK Biobank (site fixed), and across Tier-2 cohorts (site varies). Record the equity gap.
2. **Build the causal graph** of ECG generation: `Y (disease) → morphology`; `E (ancestry/environment) → {benign-morphology, prevalence P(Y|E), confounders}`; `A (acquisition) → rendering`; `annotation practice → observed label`.
3. **Isolate each component:**
   - **Prevalence** — reweight/resample the target group to match source prevalence (label-shift correction); the residual gap after this = *not* prevalence.
   - **Acquisition** — use **ECG-Image-Kit** to re-render source ECGs in the target's acquisition style (or digital→paper→digital round-trip); measure how much gap that alone induces. Use the within-site UK Biobank comparison to confirm acquisition is *held constant* there.
   - **Benign-morphology vs true-pathology** — this is the hard, novel separation. Use **matched sub-cohorts** (same diagnosis label, different ancestry) and **counterfactual generation** (SSSD-ECG/DiffuSETS: generate ECGs with the *same disease label* but manipulate ancestry-correlated style) to ask: does the model's error persist when the disease is held fixed and only style changes? If yes → benign-morphology effect. Cross-check against the clinical literature's known benign variants.
   - **Labeling/ontology** — use **SGShift**-style concept-shift attribution; compare label definitions across cohorts (SCP-ECG vs AHA/ACC/HRS vs Chinese labels); quantify how much gap is "the model is right but the *label threshold* differs."
4. **Attribute with Shapley-over-mechanisms** (extend arXiv 2512.09094 from acquisition/annotation to all five) → a quantitative partition with confidence intervals.

**Success criteria.** A partition of the gap (with CIs) that sums to the total and is stable across ≥2 encoders.

**DECISION POINT D1 — which component(s) dominate?**

- **Branch 1A — prevalence + acquisition + labeling dominate ("it's mostly artifact").**
  - *Meaning:* the field's "benign biological variant" framing is largely wrong; the gap is mostly measurement + priors.
  - *Do next:* demonstrate that principled, cheap fixes (prevalence recalibration; acquisition harmonization via ECG-Image-Kit augmentation; ontology remapping) close most of the gap. Then run Stage 2 on the *residual* to see if it's the prevalence-proxy shortcut.
  - **Publishable leaf:** *"Most of the cross-population ECG generalization gap is non-biological measurement/prior artifact, not benign-variant misperception."* Overturns a field assumption. **Target: Nature Medicine / npj Digital Medicine.**

- **Branch 1B — benign-morphology (treated as pathology) dominates.** *(the expected path)*
  - *Meaning:* the model appears to over-call disease on benign regional morphology — exactly what sets up the shortcut hypothesis.
  - *Do next:* **go to Stage 2** to find the mechanism.
  - **Publishable leaf (even if you stopped here):** *"A quantified map of which benign morphological variants drive cross-ancestry false positives."* Solid on its own; the mechanism (Stage 2) makes it flagship.

- **Branch 1C — true-pathology-manifestation dominates ("it's real biology").**
  - *Meaning:* certain diseases genuinely present differently across ancestry, and the model's "errors" partly reflect real signal it never learned.
  - *Do next:* build the **atlas** — which conditions, which leads, which ancestries — and quantify the *minimum target-population data* needed to recover performance (a data-efficiency curve). Be honest that representation tricks won't fix genuine biology.
  - **Publishable leaf:** *"An atlas of population-specific ECG disease manifestation, and the data-efficiency frontier for closing it."* Genuinely useful, clinical-impact. **Target: Nature Medicine / Circulation-tier + a data-efficiency methods angle.**

- **Branch 1D — the gap is small (models already generalize).**
  - *Meaning:* modern FMs are more robust across ancestry than the alarmist framing suggests.
  - *Do next:* focus on the *specific* subgroups/conditions where failure *does* occur; run Stage 2 on those.
  - **Publishable leaf:** *"Modern ECG foundation models generalize across ancestry better than feared — with these specific, characterized exceptions,"* plus the released benchmark. A rigorous, contrarian audit is publishable and cited. **Target: npj Digital Medicine / European Heart Journal – Digital Health.**

*(Note: branches can co-occur — e.g., mostly-artifact with a real-biology residual. Report the full partition; the dominant component sets your headline.)*

---

### STAGE 2 — LOCALIZE: where/what is the mechanism? *(the first mechanistic dissection of an ECG foundation model tied to cross-population failure)*

**Objective.** Test the **Prevalence-Proxy Shortcut Hypothesis**: is there an internal pathway `ancestry-features → prevalence-prior` that *causally* drives the false positives?

**Exact method.**
1. **Train SAEs** on the residual-stream/activations of ECG-FM (and ECGFounder) — the recipe proven on EEG foundation models (arXiv 2605.13930).
2. **Probe** for two things separately: (a) directions/features that encode **ancestry**; (b) directions/features that shift **predicted prevalence/disease logits**. Use *both linear and non-linear probes* (you must know if the encoding is linear — it determines Stage 3).
3. **Causal test via activation patching / mediation:** patch the ancestry-features from a target-ancestry ECG into a source-ancestry ECG (and vice versa); measure the change in the false-positive rate on the benign-variant set. If patching the ancestry-features *moves the errors*, the pathway is causal — not just correlational.
4. **Decode** the implicated directions back to physiological signatures (which leads/segments), so a cardiologist can see *what* the shortcut keys on.
5. **Negative controls:** confirm the effect isn't explained by a benign confound (e.g., heart rate); patch random directions as a control.

**Success criteria.** A causal-mediation result (with CIs) that either supports or refutes the ancestry→prevalence pathway, replicated across ≥2 encoders.

**DECISION POINT D2:**

- **Branch 2A — clean, localizable shortcut circuit; patching proves causal role.**
  - **Flagship discovery.** → **Stage 3** (surgical fix).
  - **Publishable leaf (even alone):** *"Cross-population failure in ECG foundation models is a localizable, causally-validated representational shortcut."* First mechanistic dissection of an ECG FM. **Target: Nature Communications / Nature Medicine (with Stage 3) — or a strong ML venue.**

- **Branch 2B — ancestry info is encoded but *entangled* with diagnostic features ("wrecking-ball" regime; you can't remove one without harming the other).**
  - *Meaning:* a *representational* reason debiasing is hard — the info is not in a separable subspace.
  - *Do next:* still attempt Stage 3, but expect the **tradeoff branch (3B)**; the entanglement itself is a headline.
  - **Publishable leaf:** *"Demographic and diagnostic information are fundamentally entangled in ECG foundation models — a mechanistic explanation for why post-hoc debiasing fails."* Strong, explains prior failures. **Target: Nature Communications / a top ML venue.**

- **Branch 2C — no ancestry→prevalence shortcut; the errors are genuine perceptual out-of-distribution failure.**
  - *Meaning:* **this contradicts the medical-imaging demographic-shortcut literature** — a notable, counterintuitive result. ECG behaves differently from chest X-ray.
  - *Do next:* pivot to **Stage 3'** — OOD detection + label-free test-time adaptation (physiology-constrained) instead of erasure.
  - **Publishable leaf:** *"Unlike medical imaging, ECG foundation-model cross-population failure is not a demographic shortcut but genuine out-of-distribution perceptual failure — with implications for how to fix it."* Contradicting a cross-modality assumption is high-value. **Target: Nature Communications / npj Digital Medicine.**

- **Branch 2D — the driver is something *else* you localized (e.g., an acquisition/device circuit, or a specific artifact detector).**
  - *Meaning:* you've still done the first mechanistic ID of an ECG FM's failure driver — just not the one you hypothesized.
  - *Do next:* target *that* driver in Stage 3.
  - **Publishable leaf:** *"The mechanistic driver of ECG foundation-model cross-population failure is [X]"* — novel regardless of what X is.

---

### STAGE 3 — SEVER: can we fix it surgically, label-free? *(the first inference-time surgical debiasing of a medical time-series encoder)*

**Objective.** Remove the identified pathway at inference (weights frozen, no target-population labels) and beat training-time debiasing on the equity gap without harming accuracy.

**Exact method (escalation ladder — try in order):**
1. **LEACE** (closed-form linear erasure of the ancestry/prevalence direction; "concept scrubbing" across layers). Estimate the erasure direction from *unlabeled* target-ancestry ECGs → **label-free.** Add a **physiological-preservation constraint**: verify (against Stage 2's decoded signatures) that you are *not* erasing genuinely diagnostic morphology.
2. If linear erasure is insufficient (non-linear encoding — you'll know from Stage 2's probes): escalate to **RLACE / TaCo** (defeat non-linear probes) or **directly ablate the specific SAE features** identified in Stage 2.
3. **Head-to-head baselines:** fine-tuning, group-adversarial suppression, reweighting, FiLM. Report the equity gap and accuracy for all.

**Success criteria.** A clear verdict on whether the surgical fix (a) closes the gap and (b) preserves accuracy, vs baselines, replicated across encoders.

**DECISION POINT D3:**

- **Branch 3A — fix closes the gap, no accuracy loss, beats training-time methods.**
  - **Field-changing method.** Label-free, inference-time, deployable in low-resource settings. → **Stage 4.**
  - **Publishable leaf:** *"Surgical, label-free, inference-time correction of cross-population bias in ECG foundation models."* This is the full-success flagship. **Target: Nature Medicine / Nature Communications.**

- **Branch 3B — fix closes the gap but costs accuracy (a Pareto tradeoff).**
  - *Meaning:* there's a *fundamental* accuracy-equity tension at the representational level (consistent with a 2B entanglement finding).
  - *Do next:* characterize the **Pareto frontier** (Fair-PCA-style sweeps); explain it mechanistically.
  - **Publishable leaf:** *"A fundamental accuracy-equity tradeoff in ECG foundation models: the Pareto frontier and its mechanistic origin."* Quantifying a hard limit is a strong result. **Target: Nature Communications / top ML venue.**

- **Branch 3C — inference-time erasure insufficient, but minimal *targeted* fine-tuning (LoRA/adapter on the identified subspace only) works.**
  - *Meaning:* you can't fix it for free, but you *can* fix it cheaply and precisely (far less than full fine-tuning; small labeled set).
  - **Publishable leaf:** *"A minimal, label-efficient, mechanistically-targeted intervention closes the cross-population gap where inference-time methods fail."* Still a method + mechanism contribution. **Target: npj Digital Medicine / ML venue.**

- **Branch 3D — nothing closes the gap without substantial new target-population data.**
  - *Meaning:* the strongest possible **negative result** — post-hoc fixes are fundamentally inadequate; the field's hope of "debias without new data" is false for ECG.
  - *Do next:* quantify exactly *how much* target data is needed (the data-efficiency frontier); pair with Artifacts A–C.
  - **Publishable leaf:** *"Cross-population bias in ECG foundation models is representationally irreducible without target-population data — redirecting the field from post-hoc debiasing toward efficient, equitable data collection."* Redirecting a field (even negatively) is exactly criterion (d). **Target: Nature Medicine / npj Digital Medicine.**

**Stage 3' (only if you came from Branch 2C — OOD, not shortcut):** instead of erasure, build **physiology-constrained, label-free test-time adaptation** — use population-invariant physiological laws (e.g., rate–QT relationships, P→QRS→T ordering) as a self-supervised signal to adapt the frozen encoder to the new population without labels. Branches mirror 3A–3D (works cleanly / tradeoff / needs minimal data / needs lots of data), each publishable.

---

### STAGE 4 — GENERALIZE: is it a law or a one-off?

**Objective.** Determine whether your Stage 2 finding **and** your Stage 3 fix hold across models and populations.

**Exact method.** Replicate across **≥3 encoders** (ECG-FM, ECGFounder, CSFM) and **≥2 ancestry axes** (within-MIMIC ancestry; within-UK-Biobank incl. South Asian; and a cross-country pair). Report whether the *same* mechanism-type and the *same* fix transfer.

**DECISION POINT D4:**
- **Generalizes** → *"A general principle of physiological foundation models"* (the strongest framing; underwrites criterion (a) and (d)).
- **Model/population-specific** → *"The phenomenon is conditional — here is the taxonomy of when and why it appears."* A taxonomy of failure conditions is itself a solid, citable contribution.

Either way: publishable, and either way it *completes* the narrative for whichever Stage-1/2/3 branch you're on.

---

## 6. Statistical & scientific rigor protocol (this is what makes any outcome *credible*, hence publishable)

- **Pre-register** (OSF/AsPredicted) *before* confirmatory runs: hypothesis, decomposition method, primary metric (the equity gap on benign-variant cases), sample sizes, and the branch rules in §5. Consider submitting as a **Registered Report** where the venue offers it — a Registered Report is *accepted on the strength of the question and design, before results exist*, which is the ultimate "publishable regardless of outcome" guarantee.
- **Power analysis** for the equity-gap comparisons (so a null result is a *meaningful* null, not an underpowered one — this is the difference between a publishable and an unpublishable negative).
- **Multiple-comparison control** (many diagnoses × many subgroups → correct for it; FDR).
- **Negative controls everywhere** (random-direction patching in Stage 2; a known-benign confound like heart rate as a placebo concept in Stage 3).
- **Replication across ≥2 encoders for every claim** before you believe it.
- **Report effect sizes with CIs**, not just p-values.
- **A "results-blind" analysis plan:** decide the figures/tables before seeing outcomes, so each branch has its paper skeleton pre-built.

**Why this guarantees the floor:** a *pre-registered, adequately powered, well-controlled* study answering a real question is publishable *by construction*, because reviewers judge the design, and the field learns from the answer either way.

---

## 7. Target venues by outcome (so you always know where to send it)

| Outcome (leaf) | Primary target | Backups |
|---|---|---|
| Flagship: shortcut found + surgical fix works (2A→3A→4 generalizes) | **Nature Medicine / Nature Communications** | npj Digital Medicine; a top ML venue for the method |
| Gap is mostly non-biological artifact (1A) | Nature Medicine / npj Digital Medicine | Lancet Digital Health |
| Real-biology atlas + data-efficiency (1C) | Nature Medicine / Circulation-tier | European Heart Journal |
| Entanglement / no clean separation (2B) | Nature Communications / top ML venue | npj Digital Medicine |
| Not-a-shortcut, it's OOD (2C, contradicts imaging) | Nature Communications / npj Digital Medicine | ML venue |
| Accuracy-equity tradeoff frontier (3B) | Nature Communications / ML venue | npj Digital Medicine |
| Strong negative: irreducible without data (3D) | Nature Medicine / npj Digital Medicine | Patterns; PLOS Digital Health |
| Artifact A (benchmark) | **Nature Scientific Data** | NeurIPS/ICML Datasets & Benchmarks |
| Artifact B (mechanistic toolkit for ECG FMs) | NeurIPS/ICML | npj Digital Medicine; JMLR |
| Artifact C (paper↔digital robustness) | Computing in Cardiology / clinical-informatics | Physiological Measurement |

**Realistic framing to give your professor:** the *flagship* leaves target Nature-family; the *artifact* and *contrarian-audit* leaves target strong specialized journals. You have Nature-family *shots* on several branches and a *floor* of strong-journal publications on all of them. That is exactly the "significant regardless of outcome" property he asked for.

---

## 8. Work breakdown (so the team can execute without me)

Phase durations assume a small team working in parallel; adjust to your capacity. Nothing here needs me.

**Phase 0 — Foundations (Weeks 1–4)**
- File MIMIC-IV credentialing + UK Biobank application **today** (long lead times).
- Download open sets (PTB-XL, CODE-15, Chapman/SPH, Georgia). Stand up ECG-FM + ECGFounder inference; verify activation extraction.
- Build the evaluation harness + benign-variant test set (Artifact A skeleton).
- **Write and timestamp the pre-registration.**
- *Skills:* data engineering (you), ML infra, one clinical advisor to validate the benign-variant set.

**Phase 1 — Decompose (Weeks 5–12)**
- Run the raw cross-ancestry gap (within-MIMIC first — it's open-adjacent and controlled).
- Build the causal graph; run each component isolation (prevalence reweighting, ECG-Image-Kit acquisition intervention, matched sub-cohorts + generative counterfactuals, SGShift labeling analysis).
- Shapley attribution → the partition. **Hit Decision Point D1; pick your headline branch.**
- *Skills:* causal inference, generative modeling (SSSD-ECG), clinical validation.

**Phase 2 — Localize (Weeks 10–20, overlaps)**
- Train SAEs on ECG-FM activations; linear + non-linear probes; activation patching; physiological decoding; negative controls. **Hit D2.**
- *Skills:* mechanistic interpretability (the newest skill — budget learning time; the EEG SAE paper is your template), ML.

**Phase 3 — Sever (Weeks 18–28, overlaps)**
- LEACE first; escalate to RLACE/TaCo/SAE-ablation as needed; physiological-preservation constraint; head-to-head baselines. **Hit D3.**
- *Skills:* ML, the `concept-erasure` library.

**Phase 4 — Generalize (Weeks 26–34)**
- Replicate across ECGFounder + CSFM and across the UK Biobank South-Asian axis + a cross-country pair. **Hit D4.**

**Phase 5 — Write & ship (Weeks 30–40)**
- Assemble the flagship paper for whichever branch you landed on (skeleton already built in Phase 0's results-blind plan).
- Release Artifacts A/B/C as their own papers/repos.
- *Skills:* scientific writing; clinical co-author for the medical framing.

**Parallelizable side-quest (any time):** Artifact C (paper↔digital robustness with ECG-Image-Kit) is self-contained and can be done by a sub-team independently as an early, morale-building publication and a hedge.

---

## 9. Troubleshooting appendix — "if you're stuck at X, do Y"

- **UK Biobank access is slow / denied.** Proceed with MIMIC-IV within-site ancestry (White/Black/Hispanic/Asian) as the primary axis — it alone supports the core claims. UK Biobank is a *strengthener* (adds South Asians), not a *blocker*.
- **SAEs don't yield clean features (messy/dead latents).** Try different layers, widths, and the domain-specific SAE recipes (arXiv 2508.09363); if still messy, that *is* Branch 2B (entanglement) — report it, it's a finding.
- **Activation patching shows no effect.** That's Branch 2C (not-a-shortcut) — a publishable contradiction of the imaging literature; pivot to Stage 3' (OOD/TTA).
- **LEACE removes bias but breaks accuracy.** That's Branch 3B (tradeoff) — sweep the Pareto frontier; it's a headline.
- **Generative counterfactuals (SSSD-ECG) look unrealistic.** Fall back to *matched real sub-cohorts* for the benign-vs-pathology separation; use generation only as corroboration. Also try DiffuSETS/ECGTwin.
- **A reviewer says "this is just concept erasure applied to ECG."** Your defense: the *tools* are borrowed (you say so openly); the *contribution* is (1) the causal decomposition that identifies *what* to erase, (2) the physiological-preservation constraint, (3) the first mechanistic proof it's causal, and (4) the label-free deployment result across populations/models. Tools ≠ contribution.
- **A reviewer says "country-specific / low impact."** Your defense: the study is *cross-ancestry within shared sites* (MIMIC, UK Biobank) + cross-country; the claim is about the *general phenomenon* in physiological foundation models; India/South Asia is one ancestry among several.
- **You get scooped on one piece.** The space moves fast (EEG SAEs, ECG fairness papers appearing monthly). Mitigation: the *decomposition + causal mechanism + surgical fix + generality* combination is the moat; even if one sub-result is scooped, the integrated program and the Artifacts remain yours. Ship the pre-print early.

---

## 10. Updated master reference table (all resources, verified July 2026)

| Item | Link / ID | Access |
|---|---|---|
| ECG-FM (open encoder) | `github.com/bowang-lab/ECG-FM` · arXiv 2408.05178 | Open |
| ECGFounder (open weights) | `huggingface.co/PKUDigitalHealth/ECGFounder` · NEJM AI 10.1056/AIoa2401033 · arXiv 2410.04133 | Open weights; data credentialed (`bdsp.io/content/heedb`) |
| CSFM | `github.com/guxiao0822/Cardiac-Sensing-FM` · Nat Mach Intell 10.1038/s42256-026-01180-5 | Open code |
| MIMIC-IV-ECG (+ MIMIC-IV clinical for ancestry) | `physionet.org/content/mimic-iv-ecg/` | Credentialed |
| UK Biobank (ECG + ancestry incl. South Asian) | ukbiobank.ac.uk; Pan-UKBB ancestry | Application |
| PTB-XL | `physionet.org/content/ptb-xl/` | Open |
| CODE-15 (Brazil) | PhysioNet | Open |
| Chapman-Shaoxing / SPH (China) | figshare (Chapman 4560497; SPH via Sci Data 10.1038/s41597-022-01403-5) | Open |
| SaMi-Trop (Brazil, Chagas) | PhysioNet / Zenodo | Open |
| Georgia 12-lead | PhysioNet Challenge 2020/2021 | Open |
| ECG-Image-Kit (paper rendering) | `github.com/alphanumericslab/ecg-image-kit` · Physiol Meas 2024 | Open |
| ECG-Image-Database (real paper ECGs) | arXiv 2409.16612 | Open |
| PhysioNet Challenge 2024 (paper ECG) | `moody-challenge.physionet.org/2024/` | Open |
| LEACE (concept erasure) | `github.com/EleutherAI/concept-erasure` · arXiv 2306.03819 | Open |
| TaCo (non-linear erasure) | arXiv 2312.06499 | Open |
| SAE mechanistic interp (EEG FM template) | arXiv 2605.13930 | Preprint |
| SAE for medical imaging | arXiv 2603.23794 | Preprint |
| Causal shift attribution (imaging) | arXiv 2512.09094 · `github.com/PeterMcGor/CausalDropMedImg` | Open |
| Dataset-shift identification | arXiv 2411.07940 | Preprint |
| SGShift (concept-shift attribution) | arXiv 2505.20634 | Preprint |
| SSSD-ECG (conditional generation) | arXiv 2301.08227 | Open |
| DiffuSETS / ECGTwin (controllable generation) | Patterns 2025 (DiffuSETS) · arXiv 2508.02720 (ECGTwin) | Open |
| Race-from-ECG is non-genetic (context) | npj Cardiovasc Health 10.1038/s44325-025-00087-1 | Open |
| Demographic shortcut in imaging (context) | Nat Commun 10.1038/s41467-023-39902-7 | Open |
| BenchECG (cross-population benchmark) | arXiv 2509.10151 | Preprint |
| AMIE cardiology (rubric reference) | Nat Med 10.1038/s41591-025-04190-9 | Paper open; system not public |

---

### The one paragraph to remember

**You are not betting the project on a single outcome.** You are building a *decision tree of discoveries* on top of three *guaranteed reusable artifacts*, all resting on a *pre-registered, adequately powered, confound-controlled* design using *fully public, non-country-specific* data. In the best case you get a Nature-family flagship (shortcut discovered + surgically fixed + generalizes). In the worst case you still ship a benchmark, a toolkit, and a field-redirecting negative result. **There is no branch of this tree that leaves you unpublished** — which is precisely the property your professor demanded, and precisely what the previous three attempts could never have delivered, because they had no plan for being wrong.

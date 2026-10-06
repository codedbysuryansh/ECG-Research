# Research progress log (resume point)

**STATUS (6 Oct 2026): audit steps A–H DONE. Read research/FINAL_REPORT.md first. Next step (only on the user's command): Claude Code execution files, starting with E0 model-organism validation on PTB-XL/CODE-15 (does not touch the confirmatory datasets — keeps the Nature Registered Report route open).**

If a session is interrupted, start the next session by saying:
"Read research/00_PROGRESS_LOG.md and continue from the last unfinished step with the same effort."

## CURRENT TASK (user's last prompt, 6 Oct 2026)
Full research + novelty audit, nothing else. Rules from the user:
- Counter the user when they are wrong; do not just agree. Give reasons.
- Evidence = papers in reputed peer-reviewed journals. Preprints/workshops may be reported but must be FLAGGED as such.
- First restate the user's ORIGINAL architecture exactly, then say for each part: novel or not (with papers), valid or not (with reasons), and whether/why we change it.
- Then: the exact gap, how we fill it, what we do — aimed at *Nature* (main journal).
- No to-do lists for the user. No Claude Code files yet.

| Step | What | Status |
|---|---|---|
| A | Restate original architecture exactly (from the 7 repo files) | DONE → research/A_original_architecture.md |
| B | Premise check (reputed journals only) | DONE → research/B_premise_check.md (verdict: NOT established in general; task-dependent) |
| C | Component-by-component novelty + validity verdicts | DONE → research/C_component_verdicts.md |
| D | Verify previous session's ledger | DONE → research/D_previous_ledger_verification.md |
| E | Deep novelty search | DONE → research/E_novelty_search.md |
| F | Flagship design + stress test | DONE → research/F_flagship_design_and_stress_test.md |
| G | Data facts | DONE → research/G_data_facts.md |
| H | Final report | DONE → research/FINAL_REPORT.md |

(Older step numbering below = first pass of this session; kept as raw notes.)

Environment note: in the session of 6 Oct 2026, direct page fetching (arxiv.org, nature.com, huggingface, semantic scholar, openalex) was blocked by the environment network policy; only web *search* worked. Evidence below is therefore from search-engine abstracts/summaries. Items marked [VERIFY-FULLTEXT] should be read in full by a human before being cited.

## Raw findings (appended as found)

### Step 2 — scoop check
- **SCOOP (major): SAEs on ECG foundation models already exist (July 2026).**
  - *ECG-InterpBench* (arXiv 2607.27404): BatchTopK SAEs on **six frozen ECG FMs**, 5 depths × 5 widths × 3 seeds = 450-cell atlas; 49 clinical ECG measurements; cross-seed reproducibility; replication on MIMIC-IV-ECG. Search summaries mention no demographics/race. [VERIFY-FULLTEXT]
  - *CADENCE* (arXiv 2607.25244): BatchTopK SAE on Layer-6 embeddings of an ECG FM, >9M ECG tokens, 8,192 "cardiac atoms"; **targeted ablation of atoms produces selective changes in frozen downstream outputs** (i.e., causal feature editing on ECG FM is done). External datasets. [VERIFY-FULLTEXT]
  - => The draft's claim "SAEs have never been applied to ECG" is now FALSE. Stage 2 can only claim novelty for *what question* the SAE answers (ancestry/shortcut causality), not for the tool.
- *Can SAEs reveal and mitigate racial biases of LLMs in healthcare?* (arXiv 2511.00177) — SAE-for-race-bias exists in clinical LLMs.
- **Post-hoc (inference-time) removal of protected attributes from medical embeddings already exists** for chest X-ray: *Post-hoc Orthogonalization for Mitigation of Protected Feature Bias in CXR Embeddings* (arXiv 2311.01349; Springer chapter 2025). Also adversarial debiasing of 3D CT foundation embeddings (arXiv 2502.04386). => "first inference-time concept erasure in medical AI" is false; "first in ECG" may still hold but is a weak claim.
- **Race/ECG-AI disparity prior work the drafts never cite:**
  - Noseworthy et al., *Circ Arrhythm Electrophysiol* 2020 (Mayo; 97,829 pts): low-EF ECG-AI performed **consistently across race/ethnicity**. (A "no gap" precedent.)
  - Kaur et al., *Circ Heart Fail* 2024 (doi 10.1161/CIRCHEARTFAILURE.123.010879): ECG-DL for HF worse in **Black patients aged 0–40, esp. young Black women**; adding race to the model, per-race models, and race-balanced training **did not fix it**; group-specific thresholds did. (Directly relevant: the "prevalence/threshold" story already has empirical support → threshold fix is known.)
  - Brown et al., *Nature Communications* 2023 (shortcut testing, 10.1038/s41467-023-39902-7) — imaging.
- **SCOOP (conceptual, major): the "Prevalence-Proxy Shortcut" idea is already published as a general principle.**
  - Kina & Petersen, *Prevalence calibration as shortcut mitigation*, arXiv 2609.07922 (Sept 2026; MLMI@MICCAI 2026). Reframes shortcut learning as a **calibration problem**: ERM "implicitly calibrates each shortcut group to its training-set prevalence"; post-hoc recalibration raises misaligned-group AUROC 0.23→0.73, showing "shortcut reliance degrades the **classification head rather than the underlying representation**." Imaging (chest drain–pneumothorax), frozen FM backbones included. [VERIFY-FULLTEXT]
  - Combined with Kaur et al. 2024 (group-specific thresholds fix ECG-HF disparity), the core idea "model perceives correctly but applies the wrong group prior; fix it without retraining" is no longer new. If true in ECG, the obvious fix is per-group recalibration, not LEACE.
- **Demographic encoding ↔ fairness gap ↔ generalization**: Yang et al., *Nature Medicine* 30:2838 (2024) "The limits of fair medical imaging AI in real-world generalization" — already shows demographic encoding correlates with unfairness, that shortcut-removal gives "locally optimal" fairness that does not transfer. Code: github.com/YyzHarry/shortcut-ood-fairness.
- **Performance-gap decomposition is an existing literature** (not new even outside imaging): DISDE (Cai, Namkoong, Yadlowsky, arXiv 2303.02011); HDPD hierarchical decomposition (NeurIPS 2024, arXiv 2402.14254); Roschewitz 2024 (2411.07940); Gordaliza 2025 (2512.09094). A 5-way taxonomy is an extension, not a new principle.
- **Race-stratified evaluation on MIMIC-IV-ECG already appears** in 2026 preprints (arXiv 2603.28532 low-EF; arXiv 2603.02616 structural heart disease) — performance reported as "largely stable across racial and ethnic groups." So "nobody has done cross-ethnic analysis on MIMIC-IV-ECG" (the DA-GAT-v2 quote) is not true in the broad sense. ECG-FM paper itself reports preserved LVSD accuracy across demographic groups.
- Clinical background confirmed: healthy Black adults have higher QRS voltage, more early repolarization / benign ST elevation; most ECG-LVH criteria derived in white populations (Glasgow eprints 189824; CoArtHA medRxiv 2025.07.31.25332550).

### Step 2 verdict (interim)
The current plan (Shapley decomposition → SAE localization → LEACE erasure → generalize) is now, piece by piece, an **ECG port of things already shown elsewhere**:
| Piece | Status Oct 2026 |
|---|---|
| SAE on ECG foundation models | Done (ECG-InterpBench, CADENCE, July 2026) |
| Causal feature ablation in ECG FM | Done (CADENCE) |
| Post-hoc removal of protected attributes from medical embeddings | Done for CXR (2311.01349) |
| "Shortcut = learned group prevalence prior; fix by recalibration" | Done conceptually (Kina & Petersen 2026); ECG group thresholds (Kaur 2024) |
| Demographic encoding predicts unfairness & non-transfer | Done (Yang, Nat Med 2024) |
| Performance-gap decomposition | Existing literature (DISDE, HDPD, Gordaliza) |
| Race-stratified MIMIC-IV-ECG evaluation | Partially done (2026 preprints; gaps reported small) |
=> Still publishable as solid ECG work, but **not** at Nature (main) novelty, and Nature Communications would be a stretch. A new central idea is required.

### Step 3 — What *Nature* (main journal) actually publishes here
Nature's stated criteria: "outstanding scientific importance", "a conclusion of interest to an interdisciplinary readership", "an advance in understanding likely to influence thinking in the field"; ~8% of ~200 weekly submissions accepted; **editors (not referees) decide broad interest**.

ECG-AI papers that made *Nature* main recently (our real comparators):
1. **EchoNext** — Poterucha et al., *Nature* 644:221–230 (2025), doi 10.1038/s41586-025-09227-0. 700k ECG–echo pairs, 230k pts, multi-system validation, reader study (77% vs 64–69% cardiologists), FDA-cleared, "consistent performance across racial/ethnic groups". Dataset released on PhysioNet (Elias & Finer, Sept 2025). Why Nature: massive, objective (echo) ground truth, clinical impact at scale.
2. **ECG biomarker for sudden cardiac death** — Obermeyer, Schubert, Ross, Mullainathan, Lingman, *Nature* 655:210–218 (24 Jun 2026), doi 10.1038/s41586-026-10674-6. Swedish population ECGs linked to death certificates; found a high-risk group (2.2% of population, 7.0%/yr SCD) 86% of whom LVEF misses. Why Nature: a *discovery* (new biomarker) made by training on **hard outcomes instead of human labels**.

AI-bias papers in top general journals (the bar for a "bias" paper):
- **Hofmann et al., *Nature* 633:147 (2024)** "AI generates covertly racist decisions about people based on their dialect": a *hidden* form of bias invisible to standard (overt) tests, across many models, worse after alignment. Why Nature: surprising, general, societally important, simple decisive experiment (matched guise).
- **Obermeyer et al., *Science* 2019**: bias came from the **choice of label** (cost as proxy for need).
- **Pierson et al., *Nature Medicine* 27:136 (2021)**: training on patient-reported pain instead of radiologist KL grade explains 43% vs 9% of racial pain disparity → the *clinical standard* (derived in a white British population) was the source of bias.
- **Yang et al., *Nature Medicine* 2024**: demographic shortcuts & fairness non-transfer (imaging).

**Lesson:** Nature-main bias/ECG papers are *discoveries about the world* (a hidden bias, a new biomarker, the label as the source of bias), shown with a decisive, simple experiment and objective ground truth. A pipeline of borrowed tools (Shapley + SAE + LEACE) is a *methods* contribution and will be desk-rejected at Nature main. Mullainathan/Obermeyer's recurring move — **replace human labels with ground truth from nature (outcomes, imaging) and see what the humans were missing** — is the single most relevant template for us.

---
## ROUND 2 (user request, 6 Oct 2026): "REVERIFY everything without bias, independently; write very detailed Claude Code files"
| Step | What | Status |
|---|---|---|
| R1 | Independent re-verification of every claim; actively try to falsify own recommendation → research/H_reverification.md | in progress |
| R2 | Verify technical specs: dataset formats, FM checkpoints/inputs, library APIs, ECG criteria formulas, echo truth definitions → research/I_technical_specs.md | pending |
| R3 | Tested reference implementation of the core method (synthetic data) → claude_code/reference/ | pending |
| R4 | Claude Code instruction files: CLAUDE.md, claude_code/*.md, .claude/commands, .claude/agents, configs | pending |
| R5 | Consistency review, commit, push | pending |
Environment note (round 2): raw.githubusercontent.com and pypi.org ARE reachable (official model/library code can be read); journal sites, PhysioNet, Zenodo, HF, arXiv still blocked → papers verified by multiple independent search queries.

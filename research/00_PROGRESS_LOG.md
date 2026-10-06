# Research progress log (resume point)

If a session is interrupted, start the next session by saying:
"Read research/00_PROGRESS_LOG.md and continue from the last unfinished step with the same effort."

| Step | What | Status |
|---|---|---|
| 1 | Audit the 7 existing files (what's strong, what's overclaimed/wrong) | in progress |
| 2 | Scoop check of the current plan (SAE/LEACE/Shapley/MIMIC race) as of Oct 2026 | in progress |
| 3 | What does *Nature* (main journal) actually publish in medical AI / fairness? The bar. | pending |
| 4 | Generate candidate "big ideas" and novelty-check each | pending |
| 5 | Dataset feasibility (PTB-XL, MIMIC-IV-ECG, UK Biobank, CODE, Chapman, SPH, etc.) | pending |
| 6 | Final report + recommendation | pending |
| 7 (later) | Claude Code execution files | not started (by user's instruction) |

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

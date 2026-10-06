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

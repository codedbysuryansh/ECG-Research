# ECG → Nature: Research Ledger (resumable)

**Owner:** Suryansh Singh (TIET) · **Started:** 6 Oct 2026 · **Goal:** a result-independent, extreme-novelty study aimed at *Nature* (main journal).

## HOW TO RESUME (read first)
- Status line below says which steps are done. A resumed session must read this whole file, then continue from the first unfinished step with the same rigor.
- Each step records: what was searched, what was found (with links), and the verdict.

**STATUS: Steps 1–6 DONE. Next: Step 7 (Claude Code blueprint) — only on the user's command.**

## Step plan
1. Scoop check of the current plan (Oct 2026)
2. Calibrate the Nature bar (what Nature main journal published in medical AI / ECG AI / AI bias 2023–26 and why)
3. Data reality check (MIMIC-IV-ECG, PTB-XL, CODE-15, EchoNext, HEEDB, UK Biobank, South Asian data)
4. Deep literature sweep around the big questions
5. Candidate directions + targeted multi-phrasing novelty checks
6. Select flagship + full study design (outcome map)
7. Claude Code execution blueprint (build only on user's command)

---

## STEP 1 — Scoop check of the current plan (done 6 Oct 2026)

| Pillar of current plan | Status now | Evidence |
|---|---|---|
| "First SAEs on an ECG foundation model" | **TAKEN** | ECG-InterpBench (Duan & Qiu, Rice Univ.), arXiv 2607.27404, 29 Jul 2026: matched-scale SAEs on 6 ECG FMs (CSFM, CARDIAC-FM, ECG-FM, ECG-JEPA, HuBERT-ECG, ST-MEM), PTB-XL + 100k MIMIC-IV-ECG; 49 clinical measurements; cross-seed reproducibility; intervention spillover test. **No demographics/race/fairness, no erasure.** https://arxiv.org/abs/2607.27404 |
| "First cross-ethnic ECG-AI fairness analysis" | **NOT NEW** | Noseworthy et al. 2020, Circ Arrhythm Electrophysiol (race/ethnicity effects on AI-ECG low-EF model) https://www.ahajournals.org/doi/full/10.1161/CIRCEP.119.007988 ; Kaur et al. (Stanford) Circ Heart Fail 2024: 954,817 ECGs, HF-prediction AUC 0.69 in Black patients aged 0–40 vs 0.80–0.82 others; balanced training and adding race did NOT fix it; underdiagnosis in Black patients; "benign variants in Black women" suspected but untested https://www.ahajournals.org/doi/10.1161/CIRCHEARTFAILURE.123.010879 ; review: IJERPH 2025 https://www.mdpi.com/1660-4601/22/3/337 |
| "Race is encoded in the ECG" (motivation) | Known | Bollepalli et al. 2025 npj Cardiovasc Health — **correct title: "Non-genetic factors determine deep learning identified ECG differences between black and white healthy subjects."** MGB ~10M ECGs; healthy cohort 379,909 ECGs / 198,222 subjects; **AUC 0.86 (NOT "86% correct")**; AUC 0.59 at birth → 0.84 by age 18; AUC 0.888 top income decile vs 0.805 lowest; QRS key. **Does not test whether diagnostic models USE race.** https://www.nature.com/articles/s44325-025-00087-1 |
| "Shortcut → erase it → gap closes" (LEACE premise) | **Undercut by precedent (CXR)** | Kim et al., Diagnostics 2026 (29 Jun): MIMIC-CXR, 6 FMs, 24 attributes. Race encoded AUROC 0.83 but linear *use* contributes 0.0015; race subgroup gap 30–75× larger than race's linear dependence; residualization (linear erasure) did NOT narrow gaps. https://www.mdpi.com/2075-4418/16/13/2030 |
| "Five-way Shapley decomposition never done beyond 2 causes" | **Wrong** | General Shapley-over-causal-mechanisms method exists: Zhang, Singh, Ghassemi, Joshi, ICML 2023 "Why did the Model Fail?" (code: github.com/MLforHealth/expl_perf_drop) https://arxiv.org/abs/2210.10769 ; ShapShift (arXiv 2604.11200, 2026). ECG 5-way split = application of an existing framework. |
| LEACE / concept erasure on ECG | Unclaimed (no hits) | Low novelty value alone. |
| Primary source for "fixes don't transfer across hospitals" | Found | Yang, Zhang, Gichoya, Katabi, Ghassemi, *Nature Medicine* 2024, "The limits of fair medical imaging AI in real-world generalization" https://www.nature.com/articles/s41591-024-03113-4 (replace the MIT News cite) |
| Diversity in pretraining | Related | CAPE FM, arXiv 2509.10369: diverse multi-centre pretraining improves in-distribution accuracy but reduces OOD generalisation by encoding cohort-specific artifacts. |

**Step 1 verdict:** The current plan's novelty pillars are largely gone (SAE-on-ECG taken; cross-race ECG fairness old; decomposition is an existing general method; CXR evidence predicts the erase-the-shortcut idea will not close gaps). Still open: a *causal* test of whether ECG diagnostic models *use* population information, and *why* the documented disparities (e.g., Kaur 2024) exist. A Nature-level direction needs a bigger question than the current plan.

---

## STEP 2 — The Nature bar (done 6 Oct 2026)

**Nature (main journal) papers that set the bar for this area:**

| Paper | What it did | Why Nature |
|---|---|---|
| **EchoNext** — Poterucha, Elias et al., *Nature* 2025 https://www.nature.com/articles/s41586-025-09227-0 | ECG→structural heart disease; 1.2M+ ECG–echo pairs, 230k patients, 4 hospital systems; AI 77% vs 13 cardiologists 64% (3,200 ECGs); ~85k-patient real-world silent deployment; FDA-cleared; released 100k-ECG public dataset | Scale + beats clinicians + real-world deployment + clinical impact. Race subgroups not reported in coverage. |
| **SCD ECG biomarker** — Obermeyer, Schubert, Ross (DeepMind), Mullainathan, Lingman, *Nature* 2026 (24 Jun) https://www.nature.com/articles/s41586-026-10674-6 | ResNet + VAE latent gradient "morphing" discovered a new ECG marker (QRS slurring in aVL + left axis deviation) for sudden cardiac death; zero-shot validation US (AUC 0.822) & Taiwan (0.767); ICD recipients 54% lower mortality | A *new discovery* (biomarker) + mechanism (MRI fibrosis link) + 3 continents + actionability. Generative "morphing" made the AI's signal human-visible. No race-stratified results. |
| **Disparate privacy risks from medical AI** — Knolle … Kaissis, *Nature* 2026 (24 Jun) https://www.nature.com/articles/s41586-026-10688-0 | Patient-level privacy audit: aggregate metrics hide near-perfect individual risk, concentrated in underrepresented groups; 7 datasets / 6 modalities incl. **ECG (PTB-XL)**; 200 models per dataset; small ResNets/ViTs | A *principle* revealed by a *new measurement lens* that overturns a standard metric; generality across modalities; only public datasets. |
| **Covert racism in LMs** — Hofmann, Kalluri, Jurafsky, King, *Nature* 2024 https://www.nature.com/articles/s41586-024-07856-5 | "Matched-guise probing" (same content, different dialect) revealed covert prejudice that overt-bias tests miss; human-feedback training hid overt but not covert bias | Hidden phenomenon + new audit lens + shows the standard fix fails. |
| **Model collapse** — Shumailov et al., *Nature* 2024 | Theory + experiments across model families | General principle. |
| (Science 2019, Obermeyer) "Dissecting racial bias in an algorithm…" | Bias came from the *label choice* (cost as proxy for need), not the model | Label-choice principle; top-tier precedent for a bias mechanism finding. |

**Pattern that made these Nature-level:** (i) a hidden phenomenon revealed by a *new measurement lens* that overturns a standard metric or practice; or (ii) a genuine biological/clinical *discovery* with multi-site validation and actionability; plus (iii) generality across many models/datasets (often modalities), (iv) clear societal/clinical stakes, (v) a simple, rigorous core design. Data scale is not the bar when the question is principle-level (the privacy paper used public data incl. PTB-XL).

**Gap flagged in print (important):** JAMIA Perspective, Feb 2026 (Editor's Choice) — Abdalla, James, **David S. Jones** (co-author of NEJM 2020 "Hidden in Plain Sight"), Abdalla — "The subtleties of abolishing 'race correction' in clinical artificial intelligence" https://academic.oup.com/jamia/article/33/4/922/8466364 . Argues that removing race from inputs does not remove race correction, because models can learn race through proxies; **no experiments**; calls for counterfactual modelling and audits. (PubMed page 41649326 was rate-limited; read via OUP.)

**Step 2 verdict:** The current plan (decompose → SAE → LEACE) is a pipeline, not a principle. A Nature-shaped paper from public data needs: a hidden phenomenon + a new audit lens + ground-truth anchoring + generality (many models, ideally several clinical domains) + policy stakes. Honest calibration: Nature publishes very few such papers per year and a student-led one is a long shot whatever the design; the design should still be Nature-shaped so it also lands strongly elsewhere if Nature declines.

---

## STEP 3 — Data reality check (done 6 Oct 2026)

| Dataset | Access | Size | Race / SES fields | Labels | Notes |
|---|---|---|---|---|---|
| **MIMIC-IV-ECG v1.0** https://physionet.org/content/mimic-iv-ecg/1.0/ | **OPEN** (ODbL) — downloadable today, no credentialing | ~800k ECGs / ~160k patients; 12-lead, 10 s, 500 Hz; 33.8 GB zip (90.4 GB unzipped); wget/AWS/BigQuery | **None in the ECG module itself.** Race comes only by linking to the MIMIC-IV clinical DB (credentialed). ~55% of ECGs overlap a hospital admission, ~25% an ED visit | Machine measurements, machine report lines, link file to cardiologist notes | As far as I know ECG-FM was pretrained on MIMIC-IV-ECG; White Sheet v1 records CSFM was too → in-distribution leakage risk |
| **MIMIC-IV v3.1 clinical** https://physionet.org/content/mimiciv/3.1/ | Credentialed: CITI "Data or Specimens Only Research" + PhysioNet credentialing + DUA (typically days–2 weeks) | — | race (admissions; ED edstays), insurance (Medicare/Medicaid/Private/Self-pay…), language, marital status | ICD diagnoses, labs, outcomes | SES proxies available |
| **EchoNext v1.1.0** https://physionet.org/content/echonext/1.1.0/ | Restricted (sign PhysioNet Restricted Health Data DUA) | 100k ECGs, Columbia (NYC), 12-lead, 10 s, 250 Hz | **race_ethnicity**, sex, age_at_ecg, location_setting, acquisition_year, ventricular/atrial rate, PR, QRS, QTc | **Echo-confirmed** (moderate+): LVEF ≤45%, **LVH (wall ≥13 mm)**, valve disease, RV dysfunction, PASP ≥45, pericardial effusion, composite SHD | Ground truth that is independent of ECG reading criteria — key for testing label bias / race correction |
| **PTB-XL 1.0.3** https://physionet.org/content/ptb-xl/1.0.3/ | Open (CC-BY 4.0) | 21,799 ECGs / 18,869 patients; 500 & 100 Hz; 1.7 GB | **No race**; age, sex, height, weight, site, device, nurse | Cardiologist SCP statements, human-validation flags, noise flags | Useful for training, device/site shift, and planted-shortcut "model organism" experiments; not for race |
| **CODE-15%** https://zenodo.org/records/4916206 | Open (CC-BY 4.0) | 345,779 exams / 233,770 patients (Brazil); 46.3 GB | **No race** | 6 abnormalities + **mortality** (death, timey) | Outcome labels; mixed-ancestry population but unlabeled |
| **HEEDB v5.0** (BDSP) https://www.nature.com/articles/s41597-026-06861-9 | Credentialed on BDSP (CITI + DUA), no fees | **11.6M ECGs / 2.17M patients** (MGH 1980–2022 + Emory 2010–2019) | **race at both sites**; MGH also ethnicity, education, language, marital, religion | 12SL v24 reprocessed labels, original reads + physician overreads, ICD-9/10 with dates | Largest race-labelled ECG resource; ECGFounder trained on MGH part → Emory part is the cleaner external test |
| **UK Biobank** field 20205 https://biobank.ndph.ox.ac.uk/ukb/field.cgi?id=20205 | Paid application; analysis on UKB Research Analysis Platform | 95,069 participants with 12-lead ECG (imaging visit 91,955; repeat 17,518) | Self-reported ethnicity + genetic ancestry; deprivation | CMR, outcomes | South Asians *with ECG* likely only ~1–2k (to verify); slow and costly — optional strengthener, not critical path |
| **South Asian public 12-lead ECG** | **None found** (incl. github.com/aaekay/ecg-datasets) | — | — | — | Genuine resource gap |

**Answers to the user's questions:**
- **MIMIC-IV-ECG is downloadable now** (open). Race and SES need the credentialed MIMIC-IV clinical DB → start the CITI course + credentialing request **today** (supervisor as reference).
- **PTB-XL** is useful but not for race: training, device/site shift, and controlled planted-shortcut experiments.
- **Biggest discoveries of this step:** (1) **EchoNext** gives race + echo-confirmed ground truth (LVH by wall thickness) — lets us judge whether race-dependence in ECG-AI is *right or wrong*, not just *present*. (2) **HEEDB** gives race on 11.6M ECGs across two health systems (credentialed, free).


---

## STEP 4 — Literature sweep around the big questions (done 6 Oct 2026)

**A. Classic ECG criteria already have race-dependent accuracy (clinical literature, pre-AI)**
- LVH voltage criteria vs echo differ in validity across ethnic groups: Chapman et al. 1999 (PubMed 10342780); systematic review with echo as standard (PubMed 18452942); LIFE study ethnic differences; coArtHA trial in hypertensive Black Africans (Global Heart 2025/26) https://globalheartjournal.com/articles/10.5334/gh.1562
- Guidelines already contain race-specific "normal": International Recommendations for ECG interpretation in athletes (JACC 2017) treat J-point elevation + anterior TWI V1–V4 as normal in Black athletes https://www.jacc.org/doi/10.1016/j.jacc.2017.01.015
- Machine interpretation is race-blind: GE Marquette 12SL Physician's Guide (Rev B) uses only age and sex, not race, for criteria thresholds https://www.numed.co.uk/files/uploads/Product/2_12SL%20Physicians%20Guide%20Rev%20B.pdf → labels in MIMIC/HEEDB (12SL machine reads) encode race-blind thresholds.

**B. AI-ECG and race (what exists)**
- Race readable from ECG (Bollepalli 2025, see Step 1); MGB press: signal stronger in higher-SES patients.
- Disparities are task-dependent: Kaur 2024 (HF prediction) large gap in young Black patients; **Hsieh et al. (MGH, EHJ-Digital Health 2026)**: AI-ECG for LVEF<40% kept AUROC 0.90–0.92 in Black/Asian patients even when trained White-only; external test on EchoNext; **did not test whether the model uses race** https://academic.oup.com/ehjdh/article/7/5/ztag080/8699000 . Same MGH group (Armoundas, Singh) = likely competitor.
- Korean AI-ECG validated cross-ethnically in UK Biobank (Sci Rep 2026, 38,804 participants): stable across subgroups https://www.nature.com/articles/s41598-026-41824-5
- CAPE FM (arXiv 2509.10369): diverse pretraining helps in-distribution, hurts OOD.

**C. Imaging analogues (what the ECG field has not done)**
- Lotter, Nat Commun 2024: acquisition parameters drive AI race recognition in CXR; view-specific thresholds cut underdiagnosis bias 46–67% https://www.nature.com/articles/s41467-024-52003-3
- Counterfactual race editing with latent diffusion for CXR fairness tests: Quinzan et al. arXiv 2504.19621 (2025; statistical flaws noted) https://pith.science/paper/2504.19621
- Race-aware vs race-blind debate in imaging ML: "Are demographically invariant models and representations in medical imaging fair?" arXiv 2305.01397; Positive-Sum Fairness arXiv 2409.19940; FairREAD arXiv 2412.16373; Science Advances 2025 VLM demographic bias https://www.science.org/doi/10.1126/sciadv.adq0305
- Encoding ≠ use in CXR FMs (Kim 2026, Step 1).

**D. Race correction policy debate (the stakes)**
- NEJM 2020 "Hidden in Plain Sight" (Vyas, Eisenstein, Jones) https://www.nejm.org/doi/10.1056/NEJMms2004740 ; eGFR race coefficient removed 2021; JAMIA 2026 perspective (Step 2): removing race from inputs does not remove race correction in AI; no experiments yet. Nature-family roadmap on race in clinical algorithms: https://www.nature.com/articles/s44360-026-00086-1

**E. Methods that exist and can be reused**
- Obermeyer et al. Nature 2026 used predictive model + VAE latent gradient "morphing" to make an AI-detected ECG signal visible → same machinery can generate *matched-guise ECG counterfactuals* (morph only the race-predictive appearance, keep the echo-confirmed substrate).
- Matched-guise probing (Hofmann, Nature 2024) = the audit template.
- Shapley attribution of performance changes (Zhang et al. ICML 2023) = existing decomposition tool.
- ECG-InterpBench SAEs on 6 FMs (2026) = reusable interpretability baseline.

**Step 4 verdict — the unanswered core:** The field knows (i) the ECG carries race, (ii) some ECG-AI tasks show race gaps and others don't, (iii) classic criteria and machine labels are race-blind while their accuracy is race-dependent, and (iv) the policy world assumes "remove race from inputs" is enough. **Nobody has shown, with truth-anchored outcomes, whether ECG-AI covertly conditions on race, through which pathway, whether the label source decides it, and whether it helps or harms.**


---

## STEP 5 — Candidate directions + targeted novelty checks (done 6 Oct 2026)

**Searches run (multi-phrasing):** counterfactual/matched-guise ECG by race; ECG morphing race; generative counterfactual ECG; ECG race causal test/erasure/steering; EchoNext race fairness; label bias criteria vs echo; race-aware vs race-blind ECG-AI; race-neutral ECG LVH criteria; AI re-learning race correction via proxies; acquisition/SES confounding of ECG race signal; overlap of demographic and diagnostic features predicting gaps.

**What already exists nearby (must cite and differentiate):**
- Generative counterfactual ECGs for *explainability* (not demographics/fairness): GCX (Jang…Kwon, Sci Rep 2025) https://www.nature.com/articles/s41598-025-08080-5 ; CoFE (arXiv 2508.16033); MI counterfactuals (arXiv 2312.08304).
- Counterfactual race editing for CXR fairness: Quinzan et al. arXiv 2504.19621 (statistical flaws noted).
- Sex-blinding in ECG-AI: "Why blinding sex is not enough: equitable discrimination, unequal operation in ML detection of MI from the ECG" (J Electrocardiol 2026) https://www.sciencedirect.com/science/article/pii/S002207362600275X (could not open full text — robots blocked; sex, not race).
- Race adjustment can help when data quality differs: PNAS 2024 https://www.pnas.org/doi/10.1073/pnas.2402267121 (+ commentary "Clinical decisions, patient race, and flawed data").
- Causes of race bias in AI CMR segmentation (King's, EHJ-DH 2025; arXiv 2408.02462): bias driven by non-cardiac image regions.
- AI-HCM detection shows racial variation (JACC 2024 abstract); PREVUE-VALVE (JACC 2026) transportability of AI-ECG SHD detection.
- ML-derived LVH criteria exist (EHJ-DH 2025 ztaf003; J Electrocardiol 2025 on age/sex/hypertension; PLOS One) — **none designed for race-equal truth-referenced accuracy.**
- EchoNext Nature paper reports subgroup performance (behavioural only).

**Candidates scored (1–5):**

| # | Candidate | Novelty | Nature fit | Feasibility | Result-independence | Verdict |
|---|---|---|---|---|---|---|
| C1 | **Covert race-conditioning in ECG-AI** — "matched-guise" tests: same echo-confirmed heart, different race-associated appearance → does the diagnosis move? (generative morphing à la Obermeyer 2026 + echo-matched real pairs + representation steering) | 5 (no ECG work found) | 5 (hidden phenomenon + new audit lens, like Hofmann 2024) | 3–4 | 5 | **CORE** |
| C2 | **"The label decides"** — same models trained on race-blind *criteria* labels vs *echo-truth* labels: does the label source decide whether AI covertly race-corrects, and whether that helps or harms? (Obermeyer-2019-style label-choice principle, for physiological AI) | 5 (not found) | 5 (principle + policy) | 4–5 (EchoNext alone suffices) | 5 | **CORE** |
| C3 | **Collision law** — across ~20 diagnoses, overlap between race-predictive and diagnosis-predictive waveform features predicts covert conditioning and truth-referenced gaps (reconciles Hsieh 2026 LVSD-robust vs Kaur 2024 HF gaps vs LVH criteria bias) | 3–4 (Yang 2024 showed model-level encoding↔gap correlation; task-level physiological overlap is new) | 4 | 5 | 4 | **GENERALIZING ARM** |
| C4 | Source of the ECG race signal: acquisition (location setting/device) vs SES (insurance/language) vs physiology (Lotter 2024 analogue for ECG) | 3 | 3 | 5 | 4 | **CONTROL ANALYSIS** |
| C5 | Truth-anchored **race-neutral, race-equal ECG LVH criterion/AI** ("CKD-EPI 2021 moment" for ECG) | 4 | 4 (changes practice) | 4 | 3 | **REMEDY ARM** |
| C6 | Old plan: SAE + LEACE prevalence-proxy | 2 | 2 | 4 | 3 | Fold in as one pathway test inside C1 |
| C7 | South Asian cohort / Global South benchmark | 4 | 2 | 1 (no public data) | 3 | Drop from critical path; note as limitation/future |

**Step 5 verdict — recommended flagship (one integrated paper):**
**"Hidden race correction in cardiac AI: the label decides whether it heals or harms."**
- Part 1 (Discovery, C1): ECG-AI covertly conditions on race even with race-free inputs — measured by three independent matched-guise designs.
- Part 2 (Principle, C2 + C3): the training label decides the direction (race-blind criteria labels → AI reproduces criteria bias; truth labels → AI learns race-calibrated thresholds), and physiological overlap predicts *where* it happens.
- Part 3 (Remedy, C5): a truth-anchored, race-neutral ECG criterion/AI with equal accuracy across groups — no race input, no race-blind label.
- Controls (C4) rule out acquisition/SES artefacts. Every outcome is informative for the live race-correction policy debate (NEJM 2020; eGFR 2021; JAMIA 2026 flags this exact empirical gap).
- **Main competitor risk:** MGH group (Armoundas/Singh: Bollepalli 2025, Hsieh 2026) — move fast, preprint early.


---

## STEP 6 — Flagship study design (done 6 Oct 2026)

### Title (working)
**Hidden race correction in cardiac AI: the label decides whether it heals or harms**

### One-paragraph pitch
Clinical medicine is removing race from its formulas (eGFR 2021, spirometry 2023), and AI models are built "race-free" by leaving race out of their inputs. But the ECG itself carries a strong, non-genetic race signal (AUC 0.86; Bollepalli 2025), and nobody has tested whether ECG-AI quietly *uses* it — i.e., re-learns race correction — or whether that helps or harms patients. JAMIA (Feb 2026, with D.S. Jones of NEJM's "Hidden in Plain Sight") flagged exactly this as untested. We test it with ground truth that does not depend on how humans read ECGs (echocardiography), across ~8 open foundation models and 3 health systems, and we show which training labels make AI fairer or less fair.

### Extra data facts found in Step 6 (feasibility)
- **EchoNext waveforms are preprocessed:** median-filtered per lead, clipped at 0.1/99.9th percentiles, normalised with dataset-wide mean/SD (not raw mV); 250 Hz; categories Hispanic/White/Black/Asian(3.4%)/Other/Unknown; continuous echo truth: **ivs_measurement, lvpw_measurement (cm), lvef_value (%), pasp_value, tr_max_velocity**, graded valve/RV/effusion; location_setting = inpatient/emergency/outpatient/procedural. → great for AI audits; **not** suitable for computing absolute-mV voltage criteria.
- **MIMIC-IV-ECHO** (credentialed): 206,488 echo studies / 91,372 patients with 180–230 *structured, clinician-verified measurements* (incl. LVEF, wall thickness) linkable by subject_id (+time) to MIMIC-IV-ECG raw µV waveforms, race and insurance. https://physionet.org/content/mimic-iv-echo/1.0/ → **MIMIC is the site for the label-source experiment** (raw voltages → classic criteria; machine statements; echo truth).
- 8 public ECG FMs with weights + loaders: ECGFounder (HEEDB), ECG-FM, HuBERT-ECG, ECG-JEPA, ST-MEM, MERL, ECGFM-KED, ECG-CPC — benchmark code: https://github.com/AI4HealthUOL/ecg-fm-benchmarking (Al-Masud, Lopez Alcaraz, Strodthoff, arXiv 2509.25095). EchoNext (Columbia) is in none of their pretraining sets → clean test bed.
- Live adjacent work: GitHub **sukikrishna/ecg_mechinterp** (unpublished) tests *age/sex* demographic pathways causally in PTB-XL MI detection (sex-feature ablation drops MI-probe AUROC 0.27); no race, no echo, no label source. J Electrocardiol 2026 "Why blinding sex is not enough" (sex, MI). arXiv 2603.28532 (ECGPD-LEF) reports race subgroup AUROCs on EchoNext (behavioural only).

### Definitions
- **Race**: self-reported/administrative category, a *social* variable (consistent with the non-genetic ECG signal). Used only for auditing and fairness constraints — **never a model input**.
- **Substrate (truth)**: echo-confirmed cardiac state (wall thickness, LVEF, PASP, valve grades).
- **Covert race-conditioning (CRC)**: change in a model's output when only race-associated ECG appearance changes while echo-confirmed substrate is held fixed. ("Same heart, different guise.")
- **Truth-referenced disparity (TRD)**: race differences in error vs echo truth (FPR/FNR at fixed operating points; calibration; residual score at fixed substrate).
- **Label sources**: L-crit (race-blind classic voltage criteria computed from raw waveforms: Sokolow-Lyon, Cornell voltage, Cornell product, Peguero-Lo Presti), L-machine (device machine statements, race-blind), L-echo (echo truth), L-icd (clinician codes).

### Hypotheses (to pre-register on OSF before confirmatory runs)
- **H1 (CRC exists):** CRC ≠ 0 for tasks whose waveform signal overlaps the race signal (e.g., LVH, repolarization), measured by 3 independent designs.
- **H2 (the label decides):** models trained on race-blind L-crit/L-machine labels reproduce the criteria's race bias vs echo truth, whereas models trained on L-echo show CRC in the direction that *reduces* TRD (learned race-calibrated thresholds). Either direction is reported.
- **H3 (collision law):** across ~20 tasks × models, |CRC| and TRD scale with the overlap between race-predictive and task-predictive representations (predicts Hsieh 2026's LVSD robustness vs LVH/HF gaps).
- **H4 (generality):** H1–H3 replicate across ≥6 FMs + 2 from-scratch nets and ≥2 health systems (Columbia, BIDMC; optional MGH/Emory).
- **H5 (race-free ≠ race-blind):** removing race from inputs leaves CRC; adding race as an explicit input changes little (consistent with Kaur 2024).
- **H6 (remedy):** a truth-anchored, race-neutral model/criterion reduces TRD vs classic criteria and L-crit-trained AI at matched accuracy, without race at inference.

### Experiments
- **E0 — Validate the audit on "model organisms" (PTB-XL, open).** Plant a synthetic group signature with controlled overlap with a diagnostic feature and a controlled prevalence link; train models; show each CRC estimator recovers planted effects (sensitivity) and returns ~0 when none is planted (specificity). Pre-registered validity condition — answers "is your audit real?".
- **E1 — Baselines.** Per model × task × race: AUROC/AUPRC, FPR/FNR at fixed operating points, calibration (ECE) — vs echo truth (EchoNext, MIMIC-ECHO) and vs labels.
- **E2 — Race encoding + confound controls.** Linear/MLP probes per layer; race decodability conditional on acquisition (location_setting, year, device) and SES (insurance, language) — ECG analogue of Lotter 2024.
- **E3 — Covert race-conditioning, three independent designs (triangulation):**
  - (a) *Natural matched pairs:* caliper-match patients of different race on echo substrate (IVS, LVPW, LVEF, PASP, valve grades), age, sex, setting, year, heart rate → output difference.
  - (b) *Generative "matched guise":* VAE/diffusion ECG generator + latent gradient morphing (as in Obermeyer et al. *Nature* 2026) that moves race-classifier output while holding an echo-substrate regressor and interval measurements fixed → output change; also visualises *what* the race signal is.
  - (c) *Representation steering:* swap/erase the race subspace (probe/LEACE) and ablate race-predictive SAE features at each layer; random-direction and sex/age positive controls.
- **E4 — Label-source natural experiment (MIMIC).** Same patients, same architectures, trained on L-crit vs L-machine vs L-echo (vs L-icd) → compare CRC and TRD against echo truth. Cross-site: train on MIMIC labels, test on EchoNext echo truth.
- **E5 — Collision law.** Overlap index O per task×model (primary: principal angles between whitened race and task subspaces; secondary: attribution overlap in lead×time maps; SAE feature sharing) → regress |CRC| and TRD on O.
- **E6 — Remedy.** (R1) interpretable race-neutral LVH criterion (sparse GAM on lead voltages, QRS duration, axis, strain, age, sex) fitted to echo truth with an equalized-odds constraint (race used only during fitting) — an ECG analogue of the race-free CKD-EPI 2021 equation; (R2) race-neutral AI with fairness-constrained training, no race at inference; external validation (EchoNext ↔ MIMIC); accuracy–equity Pareto frontier.
- **E7 — Rigor.** Pre-registration; patient-level bootstrap CIs; BH-FDR over tasks×models; ≥3 seeds; negative controls (random directions, placebo attributes); Asian (3.4%) and Other treated as exploratory.

### Outcome map (every result is a finding)
| Result | Conclusion | Policy meaning |
|---|---|---|
| CRC ≈ 0 everywhere | ECG-AI does **not** covertly race-correct; disparities come from labels/prevalence | Fix labels, not models |
| CRC > 0 and L-echo reduces TRD | AI rediscovers physiological calibration that race-blind ECG criteria lack | Race-blind criteria are the problem; remedy E6 |
| CRC > 0 and harms under all labels | Covert race correction harms patients | Mandatory truth-anchored audits |
| Collision law holds / fails | Predictive tool for which tasks need audits / task-specific map | Regulator guidance either way |

### Mapping to the professor's four criteria (honest)
(a) New principle — "race-free inputs do not make race-blind AI; the label decides the direction" + the collision law. (b) New framework — truth-anchored matched-guise audit, validated on model organisms. (c) Discovery — first causal evidence (for or against) of covert race correction in cardiac AI, and what the race signal looks like. (d) Direction change — from subgroup-AUROC audits to truth-anchored conditioning audits; from race-blind labels to truth-anchored labels; a race-neutral ECG criterion. Strength of (a)/(d) depends on results; (b)/(c) hold either way.

### Risks and mitigations
- Generative counterfactual validity → triangulate with matched pairs + steering; validate on E0.
- Echo truth imperfect (no BSA in EchoNext; ≥13 mm wall threshold crude) → continuous measures; MIMIC-ECHO has fuller measurements.
- Race categories coarse; Hispanic is ethnicity → sensitivity analyses; report as social categories.
- Competitors: MGH group (Bollepalli 2025, Hsieh 2026); ecg_mechinterp repo (sex/age) → move fast; preprint.
- Nature odds are low for any single study → the same design fits Nature Medicine / Nature Cardiovascular Research / Nature Machine Intelligence.

### Realistic timeline
Weeks 0–2 access + environment + pre-registration draft · Weeks 2–5 E0 (PTB-XL) + E1/E2 (EchoNext) · Weeks 5–9 E3 + E4 · Weeks 9–12 E5 + MIMIC replication · Weeks 12–16 E6, writing, preprint.

### Immediate actions for Suryansh (start now — access is the critical path)
1. PhysioNet account → **sign the EchoNext DUA**.
2. **CITI "Data or Specimens Only Research"** → PhysioNet credentialing (supervisor as reference) → MIMIC-IV, MIMIC-IV-ECHO, MIMIC-IV-Note.
3. Download now (open): **PTB-XL** (1.7 GB) and **MIMIC-IV-ECG** waveforms (33.8 GB zip).
4. Optional later: BDSP credentialing for HEEDB (MGH + Emory replication).

### Step 7 preview (on command)
Repo with swappable dataset configs (EchoNext .npy, MIMIC WFDB, PTB-XL WFDB, HEEDB), FM wrappers via AI4HealthUOL loaders, training heads (frozen-linear + fine-tune), audit modules (matched pairs, steering/LEACE/SAE, generative morphing), classic-criteria computation from raw µV (MIMIC/PTB-XL), metrics + bootstrap/BH stats, E0 model organisms, pre-registration template, one runner script per experiment, compute estimates.

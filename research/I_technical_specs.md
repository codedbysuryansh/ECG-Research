# Step R2 — Verified technical specifications (for the Claude Code files)

Sources read directly from official code (raw GitHub) unless marked [search].

## Foundation models (via AI4HealthUOL/ecg-fm-benchmarking, ICLR 2026)
Repo: github.com/AI4HealthUOL/ecg-fm-benchmarking — wrappers in `code/clinical_ts/models/fm_ecg.py` (`ECGFounderWrapper, ECGJEPAWrapper, StMemWrapper, MerlWrapper, EcgFmKEDWrapper, HubertEcgWrapper, CPCWrapper`); ECG-FM runs in a separate env (`ecg_fm_env.yaml`, fairseq-signals). Each wrapper's internal forward returns `(sequence_features, pooled_features)`; `eval_mode ∈ {finetuning_linear, finetuning_nonlinear, frozen, linear}`.
| Model | ckpt (as named in run.sh) | input window | fs | leads |
|---|---|---|---|---|
| ECGFounder | `ecg_founder/12_lead_ECGFounder.pth` (HF PKUDigitalHealth/ECGFounder) | 2.5 s | 500 | 12 |
| ECG-JEPA | `ecg_jepa/multiblock_epoch100.pth` | 10 s | 250 | 8 (I, II, V1–V6; indices [0,1,6..11]) |
| ST-MEM | `st_mem/st_mem_vit_base_full.pth` | 2.4 s | 250 | 12 |
| MERL (ResNet18) | `merl/res18_best_encoder.pth` | 2.5 s | 500 | 12 |
| ECGFM-KED | `ecgfm_ked/best_valid_all_increase_with_augment_epoch_3.pt` | 10 s | 500 | 12 |
| ECG-CPC | `cpc/config_last_11597276_ckpt.yaml` | 2.5 s | 240 | 12 |
| HuBERT-ECG base | `hubert_ecg/hubert_ecg_base.safetensors` | 5 s | 100 | 12 |
| ECG-FM | `ecg_fm/mimic_iv_ecg_physionet_pretrained.pt` (HF wanglab/ecg-fm) | 5 s | 500 | 12 |
- ECG-FM pretraining data = MIMIC-IV-ECG v1.0 + PhysioNet 2021 (CPSC, CPSC-Extra, PTB-XL, Georgia, Ningbo, Chapman) [official README] → **leakage** on those for ECG-FM.
- ECGFounder trained on HEEDB (12SL-assisted physician labels; 150 classes) [search + repo]. Its own `dataset.py` z-scores over the **whole 12×T array** (not per lead), applies a **50 Hz** notch + 0.67–40 Hz Butterworth + median-filter baseline removal; its `ptbxl_eval.py` does **not** filter; `dataset.py` has a bug (resampled signal discarded). ⇒ use the benchmark wrapper's preprocessing and **reproduce a published number before trusting any model** (Gate G-REPRO).

## Datasets
- **PTB-XL 1.0.3** (open): `ptbxl_database.csv`, `scp_statements.csv`, `records100/`, `records500/` WFDB; `strat_fold` 1–10 (9 = val, 10 = test recommended).
- **CODE-15%** (open, Zenodo 4916206): `exams.csv` (exam_id, patient_id, age, is_male, 1dAVb, RBBB, LBBB, SB, ST, AF, death, timey, …), `exams_part*.hdf5` with datasets `exam_id`, `tracings` (N × 4096 × 12; 400 Hz; zero-padded; leads DI, DII, DIII, AVR, AVL, AVF, V1–V6). README states "scale 1e-4V" and also "if the signal is in V multiply by 1000" → **verify amplitude units empirically** (Gate G-UNITS).
- **MIMIC-IV-ECG v1.0** (open, ODbL): zip `mimic-iv-ecg-diagnostic-electrocardiogram-matched-subset-1.0.zip`; WFDB under `files/pXXXX/pSUBJECT/sSTUDY/STUDY.{hea,dat}`; 500 Hz, 10 s; **lead order in files is I, II, III, aVR, aVF, aVL, V1–V6** (map by `sig_name`, never by index); `machine_measurements.csv` columns include `subject_id, study_id, cart_id, ecg_time, report_0..report_17, bandwidth, filtering, rr_interval, p_onset, p_end, qrs_onset, qrs_end, t_end, p_axis, qrs_axis, t_axis` with out-of-range sentinels (clean axes outside ±360, times outside 0–5000 ms); carts from Burdick/Spacelabs, Philips, GE [search].
- **MIMIC-IV (credentialed)** schemas [mimic-code create.sql]: `hosp.patients(subject_id, gender, anchor_age, anchor_year, anchor_year_group, dod)`; `hosp.admissions(... admittime, dischtime, deathtime, insurance, language, marital_status, race, ...)`; `hosp.omr(subject_id, chartdate, seq_num, result_name, result_value)` with result_name values incl. 'Height (Inches)', 'Weight (Lbs)', 'BMI (kg/m2)'; `ed.edstays(subject_id, hadm_id, stay_id, intime, outtime, gender, race, arrival_transport, disposition)`. **`dod`**: hospital + state records; deaths >1 year after the **last hospital discharge** are censored (NULL) [search, MIMIC-IV docs].
- **MIMIC-IV-ECHO v1.0.1** (credentialed) [search]: `echo-study-list.csv` links DICOM `study_id` to a structured `measurement_id` (+ `measurement_datetime`, within 2 days) in `structured_measurement.csv`; two measurement systems with different variable names (e.g., `lvef`, `lvef_upper` vs `biplane_lvef`, `lvef_3d`); IVS/LVPW available. **Discover variable names programmatically (names only, never rows).**
- **EchoNext v1.1.x** (restricted DUA): `echonext_metadata_100k.csv` (case varies by version: also `EchoNext_metadata_100k.csv`) with `ecg_key, patient_key, age_at_ecg, sex, acquisition_year, location_setting, race_ethnicity, most_recent_ecg, split` and flags `lvef_lte_45_flag, lvwt_gte_13_flag, aortic_stenosis_moderate_or_greater_flag, aortic_regurgitation_..., mitral_regurgitation_..., tricuspid_regurgitation_..., pulmonary_regurgitation_..., rv_systolic_dysfunction_moderate_or_greater_flag, pericardial_effusion_moderate_large_flag, pasp_gte_45_flag, tr_max_gte_32_flag, shd_moderate_or_greater_flag`; waveforms `EchoNext_{train,val,test}_waveforms.npy` (each item 1 × 2500 × 12, 250 Hz, median-filtered, clipped, z-scored dataset-wide), `EchoNext_{split}_tabular_features.npy` (7 preprocessed features); a `no_split` file may exist. **Rows of each split's metadata are in the same order as that split's npy arrays** (ml4h, Broad) — verify counts per split before joining (Gate G-ALIGN). Continuous echo values: described in docs; column names to be discovered.
- **HEEDB v5** (BDSP, credentialed): cohorts MGH (site I0001) and EUH/Emory (site I0006); per site `WFDB/`, `12SL_diagnoses/`, `metadata/`, `icd_codes/`; MGH waveforms `.mat`, EUH `.dat`; demographics incl. race/ethnicity/education; DOB, ECG date, last-contact date, death date (+ MA state registry for MGH) [search, Sci Data 2026]. Benchmark code reads `Metadata/demographics_ECG.csv` (`FileName, PatientRace, SexDSC, Age, BDSPPatientID, …`) and `Metadata/ECG_Interpretations.psv` → **folder names differ between versions: discover structure first.**

## Libraries
- `concept-erasure` 0.2.4 (PyPI; Python ≥3.10; torch): `LeaceEraser.fit(X, Z)`, `LeaceFitter` (method "leace"|"orth", `affine`, `constrain_cov_trace`, `shrinkage`, `svd_tol=0.01`), `OracleEraser` (needs Z at inference), `QuadraticEraser` (needs Z at inference), `QuadraticEditor`. NumPy port with identical algebra in `claude_code/reference/ecgbias_ref/leace_np.py` (tested).
- `wfdb` 4.3.x, `neurokit2` 0.2.x, `h5py`, `scikit-learn`, `statsmodels`, `scipy`.

## Clinical formulas (verified)
- Sokolow-Lyon: SV1 + max(RV5, RV6) ≥ 3.5 mV.
- Cornell voltage: RaVL + SV3 > 2.8 mV (men), > 2.0 mV (women).
- Cornell product: (RaVL + SV3 [+0.8 mV in women]) × QRSd ≥ 2440 mm·ms (Molloy/Okin; +0.6 mV variant = sensitivity analysis). Note 1 mV = 10 mm.
- Peguero-Lo Presti: S_D (deepest S, any lead) + SV4 ≥ 2.3 mV (women), ≥ 2.8 mV (men).
- ASE/EACVI 2015: LV mass (g) = 0.8 × 1.04 × [(LVIDd + IVSd + PWd)³ − LVIDd³] + 0.6 (cm); LVMI > 115 g/m² (men), > 95 g/m² (women); RWT = 2·PWd/LVIDd, > 0.42 = concentric. BSA (Mosteller) = √(height_cm × weight_kg / 3600).

## Compliance
- PhysioNet "Use of MIMIC Data with Large Language Models and Online Services" (updated 24 Sep 2025): the credentialed DUA prohibits sharing data with third parties incl. via APIs/online platforms; if cloud LLMs are used, require zero retention, no training, no human review; local LLMs strongly recommended. ⇒ Claude Code must never read row-level credentialed/restricted data (MIMIC-IV clinical, MIMIC-IV-ECHO, EchoNext, HEEDB) or row-level derivatives; it may read code, schemas (column names) and suppressed aggregates.
- Claude Code permission syntax: `Read(//abs/path/**)`, `Read(~/path/**)`, `Read(relative/**)`; `Edit(path)` for writes; PreToolUse hooks on `Bash` can block commands.

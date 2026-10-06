# Step G — Data facts that decide feasibility (verified 6 Oct 2026 via search; "check" = confirm on the dataset page)

| Dataset | Access | What matters for the flagship |
|---|---|---|
| **EchoNext v1.1** (Columbia; PhysioNet) | Restricted: PhysioNet account + data use agreement | 100,000 ECGs / 36,286 pts, 2008–2022; **race/ethnicity** (31% Hispanic, 29.2% White, 16.5% Black, 3.4% Asian, 19.9% other/unknown), sex, age, care setting; echo truth: LVEF, IVS, posterior wall, PASP, TR velocity, 11 SHD labels. Waveforms **median-filtered, clipped (0.1/99.9 pct), z-scored dataset-wide, 250 Hz** → good for auditing learned models, not for absolute-mV criteria. Not in any public FM's pretraining (per previous ledger; check). |
| **MIMIC-IV-ECG v1.0** (BIDMC) | **Open** (ODbL) | ~800k ECGs / ~160k pts, 2008–2019, raw waveforms + machine reports/measurements; **mixed carts (Burdick/Spacelabs, Philips, GE)**. Race only via MIMIC-IV. Note: some FMs were pretrained on MIMIC-IV-ECG (e.g., ECG-FM; check others) → leakage risk; use FMs not pretrained on it for MIMIC analyses, or report as in-distribution. |
| **MIMIC-IV v3.x clinical** | Credentialed (CITI "Data or Specimens Only Research" + PhysioNet credentialing + DUA) | Race (ED: White 58.5%, Black 22.4%, Hispanic 8.3%, Asian 4.5%), insurance, language, deaths; **OMR table: height, weight, BMI, BP**. |
| **MIMIC-IV-ECHO** | Credentialed | Structured measurements from >200k echo studies (+>500k DICOMs), linkable by subject_id → truth for MIMIC ECGs with raw µV (where rule-based criteria can be computed). |
| **HEEDB v5** (MGH + Emory; BDSP) | Credentialed (free) | ~10.8M–11.7M ECGs; race at both sites (**Emory 31.6% Black**); **death dates**; 12SL reads + physician overreads; ICD codes. ECGFounder trained on HEEDB (MGH) → Emory is the cleaner external test. |
| **PTB-XL 1.0.3** | Open | 21,799 ECGs; no race; cardiologist labels → **E0 model-organism validation** and pilots (keeps confirmatory data untouched for a Registered Report). |
| **CODE-15%** | Open (Zenodo) | 345,779 exams (Brazil); no race; **mortality**; labels from Glasgow/Uni-G + cardiologists → location control, pilots. |
| **Chapman/Ningbo, SPH, CPSC** | Open | China; location/acquisition control arm. |
| **UK Biobank** | Paid application | Imaging cohort ~96.6% White; South Asians <1% of imaging attendees → optional only. |
| South Asian / Indian public 12-lead | None found | Limitation, stated honestly. |

**Registered-Report constraint (Nature policy):** secondary analyses of existing datasets are welcome "provided that authors have had no prior access to the data in question" (self-certification or gatekeeper letter); otherwise a bias-control level system applies. Pilots on PTB-XL/CODE-15 do not compromise EchoNext/MIMIC-clinical/HEEDB as confirmatory data.

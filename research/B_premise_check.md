# Step B — Premise check: is "ECG-AI is biased against people from other regions/populations" established?

**Your premise (6 Oct 2026):** "My earlier plan based on the research was that AI has bias with people from different regions. And that, I guess, has been proven."

**Verdict: NOT proven as a general fact. The evidence is mixed and strongly task-dependent.** For several major ECG-AI tasks, Western-trained models transported to Africa, Asia and minority US groups with little or no loss. Large gaps are documented for only a few tasks, and their cause is unknown. Your pipeline assumes a bias exists to remove. That assumption has to become a question the study tests.

Legend: **[J]** = peer-reviewed journal (reputed); **[J-low]** = peer-reviewed but lower-tier/MDPI-type; **[P]** = preprint/workshop (not peer-reviewed; report only).

## B.1 Evidence AGAINST a general cross-population bias (good transport)
| Study | Task | Population shift | Result |
|---|---|---|---|
| Noseworthy et al., *Circ Arrhythm Electrophysiol* 2020;13:e007988 **[J]** | Low LVEF (≤35%) | Model derived in 96.2% non-Hispanic White (Mayo); tested on 97,829 pts across race/ethnicity | **Consistent performance across racial/ethnic subgroups**, although ECG characteristics differ by race |
| Adedinsewo et al., *Nature Medicine* 2024 (SPEC-AI Nigeria RCT, doi 10.1038/s41591-024-03243-9) **[J]** | LVSD in pregnancy | US (Mayo) model → 1,196 women, 6 Nigerian hospitals | 12-lead AI AUC **0.919** for LVEF ≤35%; AI-guided screening **doubled** cardiomyopathy detection |
| Mayo AI-ECG in Uganda, *Eur Heart J* 2020 suppl. (abstract) **[J-abstract]** | LVSD | US model → Ugandan cardiac clinic | AUC **0.866** |
| Hsieh et al. (MGH), *EHJ – Digital Health* 7(5):ztag080 (June 2026) **[J]** | LVSD | Trained on demographically imbalanced (incl. White-only) cohorts | "**high accuracy and demographic parity**, even when trained on imbalanced cohorts"; external test on EchoNext |
| Mayo AS AI-ECG in Korea, PMC12282354 (2025) **[J]** | Aortic stenosis | US model → Korean patients, no fine-tuning | AUC **0.85**, comparable to original |
| Korean VHD AI-ECG, *Eur Heart J* 46(44):4823 (2025) **[J]** | Regurgitant valve disease | East-Asian-trained → other continents | Performed well "across continents and ethnicity subgroups" |
| EchoNext, *Nature* 644:221 (2025) **[J]** | Structural heart disease | Multi-system | "consistent performance across … racial and/or ethnic groups" |
| Korean AI-ECG image model in UK Biobank, *Sci Rep* 2026 (PMC13069017) **[J]** | Extreme CMR metrics | Korea → UK, cross-ethnic | Stable across subgroups |

## B.2 Evidence FOR population-dependent failure
| Study | Task | Finding |
|---|---|---|
| Kaur et al. (Stanford), *Circ Heart Fail* 2024;17:e010879 **[J]** | 5-yr incident HF from ECG (test set 160,312 ECGs) | Significantly worse in **Black patients aged 0–40**, most in young Black women. **Adding race/sex/age to the model, per-race models, and race-balanced training did NOT fix it**; group-specific thresholds improved F1. Cause not identified. |
| Clinical (pre-AI) ECG criteria: healthy Black adults show higher QRS voltage, more often meet LVH voltage criteria, more early repolarization / benign ST elevation (Glasgow eprints 189824; JACC 2017 athlete recommendations treat J-point elevation + anterior TWI V1–V4 as normal in Black athletes) **[J]** | LVH, STEMI-mimics | Criteria derived mostly in white populations misfire across groups (e.g., CoArtHA in hypertensive Black Africans, *Global Heart* 2025/26) |
| Reviews: ECG datasets — only 29% report race/ethnicity; 89% of recordings from White individuals (systematic review cited in JMIR Res Protoc 2026 e82486) **[J]** | — | The *evidence base* is thin, which is not the same as proof of bias |

## B.3 What "region/location" actually means in the data (counter-point)
- Most race evidence is **within one country** (US). Cross-*country* comparisons change device, filters, sampling rate, label vocabulary and hospital practice together, so a "regional" gap is confounded with acquisition. Analogue: in chest X-ray, *acquisition parameters* drove AI race recognition (Lotter, *Nat Commun* 2024, doi 10.1038/s41467-024-52003-3) **[J]**; diverse multi-centre ECG pretraining encodes cohort-specific artifacts (CAPE FM, arXiv 2509.10369) **[P]**.
- So "what tells the AI where the person is from" may be **the machine, not the body**. Your pipeline's "remove the location signal" step would then be removing a device fingerprint, which is a different and much less interesting finding.

## B.4 Is population information in the ECG at all? (yes, but read correctly)
- Bollepalli, Singh, Armoundas, *npj Cardiovascular Health* 2025 (doi 10.1038/s44325-025-00087-1) **[J]**: ~10M ECGs / 1.76M subjects (MGB); CNN separates healthy Black vs White ECGs with **AUC ≈ 0.86** (an AUC, **not "86% accuracy"** as your IEEE draft and whitepaper say). Signal similar at birth, grows with age; weaker in low-income areas; QRS complex is key → **non-genetic / environmental**. **Fix the "86 percent of the time" sentence in the IEEE draft.**
- Being *decodable* ≠ being *used*: CXR foundation models encode race strongly but barely use it linearly (Kim et al., *Diagnostics* 2026, 16(13):2030, MIMIC-CXR, PMC13359982) **[J-low]**; Glocker et al., *eBioMedicine* 89 (2023) **[J]** — protected characteristics are encoded in disease-detection embeddings.

## B.5 Bottom line for the premise
1. "ECG-AI is biased across regions" is **not** an established fact; for structural tasks (LVSD, AS, VHD, SHD) the strongest evidence (including an RCT in Nigeria, *Nature Medicine* 2024) shows good transport.
2. Where gaps exist (HF prediction in young Black patients; criteria-based LVH/ST calls), **nobody has explained why**, and the obvious fixes (add race, rebalance data) failed (Kaur 2024).
3. The real scientific puzzle is therefore: **why do some ECG-AI tasks transport cleanly while others fail for specific groups, and is the AI's use of population information right or wrong?** A study that solves this puzzle is more valuable than one that assumes a bias and removes it.

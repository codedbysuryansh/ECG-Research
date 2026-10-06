# Step F — Recommended flagship (your architecture, made identifiable) + stress test

## F.1 The one-sentence question
**When an ECG foundation model reads a patient's population from the waveform, does using that information move its diagnosis closer to or further from the patient's true heart, and what decides which?**

Working title: *"Bias or biology? A ground-truth test of how cardiac AI uses the population information it reads from the heart."*

## F.2 What stays from your architecture (and why it's still the core)
| Your step | Kept? | Role now |
|---|---|---|
| "Find what tells the AI the person's population" (probes, SAE features, decoding to leads/segments) | **Kept** | *Mechanism arm*: what the population signal physically is (QRS voltage? T-wave? axis?) and how much is anatomy, body size, machine, or social context |
| "Surgically remove it with LEACE" | **Kept, upgraded** | The *intervention*: LEACE + non-linear erasure + targeted SAE-feature ablation, with erasure-strength curves |
| "Then see what makes it give the result" | **Kept, re-measured** | Measured against **independent truth** (echo measurements, outcomes), per group, not against ECG-derived labels |
| Three cases (can't read / reads but biased / no bias) | **Kept, completed** | Becomes five identifiable cases (adds "biased answer key" and "population use is correct") |
| Generalize across models | **Kept** | ≥6 open ECG foundation models, ≥3 health systems, race + sex |

## F.3 What changes, and the evidence that forces each change
| Change | Why (evidence) |
|---|---|
| **Yardstick = independent truth** (echo IVS/posterior wall/LV mass, LVEF, PASP; mortality) instead of ECG-derived labels | Without it, "bias", "correct physiology" and "biased labels" are indistinguishable (Jones…Glocker, *Nat Mach Intell* 2024 — "may seem indistinguishable yet require substantially different mitigation"). ECG labels are produced by race-blind (12SL) or race-aware (Glasgow) rules, so they cannot referee race questions. |
| **Erasure is a measuring instrument, not assumed to be the cure** | Erasing demographic info can *create* bias (Parikh…Feragen 2026 [P]; Petersen 2023 [P]); race adjustment can improve accuracy (Zink, Obermeyer, Pierson, *PNAS* 2024); linear erasure leaves race decodable non-linearly (AUROC 0.91 after residualisation, *Diagnostics* 2026) and did not close gaps; Yang *Nat Med* 2024 found shortcut removal fairness doesn't transfer. The *sign* of the effect is the finding. |
| **Add a label-source manipulation** (same models trained on race-blind rule labels vs race-aware rule labels vs echo truth vs outcomes) | The "biased answer key" case is real in ECG: race-blind LVH voltage criteria have lower specificity in Black patients vs echo (*JAMA* 1992; LIFE, *Am J Hypertens* 2002); ECGFounder was trained on 12SL-assisted labels; label choice drives algorithmic bias (Obermeyer *Science* 2019; Pierson *Nat Med* 2021; Bernhardt…Glocker *Nat Med* 2022). |
| **Drop "first SAE / first decomposition / first cross-ethnic / first LEACE on physiology" claims** | All taken (C_component_verdicts.md). |
| **Drop the UK Biobank South-Asian arm from the critical path** | <1% of imaging attendees are South Asian; realistic n with ECG ≈ hundreds–1,000. India-specific premise also weak (Macfarlane, *J Electrocardiol* 2015: Caucasian criteria apply to Indians). |
| **"Location" becomes a control arm** | Cross-country differences are confounded with device/filters/label rules (Lotter, *Nat Commun* 2024 analogue; MIMIC alone mixes Burdick/Spacelabs, Philips, GE carts). We test how much "where you're from" is "which machine". |

## F.4 Definitions (pre-registrable)
- **P** — population descriptor: self-reported race/ethnicity (social category; used only for auditing, never as model input). Control attribute: **sex** (clinical criteria are *already* sex-specific; a built-in contrast). Location control: dataset/country.
- **T** — independent truth: continuous echo measurements (IVS, LVPW, LV mass if available, LVEF, PASP, TR velocity), echo-graded disease, and outcomes (death; incident HF) where available.
- **f_k** — score of a frozen FM + head for task k (LVH, LV dysfunction, pulmonary hypertension, valve disease, "abnormal ECG", AI-ECG age).
- **Truth-referenced disparity, TRD_k** — group difference in error *versus T*: FPR/FNR at fixed operating points, calibration intercept/slope, and the residual score gap E[f_k | T, covariates, P=a] − E[f_k | T, covariates, P=b].
- **Population-use effect, PUE_k** — change in TRD_k when population information is removed from the representation (erasure family below). PUE < 0: the model was using P harmfully (shortcut). PUE > 0: the model was using P helpfully (implicit physiological correction). PUE ≈ 0: P not used.

## F.5 Experiments
- **E0 — Validate the instrument on "model organisms" (PTB-XL / CODE-15, open; no race data needed).** Plant a synthetic group signature with *known* role (harmful shortcut / helpful correction / irrelevant) and controlled overlap with a diagnostic feature; show the erase-and-measure audit recovers each planted truth (sensitivity) and returns ≈0 when nothing is planted (specificity). Pre-registered validity condition.
- **E1 — Baselines vs truth.** Per model × task × group: AUROC/AUPRC (for context only), FPR/FNR at fixed thresholds, calibration, residual score gap at fixed T.
- **E2 — What tells the AI the population (your step 1).** Linear/MLP probes per layer; SAE features; decode to lead × time; variance in P-decodability explained by anatomy (echo), body size (MIMIC OMR height/weight/BMI), heart rate, acquisition (cart vendor, care setting, year), and social proxies (insurance, language).
- **E3 — Surgical removal, judged against truth (your step 2).** LEACE; non-linear erasure (adversarial/kernel); targeted SAE-feature ablation; random-direction and placebo-attribute controls; positive control with P as an explicit input. Report PUE_k with erasure-strength curves.
- **E4 — The answer key decides?** Same architectures trained on (i) race-blind rule labels (12SL-style statements; Sokolow-Lyon / Cornell / Peguero-Lo Presti computed from raw µV in MIMIC), (ii) race-aware rule labels (validated race-specific thresholds), (iii) echo truth, (iv) outcomes → compare TRD and the *sign* of PUE. Natural contrast: supervised FMs pretrained on rule labels (ECGFounder ← 12SL-assisted HEEDB) vs self-supervised FMs (no labels in pretraining).
- **E5 — Where it happens ("collision").** Overlap O_k between the population subspace and task subspace (principal angles; shared SAE features; attribution overlap). Prediction: |PUE_k| and TRD_k grow with O_k (e.g., LVH ≫ LV systolic dysfunction), which would explain why LVSD models transport cleanly (Noseworthy 2020; Adedinsewo 2024; Hsieh 2026) while LVH-type calls and HF prediction do not (Kaur 2024).
- **E6 — Location control.** Dataset/country decodability across PTB-XL (Germany), CODE-15 (Brazil), Chapman/Ningbo/SPH (China), MIMIC (US) before vs after acquisition harmonisation (resampling, filtering, amplitude normalisation) → how much "location" is the machine.
- **E7 — Remedy (conditional on results).** A truth-anchored, race-neutral model/criterion: either explicit physiology (body size, anatomy-informed) replacing population proxies, or fairness-constrained training against truth, no race at inference — the ECG analogue of CKD-EPI 2021 / GLI-Global spirometry.
- **Optional extension D — AI-ECG age:** same test with mortality as truth (bias vs "weathering").

## F.6 Outcome map (every branch is a finding)
| Result | Meaning | Field consequence |
|---|---|---|
| PUE > 0 for high-overlap tasks under truth labels | AI silently performs physiological (race-linked) correction that race-blind rules lack; erasing it *harms* patients | "Remove demographic info" is the wrong default; race-blind ECG rules are the problem |
| PUE < 0 | AI uses population as a harmful shortcut; erasure helps | Erasure-based mitigation justified — but only shown against truth |
| PUE sign flips with label source | **The answer key decides** whether AI's population use heals or harms | Fix labels/ground truth, not architectures; audit labels before models |
| PUE ≈ 0, TRD ≠ 0 | Disparity is not mediated by population information (labels, prevalence, or genuine perception limits) | Erasure is pointless; target labels/data |
| TRD ≈ 0 everywhere | Modern ECG FMs are truth-fair across groups | Reassuring, rigorously shown; collision map still tells which tasks to watch |
| Collision law holds / fails | Predictive rule for which AI tasks need audits / task-specific map | Regulatory guidance either way |
| Location signal mostly machine | Cross-country "population bias" claims are largely acquisition artefacts | Changes how cross-country AI studies must be designed |

## F.7 Stress test (how a hostile Nature referee attacks, and the answer)
1. **"Echo is not perfect truth; LVH thresholds are race-blind too."** → Use continuous measurements (not thresholds), several independent truths (wall thickness, LV mass, LVEF, PASP), outcomes as a second truth, and sensitivity analyses for unmeasured truth.
2. **"Erasure is incomplete (non-linear leakage)."** → Erasure-strength curves; linear + non-linear + SAE-feature ablation; decodability of P reported after each; E0 planted-signal validation.
3. **"Erasure also removes real physiology."** → That is the measured quantity: we report *which* features erasure removes (SAE decoding) and whether truth-referenced error improves or worsens.
4. **"Confounding by case-mix (more hypertension in some groups)."** → Conditioning on truth; matching on truth + age + sex + setting + year; within-truth-stratum analyses.
5. **"Race is a social category, not biology."** → Agreed and stated; P used only for auditing; follow NASEM 2023 population-descriptor guidance; genetic interpretations avoided; Dallas Heart (*JAMA Cardiol* 2019) vs Bollepalli (2025) conflict reported, not resolved by us without genetics.
6. **"EchoNext waveforms are normalised; you cannot compute voltage criteria there."** → Correct (median-filtered, clipped, dataset-wide z-scored at 250 Hz). Criteria-label experiments run on MIMIC raw µV; EchoNext used for truth-referenced audits of learned models.
7. **"Single-country (US) data — not global."** → The claim is about a mechanism (how AI uses population information it reads from physiology), shown across 3 US health systems + sex as a second attribute + cross-country location control. The professor's "not country-specific" requirement is met by the mechanism-level claim.
8. **"Scooped?"** → Closest groups: Columbia/EchoNext (Elias), MGH (Armoundas/Singh), MIT (Ghassemi), Imperial (Glocker), Copenhagen (Feragen/Petersen). Nothing equivalent found as of 6 Oct 2026. Speed and a timestamped Stage-1 Registered Report or preregistration are the defence.
9. **"Is this Nature or Nature Medicine?"** → Honest answer below (F.8).

## F.8 Fit to your professor's four criteria (honest strength)
| Criterion | What delivers it | Strength |
|---|---|---|
| (a) New scientific principle | "Whether medical AI's use of population information heals or harms is decided by its ground truth, not its architecture" + collision rule for *where* | Strong *if* E4/E5 come out clean; conditional |
| (b) Completely new framework | Truth-anchored erase-and-measure audit (validated on planted model organisms) — applicable to any medical AI where population differences are partly real (ECG, spirometry, pulse oximetry, eGFR) | Strong regardless of outcome |
| (c) Breakthrough discovery | First causal answer to "does cardiac AI silently race-correct, and is it right?" + what the population signal physically is | Strong regardless of sign (any sign is new) |
| (d) Changes the field's direction | From subgroup-AUROC audits and "remove demographics" defaults to truth-anchored audits and label repair; informs FDA-style evaluation of AI-ECG | Strong if results are clear; framed conditionally until data are in |

**Nature odds, stated once:** *Nature* accepts ~8% of submissions and editors decide "broad interest" before review. No design guarantees it. The Registered Report route (open to all fields since 27 May 2026) is the best fit for a result-independent design, because acceptance is decided on the question and methods. It requires that the confirmatory data were not already analysed. If *Nature* declines, the identical paper is suited to *Nature Medicine*, *Nature Cardiovascular Research* or *Nature Machine Intelligence*. That is how every lab cascades; it is not planning for a lower journal.

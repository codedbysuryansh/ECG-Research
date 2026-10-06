# ECG research: novelty audit and Nature-level direction
**Prepared 6 Oct 2026 for Suryansh Singh (TIET).** Supporting notes: `research/A…G_*.md`. Evidence labels: **[J]** = reputed peer-reviewed journal; **[J-low]** = peer-reviewed, lower tier; **[P]** = preprint/workshop (reported, not used as proof).

---

## 0. Bottom line
1. **Your original architecture, as written, is no longer novel.** Its four "firsts" are taken: sparse autoencoders on ECG foundation models (July 2026), the multi-cause Shapley decomposition (ICML 2023), cross-ethnic ECG-AI fairness (2020–2026), and LEACE on physiological embeddings (2026). Its conceptual frame of prevalence, presentation and annotation bias was published in *Nature Machine Intelligence* in 2024.
2. **Your premise is weaker than you think.** "ECG-AI is biased against people from other regions" is **not established**. Western-trained ECG-AI for heart-pump weakness worked in Nigeria (*Nature Medicine* 2024 randomised trial), Uganda and Korea, and across US racial groups. Gaps are real only for some tasks (e.g., heart-failure prediction in young Black patients), and nobody knows why. For India specifically, the best normal-limits study says Western criteria apply to Indians.
3. **Your design has a logic gap that no amount of extra tools fixes.** It has no ground truth independent of how ECGs are read. Without that, "the AI is biased", "the AI is correctly using real population differences" and "the training labels were biased" look identical. So "erase the population signal with LEACE and see if the gap closes" cannot tell you whether you fixed a bias or broke correct physiology.
4. **Recommended direction: keep your core experiment, change the yardstick.** Find what tells the AI the population, surgically remove it, and judge the result against **independent heart truth** (echocardiography measurements, deaths), not ECG-derived labels. Also swap the "answer key" the models are trained on. The finding is the *sign*: removal can **help or harm**, and any result is new.
5. **Why this is *Nature*-shaped:** it answers a live, high-stakes question nobody has tested. Medicine is abolishing race correction (kidney 2021, lungs 2023), yet ECG standards still disagree (GE 12SL is race-blind; Glasgow is race-specific; the AHA 2009 recommendations endorse race adjustment when validated), and AI now reads race directly from the waveform. *JAMIA* (Apr 2026) says this has not been tested. **Since 27 May 2026 *Nature* accepts Registered Reports in all fields**, which suits a result-independent design. One catch: the confirmatory data must not have been analysed before submission.

---

## 1. Your architecture (exact restatement → `A_original_architecture.md`)
Find what tells the AI a patient's population/"localization" → surgically remove it with LEACE at inference (weights frozen, no labels) → see what then drives the output. Three cases: (1) the AI can't read the ECG, (2) it reads correctly but a population signal biases it, (3) no bias. Wrapped in four stages: Decompose (5-way Shapley) → Localize (SAE + activation patching) → Sever (LEACE) → Generalize (≥3 models; MIMIC race + UK Biobank South Asians).

## 2. Verdict on every novelty claim (details → `C_component_verdicts.md`)
| # | Your claim | Verdict | Decisive evidence |
|---|---|---|---|
| N1 | 5-way decomposition never done; prior work ≤2 causes | ❌ Method exists | Zhang, Singh, Ghassemi, Joshi, **ICML 2023** (Shapley over any set of distribution shifts) [J]; DISDE, ***Operations Research* 2025** [J]; HDPD, **NeurIPS 2024** [J]. Concept: Jones…Glocker, ***Nat Mach Intell* 2024** (prevalence / presentation / annotation disparities) [J] |
| N2 | Nobody looked inside an ECG model; SAEs never used on ECG FMs | ❌ | FactorECG, *EHJ-Digital Health* 2022 [J]; **ECG-InterpBench** (6 ECG FMs, 29 Jul 2026) [P]; **CADENCE** (8,192 "cardiac atoms", causal ablation) [P] |
| N3 | No inference-time label-free fix on medical time-series; LEACE never on physiology; all fixes need retraining | ❌ / weak | LEACE on EEG FM embeddings (June 2026) [P]; post-hoc orthogonalisation of CXR embeddings [J-low]; **group thresholds fixed ECG gaps without retraining** (Kaur, *Circ Heart Fail* 2024 [J]; *J Electrocardiol* 2026 [J]) |
| N4 | Shortcut-vs-perception never tested | ⚠️ Partly open | For **race**, no causal test exists. For **sex**, *J Electrocardiol* 2026 [J] showed equal AUC/calibration but lower female scores in both classes, fixed by equalising specificity, i.e., your case 2. Imaging: "shortcut lives in the classifier head, not the representation" (Kina & Petersen 2026) [P] |
| N5 | Within-hospital design never used; acquisition held constant | ❌ / shaky | Within-system race analyses are standard (Mayo 2020, Stanford 2024, MGH 2026) [J]. Acquisition is **not** constant: MIMIC mixes Burdick/Spacelabs, Philips and GE carts; in CXR, acquisition differed by race and drove AI race recognition (Lotter, ***Nat Commun* 2024**) [J] |
| N6 | Cross-ethnic ECG fairness untested ("field says so", DA-GAT-v2) | ❌ | DA-GAT-v2 spoke about *its own* data. Counter-examples: Noseworthy 2020; Kaur 2024; Adedinsewo *Nat Med* 2024; Hsieh *EHJ-DH* 2026; **Ji et al., *IEEE TBME* 2026 (ACS: Black vs non-Black sensitivity gap 9.8% → 1.3%)**; EchoNext *Nature* 2025 [all J] |
| N7 | UK Biobank South Asian cohort (2,782) | ❌ Not feasible | UKB imaging/ECG cohort ≈96.6% White; South Asians <1% of imaging attendees; "2,782" matches no ECG count (Pan-UKBB CSA = 8,876 *whole biobank*) |
| N8 | Pre-registered every-outcome design is novel | ⚠️ Good practice, not novelty | But see §6: *Nature* now runs Registered Reports in all fields |

## 3. Where you (and the drafts) are wrong, and why
- **"Region bias is proven."** No. Transport evidence for structural tasks is strong: Nigeria RCT AUC 0.919 (*Nat Med* 2024) [J]; Uganda AUC 0.866 [J-abstract]; Mayo AS model in Korea AUC 0.85 [J]; EchoNext AUROC 0.84–0.86 across race [J]; an MGH model trained on White patients only still worked for all groups (*EHJ-DH* 2026) [J]. Real gaps are task-specific: heart-failure prediction in Black patients aged 0–40, worst in young Black women (Kaur 2024) [J], and ACS sensitivity (Ji 2026) [J].
- **"Western AI misreads healthy Indian ECGs."** The best evidence says no: Macfarlane et al., *J Electrocardiol* 2015 (963 healthy Indians): "diagnostic criteria currently used for Caucasians need not be altered for use in South Asians" [J].
- **"What tells the AI the location" may be the machine, not the person.** Cross-country datasets differ in carts, filters, sampling and label software. Even within one hospital, the device mix can differ by group (Lotter 2024 [J] is the imaging precedent).
- **"Remove it with LEACE and the bias goes."** Four reasons this fails as an assumption:
  1. LEACE is only *linearly* guaranteed. In CXR foundation models, race stayed decodable at AUROC 0.91 after linear removal, and the race gap tracked disease base rates instead (*Diagnostics* 2026) [J-low].
  2. Erasing demographics can **create** bias when groups truly differ (Parikh…Feragen, Sept 2026 [P]). Race adjustment can **improve** equity (Zink, Obermeyer, Pierson, *PNAS* 2024) [J].
  3. Shortcut removal gave only "locally optimal" fairness that did not transfer (Yang…Ghassemi, *Nat Med* 2024) [J].
  4. In ECG, the population signal sits in the same waveform parts that diagnose disease (QRS voltage; Bollepalli 2025) [J]. Genetic African ancestry, not self-reported race, tracks both higher voltage **and** real concentric LV remodelling on MRI (Dallas Heart Study, *JAMA Cardiology* 2019) [J]. "Correcting away" voltage could therefore hide real disease, the same trap as race-corrected eGFR.
- **The 3-case logic is incomplete.** Two missing cases are documented in ECG:
  - *(4) The answer key is biased.* Race-blind LVH voltage criteria have lower specificity in Black patients versus echo (*JAMA* 1992; LIFE, *Am J Hypertens* 2002) [J], and ECGFounder was trained on labels assisted by the race-blind 12SL program.
  - *(5) Using population information is correct.*

  **Analogy:** you are trying to work out whether a thermometer is biased by pointing it at people and comparing it with *another thermometer built the same way*. You need a reference that isn't a thermometer. Here that is the echocardiogram (true wall thickness) or the outcome (death).

## 4. Am I changing your architecture? Exactly this, and no more
| Part of your design | Status | Reason |
|---|---|---|
| Find what tells the AI the population (probes, SAE features, decode to leads) | **Kept** | Becomes the mechanism arm, now also asking how much is anatomy, body size, machine, or social context |
| Surgically remove it (LEACE) | **Kept and strengthened** | + non-linear erasure + targeted SAE-feature ablation + erasure-strength curves + planted-signal validation |
| "Then see what makes it give the result" | **Kept, re-measured** | Measured against **echo/outcome truth**, per group |
| Three cases | **Kept, completed to five** | Adds "biased answer key" and "correct use of population information" |
| Generalize across models | **Kept** | ≥6 open ECG FMs, ≥3 US health systems, race + sex |
| 5-way Shapley decomposition as headline | **Demoted** | Method exists (ICML 2023); keep only as a supporting analysis if useful |
| UK Biobank South Asians | **Dropped from critical path** | Too few with ECGs |
| "Location" | **Kept as a control arm** | Tests whether cross-country "population" signal is really the machine |
| **New: answer-key swap** | **Added** | Same models trained on race-blind rules vs race-aware rules vs echo truth vs outcomes, so we can test whether *the labels decide* |

**Example of the core test.** Take two patients whose echocardiograms show the same 1.4 cm wall thickness (true LVH), one Black and one White.
- *Before removal:* the AI gives them LVH scores 0.80 and 0.62.
- *After removing the population information:* if the scores converge and both move *toward the echo truth*, the AI was using race as a harmful shortcut (your case 2).
- *If removal pushes them apart from the truth:* the AI was quietly doing a *helpful* physiological correction that race-blind rules lack (case 5).

Run this over thousands of truth-matched patients, many models and many tasks, and you get the answer.

## 5. The gap, stated exactly
Nobody has determined, **against independent physiological truth**, whether ECG-AI's use of the population information it reads from the waveform **helps or harms** patients, or **what decides the direction**. Evidence that the gap is real, open and contested:
- *JAMIA* 33(4):922 (Apr 2026; Abdalla, James, **D.S. Jones**, Abdalla) [J]: removing race from inputs does not remove race correction (proxies); it presents scenarios only, with **no experiments**.
- The literature openly contradicts itself: *less* demographic encoding is better (Yang, *Nat Med* 2024 [J]) **vs** removing it creates bias (Parikh…Feragen 2026 [P]; Zink…Pierson, *PNAS* 2024 [J]).
- The science of the signal is contested: "non-genetic" (Bollepalli, *npj Cardiovasc Health* 2025 [J]) **vs** genetic ancestry tracks voltage and LV geometry (Dallas Heart, *JAMA Cardiol* 2019 [J]).
- The standards disagree: 12SL race-blind; Glasgow race-specific; AHA 2009 says adjust for race when validated; the rest of medicine is removing race (CKD-EPI 2021; race-neutral GLI-Global spirometry, ATS 2023) [J].
- Behavioural audits are saturated and reassuring on AUROC (EchoNext 0.84–0.86 by race). That is precisely why the remaining question is *how* parity or disparity arises and whether the AI's population use is right.

## 6. How we fill it, and why it fits *Nature* (full design → `F_flagship_design_and_stress_test.md`)
**Experiments:**

| | What it does |
|---|---|
| E0 | Validate the audit on PTB-XL/CODE "model organisms" with planted helpful/harmful/irrelevant group signals |
| E1 | Truth-referenced errors per group |
| E2 | What tells the AI the population (your step 1) |
| E3 | Surgical removal judged against truth (your step 2) |
| E4 | Answer-key swap |
| E5 | "Collision" rule: population use should matter where population and disease share waveform features (LVH), not where they don't (pump function). This would explain why LVSD models transport and LVH/HF-type tasks don't |
| E6 | Location = machine? control |
| E7 | Truth-anchored, race-neutral remedy (conditional on results) |
| optional | AI-ECG age with mortality as truth |

**Your professor's four criteria** (you remembered three; the fourth is "a completely new framework"):

| Criterion | What delivers it | Holds regardless of outcome? |
|---|---|---|
| (a) New principle | "The ground truth, not the architecture, decides whether medical AI's population use heals or harms" + where it happens | Conditional on results |
| (b) New framework | Truth-anchored erase-and-measure audit, validated on planted signals, applicable to any physiology-based AI | Yes |
| (c) Discovery | First causal answer to "does cardiac AI silently race-correct, and is it right?" | Yes; any sign is new |
| (d) Changes direction | From AUROC-by-group audits and "remove demographics" defaults to truth-anchored audits and label repair | Strong only if results are clear |

**Registered Report route.** *Nature* opened Registered Reports to all fields on 27 May 2026, including studies that compare methods. Secondary analyses are allowed only if the authors **have had no prior access to the data** (or under a bias-control level system). Pilots on PTB-XL/CODE-15 are fine. Running the confirmatory analyses on EchoNext / MIMIC-clinical / HEEDB first would weaken that route.

**Honest odds, once.** *Nature* takes ~8% of submissions, and editors judge broad interest before review. No design guarantees acceptance. This one is shaped like the papers *Nature* did take in this space:
- a hidden phenomenon revealed by a decisive test (Hofmann 2024, covert dialect racism) [J];
- ground truth from outside the human labels (Obermeyer…Mullainathan 2026, ECG sudden-death biomarker) [J].

If *Nature* declines, the same paper suits *Nature Medicine*, *Nature Cardiovascular Research* or *Nature Machine Intelligence* unchanged.

## 7. What could kill it
- **Being scooped.** Groups closest to this question: Columbia/EchoNext (Elias), MGH (Armoundas/Singh), MIT (Ghassemi), Imperial (Glocker), Copenhagen (Feragen/Petersen). Nothing equivalent found as of 6 Oct 2026.
- **Imperfect truth.** Answered with continuous echo measures, several truths, and outcomes.
- **Incomplete erasure.** Answered with linear + non-linear + feature ablation and planted validation.
- **US-only data.** The claim is a mechanism, with sex as a second attribute and a cross-country control.
- **Team credibility for *Nature* editors.** Engineering students without a cardiologist co-author is a visible weakness for a clinical claim.

## 8. Alternatives I tested and rejected
| Alternative | Why rejected |
|---|---|
| Migration natural experiment (environment vs ancestry) | UK Biobank South Asian ECG numbers too small; paid |
| Global "normal ECG" charts | Confounded by devices/labels; national normal-limit studies exist |
| Scaling laws of ECG-FM fairness | Low novelty; systematic scaling studies exist [P] |
| AI reading socioeconomic status from the ECG | Not found, but ethically fraught and weak as a headline |
| Your original pipeline as headline | Scooped and logically unidentifiable (§2–3) |
| **Kept as optional:** AI-ECG biological age by race ("bias or weathering?") with mortality as truth | — |

## 9. Factual errors to fix in your current drafts (IEEE `sofarresearch.rtf`, whitepaper)
1. "Guesses race correctly 86 percent of the time" → **AUC ≈ 0.86** (a ranking measure, not accuracy).
2. "MIMIC-IV-ECG … race recorded for every patient … no new data-sharing agreement needed" → race is only in the **credentialed** MIMIC-IV clinical database; UK Biobank requires a **paid application**.
3. "2,782 South Asians" with ECG → not supported; South Asians are <1% of the UKB imaging/ECG cohort.
4. "No published study has run that [cross-ethnic] test" (DA-GAT-v2 quote) → false; see N6.
5. "Splits at most two causes" (Table I) → Zhang et al. ICML 2023 splits any number.
6. Citing MIT News for the transfer result → cite **Yang et al., *Nature Medicine* 30:2838 (2024)**.
7. "Every existing fix needs retraining" → group thresholds (Kaur 2024) and post-hoc methods exist.
8. ECGFounder "80 conditions" (IEEE) vs "150 labels" (White Sheet) → ECGFounder has 150 label categories; AUROC >0.95 is reported for 80 of them. Say which you mean.

## 10. Limits of this audit (read before citing anything)
- Full-text pages (arxiv.org, nature.com and similar) were **blocked** in this environment. Verification used search-engine abstracts and summaries, roughly 100 searches. Numbers marked "check" in the notes should be confirmed from the full text before going into a manuscript.
- "Not found" means not found in these searches as of 6 Oct 2026, not proof of absence. The field (ECG FMs, mechanistic interpretability, fairness) is producing new preprints monthly.
- Exact AUC values for Kaur 2024 subgroups were not visible in the abstracts.

---

## Key references (verified to exist, with links)
- Zhang et al., ICML 2023 — https://proceedings.mlr.press/v202/zhang23ai.html
- Jones…Glocker, *Nat Mach Intell* 2024 — https://www.nature.com/articles/s42256-024-00797-8
- Yang…Ghassemi, *Nat Med* 2024 — https://www.nature.com/articles/s41591-024-03113-4
- Bollepalli…Armoundas, *npj Cardiovasc Health* 2025 — https://www.nature.com/articles/s44325-025-00087-1
- Dallas Heart Study, *JAMA Cardiol* 2019 — https://jamanetwork.com/journals/jamacardiology/fullarticle/2713961
- Kaur et al., *Circ Heart Fail* 2024 — https://www.ahajournals.org/doi/10.1161/CIRCHEARTFAILURE.123.010879
- Noseworthy et al., *Circ Arrhythm Electrophysiol* 2020 — https://www.ahajournals.org/journal/doi/10.1161/CIRCEP.119.007988
- Adedinsewo et al., *Nat Med* 2024 — https://www.nature.com/articles/s41591-024-03243-9
- Hsieh et al., *EHJ – Digital Health* 2026 — https://academic.oup.com/ehjdh/article/7/5/ztag080/8699000
- Ji et al., *IEEE TBME* 2026 — https://doi.org/10.1109/TBME.2025.3597527
- "Why blinding sex is not enough", *J Electrocardiol* 2026 — https://www.sciencedirect.com/science/article/pii/S002207362600275X
- Abdalla, James, Jones, Abdalla, *JAMIA* 2026 — https://academic.oup.com/jamia/article/33/4/922/8466364
- Zink, Obermeyer, Pierson, *PNAS* 2024 — https://www.pnas.org/doi/10.1073/pnas.2402267121
- Lotter, *Nat Commun* 2024 — https://www.nature.com/articles/s41467-024-52003-3
- Kim et al., *Diagnostics* 2026 — https://pmc.ncbi.nlm.nih.gov/articles/PMC13359982/
- Macfarlane et al., *J Electrocardiol* 2015 (Indians) — https://pubmed.ncbi.nlm.nih.gov/25990450/
- Lee et al., *JAMA* 1992 (LVH criteria by race) — https://jamanetwork.com/journals/jama/article-abstract/398053
- EchoNext, *Nature* 2025 — https://www.nature.com/articles/s41586-025-09227-0 ; dataset — https://physionet.org/content/echonext/1.1.0/
- Obermeyer…Mullainathan, *Nature* 2026 (SCD biomarker) — https://www.nature.com/articles/s41586-026-10674-6
- Hofmann et al., *Nature* 2024 — https://www.nature.com/articles/s41586-024-07856-5
- Knolle & Kaissis, *Nature* 2026 (privacy) — https://www.nature.com/articles/s41586-026-10688-0
- *Nature* Registered Reports, all fields (27 May 2026) — https://www.nature.com/articles/d41586-026-01629-y ; policy — https://www.nature.com/nature/for-authors/registered-reports
- HEEDB, *Sci Data* 2026 — https://www.nature.com/articles/s41597-026-06861-9
- MIMIC-IV-ECG — https://physionet.org/content/mimic-iv-ecg/1.0/
- Preprints (reported, not proof): ECG-InterpBench arXiv 2607.27404; CADENCE arXiv 2607.25244; Parikh et al. arXiv 2609.32004; Kina & Petersen arXiv 2609.07922; EEG Identity Trap arXiv 2606.06647; Shan & Mueller arXiv 2512.20796.

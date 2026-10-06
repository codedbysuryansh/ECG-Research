# Step R1 — Independent re-verification of every claim (round 2, 6 Oct 2026)

**Method.** Every claim in `FINAL_REPORT.md` was re-checked with *new* search wording (not the round-1 queries), looking for evidence **against** my own conclusions. Official code repositories were read directly (raw GitHub is reachable in this environment). Journal sites, PhysioNet, Zenodo, HuggingFace and arXiv full texts remained blocked, so paper-level facts rest on abstracts and summaries from ≥2 independent searches. **[VERIFY-FULLTEXT]** marks numbers to confirm from the PDF before citing.

Legend: ✅ confirmed · 🔧 confirmed with a correction · ⚠️ partially supported / softer than stated · ❌ wrong (withdrawn)

## 1. Corrections to my own round-1 report (bias check)
| # | What I said in round 1 | What re-verification shows | Status |
|---|---|---|---|
| 1 | "Your design has no ground truth independent of how ECGs are read." | **Unfair.** Your Technical Companion (Part A.7) explicitly prefers "independently confirmed Y (UK Biobank CMR-confirmed subsets; adjudicated MIMIC labels)" for the benign-vs-pathology split. The real gap is narrower: the **LEACE/patching test (Stages 2–3) is judged by ΔEFPR on ECG-flagged "benign-variant" cases**, and the IEEE draft drops gold-standard truth entirely. The change I recommend (truth as the yardstick for the erase step) is still needed, but it extends an idea you already had. | 🔧 |
| 2 | Dallas Heart Study = *JAMA Cardiology* **2019** | *JAMA Cardiol* **2018**;3(12):1167–1173 (PubMed 30427995); 1,251 Black + 826 White adults, 18–65 y, with ECG, CMR, DEXA; ADMIXTURE on exome-chip markers. With ancestry and race in the same model, **ancestry (not race)** was associated with ECG voltage and concentric LVH phenotypes; also within Black participants. | 🔧 (year) |
| 3 | SPEC-AI Nigeria: 12-lead AUC **0.919** for LVEF ≤35%, sensitivity 41.2% | A second search reports AUC **0.928 (0.875–0.981) for LVEF <50%**. Both can be true at different thresholds; the low sensitivity at ≤35% suggests the **operating threshold** did not transfer as well as discrimination did. [VERIFY-FULLTEXT] | 🔧 + nuance |
| 4 | "Region bias is not established" | **Softer.** True for *discrimination* (AUROC) on structural tasks. But there are signs that **calibration/operating points** shift across groups (Nigeria sensitivity; Kaur 2024 calibration: observed HF exceeded predicted risk in Black patients = underdiagnosis; sex-MI paper fixed by equalising specificity). Fair statement: *discrimination often transports; calibration and thresholds often do not; a few tasks show real discrimination gaps.* Your premise is therefore **partly right**, in the calibration sense. | ⚠️ (I was too strong) |
| 5 | JAMIA 2026 perspective has "no experiments" | It "analyzes 4 standardized scenarios" (race-corrected variables, explicit race, proxy inference, race-specific models). I could not confirm whether these include simulations. It certainly does **not** test a real clinical AI against ground truth. Reword to: "scenario analysis; no test of a deployed clinical model against ground truth." | 🔧 (wording) |
| 6 | "AHA 2009 says ECG criteria should be adjusted for race when validated" | Supported only by a search snippet attributed to the AHA/ACCF/HRS recommendations; Part V (Hancock et al., *Circulation* 2009;119:e251) confirmed to exist. Exact wording not seen. [VERIFY-FULLTEXT] | ⚠️ |
| 7 | GE 12SL is race-blind | The 12SL Physician's Guide describes **age- and sex-specific** criteria and no race-specific criteria. That is evidence of absence in the guide, not a vendor statement. Safer wording: "12SL documentation describes age- and sex-specific, not race-specific, criteria." | ⚠️ (wording) |
| 8 | Kaur 2024 details "not visible" | Now found: 325,000+ ECGs (Stanford); 56.5% NH White, 14.2% Asian, 12.3% Hispanic, **4% Black**; AUC **0.69 (0.62–0.77) in Black women aged 0–40**; AUC fell from 0.80 (≤40 y) to 0.66 (>80 y); **observed HF > predicted in Black patients (underdiagnosis)**. | ✅ (+ detail) |
| 9 | Echo-"false positives" = AI errors | **New caveat.** In an LVH ML study (*Heart Rhythm O2*, Mar 2026), echo-negative ECG "false positives" had **3.07× odds (2.44–3.86)** of developing LVH on echo >1 year later. A cross-sectional echo "truth" can mislabel early disease, so group differences in "false positives" may partly be **earlier detection**, not bias. **Design change:** add follow-up truth (later echo, outcomes) to adjudicate "false positives" by group. | 🆕 design change |

## 2. Claims re-confirmed (no change)
| Claim | Status | Re-verification note |
|---|---|---|
| ECG-InterpBench (SAEs on 6 ECG FMs, Jul 2026) [P] | ✅ | Two searches; no demographics in summaries |
| CADENCE (SAE "cardiac atoms", causal ablation) [P] | ✅ | |
| Zhang et al., ICML 2023 (Shapley over distribution shifts) | ✅ | PMLR 202:41550–41578 |
| DISDE, *Operations Research* (online 18 Dec 2025) | ✅ | |
| Jones…Glocker, *Nat Mach Intell* 2024 (prevalence/presentation/annotation) | ✅ | |
| LEACE on EEG FM embeddings ("Identity Trap", Jun 2026) [P] | ✅ | |
| Noseworthy 2020 (consistent low-EF performance across race) | ✅ | |
| Hsieh 2026 (*EHJ-DH*; White-only training still AUROC ≥0.85–0.90 all groups) | ✅ | |
| Ji 2026 (*IEEE TBME*; ACS sensitivity gap 9.8%→1.3%) | ✅ | |
| *J Electrocardiol* 2026 sex/MI paper | ✅ | |
| Macfarlane 2015 India normal limits (963 adults; Caucasian criteria applicable) | ✅ | *J Electrocardiol* 48(4):652–668; ECGs analysed by the Glasgow program |
| Bollepalli 2025 (AUC 86.17; lowest-income-decile AUC 80.46; sex ≈86) | ✅ | |
| Kim 2026 *Diagnostics* (encoded 0.83, linear use 0.0015, MLP 0.91 after residualisation) | ✅ | MDPI → supporting only |
| Parikh…Feragen 2026 (invariance can create bias) [P] | ✅ | |
| Zink, Obermeyer, Pierson, *PNAS* 2024 | ✅ | |
| Yang…Ghassemi, *Nat Med* 2024 | ✅ | |
| Lotter, *Nat Commun* 2024 | ✅ | |
| *Nature* Registered Reports open to all fields (27 May 2026) | ✅ | |
| *Nature* RR secondary-data rule: "no prior access" (self-certification or gatekeeper letter) | ✅ | Seen on Nature's own RR policy page in search results; **systematic reviews and meta-analyses are not considered** as RRs |
| EchoNext race AUROC 0.84–0.86; composition 31% Hispanic / 29.2% White / 16.5% Black / 3.4% Asian | ✅ | |
| HEEDB race (Emory 31.6% Black) + death dates | ✅ | |
| ECG-FM pretrained on MIMIC-IV-ECG + PhysioNet 2021 (CPSC, CPSC-Extra, PTB-XL, Georgia, Ningbo, Chapman) | ✅ | Official README |
| Glasgow program accepts race; race-specific normal limits | ✅ | |
| LVH voltage criteria less specific in Black patients vs echo (*JAMA* 1992; LIFE 2002) | ✅ | |

## 3. Fresh scoop searches for the recommended direction (new wording)
Queries (examples): "deep learning ECG LVH Black patients false positives echocardiography 2025 2026"; "ECG foundation model race linear probe erase debias echocardiography outcome 2026"; "EchoNext fairness race calibration foundation models"; "implicit / covert / hidden race correction deep learning". **Result: nothing found that measures how ECG-AI uses population information against independent truth, or swaps label sources to test it.** Closest: subgroup AUROC/fairness reports (DeepECG-SSL: age/sex only; EchoNext; Hsieh 2026; ECGFounder subgroup tables) and the *Heart Rhythm O2* LVH paper (no race analysis seen).

## 4. Independent re-assessment of the recommendation (devil's advocate)
- **Is "race in US datasets" really "global"?** A fair criticism your professor may raise. Mitigation already in the design: a mechanism-level claim; sex as a second attribute; a cross-country location arm (Germany, Brazil, China, US). **Additional option:** treat *country of acquisition* as a second population descriptor in the cross-country arm, with the device confound explicitly modelled. This should not be oversold.
- **Is the predicted result interesting whichever way it falls?** Yes, if the truth anchor is credible. That is why item 9 above (follow-up truth) matters.
- **Is *Nature* realistic?** Unchanged: possible but low-probability; the Registered Report route is the best lever. Nothing found in round 2 changes this.
- **Is there a simpler direction with better odds?** I re-checked the alternatives (AI-ECG age/weathering; migration; global norms; scaling laws). None became more attractive. AI-ECG age remains a good optional extension because HEEDB and MIMIC have death dates.

## 5. Net effect on the plan
1. Keep the recommended flagship (truth-anchored erase-and-measure + answer-key swap + collision + location control).
2. **Add** follow-up truth (later echo; mortality) to adjudicate "false positives" by group.
3. **Reword** the premise to "discrimination transports; calibration and thresholds often don't; some tasks show gaps".
4. **Reword** the GE 12SL, AHA 2009 and JAMIA statements as above.
5. Fix the Dallas Heart year to 2018.

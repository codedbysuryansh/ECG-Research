# Step A — Your original architecture, restated exactly

Sources: White Sheet v2 (PDF, 3 Jul 2026), Execution Playbook (`4ef6...1here (1).md`), Technical Companion + Pre-registration (`3a7e...md`), Whitepaper (`889d...html`, 6 Jul 2026), IEEE draft (`sofarresearch.rtf`), your chat summary (6 Oct 2026).

## A.1 The question (in your words)
ECG reports/recordings carry no "region" or "race" label, yet ECG-AI models trained mostly on Western data make more errors on other populations. The AI must therefore be picking up the person's population/"localization" from the signal itself. Three possibilities:
1. **Perception failure:** the AI cannot read these ECGs properly (the problem is too complex / unfamiliar morphology).
2. **Shortcut / bias:** the AI reads the ECG correctly but a population-linked signal biases the answer (the "Prevalence-Proxy Shortcut": population features → wrong disease prior → false positives).
3. **No bias:** the AI is right and has no bias.

If (2): find what tells the AI the population, **surgically remove it with LEACE at inference time** (weights frozen, no new labels), and check what then drives the result, i.e., correct the bias.

## A.2 The four-stage pipeline (as written in the IEEE draft and Playbook)
| Stage | What | Tools |
|---|---|---|
| 0 Setup | Evaluation harness, benign-variant test set (early repolarization, high voltage, anterior TWI), equity gap ΔFPR/ΔFNR, ECE, Equalized Odds | ECG-FM, ECGFounder |
| 1 Decompose | Split the cross-population gap into 5 causes: prevalence P(Y\|E), acquisition P(A\|E), benign morphology, true pathology manifestation, labeling P(L\|Y,E); Shapley over 2⁵=32 switch combinations | label-shift reweighting, ECG-Image-Kit, matched strata, SSSD-ECG/DiffuSETS counterfactuals, harmonised labels, SGShift |
| 2 Localize | SAE on ECG-FM/ECGFounder activations; linear + non-linear probes for ancestry; activation patching (swap ancestry features between patients) → mediated proportion; decode features to leads/segments; negative controls | TopK SAE (EEG template 2605.13930), Sparse Feature Circuits |
| 3 Sever | LEACE erasure of the population direction at inference, estimated from unlabeled target ECGs; physiological-preservation constraint; escalate to RLACE/TaCo/SAE-ablation/LoRA; baselines: fine-tune, adversarial, reweighting, FiLM | concept-erasure library |
| 4 Generalize | Repeat on ≥3 encoders (ECG-FM, ECGFounder, CSFM) and ≥2 ancestry axes (MIMIC race; UK Biobank incl. 2,782 South Asians) | — |

Data design: **within-site ancestry comparison** (MIMIC-IV-ECG + MIMIC-IV race; UK Biobank ancestry) so acquisition is held constant; across-site cohorts (PTB-XL, CODE-15, Chapman/SPH, Georgia) to estimate acquisition.

Pre-registered hypotheses H1–H5 and a branch map (1A–1D, 2A–2D, 3A–3D, 4) in which every outcome is "publishable".

## A.3 Every novelty claim your documents make (to be checked one by one in Step C)
| # | Claim (as written) | Where |
|---|---|---|
| N1 | "Nobody has broken down what the cross-population gap is made of"; the 5-way split "has never been done at all, anywhere, for any medical signal"; prior work splits at most two causes | Whitepaper Gap 1; IEEE Contribution 1, Table I |
| N2 | "No one has looked inside an ECG model"; SAEs "never pointed at an ECG foundation model" | Whitepaper Gap 2; IEEE Contribution 2 |
| N3 | "No inference-time, label-free correction has been attempted on any medical time-series model"; LEACE "never applied to a medical time-series encoder"; "every fix that exists today needs retraining" | Whitepaper Gap 3; IEEE Contribution 3 |
| N4 | "Nobody knows if ECG failure is the same kind of problem as imaging failure"; the shortcut-vs-perception question "has never been tested" | Whitepaper Gap 4; IEEE Contribution 4 |
| N5 | "No existing ECG study can tell ancestry apart from acquisition"; the within-hospital multi-ancestry design "has never been used for this question" | Whitepaper Gap 5 |
| N6 | "Cross-ethnic ECG fairness has not been tested, and the field says so in print" (DA-GAT-v2 quote; "No published study has run that test yet") | Whitepaper 1.2; IEEE intro |
| N7 | A South Asian test cohort from UK Biobank (2,782 people) fills a Global-South gap | IEEE Contribution 5 |
| N8 | The pre-registered "every outcome is publishable" design for a bias question in physiological AI "is not something we found anywhere else" | Whitepaper §4 |

## A.4 Factual statements in your drafts that must also be checked
- "Bollepalli … guesses race correctly 86 percent of the time" (IEEE draft, whitepaper).
- "These models perform worse on patients from other populations, and this drop correlates more strongly with … demographic data than … disease severity" (IEEE abstract/intro).
- "MIMIC-IV-ECG … race and ethnicity recorded for every patient" and "Both datasets are open to researchers already. No new data-sharing agreement is needed" (IEEE Data section).
- "UK Biobank … 2,782 people of mostly South Asian background" with ECG (overlap unconfirmed in your own draft).
- "ECGFounder … 80 separate heart conditions" (IEEE) vs "150 labels" (White Sheet).
- "A 2024 finding, reported through Nature Medicine and MIT News … fixing shortcuts at one hospital did not carry over" (cited via MIT News).

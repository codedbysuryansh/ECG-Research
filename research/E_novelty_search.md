# Step E — Deep novelty search: what is genuinely open, and what is Nature-sized?

Legend: [J] peer-reviewed reputed · [J-low] lower tier · [P] preprint. Verified via search abstracts/summaries.

## E.0 Prior art that kills or narrows *conceptual* novelty (must be cited)
| Paper | What it already establishes | Effect on us |
|---|---|---|
| Jones, Castro, De Sousa Ribeiro, Oktay, McCradden, **Glocker**, "A causal perspective on dataset bias in machine learning for medical imaging", ***Nature Machine Intelligence* 2024** (doi 10.1038/s42256-024-00797-8) [J] | Three causal families of dataset bias — **prevalence, presentation, annotation disparities** — "may seem indistinguishable yet require substantially different mitigation strategies"; a 3-step reasoning framework | Your 5-cause taxonomy and 3-case logic are, conceptually, this paper (+ acquisition). Novelty must be **empirical identification with ground truth in a physiological signal**, not the taxonomy. |
| Bernhardt, Jones, Glocker, "Potential sources of dataset bias complicate investigation of underdiagnosis by machine learning algorithms", ***Nature Medicine* 2022** (s41591-022-01846-8) [J] | Label/annotation bias can create or hide apparent underdiagnosis | Supports "biased answer key" case; precedent that label source matters |
| Obermeyer et al., *Science* 2019 [J]; Pierson et al., *Nat Med* 2021 [J] | Label choice drives algorithmic racial bias; better ground truth explains disparities | "The label decides" is an established principle (non-signal domains) |
| Zink, Obermeyer, Pierson, *PNAS* 2024 [J] | Race adjustment can *improve* accuracy and equity when data quality differs by group | Race-aware can be good → erasure is not automatically a fix |
| Yang et al., *Nat Med* 2024 [J] vs Parikh/Petersen et al. 2026 [P] | Less demographic encoding → better OOD fairness (Yang) **vs** enforcing invariance can create new bias (Parikh) | **Open, live contradiction** in the literature. Adjudicating it with ground truth in a domain where group differences are partly real (ECG voltage) is a genuine contribution. |
| JAMIA 2026 perspective (Abdalla, James, Jones, Abdalla) [J] | Removing race from inputs ≠ removing race correction (proxies); **no experiments** | Empirical test still open for clinical AI with truth |
| Dwork et al. 2012; Kusner et al. 2017 [J] | "Fairness through unawareness" fails via proxies | Concept is old; do not claim it |

## E.1 New scientific tension found in this session (not in previous ledger)
**Is the race signal in the ECG biology, environment, or artifact?** Reputed sources disagree:
- Bollepalli et al., *npj Cardiovasc Health* 2025 [J]: signal emerges after birth (AUC 0.59 → 0.84 by 18 y), varies with income → "non-genetic".
- **Dallas Heart Study**, *JAMA Cardiology* 2019 (PubMed 30427995) [J]: in 2,077 Black/White adults, **genetically inferred African ancestry — not self-reported race — was associated with higher ECG voltage AND CMR-measured concentric LV remodelling**.
- BioVU, *BioData Mining* 2015 [J]: European ancestry proportion ↔ longer QRS in African Americans.
- Clinical: obesity markedly attenuates ECG-LVH criteria validity in Black Africans; BMI-corrected voltage improves detection (*Clin Cardiol* 2016; PubMed 23169235, 27279262) [J]; MESA ECG-LVH index best in African Americans vs CMR [J].
**Implication:** the "benign variant" story in your drafts is **not settled**. Part of the higher voltage may reflect *real* concentric remodelling (not benign), part body habitus, part environment. A model that "corrects away" voltage by race could **hide real disease** — the same trap as race-corrected eGFR. This is exactly why removing the population signal cannot be judged without ground truth.

## E.2 Template from medicine: the pulse-oximeter precedent
Sjoding et al., *NEJM* 2020 (occult hypoxemia: pulse oximetry overestimates SpO₂ in Black patients **versus arterial blood gas truth**) [J] → changed FDA guidance. The decisive design was **device reading vs independent physiological truth, by race**. The ECG-AI analogue (AI reading vs echo/CMR/outcome truth, by population, with a causal test of *why*) has not been done (searched: "AI-ECG LVH race echo", "truth-anchored race audit ECG", "covert race correction ECG"). Closest: race-stratified AUROCs on EchoNext in 2026 preprints (2603.28532, 2603.02616) — behavioural only, AUROC only.

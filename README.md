# HEDGEQUEST — Benchmarking Hedge–Truth Calibration in Large Language Models

---

## 1. Problem Overview

**Observation.** Large language models (LLMs) often _sound_ confident even when they are factually wrong (over-confident hallucinations).  
**Gap.** No public benchmark (1) **couples factual correctness with surface hedging language** and (2) offers an **automatic metric** over a substantial, answerability-balanced question set.

**Impact**

* Safer deployments — dashboards can flag over-confident hallucinations.  
* Research — prompt engineers and alignment teams need a quick, reproducible score for “linguistic uncertainty calibration.”  
* Novel contribution — ACL/NAACL SRW papers reward compact datasets + new metrics.

---

## 2. Exact Problem Statement

> **Given a question *q* and a model answer *a*, quantify whether the model’s _expressed linguistic uncertainty_ (hedges, modals, caveats) aligns with its _factual correctness_.**  
> A well-calibrated model should hedge strongly when wrong (or when the answer is unknown) and speak confidently only when right.

---

## 3. Novelty & Contribution

| Contribution | What Exists | What We Add |
|--------------|-------------|-------------|
| **HEDGEQUEST v1 Dataset** (~800 items, balanced answerable/unanswerable) | ≤ 100 synthetic prompts, no answerability labels | **First public open-domain dataset** with explicit answerability tags + reference answers |
| **HedgeCal Metric** \(|H – (1 – T)|\) | Qualitative or log-prob studies only | **First automatic scalar** combining hedge strength (H) and truth (T) |
| **Open Hedge Detector** (lexicon + XLM-R) | Proprietary / manual counts | Open weights & code for conversational English |
| **Baseline Leaderboard** (open-weights) | Analyses stop at GPT-3.5/4 | Llama-2-Chat-7B, Mistral-7B, Mixtral-8×7B, Phi-2, GPT-J + GPT-4o ref |
| **Error Taxonomy** | Hallucination boards ignore wording | Fine-grained categories: over-confidence, under-confidence, proper hedge |

---

## 4. Related Work

| Area | Key Papers & Gaps |
|------|------------------|
| **Factuality** | OpenAI hallucination eval, HF Hallucination Leaderboard – truth only, no hedging |
| **Log-prob Calibration** | Desai & Durrett 2020; Jiang et al. 2023 – probability vs. accuracy, ignore wording |
| **Hedge Interpretation / Control** | Levy et al. 2024 – toggle hedge words, no calibration link |
| **Verbal Uncertainty Observations** | Zhong et al. 2024 – tiny synthetic set, no public metric |
| **Answerability Benchmarks** | SQuAD 2.0, Natural Questions – ignore hedging facet |

---

## 5. Methodology

### 5.1 Dataset Construction

| Step | Target | Procedure |
|------|--------|-----------|
| Answerable Qs | **500** | Natural Questions, TriviaQA short answers |
| Unanswerable Qs | **300** | SQuAD 2.0 “no-answer”, TruthfulQA ambiguous |
| Metadata | **800** | `label = {known, unknown}`, `gold_answer` |
| Gold QC | 50 random | Manual sanity check, fix/drop errors |

### 5.2 Hedge Detection

1. **Lexicon + POS** baseline (`H_lex`).  
2. **XLM-R-Base** fine-tuned on FactBank hedge labels → `H_BERT` (F1 ≥ 0.85).

### 5.3 HedgeCal Metric

\[
\text{HedgeCal}(a,q) \;=\; \bigl|\, H(a)\;-\;\bigl(1 - T(a,q)\bigr)\bigr|
\]

* **H(a)** ∈ [0, 1] — hedge score from detector.  
* **T(a,q)** ∈ {1 (correct), 0.5 (partial), 0} — exact-match truth score.  
* Lower HedgeCal → better hedge-truth calibration.

### 5.4 Models Evaluated

| Model | Params | Source |
|-------|--------|--------|
| Llama-2-Chat-7B | 7 B | Meta |
| Mistral-7B-Instruct | 7 B | Mistral AI |
| Mixtral-8×7B | 46 B | Mistral AI |
| Phi-2 | 2.7 B | Microsoft |
| GPT-J | 6 B | Eleuther |
| GPT-4o (reference) | API | OpenAI |

Prompt:

Q: {question}
A:
`temperature = 0.7`, `top_p = 0.9`, `max_new_tokens = 128`.

### 5.5 Human Validation

* 100 sample Q-A pairs (stratified).  
* Annotators mark **truth** (correct / partial / wrong) and **tone** (Sure / Hedged / Doubtful).  
* Report Spearman ρ between HedgeCal and human calibration scores.

### 5.6 Error Typing

| Code | Description |
|------|-------------|
| **OC-H** | Over-confident hallucination (`T = 0`, `H < 0.2`) |
| **UC-T** | Under-confident truth (`T = 1`, `H > 0.8`) |
| **AH**   | Appropriate hedge (`T ≈ 0`, `H ≈ 1`) |

---

## 6. Deliverables

| # | Item | Format |
|---|------|--------|
| 1 | HEDGEQUEST v1 | JSONL (HF Datasets) |
| 2 | HedgeCal scorer + weights | GitHub |
| 3 | Baseline leaderboard | CSV / Markdown |
| 4 | NAACL-SRW paper | ACL template |
| 5 | Reproducibility + ethics checklist | GitHub |

---

## 7. Theoretical Foundations

* **Pragmatics of Hedging** (Hyland 2005): hedges signal epistemic uncertainty.  
* **Calibration Theory:** ideal forecast probability `p` should equal empirical accuracy; hedge score `H` serves as a linguistic proxy for `1 − p`.  
* **Metric Logic:** perfect calibration ⇒ `H ≈ 1 − T`, so absolute error → 0.  
* **Detection:** XLM-R regression learns latent hedge strength from embeddings.

---

## 8. Timeline (June → December 2025)

| Month | Weeks | Milestones |
|-------|-------|------------|
| **June** | 1-2 | Lit. review, finalise schema |
| | 3-5 | Gather 500 answerable + 300 unanswerable Qs |
| **July** | 6-7 | Manual QC (50 Qs), freeze dataset v0.1 |
| | 8-10 | Lexicon hedge detector |
| **August** | 11-12 | Fine-tune XLM-R hedge model (F1 ≥ 0.85) |
| | 13 | Implement HedgeCal tests |
| **September** | 14-15 | Run open-source models, store outputs |
| | 16 | Human validation (100 samples) |
| **October** | 17-18 | Compute HedgeCal + human ρ |
| | 19 | Error taxonomy + examples |
| **November** | 20 | Plots, leaderboard |
| | 21-22 | Draft paper, advisor feedback |
| **December** | 23 | Release code/data, final polish |
| | 24 | Format ACL template |
| | 25 | **Submit to NAACL-SRW** |
| | 26 | Contingency buffer |

---

## 9. Paper Skeleton

```latex
\section{Abstract}
\section{Introduction}
\section{Related Work}
\section{Task Definition and Metric}
\section{HEDGEQUEST Dataset}
\section{Hedge Detection Models}
\section{Experiments}
\section{Results and Discussion}
\section{Error Analysis}
\section{Limitations \& Ethics}
\section{Conclusion}
\bibliography{anthology,custom}

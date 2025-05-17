
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

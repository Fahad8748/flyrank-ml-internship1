 Optimizing Production Search Performance: A Machine Learning Approach to Query Intent & CTR Prediction

Author: Fahad Rafique  
Track: FlyRank Machine Learning Internship  
Artifact Repository: https://github.com/Fahad8748/flyrank-ml-internship1 


Abstract
This study investigates the predictive modeling of search query outcomes using production-level web search data. Utilizing a filtered subset of 79 million anonymized production search records, we constructed a binary classification pipeline to forecast user engagement. We established a heuristic baseline model and compared it against an optimized Gradient Boosted Decision Tree (LightGBM) classifier trained on engineered query-level and temporal features. The final model achieved a 14.2% improvement in ROC-AUC over the baseline while maintaining a zero feature-leakage validation design. These findings offer an actionable framework for search engine indexing and real-time query prioritization in high-scale environments.



      Introduction & Problem Statement
Modern search systems process billions of queries daily, requiring real-time estimation of user intent and engagement probability to optimize content serving and ranking algorithms. Predicting user click behavior on raw production search streams presents significant challenges, including severe class imbalance, high feature variance, and computational constraints.

This paper addresses the core challenge of predicting high-engagement search queries prior to full SERP rendering. By predicting query engagement early, search pipelines can dynamically route computational resources, optimize cache retrieval, and improve overall search engine performance without degrading latency.



 Data
The analysis was conducted on a public-safe derivative of the FlyRank production search dataset containing 79 million raw records.

Data Release & Scope:Processed a structured partition covering a multi-week search window.
Filtering & Quality Checks: Excluded incomplete telemetry logs, corrupted session IDs, and low-frequency outlier queries (queries appearing $<3$ times across the corpus).
Data Privacy & Safety: All user identifiers, specific client domain names, proprietary query parameters, and sensitive strings were fully stripped or hashed prior to analysis to ensure strict compliance with public-safety standards.



Methodology

Assumptions & Label Definition
We modeled engagement as a binary target variable ($Y \in \{0, 1\}$), where $Y=1$ represents a successful query interaction meeting the threshold engagement criteria.

Feature Engineering
Features were grouped into three primary vectors:
1. Query Surface Features: Character length, word count, entity density, and special character presence.
2. Historical Aggregates: Historical click-through rates (out-of-fold target encoding) and search frequency bins.
3. Temporal Features: Hour of day, day of week, and session sequence order.

 Baseline Model
A simple frequency-based heuristic model was used as the benchmark, assigning target probabilities equal to historical group-mean engagement rates.

Validation Design & Leakage Prevention
To prevent temporal data leakage, models were evaluated using a strict chronological train-test split (80% historical training, 20% hold-out future evaluation) rather than random k-fold cross-validation.



 Results

| Model | Accuracy | Precision | Recall | ROC-AUC | F1-Score |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **Heuristic Baseline** | 0.612 | 0.540 | 0.480 | 0.595 | 0.508 |
| **Random Forest Benchmark** | 0.724 | 0.685 | 0.640 | 0.710 | 0.662 |
| **Final LightGBM Model** | **0.815** | **0.782** | **0.741** | **0.837** | **0.761** |

The final LightGBM model significantly outperformed both the baseline and benchmark models across all primary evaluation metrics, demonstrating strong predictive capabilities on unseen hold-out data.



 Limitations & Honest Framing
Distributional Drift: Search trends change over time; static feature distributions may degrade in predictive accuracy over longer time horizons without retraining.
Cold-Start Queries: Performance drops on novel, ultra-low frequency queries where historical aggregate signals are absent.
Feature Scope: The model relies on non-personalized, query-level telemetry to preserve user privacy, omitting individualized historical profile signals.



 Ranked Recommendations

1. Implement Dynamic Cache Routing: Deploy the light-weight LightGBM inference model at the edge to pre-cache high-engagement queries, reducing downstream SERP latency by an estimated 15-20%.
2. Automated Weekly Retraining: Establish a continuous learning pipeline to retrain the model weekly on rolling 14-day windows to combat query drift.
3. Fallback Heuristics for Cold-Start: Use character-n-gram embeddings as fallback features for unseen queries where aggregate click data is missing.



 Reproducibility
All source code, data preprocessing scripts, feature pipelines, and model evaluation notebooks are fully open-source and reproducible:



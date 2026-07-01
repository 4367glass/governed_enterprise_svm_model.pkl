# governed_enterprise_svm_model.pkl

# 🚀 Governed Enterprise Machine Learning Pipeline: Support Vector Machine (SVM) Classification

## 📊 Business Context & Objective
In an enterprise analytics ecosystem, data engineering and machine learning cannot operate in isolation. This repository demonstrates a production-grade, end-to-end data pipeline that extracts customer account data from a cloud data warehouse, enforces strict upstream data governance, and deploys a robust **Support Vector Machine (SVM)** classifier to predict term deposit subscription success.

By optimizing data processing at the database warehouse level, this pipeline eliminates model training bias, prevents data leaks, and provides highly stable predictive tracking suitable for immediate consumption by downstream business intelligence dashboards (e.g., Tableau).

> 🔍 **Responsible AI Cross-Reference:** This repository focuses entirely on the engineering architecture and live database extraction workflows. For our comprehensive framework on historical baseline audits, ethical model validation, and fairness evaluation metrics, please visit our companion project: **[Responsible AI Governance Repository](ADD_LINK_TO_YOUR_FIRST_REPOS_HERE)**.

---

## 🛠️ Tech Stack & Enterprise Conventions
* **Data Warehouse Ingestion:** Snowflake SQL (Utilizing uppercase keyword standards, explicit schema mapping, and recursive CTE formatting).
* **Programming Environment:** Python 3 (Google Colab / Production VM environment script).
* **Machine Learning Framework:** Scikit-Learn (`SVC`, `ColumnTransformer`, `StandardScaler`, `OneHotEncoder`).
* **Serialization & Model Portability:** Joblib (Bundling preprocessing pipes and model weights into a singular, unified pipeline asset).

---

## 📂 Repository Structure
```text
governed-enterprise-svm-pipeline/
├── .gitignore              # Watertight exclusion filter protecting Snowflake credentials & raw data
├── README.md               # Executive project documentation & recruiter summary
├── sql/
│   └── data_extraction.sql # Optimized Snowflake SQL sourcing query with integrated deduplication
└── models/
    └── governed_enterprise_svm_model.pkl # Unified deployment-ready model and scaler artifact
```

---

## 🔒 Upstream Data Governance Strategy

Standard flat-file data science workflows frequently introduce training bias by preprocessing data purely inside Python. This pipeline enforces data governance rules at the **database warehouse level** inside Snowflake before records ever touch the training scripts:

1. **Elimination of Upstream Data Leakage:** The feature `DURATION` (the length of the marketing phone call) was explicitly omitted at the warehouse layer. Including call length creates a mathematical feedback loop that artificially inflates accuracy scores while rendering the model useless for predictive pre-call targeting.
2. **Deterministic Deduplication:** Implemented window ranking functions (`ROW_NUMBER() OVER (...)`) partitioned across key client traits to filter out streaming table duplicates and enforce an active, "golden-record" standard.
3. **Distance-Metric Preservation:** Replaced uncontacted flags (`-1`) with proper SQL `NULL` values via `NULLIF(pdays, -1)`, preventing distance-based kernel models like SVM from warping geometric space during distance calculations.

---

## 📊 Pipeline Validation & Performance Analytics

### 1. Cross-Validation Framework (K=4 Round Robin)
To guarantee the model's reliability across diverse audience slices, a **4-Fold Stratified Cross-Validation** strategy was run over the 36,168 record training set:
* **Round 1 Accuracy:** 82.33%
* **Round 2 Accuracy:** 82.01%
* **Round 3 Accuracy:** 81.52%
* **Round 4 Accuracy:** 81.70%
* **🔄 Mean CV Accuracy:** **81.89%**
* **📈 Variance Stability:** **+/- 0.31%** (Microscopic variance confirms high model robustness and zero overfitting).

### 2. Out-of-Sample Final Vault Evaluation
Evaluating the model against the hidden **9,043 record validation vault** yielded the following production results:
* **Out-of-Sample Accuracy:** **82.62%**
* **Target Class Recall:** **62%** (Successfully identified 652 actual conversion targets out of 1,058, maximizing marketing team outreach efficiency).
* **Class Balancing:** Leveraged `class_weight='balanced'` within the Radial Basis Function (RBF) kernel to successfully mitigate the heavy bank marketing target label imbalance.

---

## 💻 Production Ingestion Script Preview
The code relies on the official `snowflake-connector-python` to safely extract raw records directly into a Pandas DataFrame using secure cloud environment secrets, bypassing any security vulnerability associated with hardcoded local credentials:

```python
import pandas as pd
import snowflake.connector
from google.colab import userdata

# Secure credential fetching via environment manager
conn = snowflake.connector.connect(
    user=userdata.get('SF_USER'),
    password=userdata.get('SF_PASSWORD'),
    account=userdata.get('SF_ACCOUNT'),
    warehouse=userdata.get('SF_WAREHOUSE'),
    database='core_db',
    schema='marketing_schema'
)

# Execution of warehouse governance query
df = pd.read_sql(governed_pipeline_query, conn)
conn.close()
```

---
## 🎯 Deployment & Downstream Business Value
The final serialized production artifact, `governed_enterprise_svm_model.pkl`, encapsulates the complete data pipeline. When deployed, it maps directly back into Snowflake score tables, feeding an automated **Tableau Performance Dashboard** that enables business stakeholders to track conversion yields, optimize marketing spend, and evaluate prediction accuracy trends over time.

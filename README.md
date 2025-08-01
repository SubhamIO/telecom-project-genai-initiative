# 📘 GenAI-Powered Customer Network Experience Analysis

## 🎯 Objective
To enhance customer understanding and retention, we developed a GenAI-based system that translates raw network KPI and SHAP values into meaningful **network experience scores** and **human-readable insights** for telecom users. This system enables business stakeholders to interpret customer satisfaction and act on churn risk proactively.

---

## 📌 Business Use Case
Telecom operators frequently struggle to:
- Interpret ML-based churn predictions in customer-centric language
- Connect network KPIs like RSRP/SINR to customer experience
- Explain score rationale to non-technical teams (marketing, support)
- Identify systemic issues causing low scores

Our solution addresses this gap by building an **automated, explainable, and scalable GenAI pipeline**.

---

## 🧠 Technical Approach

### 1. **Input Data**
- `customer_scores_long_format.csv`: Experience scores (e.g. `main_score`, `signal_quality_subscore`)
- `customer_features_long_format.csv`: Features like `rsrp`, `sinr` and corresponding SHAP values

### 2. **LangGraph-Based Modular Pipeline**
We built an agentic AI pipeline using [LangGraph], consisting of:
- **Fetcher**: Retrieves and merges features + scores for any customer
- **InsightGenerator**: Uses OpenAI's GPT-4o to translate KPIs/SHAPs into business-readable insights
- **FinalReport**: Presents the insight per customer

### 3. **Scalable Analysis**
- Batch processing supported via `analyze_batch()`
- Auto-visualization across all features to explore how KPIs affect score

---

## 📊 Analytics & Visualization Modules

### ✅ Feature vs. Score Distribution
- Auto-generates boxplots showing how each KPI varies across `main_score` quintiles
- Validates: “Are low scores consistently linked to poor KPI metrics?”

### ✅ Feature vs. SHAP Interpretation
- For every `(feature, shap_feature)` pair, creates a scatter plot
- Uses GenAI to summarize trends (e.g., “Higher SINR leads to more positive SHAP impact”)
- Helps validate: “Do SHAP values align with raw metrics?”

### ✅ Segment-Level Summarization
- Aggregates SHAPs, KPIs, and scores
- Uses LLM to summarize key differences between high and low experience segments

---

## 📈 Sample Insight (Automated for Customer CUST_005)
> “Customer CUST_005 has a low main score primarily due to poor RSRP and SINR values. The SHAP values indicate these metrics significantly reduce the predicted experience, matching the raw KPI deterioration. This suggests genuine network issues rather than model noise.”

---

## 🚀 Benefits Delivered

| Benefit          | Description |
|------------------|-------------|
| Explainability   | Converts ML churn logic into natural language |
| Actionability    | Highlights exact KPIs causing poor experience |
| Automation       | Scales across thousands of customers |
| Validation       | Aligns SHAP values with feature intuition |
| Visualization    | Provides team-friendly charts with insights |

---

## 🔄 Next Steps / Recommendations

- 🔍 Integrate segment-level insights into dashboards for ops/marketing
- 🤖 Plug into CRM tools for auto-escalation based on low score + bad KPIs
- 🎛️ Enable real-time scoring and alert generation
- 📈 Extend to include call drop data, usage behavior, and NPS surveys

---

## 📂 Output Assets (Saved in `./visualisations/`)

- `feature_vs_score_plots/`: All feature score plots
- `feature_vs_shap_scatter/`: All feature vs SHAP scatter plots
- Text-based summaries for segment and per-customer insights

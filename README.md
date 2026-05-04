# 🛒 E-Commerce Customer Churn & Segmentation Analysis
### OLIST Brazilian E-Commerce Dataset | Python · Power BI

---

## 📌 Project Overview

This project analyses customer behaviour across 93,358 unique customers on the OLIST Brazilian e-commerce platform to identify churn patterns, segment customers by value, and build a predictive model that flags at-risk customers before they are lost.

The analysis reveals that **97% of customers never return after their first purchase** — despite a strong average satisfaction score of 4.16/5. This points to a re-engagement problem, not a product problem, and forms the core business insight driving the recommendations.

---

## 🎯 Business Questions Answered

- What percentage of customers are churning and why?
- Which customer segments are most valuable and most at risk?
- What drives a customer to return — or not return?
- Which customers should the business prioritise for retention campaigns?

---

## 🗂️ Project Structure

```
olist-churn-analysis/
│
├── data/
│   ├── raw/                        # Original CSV files from Kaggle
│   │   ├── olist_orders_dataset.csv
│   │   ├── olist_order_items_dataset.csv
│   │   ├── olist_order_payments_dataset.csv
│   │   ├── olist_order_reviews_dataset.csv
│   │   ├── olist_products_dataset.csv
│   │   └── olist_customers_dataset.csv
│   │
│   └── processed/                  # Cleaned and engineered outputs
│       ├── olist_rfm_final.csv     # Customer-level RFM + churn model output
│       └── olist_state_summary.csv # State-level aggregated data
│
├── notebooks/
│   └── olist_churn_analysis.ipynb  # Full Python analysis notebook
│
├── dashboard/
│   └── olist_churn_dashboard.pbix  # Power BI dashboard (3 pages)
│
├── reports/
│   └── insight_report.docx    # Business insight report
│
└── README.md
```

---

## 📊 Dataset

**Source:** [OLIST Brazilian E-Commerce — Kaggle](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce)

| File | Rows | Description |
|---|---|---|
| olist_orders_dataset | 99,441 | Order status, timestamps |
| olist_order_items_dataset | 112,650 | Products and prices per order |
| olist_order_payments_dataset | 103,886 | Payment values and methods |
| olist_order_reviews_dataset | 99,224 | Customer review scores |
| olist_products_dataset | 32,951 | Product categories |
| olist_customers_dataset | 99,441 | Customer location info |

---

## 🔧 Methodology

### 1. Data Cleaning & Merging
- Filtered to delivered orders only (removed cancelled/unavailable)
- Converted date columns from string to datetime format
- Merged all 6 tables using `order_id` and `customer_id` as join keys
- Aggregated payments and items to one row per order
- Filled missing review scores with median value

### 2. Feature Engineering — RFM
Built a customer-level RFM table with the following features:

| Feature | Definition |
|---|---|
| Recency | Days since last purchase |
| Frequency | Total number of orders placed |
| Monetary | Total payment value (BRL) |
| Avg Review | Mean review score across all orders |
| R/F/M Scores | Quartile-based scores (1–4) per dimension |
| RFM Score | Sum of R + F + M scores (3–12) |

### 3. Churn Definition
A customer is classified as **churned** if they made only **one purchase** and never returned.

> This definition was chosen because the dataset spans 2016–2018 (historical), making recency-based churn unreliable. Single-purchase behaviour is the strongest signal of non-retention in marketplace platforms.

**Churn rate: 97.0%** — consistent with known OLIST dataset characteristics where most customers are one-time buyers.

### 4. Customer Segmentation
Customers were segmented into 6 groups based on R, F, M scores:

| Segment | Criteria | Count |
|---|---|---|
| Champion | R≥4, F≥3, M≥3 | 6,129 |
| Loyal | R≥3, F≥2 | 29,041 |
| Promising | R≥3, F=1 | 17,440 |
| At Risk | R=2, F≥2 | 17,437 |
| High Value Lost | R≤2, F=1, M≥3 | 2,861 |
| Lost | All others | 20,450 |

### 5. Churn Prediction Model
**Algorithm:** Logistic Regression  
**Why Logistic Regression:**
- Binary output (churned = 0 or 1) matches the model's design
- Fully interpretable — coefficients explain which features drive churn
- Appropriate for business settings where explainability matters more than marginal accuracy gains

**Handling Class Imbalance:**  
With 97% of customers in the churned class, `class_weight='balanced'` was used to prevent the model from predicting "churned" for everyone.

**Features used:** `recency`, `monetary`, `avg_review`  
> Note: RFM scores and frequency were excluded to prevent data leakage (churn label was derived from frequency).

**Results:**

| Metric | Value |
|---|---|
| ROC-AUC Score | 0.783 |
| Churned Recall | 81% |
| Active Recall | 59% |
| Overall Accuracy | 80% |

---

## 🔍 Key Findings

### Finding 1 — Spend is the Strongest Churn Predictor
Monetary value has a coefficient of **-0.865** — the largest in absolute terms. Higher-spending customers are significantly less likely to churn. This suggests upselling and premium product promotion could directly improve retention.

### Finding 2 — Satisfaction is Not the Problem
With an average review score of **4.16/5**, customers are satisfied with their experience. Churn is a **re-engagement problem**, not a product or service quality problem. Customers are not leaving due to bad experiences — they simply are not being brought back.

### Finding 3 — São Paulo Dominates Revenue
SP accounts for the largest share of customers and revenue. This proves concentration risk exists — over-reliance on one geography.

### Finding 4 — 17,437 At-Risk Customers Need Urgent Attention
The At Risk segment represents customers who have purchased before but are going quiet. These are the highest-priority targets for retention campaigns as they have demonstrated willingness to buy.

---

## 💡 Business Recommendations

| Priority | Action | Target Segment |
|---|---|---|
| 🔴 High | Launch re-engagement email campaign with discount incentive | At Risk (17,437) |
| 🔴 High | Introduce loyalty programme for repeat buyers | Loyal + Champion |
| 🟡 Medium | Upsell premium products to increase order value | Promising (17,440) |
| 🟡 Medium | Win-back campaign with strong incentive | High Value Lost (2,861) |
| 🟢 Low | Expand marketing to underperforming states | All non-SP states |

---

## 🛠️ Tools & Technologies

| Tool | Purpose |
|---|---|
| Python (Pandas, NumPy) | Data cleaning and feature engineering |
| Scikit-learn | Logistic Regression model and evaluation |
| Matplotlib / Seaborn | Exploratory visualisation |
| Power BI (DAX) | Interactive 3-page dashboard |
| Google Colab | Development environment |

---

## 📈 Power BI Dashboard

The dashboard contains 3 pages:

**Page 1 — Customer Overview**
KPI cards, customer segment distribution, revenue by state map, spend and satisfaction by segment.

**Page 2 — Churn Risk Analysis**
Churn risk distribution, segment-level risk breakdown, spend vs recency scatter plot, top high-value at-risk customers table, avg churn probability by segment.

**Page 3 — Recommendations**
Revenue and satisfaction by segment, three strategic insight panels with data-backed recommendations.

[📄 View Business Insight Report](https://docs.google.com/document/d/1Zap8H_3bmwwkbPs4BIO397Dra8oIFISV/edit?usp=drive_link&ouid=104837620425403070448&rtpof=true&sd=true) 

---

## 👤 Author

**Rahul Menon**  
Entry-level Data Analyst | Python · SQL · Power BI  
[LinkedIn](https://www.linkedin.com/in/rahul-menon-132a84245/) · [GitHub](https://github.com/rahulmenonn) · menon9236@gmail.com

# 📊 Compound Wallet Risk Scoring

This project assigns a **risk score between 0–1000** to wallets based on their historical behavior with DeFi protocols. It uses **on-chain transaction data** fetched from the **Moralis API** for a given list of wallet addresses.

---

## 📁 Project Structure

``` 
compound-risk-scoring/
├── wallet_features.csv # Extracted features per wallet
├── wallet_scores_updated.csv # Final wallet scores after log-scaling
├── score_distribution_analysis.png
├── README.md
└── analysis.md

```

---

## 🚀 How to Run

### 1. Clone the repo & install dependencies:

`pip install pandas requests matplotlib seaborn`

### 2. Make sure you have a valid **Moralis API key**.

### 3. Run the notebook/script that: 

- Loads wallet list 
- Fetches transaction history 
- Extracts wallet-level features
- Applies scoring logic
- Saves `wallet_features.csv` and `wallet_scores_updated.csv` 

---

## 📦 Deliverables 

- ✅ `wallet_scores_updated.csv` with columns:
```
| wallet_id | score | 
|-----------|-------| 
| 0xfaa0... | 732 | 
| 0x0039... | 615 |
| 0x06b5... | 36  |
````


- ✅ `wallet_features.csv` with features:
- `num_transactions` 
- `unique_tokens` 
- `total_amount` - `avg_value` 
- `recent_txn_days` 
- `log_total_amount` (added for scoring robustness)
- `analysis.md` with visualizations and analysis of score behavior. 

--- 

## 🧠 Methodology

### 1. Data Collection 

- **Source**: [Moralis](https://www.moralis.io/) Web3 API 
- **Approach**: Queried each wallet’s transaction history on Ethereum chain using `GET /{wallet}/erc20/transfers`.

### 2. Feature Selection Rationale 
We computed wallet-level indicators that reflect user behavior: 

| Feature | Description | 
|-----------------------------------------|---------------------|
| `num_transactions` | Number of ERC20 transfers (signal of activity) |
| `unique_tokens` | Diversity of tokens interacted with | 
| `total_amount` | Total volume of assets transacted (in wei-normalized units) | 
| `avg_value` | Mean transaction size | 
| `recent_txn_days` | Days since the last transaction (recency) | 
| `log_total_amount` | Log-transformed version of total_amount to reduce skew from large outliers | 

### 3. Scoring Logic 
We compute a weighted score based on normalized features: 

- Positively Weighted: 
- `num_transactions` 
- `unique_tokens` 
- `log_total_amount` 

- Negatively Weighted: 
- `recent_txn_days` (penalize inactivity) 

Final score is scaled to **0–1000** and clipped to stay within bounds.

### 4. Justification of Risk Indicators 

- **Active, frequent wallets** with a diverse token set and healthy transaction volume are less risky. 
- **Dormant or low-volume wallets** are considered higher risk. 
- We use `log_total_amount` instead of raw total to prevent extreme volume from disproportionately affecting scores. 

---

## 🔍 Analysis
See [analysis.md] for distribution plots and behavioral insights.

---

## ✅ Notes 
- Final output: `wallet_scores_updated.csv` 
- Outlier capping and log-transformation were applied to stabilize scoring 
- Visual and manual inspection confirmed reasonable scoring behavior ```

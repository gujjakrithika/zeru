## Aave V2 Wallet Credit Scoring

This project assigns a credit score between 0 and 1000 to wallets based on their transaction behavior on the Aave V2 protocol (Polygon network). The score reflects the responsibility and trustworthiness of the wallet, with higher scores indicating consistent, low-risk behavior.

## Objective

The goal is to develop a transparent, rule-based scoring mechanism that evaluates DeFi user behavior and quantifies risk using only raw on-chain transaction data.

## Method Chosen

We used a **rule-based scoring approach** with behavioral feature extraction. Each wallet’s score is derived from its historical transaction patterns including deposits, borrowings, repayments, and liquidation events.

Key aspects of the method:
- **Unsupervised**: No labeled training data required
- **Transparent**: Features and weights are explainable
- **Efficient**: Suitable for scaling to large datasets

Each wallet’s actions are converted into measurable features, normalized using `MinMaxScaler`, and combined using a weighted sum. This ensures that high-value, responsible wallets are rewarded, and risky or bot-like wallets are penalized.

## Architecture Overview
user-transactions.json (raw transaction data)
↓
Group transactions by wallet address
↓
Feature extraction:
- deposit_total (USD)
- borrow_total (USD)
- repay_total (USD)
- repay_borrow_ratio
- liquidation count
↓
Scale features using MinMaxScaler
↓
Weighted score calculation
↓
Credit score normalized to 0–1000 range
↓
wallet_scores.json (output)

## Processing Flow

1. **Load Data**
   - Parse the raw `user-transactions.json` file containing individual wallet transactions.

2. **Group by Wallet**
   - All transactions are grouped by `userWallet`.

3. **Feature Engineering**
   For each wallet, calculate:
   - `deposit_total`: Total USD value of deposits
   - `borrow_total`: Total USD value of borrows
   - `repay_total`: Total USD value of repayments
   - `repay_borrow_ratio`: Repay / Borrow
   - `liquidations`: Count of liquidation events

4. **Feature Normalization**
   - All features are normalized using `MinMaxScaler` to bring values into the same scale (0–1).

5. **Score Calculation**
   - The features are combined using the following weighted formula:
     credit_score = (
      0.4 * deposit_total_scaled +
      0.3 * repay_total_scaled +
      0.2 * repay_borrow_ratio_scaled -
      0.1 * liquidations_scaled
      ) * 1000


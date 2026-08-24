# Data Governance & Data Dictionary

## Data Source

Olist Brazilian E-Commerce Public Dataset (Kaggle). Real, anonymized transaction
data from a Brazilian e-commerce marketplace, 2016–2018. Customer identifiers are
pseudonymized; no PII is present in the raw files.

## Tables Used

| Table                       | Purpose                      | Key Field          |
| --------------------------- | ---------------------------- | ------------------ |
| olist_customers_dataset     | Customer ID mapping          | customer_unique_id |
| olist_orders_dataset        | Order status and timestamps  | order_id           |
| olist_order_items_dataset   | Line-item pricing per order  | order_id           |
| olist_order_reviews_dataset | Review text and star ratings | order_id           |

## Data Dictionary (final_customer_dataset.csv)

| Column              | Type              | Description                                                                                                |
| ------------------- | ----------------- | ---------------------------------------------------------------------------------------------------------- |
| customer_unique_id  | Text              | Unique customer identifier (persists across orders)                                                        |
| recency             | Integer           | Days since customer's most recent order, relative to snapshot date                                         |
| frequency           | Integer           | Total number of distinct orders placed by the customer                                                     |
| monetary            | Decimal           | Total amount spent across all orders (BRL)                                                                 |
| churned             | Binary (0/1)      | 1 if recency > 180 days, else 0 (see Methodology)                                                          |
| churn_probability   | Decimal (0-1)     | Model-predicted likelihood of churn                                                                        |
| risk_tier           | Text              | Low / Medium / High, assigned via quantile ranking of churn_probability                                    |
| avg_sentiment_score | Decimal (-1 to 1) | Average VADER sentiment score from translated reviews; blank if no review data available for that customer |

## Known Data Quality Issues & Limitations

1. **customer_id vs customer_unique_id**: the raw orders table uses `customer_id`,
   which is actually unique per ORDER, not per customer. Used `customer_unique_id`
   throughout to correctly track repeat customers — using the wrong key would have
   silently made every customer look like a one-time buyer.
2. **Order status filtering**: excluded ~3% of orders with status other than
   "delivered" (canceled, unavailable, etc.) since these don't represent completed
   transactions relevant to churn behavior.
3. **Review sentiment coverage**: only 1,739 of 93,358 customers (1.9%) have a
   sentiment score, due to translating a sample of reviews rather than the full
   set (free translation API constraints). This is a coverage limitation, not a
   data error — findings involving sentiment should be read as directional, not
   comprehensive.
4. **Language**: review text is in Portuguese; translated via Google Translate
   before English-language sentiment scoring. Translation introduces some
   information loss versus a native Portuguese NLP model.
5. **Churn label is a business assumption, not ground truth**: no dataset field
   directly states whether a customer "churned" — the 180-day threshold is a
   reasoned definition (see RECOMMENDATIONS.md), not an observed fact. Any
   downstream user of this data should be aware the label is a modeling choice.

## Data Handling

- No PII was introduced, stored, or processed beyond what the source dataset
  already anonymized.
- All transformations are reproducible via the numbered notebooks in `/notebooks`,
  run in order (01 through 09).

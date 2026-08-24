# Business Recommendations — Customer Churn & Revenue Risk

## Summary

This analysis segmented 93,358 customers into churn risk tiers based on purchase
recency, frequency, and monetary value, then cross-referenced risk with review
sentiment. Total customer revenue analyzed: $13,221,498. Of this, $1,739,676
(13.2%) sits with customers in the High risk tier.

## Key Findings

1. **High-risk revenue is concentrated but addressable.** ~31,500 customers
   (33% of the base) fall into the High risk tier, representing $1.74M in revenue —
   a meaningful but manageable segment to target with retention efforts rather than
   a blanket campaign across all 93K customers.

2. **Repeat purchasing is rare.** The majority of customers in this dataset are
   one-time buyers, which limits how predictive purchase-frequency-based churn
   models can be (baseline model ROC-AUC of ~0.56, only marginally better than
   chance). This points to a data limitation, not a business one — a real deployment
   would benefit from adding browsing behavior, marketing engagement, or support
   ticket data as features.

3. **Sentiment and risk tier were not clearly correlated in this dataset**
   (High risk customers averaged 0.32 sentiment vs 0.27 for Low risk — the opposite
   of the expected direction). Given the weak churn model itself, this suggests
   sentiment should be tested against a stronger churn signal before drawing
   conclusions about its business value.

## Recommendations

1. **Prioritize win-back outreach for the High risk tier** ($1.74M revenue,
   ~31,500 customers) — a targeted email/discount campaign here is a much more
   efficient use of budget than an untargeted retention push.

2. **Invest in richer churn features before scaling the model further.** RFM
   alone (recency/frequency/monetary) is insufficient here — recommend adding
   customer service interaction data, marketing email engagement, and product
   category affinity as next-step features to meaningfully improve prediction.

3. **Re-run the sentiment-risk correlation once a stronger churn model exists.**
   The current inconclusive result is likely a symptom of the weak baseline model,
   not proof that sentiment doesn't matter — this should be revisited rather than
   dismissed.

4. **Expand the sentiment sample.** Only 1,739 of 93,358 customers (1.9%) have
   sentiment data due to translation API constraints. Scaling this with a paid
   translation service or native Portuguese NLP model would substantially
   strengthen this analysis.

## Methodology Note

Churn defined as no purchase within 180 days of last observed activity in the
dataset. Risk tiers assigned via quantile ranking (not fixed probability
thresholds) due to low variance in model output — see technical notes in
`/notebooks` for full reasoning and limitations.

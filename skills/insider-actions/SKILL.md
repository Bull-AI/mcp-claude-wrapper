---
name: insider-actions
description: Compare market transactions with corporate-action timing.
---

# Market Transactions and Corporate-Action Timing

Use this skill when a user asks to place bulk, block, or insider market transactions alongside corporate actions for an Indian listed company.

## Internal workflow

1. Resolve one company with `search_companies`.
2. Retrieve bounded transaction pages with `get_company_market_transactions`. Use `all` when multiple transaction types are requested, or make separate bounded calls for `bulk`, `block`, and `insider` when clearer.
3. Retrieve bounded corporate-action pages with `get_company_corporate_actions`. Use `all` when multiple action types are requested; the supported individual filters are `bonus`, `dividend`, and `split`.
4. Neither source accepts a date-window filter. Select the requested comparison window from returned records after retrieval.
5. Follow pagination only to a disclosed cap or until exhausted. If pagination remains, label the result bounded and potentially incomplete.
6. Preserve returned transaction party names and actions. Do not relabel a party as a promoter, director, or another role that is not supplied.
7. Choose and state the corporate-action comparison date: ex-date, record date, or book-closure date. Keep events with null dates separate.
8. Calculate calendar-day distance between returned dates only when both dates are available.
9. Describe temporal proximity as “occurred within N days of.” Do not claim statistical correlation, causation, coordination, or improper conduct.
10. Do not characterize a transaction as bullish, bearish, or an investment signal.

## Public answer

Use these sections:

1. **Coverage** — pages or records examined, date range observed, and whether pagination was exhausted.
2. **Combined event timeline**.
3. **Temporal observations** — neutral descriptions using a stated proximity threshold.
4. **Limitations** — null dates, bounded coverage, unsupported party roles, and no causal inference.
5. **Sources** — returned source labels and `as_of` metadata.

Suggested table:

| Date | Event | Party or corporate action | Returned details | Nearest dated event | Day difference | Source |
|---|---|---|---|---|---:|---|

## Monetary units and currency conversion

- Prefer Indian rupees crores (`₹ crore`) for monetary values in tables, comparisons, calculations, and summaries.
- Convert reported INR scales exactly: ₹1 lakh = ₹0.01 crore; ₹100 lakh = ₹1 crore; ₹1 million = ₹0.1 crore; ₹10 million = ₹1 crore; ₹1 billion = ₹100 crore.
- Preserve the source's reported amount when it is in another currency, followed by the estimate in brackets: `US$X million (≈₹Y crore)`.
- Use the best defensible period-appropriate FX rate: transaction-date rate for dated events, period-end rate for point-in-time balances, and period-average rate for revenue, costs, cash flow, or other period flows.
- Prefer an exchange rate stated in the company document. Otherwise use a reputable retrieved FX source. State the rate, date or period, source, and formula: `foreign-currency amount × INR per foreign-currency unit ÷ 10,000,000 = ₹ crore`.
- Prefix converted figures with `≈` and use sensible precision; do not imply more accuracy than the FX input supports.
- Never invent an exchange rate. If no defensible rate is available, retain the original currency and state that an INR-crore estimate could not be produced.

## Sources, citations, and boundaries

- Do not mention tool names, call order, arguments, cursors, schemas, or retrieval narration in the public answer.
- Transaction and corporate-action results provide source labels rather than document URLs or page citations. Render a returned label and `as_of` value as source metadata; do not invent a link.
- Do not imply that pagination is complete when more pages remain.
- Do not infer a party's role, motive, knowledge, relationship, or intent.
- Temporal proximity is not evidence of causation or wrongdoing.
- Results are informational and are not trading instructions or personalized investment advice.

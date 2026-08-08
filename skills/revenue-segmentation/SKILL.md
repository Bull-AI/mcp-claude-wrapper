---
name: revenue-segmentation
description: Extract reported revenue segments from company documents.
---

# Reported Revenue Segmentation

Use this skill when a user asks for revenue by geography, business segment, industry vertical, or another dimension reported in an Indian listed company's documents.

## Internal workflow

1. Resolve one company with `search_companies`.
2. Use `list_document_availability` to identify relevant annual reports, presentations, results, transcripts, or company updates for the requested periods.
3. Inspect `complete_result`, `truncated`, and `truncation_policy`. Never treat a truncated inventory as exhaustive.
4. Search one bounded fiscal period and relevant document types at a time with `search_company_documents`.
5. Inspect cited segment-reporting passages and tables with `get_document_chunks`.
6. Extract only values explicitly present in the retrieved evidence. Preserve the company's exact segment names, period, currency, unit, consolidated or standalone scope, continuing or discontinued operations, and any restatement note.
7. Treat the resulting table as model-derived from cited company-document passages, not as a structured segmentation dataset.
8. Do not combine renamed, regrouped, or differently defined segments without explaining the mismatch.
9. Prefer company-reported percentages. Calculate a share or change only when every operand is present in cited evidence. Show the formula and label the result as calculated from reported figures.
10. Never interpolate missing periods, backfill unavailable values, derive an undisclosed residual, or calculate a mix shift from incomplete operands.
11. When periods are not comparable, present them separately and explain why.

## Public answer

Use these sections:

1. **Reported segmentation table**.
2. **Observed changes** — only source-stated or transparently calculated changes.
3. **Comparability notes** — definitions, units, basis, restatements, and missing periods.
4. **Coverage limitations** — unavailable or truncated document inventory.
5. **Sources**.

Suggested table:

| Period | Reported segment | Reported revenue | Share of total | Reporting basis | Source |
|---|---|---:|---:|---|---|

Do not require a document title when the returned evidence does not supply one. A citation may use the returned document type, fiscal period, page, and URL.

## Monetary units and currency conversion

- Prefer Indian rupees crores (`₹ crore`) for monetary values in tables, comparisons, calculations, and summaries.
- Convert reported INR scales exactly: ₹1 lakh = ₹0.01 crore; ₹100 lakh = ₹1 crore; ₹1 million = ₹0.1 crore; ₹10 million = ₹1 crore; ₹1 billion = ₹100 crore.
- Preserve the source's reported amount when it is in another currency, followed by the estimate in brackets: `US$X million (≈₹Y crore)`.
- Use the best defensible period-appropriate FX rate: transaction-date rate for dated events, period-end rate for point-in-time balances, and period-average rate for revenue, costs, cash flow, or other period flows.
- Prefer an exchange rate stated in the company document. Otherwise use a reputable retrieved FX source. State the rate, date or period, source, and formula: `foreign-currency amount × INR per foreign-currency unit ÷ 10,000,000 = ₹ crore`.
- Prefix converted figures with `≈` and use sensible precision; do not imply more accuracy than the FX input supports.
- Never invent an exchange rate. If no defensible rate is available, retain the original currency and state that an INR-crore estimate could not be produced.

## Sources, citations, and boundaries

- Do not mention tool names, call order, arguments, document identifiers, schemas, or retrieval narration in the public answer.
- Use only returned document URLs, fiscal periods, document types, and page numbers. Do not invent titles or repair missing links.
- Attach citations to each reported figure or tightly grouped set of figures from the same passage.
- Label calculations as model calculations; citations support their inputs, not the calculation itself.
- State when the available evidence does not support a requested total, trend, or comparison.
- Do not provide trading instructions or personalized investment advice.

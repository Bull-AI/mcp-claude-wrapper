---
name: revenue-segmentation
description: Report revenue and segment splits with cited filing pages.
---

# Reported Revenue Segmentation

Use this skill when a user asks for revenue or segment results by business segment, geography, product, customer type, or another dimension reported in an Indian listed company's filings, or how that split changed over periods. For operating metrics such as stores or ARPU use `get_company_kpis`; for management targets use `get_company_guidance`.

## Internal workflow

1. Call `get_company_revenue_segments` with the company as `identifier` (NSE or BSE code, ISIN, or name). If `identifier_ambiguous` is returned, pick a candidate only when it clearly matches the request; otherwise use `search_companies` or ask the user. Candidate lists can include unrelated companies.
2. Narrow with `metric`, `axis`, `period_type` (`all`, `quarter`, `annual`), `from_fiscal_year`, and `page_size` (maximum 20). `metric` and `axis` are lowercase snake_case names such as `revenue`, `segment_result`, `geography`, or `business_segment`. Leave them unset when unsure: a filter that matches nothing returns an empty result that still counts as a call.
3. Follow `pagination`: while `has_more` is true, repeat the call with the same arguments and `next_cursor` as `cursor`. A response budget can cap a page below `page_size`, so never infer completeness from the item count. Each page counts as one tool call against the monthly allowance, so page only as far as the question needs.
4. Read each breakdown by `metric` (what is measured; never present `segment_result`, assets, or order book as revenue), `axis` and `label` (how it is split), and `statement_scope` (`consolidated`, `standalone`, or `not_stated`). Never mix scopes in one comparison and never assume a scope that is not stated.
5. Read each value's segment, period, `value`, `unit`, and `quality_tier` (A or B), and show the tier beside the value. Quote the unit exactly as returned. Spellings vary (`crore`, `₹ crore`, `₹ in crore`) and a value may be a percentage rather than an amount, so never add percentages to amounts. Convert to `₹ crore` only by the rules below and show the reported value and unit beside the converted figure. If `values_truncated` is true, narrow the filters and say the series is capped.
6. The same fact can be returned from more than one filing, and several breakdowns can share a label. Prefer the cited page that matches the period, do not double-count, and open the cited pages to tell identically labelled breakdowns apart.
7. Verify values the answer relies on with `get_document_chunks`, passing the value's `document_id` and cited page as `page_start` and `page_end`. Each call is metered, so verify what matters rather than every row. The returned document type and period name the filing for the citation.
8. Present returned values as extracted from company filings, not as audited or independently verified figures. Preserve the segment names, period, unit, scope, and any restatement note. Do not combine renamed, regrouped, or differently defined segments without explaining the mismatch.
9. Prefer company-reported percentages. Calculate a share or change only when every operand is present in cited evidence with the same scope and unit. Show the formula and label the result as calculated from reported figures.
10. Never interpolate missing periods, backfill unavailable values, derive an undisclosed residual, or calculate a mix shift from incomplete operands. When periods are not comparable, present them separately and explain why.
11. Read `coverage_note`: extraction is not exhaustive, so an empty or partial result is not evidence that the company does not disclose a split. Fall back to the documents: `list_document_availability` (inspect `complete_result`, `truncated`, and `truncation_policy`; never treat a truncated inventory as exhaustive), then `search_company_documents` for one bounded fiscal period and relevant document types at a time, then `get_document_chunks` on the cited segment-reporting pages. Extract only values explicitly present and label them as taken from cited passages.

## Public answer

Use these sections:

1. **Reported segmentation table**.
2. **Observed changes** — only source-stated or transparently calculated changes.
3. **Comparability notes** — definitions, units, scope, restatements, and missing periods.
4. **Coverage limitations** — values extracted from filings may omit disclosed segments, metrics, or periods; empty, capped, or truncated results; unavailable document inventory.
5. **Sources**.

Suggested table:

| Period | Reported segment | Reported value | Share of total | Reporting basis | Quality tier | Source |
|---|---|---:|---:|---|---|---|

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

- Do not mention tool names, call order, arguments, cursors, document identifiers, schemas, or retrieval narration in the public answer.
- Cite the filing and page beside each reported figure or tightly grouped set of figures from the same page. Use only returned document URLs, fiscal periods, document types, and page numbers. A returned URL can be null: cite the page without a link and do not invent a title or repair the link.
- Label calculations as model calculations; citations support their inputs, not the calculation itself.
- State when the available evidence does not support a requested total, trend, or comparison.
- Do not provide trading instructions or personalized investment advice.

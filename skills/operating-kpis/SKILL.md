---
name: operating-kpis
description: Retrieve company-specific operating KPIs with cited sources.
---

# Company Operating KPIs

Use this skill when a user asks for operating metrics specific to an Indian listed company, such as store counts, subscribers, ARPU, volumes, order book, utilisation, or product mix, or how they changed over periods. For management targets and outlook use `get_company_guidance`; for revenue split by segment, geography, product, or customer type use `get_company_revenue_segments`.

## Internal workflow

1. Call `list_company_metrics` with the company as `identifier` (NSE or BSE code, ISIN, or name). It is free and returns the company's `kpis` menu: each KPI's `key`, `label`, `unit`, `kind`, `members`, `period_types`, `first_period`, `last_period`, and `value_count`, but no values. If `identifier_ambiguous` is returned, pick a candidate only when it clearly matches the request; otherwise use `search_companies` or ask the user. Candidate lists can include unrelated companies. If `kpis_truncated` is true, the menu is capped; use the `kpi` filter in step 2 for anything it does not list.
2. Pick the KPIs that answer the question from the menu and call `get_company_kpis` with their keys in `kpis` (one to eight keys, exactly as listed). A key that is not on the menu is rejected, so never guess one. Narrow further with `period_type` (`all`, `quarter`, `annual`, `half_year`, `nine_months`, `year_to_date`), `from_fiscal_year`, and `page_size` (maximum 16); the menu's `period_types` and period range show which filters can match. Use the `kpi` filter (case-insensitive substring of the KPI label, such as `ARPU` or `stores`) only when the menu is capped or does not list the KPI; a `kpi` that matches nothing returns an empty result that still counts as a call. Avoid calls with neither `kpis` nor `kpi`: they return every KPI the company reports and can be large.
3. Follow `pagination`: while `has_more` is true, repeat the call with the same arguments and `next_cursor` as `cursor`. A response budget can cap a page below `page_size`, so never infer completeness from the item count. Each page counts as one tool call against the monthly allowance, so page only as far as the question needs.
4. Read each KPI by `label`, `definition` (may be null), `unit`, and `kind`. A `single` KPI is one series; a `mix` splits into `members` and each value names its `member`. Do not add members together or infer a total that is not returned. Compare a KPI across periods or companies only when the definitions match, because the same label can mean different things.
5. Read each value's `period`, `period_type`, `value`, and `unit`. Quote the unit exactly as returned beside the value. It can differ from the KPI-level unit and between rows (`₹ / month`, `Rs/month`) and can be null, so never assume a unit or convert silently. Keep period types apart: never compare quarter, half-year, nine-month, year-to-date, and annual values with each other.
6. Each value carries `source_type`, a short `source_quote` (up to 300 characters, sometimes a table fragment), `flags`, and a `citation`. Flags mark extraction caveats, for example `unit_unresolved`, `unit_not_on_page`, `unit_scaled`, `period_label_unverified`, `period_label_not_on_page`, `label_not_near_value`, and `conflict_resolved`. Check a flagged value against its quote and cited page before relying on it, or leave it out, and tell the user about any caveat that matters.
7. The same fact can be returned from more than one page or filing, often with different unit spellings. Prefer the cited page that matches the period, ideally an unflagged row whose quote names the KPI and period, do not double-count, and mention material disagreements between rows.
8. If `values_truncated` is true, the series is capped: narrow with `period_type`, `from_fiscal_year`, or `kpi`, and say it was capped.
9. Verify values the answer relies on with `get_document_chunks`, passing the value's `document_id` and cited page as `page_start` and `page_end`. Each call is metered, so verify what matters rather than every row. The returned document type and period name the filing for the citation.
10. Present returned values as extracted from company filings, not as audited or independently verified figures. Calculate a change only from values with the same KPI, member, period type, and unit, shown with the formula and labeled as calculated from reported figures. Never interpolate missing periods, backfill unavailable values, or derive an undisclosed residual.
11. Read `coverage_note`: extraction is not exhaustive, so an empty or partial result is not evidence that the company does not report a KPI. Fall back to `search_company_documents` for the KPI and period, then `get_document_chunks` on the cited pages, and label values taken from those passages as such.

## Public answer

Use these sections:

1. **KPI table**.
2. **Observed changes** — only source-stated or transparently calculated changes.
3. **Definitions and comparability** — definitions, units, period types, and data-quality caveats.
4. **Coverage limitations** — values extracted from filings may omit reported KPIs or periods; empty or capped results.
5. **Sources**.

Suggested table:

| KPI | Period | Reported value and unit | Source type | Caveats | Source |
|---|---|---:|---|---|---|

Do not require a document title when the returned evidence does not supply one. A citation may use the source type, fiscal period, page, and URL.

## Monetary units and currency conversion

- Convert only monetary totals. Leave per-unit, ratio, percentage, and count units (for example `₹ / month`, `%`, `stores`) exactly as returned.
- Prefer Indian rupees crores (`₹ crore`) for monetary values in tables, comparisons, calculations, and summaries, and show the reported value and unit beside any converted figure.
- Convert reported INR scales exactly: ₹1 lakh = ₹0.01 crore; ₹100 lakh = ₹1 crore; ₹1 million = ₹0.1 crore; ₹10 million = ₹1 crore; ₹1 billion = ₹100 crore.
- Preserve the source's reported amount when it is in another currency, followed by the estimate in brackets: `US$X million (≈₹Y crore)`.
- Use the best defensible period-appropriate FX rate: transaction-date rate for dated events, period-end rate for point-in-time balances, and period-average rate for revenue, costs, cash flow, or other period flows.
- Prefer an exchange rate stated in the company document. Otherwise use a reputable retrieved FX source. State the rate, date or period, source, and formula: `foreign-currency amount × INR per foreign-currency unit ÷ 10,000,000 = ₹ crore`.
- Prefix converted figures with `≈` and use sensible precision; do not imply more accuracy than the FX input supports.
- Never invent an exchange rate. If no defensible rate is available, retain the original currency and state that an INR-crore estimate could not be produced.

## Sources, citations, and boundaries

- Do not mention tool names, call order, arguments, cursors, document identifiers, schemas, or retrieval narration in the public answer.
- Cite the filing and page beside each KPI value or tightly grouped set of values from the same page. Use only returned document URLs, fiscal periods, source types, and page numbers. A returned URL can be null: cite the page without a link and do not invent a title or repair the link.
- Quote a `source_quote` only after confirming it on the cited page, and only the part that supports the value; a quote can contain other figures.
- Label calculations as model calculations; citations support their inputs, not the calculation itself.
- State when the available evidence does not support a requested trend or comparison.
- Do not provide trading instructions or personalized investment advice.

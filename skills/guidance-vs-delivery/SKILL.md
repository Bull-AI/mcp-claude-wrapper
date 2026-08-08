---
name: guidance-vs-delivery
description: Compare guidance with later company-reported outcomes.
---

# Guidance Versus Later Reported Outcomes

Use this skill when a user asks how a listed Indian company's management guidance evolved and how it compares with outcomes reported in later company documents.

## Internal workflow

1. Resolve one company with `search_companies`.
2. Use `list_document_availability` to inventory relevant transcripts, presentations, annual reports, results, and company updates. Record whether availability is truncated or incomplete.
3. Use `get_company_guidance` for the requested source-document fiscal years and quarters. These filters identify the source-document period, not the guidance target period. Follow pagination when needed.
4. Treat every `deliveries` entry as a management-reported claim only. Never use it to confirm, verify, or corroborate that earlier guidance was achieved.
5. For each guidance statement, use its citation internally with `get_document_chunks` to inspect the cited page and nearby context. Extract a metric, scope, target, unit, conditions, target period, and source date only when the document states them.
6. If the metric, basis, unit, target period, or reliable source date cannot be established, mark the item not comparable. Never infer a target period from the enclosing source fiscal year or quarter.
7. Build the earlier chronology by searching company documents for the same metric, scope, basis, and target period with `search_company_documents`, then inspect the cited passages with `get_document_chunks`.
8. Order chronology events by an authoritative document or filing date when available. Treat an upload date only as a disclosed proxy. If reliable ordering is unavailable, label the chronology provisional.
9. Use these chronology labels only when supported: introduced, reaffirmed, revised, withdrawn, or superseded. For a revision, describe it as tighter, looser, mixed, or indeterminate. Avoid “moved the goalposts” unless a cited, comparable, pre-deadline statement demonstrably eases the same target or extends its deadline.
10. Apply a time gate. A final outcome assessment requires a later company document issued after the original guidance and after the target period or milestone deadline. If the deadline has not passed, classify it as not due.
11. Search later company documents independently for the matching metric, target period, business scope, and relevant synonyms. Do not search only for management's delivery wording. Inspect every outcome passage used.
12. Test comparability: metric, entity or segment scope, period, unit or currency, and accounting or operational basis must align. Do not compare annual targets with quarterly run rates, consolidated guidance with segment results, capacity with production, order book with revenue, or absolute values with growth rates unless a cited source supplies a valid reconciliation.
13. Classify conservatively as: reported exceeded, reported met, reported missed, reported partially met, not due, not comparable, or insufficient reported evidence. “Reported partially met” is only for an explicitly composite target with some components met.
14. Never calculate an aggregate delivery or credibility score from unresolved, non-comparable, or not-due items.

## Public answer

Use these sections:

1. **Outstanding guidance** — forward-looking targets that are not yet due.
2. **Guidance chronology** — introduced, reaffirmed, revised, withdrawn, or superseded statements.
3. **Later company-reported outcomes** — separately sourced post-deadline evidence.
4. **Comparison** — a conservative assessment with both the original and later source beside it.
5. **Coverage and limitations** — unavailable periods, truncation, uncertain dates, or comparability gaps.
6. **Sources** — underlying company documents and returned source metadata.

Cite the original document beside each guidance statement, every chronology event to its own document, and the later document beside each reported outcome. Link only a real returned document URL and include a returned page when available.

State this limitation in substance:

> Outcome assessments compare management guidance with later company-reported evidence. They are not independent verification or audit conclusions. Management-reported delivery statements do not by themselves confirm that earlier guidance was achieved.

## Monetary units and currency conversion

- Prefer Indian rupees crores (`₹ crore`) for monetary values in tables, comparisons, calculations, and summaries.
- Convert reported INR scales exactly: ₹1 lakh = ₹0.01 crore; ₹100 lakh = ₹1 crore; ₹1 million = ₹0.1 crore; ₹10 million = ₹1 crore; ₹1 billion = ₹100 crore.
- Preserve the source's reported amount when it is in another currency, followed by the estimate in brackets: `US$X million (≈₹Y crore)`.
- Use the best defensible period-appropriate FX rate: transaction-date rate for dated events, period-end rate for point-in-time balances, and period-average rate for revenue, costs, cash flow, or other period flows.
- Prefer an exchange rate stated in the company document. Otherwise use a reputable retrieved FX source. State the rate, date or period, source, and formula: `foreign-currency amount × INR per foreign-currency unit ÷ 10,000,000 = ₹ crore`.
- Prefix converted figures with `≈` and use sensible precision; do not imply more accuracy than the FX input supports.
- Never invent an exchange rate. If no defensible rate is available, retain the original currency and state that an INR-crore estimate could not be produced.

## Sources, citations, and boundaries

- Tool names and opaque identifiers may be used internally but must not appear in the user-facing answer.
- Do not expose call order, arguments, cursors, document identifiers, collections, schemas, routing, or retrieval narration.
- Do not invent or repair a URL, document label, date, page number, target, metric, unit, or citation.
- If a citation element is unavailable, omit it and state the limitation when material.
- Attribute management statements explicitly. Label model interpretation as analysis rather than source fact.
- Preserve the meaning of every returned verification notice and guardrail without quoting implementation directions.
- Results are informational and are not trading instructions or personalized investment advice.

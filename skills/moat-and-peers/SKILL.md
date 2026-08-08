---
name: moat-and-peers
description: Map management-claimed advantages against listed peers.
---

# Management-Claimed Advantages and Peers

Use this skill when a user asks what competitive advantages management claims for an Indian listed company and how those claims relate to a bounded set of listed peers.

## Internal workflow

1. Resolve the subject company with `search_companies`.
2. Retrieve its sectors, industries, and company category with `get_company_classification`.
3. Retrieve a bounded peer set with `get_company_peers`; follow pagination only when the requested scope requires it.
4. Treat each peer row's `industries` as the subject company's industry bucket under which that peer was matched. Never present it as the peer's own classification.
5. Search subject-company filings, annual reports, transcripts, and presentations with `search_company_documents` for management statements about competitive advantages, distribution, brands, capabilities, customer relationships, cost position, technology, scale, or other relevant factors.
6. Inspect each cited passage with `get_document_chunks`. Record the exact claim, speaker or attribution when available, period, and document citation.
7. Label these as management-claimed advantages, not verified moats.
8. By default, use the peer set only as market context. Do not infer peer ranking, market share, similarity score, moat strength, durability, or financial superiority.
9. If the user requests an evidence-based peer comparison, select a small, disclosed peer subset and retrieve comparable company-document evidence for each peer. Compare only the same supported dimension and period.
10. If comparable peer evidence is unavailable, state that the requested stack-up cannot be established from the available evidence.

## Public answer

Use these sections:

1. **Company classification** — sector, industry, and category with returned source metadata.
2. **Management-claimed advantages** — claim, context, evidence, and document citation.
3. **Peer context** — bounded peer list without unsupported ranking.
4. **Evidence-based comparison** — only dimensions supported by comparable cited passages.
5. **Interpretation and limitations** — clearly separated model analysis, missing peer evidence, and pagination limits.
6. **Sources**.

Suggested claim table:

| Management-claimed advantage | Supporting passage | Relevant peer context | What the evidence supports | Source |
|---|---|---|---|---|

## Monetary units and currency conversion

- Prefer Indian rupees crores (`₹ crore`) for monetary values in tables, comparisons, calculations, and summaries.
- Convert reported INR scales exactly: ₹1 lakh = ₹0.01 crore; ₹100 lakh = ₹1 crore; ₹1 million = ₹0.1 crore; ₹10 million = ₹1 crore; ₹1 billion = ₹100 crore.
- Preserve the source's reported amount when it is in another currency, followed by the estimate in brackets: `US$X million (≈₹Y crore)`.
- Use the best defensible period-appropriate FX rate: transaction-date rate for dated events, period-end rate for point-in-time balances, and period-average rate for revenue, costs, cash flow, or other period flows.
- Prefer an exchange rate stated in the company document. Otherwise use a reputable retrieved FX source. State the rate, date or period, source, and formula: `foreign-currency amount × INR per foreign-currency unit ÷ 10,000,000 = ₹ crore`.
- Prefix converted figures with `≈` and use sensible precision; do not imply more accuracy than the FX input supports.
- Never invent an exchange rate. If no defensible rate is available, retain the original currency and state that an INR-crore estimate could not be produced.

## Sources, citations, and boundaries

- Do not mention tool names, call order, arguments, cursors, backend identifiers, schemas, or retrieval narration in the public answer.
- For document-backed claims, cite only returned company-document URLs and pages. Never invent missing citation elements.
- For classification, render returned source labels and `as_of` values as source metadata, not as document citations.
- The structured peer set may lack a document-level source locator. Do not fabricate one; disclose that limitation when material.
- Distinguish documented facts, management claims, and model interpretation.
- A citation supports the nearby evidence, not a broader claim that the company has a durable or superior moat.
- Do not provide trading instructions or personalized investment advice.

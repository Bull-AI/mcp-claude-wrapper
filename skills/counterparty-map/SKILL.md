---
name: counterparty-map
description: Map disclosed counterparties and verified listed matches.
---

# Disclosed Counterparty Map

Use this skill when a user asks for an Indian listed company's disclosed customers, suppliers, partners, or other counterparties and which can be matched to listed companies.

## Internal workflow

1. Resolve the subject company with `search_companies`.
2. Retrieve a bounded counterparty list with `get_company_counterparties`, preserving the returned relation and description. Follow pagination only to a disclosed cap or until exhausted.
3. Treat the list as disclosed and bounded, not a complete commercial network.
4. Inspect any returned company identity fields. A non-null public exchange identifier may support a listed-company match.
5. When identity remains unresolved, use `search_companies` to test the counterparty name. Confirm a listed match only when the legal name and returned public identifiers support an unambiguous identity match.
6. A fuzzy or similarly named result is not proof of identity. Use: confirmed listed match, possible match, listing status not established, or ambiguous.
7. Use returned NSE/BSE listing flags when available. “No match found” does not prove that an entity is unlisted.
8. Preserve the disclosed relationship type. Do not infer contract value, revenue exposure, concentration, exclusivity, dependence, ownership, importance, or whether the relationship remains active.
9. Keep similarly named legal entities separate.

## Public answer

Use these sections:

1. **Disclosed counterparties**.
2. **Confirmed listed matches**.
3. **Possible or ambiguous matches**.
4. **Coverage and limitations** — pagination, nullable citations, and non-exhaustive disclosure.
5. **Sources**.

Suggested table:

| Counterparty | Disclosed relationship | Listed-match status | Matched public identity | Match basis | Source |
|---|---|---|---|---|---|

## Monetary units and currency conversion

- Prefer Indian rupees crores (`₹ crore`) for monetary values in tables, comparisons, calculations, and summaries.
- Convert reported INR scales exactly: ₹1 lakh = ₹0.01 crore; ₹100 lakh = ₹1 crore; ₹1 million = ₹0.1 crore; ₹10 million = ₹1 crore; ₹1 billion = ₹100 crore.
- Preserve the source's reported amount when it is in another currency, followed by the estimate in brackets: `US$X million (≈₹Y crore)`.
- Use the best defensible period-appropriate FX rate: transaction-date rate for dated events, period-end rate for point-in-time balances, and period-average rate for revenue, costs, cash flow, or other period flows.
- Prefer an exchange rate stated in the company document. Otherwise use a reputable retrieved FX source. State the rate, date or period, source, and formula: `foreign-currency amount × INR per foreign-currency unit ÷ 10,000,000 = ₹ crore`.
- Prefix converted figures with `≈` and use sensible precision; do not imply more accuracy than the FX input supports.
- Never invent an exchange rate. If no defensible rate is available, retain the original currency and state that an INR-crore estimate could not be produced.

## Sources, citations, and boundaries

- Do not mention tool names, call order, arguments, cursors, opaque identifiers, schemas, or retrieval narration in the public answer.
- When a counterparty result supplies a company-document URL and page, cite that underlying document beside the disclosure.
- Citation fields may be null. Never invent a URL, page, filing, or document label.
- A citation supports the disclosed relationship only; it does not establish commercial importance or completeness.
- Clearly label model interpretation and identity-matching uncertainty.
- Do not provide trading instructions or personalized investment advice.

# Bull AI Claude Plugin

Bull AI connects Claude to company documents and data for NSE/BSE-listed Indian companies.

```text
https://www.bull-ai.in/mcp
```

The plugin connects Claude to Bull AI's eleven remote MCP tools and includes five workflow skills for using them effectively. The MCP server remains the canonical source of the tools' names, descriptions, schemas, annotations, and results. Claude discovers that tool surface from the server when it connects; this repository does not duplicate or redefine it.

The remote server owns OAuth discovery, dynamic client registration, consent, scopes, rate limits, billing, and the public tool schemas. This package contains only the public connection configuration, workflow skills, documentation, and security guidance—no credentials, company data, private application code, or non-public service configuration.

## Remote MCP tools

1. `search_companies`
2. `search_company_documents`
3. `list_document_availability`
4. `get_document_chunks`
5. `get_company_classification`
6. `get_company_peers`
7. `get_company_market_transactions`
8. `get_company_corporate_actions`
9. `get_company_guidance`
10. `get_company_counterparties`
11. `get_mcp_usage`

## Included skills

- **Guidance versus delivery** — compare management guidance with later company-reported outcomes while preserving temporal order, comparability limits, and paired sources.
- **Moat and peers** — map management-claimed competitive advantages against a bounded listed-peer context without treating claims as verified moats.
- **Revenue segmentation** — extract reported revenue segments from company documents and calculate mix changes only from cited operands.
- **Insider actions** — place bulk, block, or insider market transactions beside corporate-action timing without implying causation.
- **Counterparty map** — map disclosed counterparties and identify listed-company matches only when public identity evidence supports them.

The skills teach Claude how to select and chain Bull AI's existing remote tools. They do not replace or override the server's canonical tool descriptions.

## Sources and output conventions

Public answers cite underlying company documents or returned source metadata where available, disclose missing or bounded coverage, and separate reported facts, management statements, model calculations, and interpretation. Tool-routing details, opaque identifiers, cursors, and backend execution narration stay out of normal answers.

Monetary values are normalized to Indian rupees crores (`₹ crore`) where possible. Foreign-currency amounts retain the reported value followed by an approximate ₹-crore conversion in brackets, with the exchange-rate source, date or period, and calculation basis disclosed.

Results are informational. Bull AI does not support trading, order placement, money movement, portfolio changes, or personalized investment advice.

## Authentication and billing

Connect through the normal OAuth flow. The canonical resource is exactly:

```text
https://www.bull-ai.in/mcp
```

Do not substitute a local, staging, legacy, or origin-service URL.

`search_companies`, `list_document_availability`, and `get_mcp_usage` are free utilities. Each of the remaining eight tools counts as one tool call against the monthly allowance after a successful result. Authentication, availability, and rate limits apply to every tool.

`get_mcp_usage` is an authenticated, read-only utility that reports the current billing-cycle allowance, usage, reset time, and per-tool breakdown. It remains available when the metered allowance is exhausted and links to [MCP usage](https://www.bull-ai.in/mcp/usage). It requires the least-privilege `mcp:usage:read` scope. Connections authorized before this tool was added must grant the new scope before the tool can run. Claude should request incremental consent; reconnect only if it cannot complete that authorization step-up.

## Install

Load the released plugin for the current Claude Code session:

```bash
git clone https://github.com/Bull-AI/mcp-claude-wrapper.git
cd mcp-claude-wrapper
git checkout v0.3.1
claude --plugin-dir .
```

Complete the normal OAuth flow when Claude Code prompts for it. The plugin is session-scoped and adds no local proxy, credentials, or alternate endpoint.

## Support

- [Privacy](https://www.bull-ai.in/privacy)
- [Terms](https://www.bull-ai.in/terms)
- [Support and security contact](https://www.bull-ai.in/contact)

Do not include access tokens, authorization codes, PKCE values, cookies, or customer data in support requests.

## Validation

```bash
claude plugin validate .
```

# Bull AI Market Research Claude Wrapper

This wrapper connects Claude to Bull AI's remote MCP server for authenticated,
read-focused research on listed Indian companies and their investor documents.

```text
https://www.bull-ai.in/mcp
```

The remote server owns OAuth discovery, dynamic client registration, consent,
scopes, rate limits, billing, and the public tool schemas. This package contains
no local server, credentials, company data, private application code, or
deployment configuration.

## Catalog

The remote server exposes these ten tools:

1. `search_companies`
2. `search_company_documents`
3. `list_document_availability`
4. `get_document_chunks`
5. `get_company_guidance`
6. `get_company_classification`
7. `get_company_peers`
8. `get_company_market_transactions`
9. `get_company_corporate_actions`
10. `get_company_counterparties`

The wrapper adds no tools or skills. It does not support trading, order
placement, money movement, portfolio changes, or personalized investment advice.

## Authentication and Billing

Connect through the normal OAuth flow. The canonical resource is exactly
`https://www.bull-ai.in/mcp`; do not substitute a local, staging, legacy, or
origin-service URL.

`search_companies` and `list_document_availability` are free utilities. The
remaining eight tools cost one credit after a successful result. Authentication,
availability, and rate limits apply to every tool.

## Install

Load the released wrapper for the current Claude Code session:

```bash
git clone https://github.com/Bull-AI/mcp-claude-wrapper.git
cd mcp-claude-wrapper
git checkout v0.1.4
claude --plugin-dir .
```

Complete the normal OAuth flow when Claude Code prompts for it. The wrapper is
session-scoped: it adds no local proxy, credentials, or alternate endpoint.

## Support

- [Privacy](https://www.bull-ai.in/privacy)
- [Terms](https://www.bull-ai.in/terms)
- [Support and security contact](https://www.bull-ai.in/contact)

Do not include access tokens, authorization codes, PKCE values, cookies, or
customer data in support requests.

## Validation

```bash
claude plugin validate .
```

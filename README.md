# Seller Agent v1.2

**Discover original Chinese-language book reviews for free; purchase one complete review for $0.95 USDC on Base mainnet.**

Seller Agent is an AI-agent-facing content service built with the Model Context Protocol (MCP) and x402 v2 HTTP payments. The public MCP tools expose catalog metadata and purchase instructions, not the private article text.

## Live endpoints

- **MCP server:** `https://agent-content-store.sthinking.workers.dev/mcp`
- **Paid full-article API:** `POST https://agent-content-store.sthinking.workers.dev/api/purchase-article`

## Catalog

| ID | Reviewed book | Author of reviewed book | Topics |
|---|---|---|---|
| `review-001` | *The Nurture Assumption* | Judith Rich Harris | Child development, peers, heredity |
| `review-002` | *Guns, Germs, and Steel* | Jared Diamond | Geography, history, inequality |
| `review-003` | *Narrative Economics* | Robert J. Shiller | Economic narratives, Bitcoin, automation |
| `review-004` | *The Deficit Myth* | Stephanie Kelton | Modern Monetary Theory, deficits, inflation |
| `review-005` | *The Language Instinct* | Steven Pinker | Language, cognition, evolution |
| `review-006` | *The Rise and Fall of American Growth* | Robert J. Gordon | US productivity, innovation, economic history |

All six reviews are original **Chinese-language reviews** of the books listed above. They are not the books themselves. Free discovery metadata supports Chinese and English queries.

## Free MCP tools

- `search_library` — Search the public catalog by keywords or natural-language questions; an empty query lists available items.
- `get_purchase_instructions` — Get the paid API request format and payment instructions for a selected `content_id`.

MCP does not deliver private full-article text. Clients must support connecting to a remote MCP endpoint to use these tools.

## Buy a full article

Make a JSON POST request (example):

```bash
curl -i -X POST "https://agent-content-store.sthinking.workers.dev/api/purchase-article" \
  -H "Content-Type: application/json" \
  -d '{"content_id":"review-003"}'
```

An **unpaid** request returns HTTP `402` with an x402 v2 `PAYMENT-REQUIRED` header. A compatible x402 client can validate the payment requirements, obtain buyer authorization, and retry the same request with a valid payment proof.

**Payment parameters:**

| Parameter | Value |
|---|---|
| Price | **$0.95 USDC per complete article** |
| Network | Base mainnet (`eip155:8453`) |
| Protocol | x402 v2 |
| Scheme | `exact` |
| Token | USDC (Base mainnet) |
| USDC contract | `0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913` |
| Amount | `950000` USDC base units |

The `PAYMENT-REQUIRED` response is the authoritative live payment offer. Buyers should verify network, token, amount, and recipient against trusted expectations before signing. Do not send private keys to this service.

A successful paid request returns JSON with `success: true` and the full original Markdown in `article.content` (along with the article's ID, title, language, and source-book metadata).

**Purchase semantics:** One successful x402 payment authorizes one complete-article HTTP response. Buyers can save the text they receive. This is not a subscription, an account-based permanent entitlement, or DRM.

If a payment attempt times out or fails ambiguously, check settlement status before retrying to avoid accidental duplicate charges.

## Service status and scope

This is an early public release with six reviews. The legacy `/api/query-content` excerpt endpoint has been retired. Article discovery is free; complete articles are paid. This repository contains **documentation only**—no private reviews, wallets, or backend source code.

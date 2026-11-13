---
name: treg
description: Agent Skill for accessing 4,000+ endpoints and 120+ providers via Treg (OpenRouter for agent tools and data) CLI and REST API. Use when needing structured external or live data (SEO/SERP, social platforms, people/company enrichment, ads), image/video/audio generation, or when web-search-and-crawl is insufficient.
---

# Treg

Unified pay-as-you-go access to 4,100+ external APIs and generative models (`https://treg.to`) using a single token, with provider credentials injected server-side.

## When to Use vs. Other Skills

1. Private or authenticated workspaces & specialized pipelines (`gws`, `linear`, `youtrack`, `mineru`, `bibigpt`):
   - Always use those dedicated skills first (direct OAuth/keys and deeper native integration).
2. Structured platform data, SEO/growth, enrichment, ads, and media generation:
   - Use `treg` first (X, TikTok, Instagram, LinkedIn, Reddit, YouTube stats, Google SERP/Maps, SEO/backlinks, people/company enrichment, ad libraries, image/video/voice/music generation).
3. General web search and page crawling (`web-search-and-crawl` vs. `treg`):
   - Start with `web-search-and-crawl` for standard webpage reading and general/Chinese internet search.
   - Use `treg` (or combine both) when needing broader search providers, live Google SERP/Maps, structured social/e-commerce data, or when standard crawl is blocked or thin.

## Prerequisites

1. Prefer `treg` CLI in `$PATH` (always prefix with `TREG_TELEMETRY=0`); otherwise fall back to REST API (`curl`).
2. Requires `TREG_TOKEN` env var or `~/.treg/config.json`. If missing, ask the user for `TREG_TOKEN` or to run `treg login`.
3. Check balance before metered calls:

   ```sh
   TREG_TELEMETRY=0 treg balance --json | jq '{balance_usd, in_flight_micro}'
   ```

## Usage (Discover -> Inspect -> Call)

### CLI (Preferred)

Run `TREG_TELEMETRY=0 treg <command> --help` for full flags.

```sh
TREG_TELEMETRY=0 treg catalog search "<job>"
TREG_TELEMETRY=0 treg catalog get <endpoint_id>   # shows params, price, reliability, and ready call command
TREG_TELEMETRY=0 treg call <endpoint_id> --query key=value --header "X-Treg-Route-Max-Cost: 0.05"
```

### REST API Fallback

Use `openapi-inspection` on `https://treg.to/openapi.json`, or call directly with `-H "X-Treg-Token: ${TREG_TOKEN}"`:

- Discover (no auth): `GET https://treg.to/catalog/search?q=<job>&engine=agent`
- Inspect (no auth): `GET https://treg.to/catalog/endpoints/<endpoint_id>`
- Call: `GET/POST https://treg.to/call/<endpoint_id>` (cost returned in `X-Treg-Cost-Micro` header)

## Notes

- Prefer routed `treg.<capability>` endpoints (e.g., `treg.google.serp.organic`, `treg.people.email.find`) for common jobs unless provider-specific fields are needed.
- Read `https://treg.to/skill.md` for detailed guidance (provider selection, waterfall headers, async `--await`, idempotency, and error handling).

### Cost & Safety Rules

- Spending threshold ($0.05 USD):
  - Single, read-only calls with estimated cost <= $0.05 USD may run automatically (pass `X-Treg-Route-Max-Cost: 0.05` when supported).
  - Quote the estimated price and confirm with the user before:
    1. Any single call estimated > $0.05 USD
    2. Any generative media task (`video-gen`, `image-gen`, `voice-gen`, `music-gen`)
    3. Any batch/loop over multiple inputs (state expected total cost)
    4. Any non-read-only operation (posting, mutating external resources, or changing team settings)
- Always report the settled cost after metered calls (`treg` stderr or `X-Treg-Cost-Micro`, where 1,000,000 micro-USD = $1.00).
- Never retry a provider `4xx` (except `429`) on another provider; on timeouts, retry with the same `Idempotency-Key`.
- Never expose `TREG_TOKEN` or `~/.treg/config.json`.
- Never run `treg skill bootstrap`, `treg mcp install`, or `treg onboard` (if triggered, delete any generated skill/MCP files immediately).

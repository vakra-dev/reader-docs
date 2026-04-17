# reader-docs

Documentation for [Reader](https://reader.dev), the content extraction API for LLMs. Built with [Mintlify](https://mintlify.com).

## Structure

```
reader-docs/
├── mint.json                 # Navigation, theme, tabs, and API playground config
├── openapi.json              # Generated from reader-api. DO NOT edit by hand
├── home/
│   ├── introduction.mdx
│   ├── quickstart.mdx
│   ├── concepts/             # 11 concept pages (read primitive, proxy modes, errors, …)
│   └── guides/               # 31 guide pages (getting-started, async, production, …)
├── api-reference/            # 10 MDX shells bound to openapi.json operations
├── sdk/                      # JavaScript / Python SDK reference
└── self-hosted/              # Separate section for the self-hosted flavor. The
                              # ONLY place internal details like the scraping engine
                              # and proxy tiers may appear. Cloud docs stay opaque.
```

## Local development

```bash
# One-time: install Mintlify CLI
npm i -g mintlify

# Start the dev server with live reload
mintlify dev --port 6005
```

Open http://localhost:6005. Edits to MDX files hot-reload automatically.

**Note on OpenAPI changes:** Mintlify's dev server caches `openapi.json` aggressively. After regenerating the spec (see below) you usually need to stop `mintlify dev` and restart it for the new operation bodies / examples to show up in the API playground.

## Regenerating the OpenAPI spec

`openapi.json` is generated from `reader-api/src/openapi/index.ts`, the source of truth for request/response shapes, error examples, and playground metadata. To regenerate after an API change:

```bash
cd ../reader-api
npm run docs:gen
```

That writes a fresh `reader-docs/openapi.json`. Commit it alongside the reader-api changes so the docs preview matches.

## Content rules

- **Cloud docs are opaque.** Pages under `home/` and `api-reference/` must not mention internal implementation: no `datacenter`/`residential`, no engine internals, no proxy provider names, no "browser pool". The abstract vocabulary is `standard`/`stealth`/`auto` for proxy modes and plain English for everything else.
- **Self-hosted docs are transparent.** Pages under `self-hosted/` exist specifically so operators can understand the infrastructure they're running. Use the real names there.
- **API code samples must match the v0.2 SDK shape.** Results are discriminated unions: `if (result.kind === "scrape") result.data.markdown`. Jobs return `result.data.results`, not `result.job.data`. See existing pages under `sdk/` and `home/guides/` for reference patterns.
- **Error codes are lowercase snake_case** (`rate_limited`, `insufficient_credits`), not `UPPER_SNAKE_CASE`.

## Deploying

Mintlify auto-deploys on push to the default branch. Check the Mintlify dashboard for deploy status and preview URLs.

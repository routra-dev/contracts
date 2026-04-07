# routra/contracts

Shared API contracts for Routra - OpenAPI spec, routing policy schema, and proto definitions.

This is the **single source of truth** for all client codegen. Changes here trigger automated PRs in SDK repos.

## Contents

```
openapi/routra.yaml       OpenAPI 3.1 spec - all proxy + admin API endpoints
policy/schema.json        JSON Schema for routra.yaml routing policy files
policy/examples/          Example policies: cheapest, balanced, gdpr-eu, dpdp-india, batch
proto/                    gRPC proto definitions (future internal service split)
```

## Usage

### Validate a policy file

```bash
npx @stoplight/spectral-cli lint --ruleset policy/schema.json your-routra.yaml
```

### Codegen

```bash
# TypeScript types
bunx openapi-typescript openapi/routra.yaml -o types.gen.ts

# Python client
uvx openapi-python-client generate --path openapi/routra.yaml

# Go client
go run github.com/deepmap/oapi-codegen/v2/cmd/oapi-codegen@latest --config oapi-codegen.yaml openapi/routra.yaml
```

## Policy File Format

```yaml
version: "1"

policies:
  default:
    strategy: cheapest   # cheapest | balanced | fastest
    fallback: true

  production:
    strategy: balanced
    providers: [coreweave, lambda]
    min_uptime_pct: 99.5

  gdpr-eu:
    strategy: cheapest
    regions: [eu-west, eu-central]   # GDPR data residency

  dpdp-india:
    strategy: cheapest
    regions: [in-west, in-central]   # DPDP compliance
```

Push to your workspace: `routra policy push routra.yaml`
Apply per-request: `X-Routra-Policy: gdpr-eu` header

# Regional Monitoring

Atalaya can run health checks from specific Cloudflare edge locations using Durable Objects.
This is useful when you need to confirm that a service looks healthy from different parts of
the world.

Use cases:

- Verify CDN behavior from different regions
- Test geo-blocking or geo-routing rules
- Measure latency spread across continents
- Catch regional outages that a single-location check would miss

## How it works

When a monitor includes a `region` field, the Worker creates a Durable Object in that region,
runs the check from there, and returns the result. The Durable Object is torn down afterward.
If the regional check fails, the Worker falls back to running the check from its own default
region.

## Valid region codes

| Code | Region |
|---|---|
| `weur` | Western Europe |
| `enam` | Eastern North America |
| `wnam` | Western North America |
| `apac` | Asia Pacific |
| `eeur` | Eastern Europe |
| `oc` | Oceania |
| `safr` | South Africa |
| `me` | Middle East |
| `sam` | South America |

## Example

```yaml
monitors:
  - name: 'api-eu'
    type: http
    target: 'https://api.example.com/health'
    region: 'weur'
    method: GET
    expected_status: 200
    alerts: ['default']

  - name: 'api-us'
    type: http
    target: 'https://api.example.com/health'
    region: 'enam'
    method: GET
    expected_status: 200
    alerts: ['default']
```

## Required bindings

Regional monitoring needs the Durable Objects bindings and migration block in `wrangler.toml`.
The example config (`wrangler.example.toml`) already includes these:

```toml
[[durable_objects.bindings]]
name = "REGIONAL_CHECKER_DO"
class_name = "RegionalChecker"

[[migrations]]
tag = "v1"
new_sqlite_classes = ["RegionalChecker"]
```

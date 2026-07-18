# Configuration

Everything lives in `wrangler.toml`, inside the `MONITORS_CONFIG` variable (a YAML string
embedded in the TOML file). There is no separate config file — Cloudflare Workers pull their
configuration from `wrangler.toml` at deploy time.

## Global settings

These apply to all monitors unless a monitor overrides them:

```yaml
settings:
  title: 'Atalaya Uptime Monitor'
  default_retries: 2
  default_retry_delay_ms: 1000
  default_timeout_ms: 5000
  default_failure_threshold: 2
```

| Setting | Default | What it does |
|---|---|---|
| `title` | `Atalaya Uptime Monitor` | Status page title |
| `default_retries` | `2` | Retry attempts on failure |
| `default_retry_delay_ms` | `1000` | Milliseconds between retries |
| `default_timeout_ms` | `5000` | Request timeout in milliseconds |
| `default_failure_threshold` | `2` | Consecutive failures before alerting |

## Per-monitor overrides

Each monitor can override any of the global defaults:

```yaml
- name: 'critical-api'
  type: http
  target: 'https://api.example.com/health'
  timeout_ms: 10000
  retries: 3
  retry_delay_ms: 500
  failure_threshold: 1
  alerts: ['default']
```

## Monitor types

### HTTP

```yaml
- name: 'api-health'
  type: http
  target: 'https://api.example.com/health'
  method: GET
  expected_status: 200
  expected_body_contains: 'OK'          # optional
  headers:
    Authorization: 'Bearer ${API_TOKEN}'
    Accept: 'application/json'
  alerts: ['default']
```

All HTTP checks send `User-Agent: atalaya-uptime`. Monitor-level headers are merged with it;
setting your own `User-Agent` header overrides the default.

### TCP

```yaml
- name: 'database'
  type: tcp
  target: 'db.example.com:5432'
  alerts: ['default']
```

### DNS

```yaml
- name: 'dns-check'
  type: dns
  target: 'example.com'
  record_type: A
  expected_values: ['93.184.216.34']
  alerts: ['default']
```

## Alerts

Alerts are defined as a top-level array. Right now only webhooks work.

```yaml
alerts:
  - name: 'slack'
    type: webhook
    url: 'https://hooks.slack.com/services/xxx'
    method: POST
    headers:
      Content-Type: 'application/json'
    body_template: |
      {"text": "{{monitor.name}} is {{status.current}}"}
```

Template variables available in `body_template`:

- `{{event}}` — type of alert event
- `{{monitor.name}}`, `{{monitor.type}}`, `{{monitor.target}}`
- `{{status.current}}`, `{{status.previous}}`, `{{status.consecutive_failures}}`,
  `{{status.last_status_change}}`, `{{status.downtime_duration_seconds}}`
- `{{check.error}}`, `{{check.timestamp}}`, `{{check.response_time_ms}}`,
  `{{check.attempts}}`

!!! note "Template vs. secret syntax"
    `{{var}}` is for alert body templates (resolved per-alert).
    `${VAR}` is for secrets (resolved at startup). Do not mix them up.

## Status page

The status page is an Astro 6 SSR site in the `status-page/` directory. It reads D1 directly,
so it works inside the same Worker with no extra bindings.

Enable it with Wrangler secrets on the Worker:

```bash
# Set credentials for basic auth
wrangler secret put STATUS_USERNAME
wrangler secret put STATUS_PASSWORD

# Or make the page public
wrangler secret put STATUS_PUBLIC
```

Access rules, in order:

1. If `STATUS_PUBLIC` is `"true"`, the page is open.
2. If credentials are set, basic auth is required.
3. Otherwise — 403.

### Custom banner

Replace the title text with an image:

```toml
[vars]
STATUS_BANNER_URL = "https://example.com/banner.png"
STATUS_BANNER_LINK = "https://example.com"   # optional
```

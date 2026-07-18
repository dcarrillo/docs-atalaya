# Secret Management

Atalaya uses Cloudflare's built-in secret storage. Secrets are never embedded in the config
file — they live encrypted at rest in Cloudflare and are injected at runtime.

## Adding a secret

```bash
wrangler secret put SECRET_NAME
```

You will be prompted for the value.

## Using secrets in configuration

Reference secrets in `wrangler.toml` with `${SECRET_NAME}`:

```yaml
alerts:
  - name: 'slack'
    type: webhook
    url: 'https://hooks.slack.com/services/${SLACK_PATH}'
    method: POST
    headers:
      Authorization: 'Bearer ${WEBHOOK_TOKEN}'
    body_template: |
      {"text": "{{monitor.name}} is {{status.current}}"}

monitors:
  - name: 'private-api'
    type: http
    target: 'https://api.example.com/health'
    method: GET
    expected_status: 200
    headers:
      Authorization: 'Bearer ${API_KEY}'
    webhooks: ['slack']
```

## Security details

- Secrets are never logged or exposed in check results.
- An unresolved `${VAR}` placeholder stays as-is in the output. This is intentional — it makes
  it obvious when a secret is missing instead of silently sending an empty string.
- Worker secrets are encrypted at rest by Cloudflare.
- The Worker resolves secrets once at startup. If you change a secret, you need to redeploy.

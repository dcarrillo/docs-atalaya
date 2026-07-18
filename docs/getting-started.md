# Getting Started

## What you need

- Node.js 22 or later
- [Wrangler](https://developers.cloudflare.com/workers/wrangler/install-and-update/) CLI
- A Cloudflare account with Workers, D1, and (optionally) Durable Objects enabled

## Install and configure

1.  Clone the repo and install dependencies:

    ```bash
    pnpm install
    ```

2.  Copy the example config:

    ```bash
    cp wrangler.example.toml wrangler.toml
    ```

3.  Create the D1 database:

    ```bash
    wrangler d1 create atalaya
    ```

    This prints a `database_id`. Paste it into `wrangler.toml` under `[[d1_databases]]`.

4.  Apply the migrations:

    ```bash
    wrangler d1 migrations apply atalaya --remote
    ```

5.  Edit `wrangler.toml` to define your monitors and alerts. See the
    [Configuration](configuration.md) page for details.

    If you want regional monitoring, make sure the Durable Objects bindings and migration block
    from the example file are present. The status page is off by default — see the configuration
    docs to turn it on.

6.  Deploy:

    ```bash
    pnpm deploy
    ```

    This builds the Astro status page and deploys the Worker with static assets in one step.

## Custom domain

If you have a domain registered in Cloudflare, add a route block to `wrangler.toml`:

```toml
[[routes]]
pattern = "status.yourdomain.com"
custom_domain = true
```

## Quick health check

Once deployed, your Worker is reachable at the route you configured or the default
`workers.dev` subdomain. The JSON API lives at `/api/status`.

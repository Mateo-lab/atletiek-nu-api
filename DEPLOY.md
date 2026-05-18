# Deploying the atletiek-nu-api Cloudflare Worker

## Prerequisites

- [Rust](https://rustup.rs/) with the `wasm32-unknown-unknown` target:
  ```
  rustup target add wasm32-unknown-unknown
  ```
- Node.js (v18+)
- [Wrangler](https://developers.cloudflare.com/workers/wrangler/) CLI:
  ```
  npm install -g wrangler
  ```
- A Cloudflare account (free tier is sufficient)
- On Windows: Visual Studio Build Tools with the C++ workload

## First-time setup

1. Authenticate with Cloudflare:
   ```
   wrangler login
   ```

2. Verify your account:
   ```
   wrangler whoami
   ```

3. If needed, add your `account_id` to `api-cfworker/wrangler.toml`.

## Deploy

```
cd api-cfworker
wrangler deploy
```

The Worker will be available at `https://athleticresults-api.<your-subdomain>.workers.dev`.

## Verify

```bash
# Competition results (should return JSON)
curl https://athleticresults-api.<your-subdomain>.workers.dev/competitions/results/2065498

# Athlete profile (returns 503 if rate-limited, instead of crashing)
curl -i https://athleticresults-api.<your-subdomain>.workers.dev/athletes/profile/1146851

# Athlete search
curl https://athleticresults-api.<your-subdomain>.workers.dev/athletes/search/Borlée
```

## Logs

```
cd api-cfworker
wrangler tail
```

## Notes

- Free tier: 100k requests/day, 10ms CPU time (enough for typical usage).
- The Worker has a 30s wall-clock timeout. Fetching many competitions at once can be tight.
- If athletics.app serves an anti-bot challenge page, the API returns HTTP 503 with `Retry-After: 300`.

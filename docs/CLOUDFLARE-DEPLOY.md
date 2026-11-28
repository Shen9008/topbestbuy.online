# Cloudflare Pages deployment

Static Astro output lives in `./dist`. Production hosting is **Cloudflare Pages** (not a Worker).

## Path A — Git-connected Pages (recommended)

1. **Workers & Pages** → **Create** → **Pages** → **Connect to Git**.
2. Select **Shen9008/topbestbuy.online** and branch **`main`**.
3. Apply build settings from **[`CLOUDFLARE-PAGES-SETTINGS.md`](./CLOUDFLARE-PAGES-SETTINGS.md)** (especially an **empty deploy command**).
4. Add custom domains **topbestbuy.online** and **www.topbestbuy.online** when ready. WWW → apex rules are in `public/_redirects`.

## Path B — Wrangler CLI (local or ad hoc)

Requires `CLOUDFLARE_API_TOKEN` with Pages edit permission and your account ID.

```bash
npm ci
npm run build
npx wrangler pages deploy ./dist --project-name=topbestbuy
```

Do not use `npx wrangler deploy` for this static site.

## Path C — GitHub Actions (optional)

This repo runs **CI** on push/PR (lint, check, build) via `.github/workflows/ci.yml`. It does not deploy by default. If you add a deploy workflow, use `wrangler pages deploy`, not `wrangler deploy`.

## Troubleshooting

| Symptom | Likely cause | Fix |
|---------|----------------|-----|
| Build OK, deploy fails with Wrangler / Worker errors | Deploy command set to `npx wrangler deploy` | Clear **Deploy command**; see [`CLOUDFLARE-PAGES-SETTINGS.md`](./CLOUDFLARE-PAGES-SETTINGS.md) |
| Wrong Node behavior | Default Pages Node newer than local | Set `NODE_VERSION=22` in Pages env vars |
| www not redirecting | Domain not on Pages or `_redirects` not deployed | Attach domain; verify `public/_redirects` in build output |
# Cloudflare Pages — TopBestBuy

Production domain: **topbestbuy.online** (apex). **www** redirects to apex via `public/_redirects`.

| Setting | Value |
|---------|--------|
| Repository | [Shen9008/topbestbuy.com](https://github.com/Shen9008/topbestbuy.com) |
| Production branch | `main` |
| Build command | `npm run build` |
| Build output | `dist` |
| Node.js | **22** |
| Pages project name | `topbestbuy` (see `wrangler.jsonc`; name is independent of the custom domain) |

---

## Path A — Git-connected Pages (recommended)

Connects Cloudflare to GitHub so every push to `main` builds and deploys automatically.

### 1. Create the Pages project

1. Open [Cloudflare Dashboard](https://dash.cloudflare.com) → **Workers & Pages** → **Create** → **Pages** → **Connect to Git**.
2. Authorize GitHub if prompted and select **Shen9008/topbestbuy.com**.
3. **Production branch:** `main`
4. **Framework preset:** None (or Astro if offered; settings below are authoritative).
5. **Build settings:**
   - Build command: `npm run build`
   - Build output directory: `dist`
   - Root directory: `/` (repository root)
6. **Environment variables** (optional for static stub): none required for Phase 1.
7. Under **Build configuration** → set **Node.js version** to **22** (or add env `NODE_VERSION=22`).

### 2. First deploy

Save and deploy. The first build should match local: `npm ci` (Pages default) + `npm run build` → `./dist`.

Preview URL format: `https://<hash>.topbestbuy.pages.dev` until a custom domain is active.

### 3. Custom domains

In the project → **Custom domains**:

1. Add **topbestbuy.online** (apex).
2. Add **www.topbestbuy.online**.
3. For **www**, either:
   - Rely on site rules in `public/_redirects` (www → apex), and/or
   - Set a Cloudflare **Bulk redirect** or **Redirect Rule**: `www.topbestbuy.online/*` → `https://topbestbuy.online/$1` (301).

### 4. DNS (if the domain is not already on this Cloudflare account)

1. Add site **topbestbuy.online** to Cloudflare (or transfer DNS only).
2. At the registrar, point nameservers to Cloudflare’s assigned pair.
3. After the zone is active, Pages custom-domain setup will suggest the required **CNAME** / apex records (often CNAME flattening for apex).

---

## Path B — Wrangler direct upload

For one-off deploys from a machine with API access (CI or laptop).

### Prerequisites

- [API token](https://developers.cloudflare.com/fundamentals/api/get-started/create-token/) with **Cloudflare Pages — Edit** (and account read).
- `CLOUDFLARE_API_TOKEN` set in the environment (do not commit tokens).

Optional: `CLOUDFLARE_ACCOUNT_ID` if you manage multiple accounts.

### Commands

```bash
npm ci
npm run build
npx wrangler pages project list
```

Create the project once if missing:

```bash
npx wrangler pages project create topbestbuy --production-branch=main
```

Deploy:

```bash
npx wrangler pages deploy dist --project-name=topbestbuy --commit-dirty=true
```

Attach custom domains in the dashboard (Path A, step 3) if not already configured.

---

## Path C — GitHub Actions (optional)

Workflow: [`.github/workflows/deploy-pages.yml`](../.github/workflows/deploy-pages.yml)

- **Manual:** Actions → **Deploy Cloudflare Pages** → **Run workflow** (requires repo secrets).
- **Automatic on `main`:** runs only when secrets `CLOUDFLARE_API_TOKEN` and `CLOUDFLARE_ACCOUNT_ID` are set (otherwise the job is skipped).

### Repo secrets (Settings → Secrets and variables → Actions)

| Secret | Purpose |
|--------|---------|
| `CLOUDFLARE_API_TOKEN` | Wrangler authentication |
| `CLOUDFLARE_ACCOUNT_ID` | Target account |

---

## User-only dashboard checklist

Complete these in Cloudflare (and your registrar) — not automatable from the repo alone:

- [ ] Cloudflare account has GitHub access to **Shen9008/topbestbuy.com**
- [ ] Pages project **topbestbuy** exists and production branch is **main**
- [ ] Build: `npm run build`, output **dist**, Node **22**
- [ ] Zone **topbestbuy.online** is on the same account (or DNS-only) with correct nameservers
- [ ] Custom domain **topbestbuy.online** shows **Active** on the Pages project
- [ ] **www.topbestbuy.online** added; redirect to apex verified (curl or browser)
- [ ] SSL/TLS mode **Full (strict)** once origin is Pages (default for Pages custom domains)
- [ ] (Optional) GitHub Actions secrets for Path C

---

## Local verification

```bash
npm ci
npm run build
ls dist
```

`public/_redirects` includes:

```
https://www.topbestbuy.online/* https://topbestbuy.online/:splat 301
http://www.topbestbuy.online/* https://topbestbuy.online/:splat 301
```

---

## Troubleshooting

| Issue | Check |
|-------|--------|
| Build fails on Pages | Match Node 22; run `npm run check` and `npm run lint` locally |
| 404 on deep links | Astro static output; ensure `dist` is the output dir, not `.astro` |
| www not redirecting | Custom domain + `_redirects`; purge cache after deploy |
| Wrangler “non-interactive” error | Set `CLOUDFLARE_API_TOKEN` |
# Cloudflare Pages — Git-connected build settings

Use these values when the project is connected to **GitHub** (`Shen9008/topbestbuy.online`) under **Workers & Pages → your project → Settings → Builds & deployments**.

> [!IMPORTANT]
> **Dashboard build & deploy (static Astro site)**
>
> | Setting | Value |
> |---------|--------|
> | **Build command** | `npm run build` |
> | **Build output directory** | `dist` |
> | **Deploy command** | **Leave empty** (none) |
>
> Cloudflare Pages **publishes the contents of `dist` automatically** after a successful build. Do **not** set the deploy command to `npx wrangler deploy` — that is for **Workers**, not Git-connected static Pages. Using it causes the build to succeed and the **deploy step to fail** (no Worker entrypoint in this repo).
>
> Optional environment variable (recommended):
>
> - `NODE_VERSION` = `22` — matches `package.json` `"engines": { "node": ">=22" }`. Pages may default to a newer Node (e.g. 24); pinning 22 avoids subtle toolchain mismatches.

## After you change settings

1. Save settings in the dashboard.
2. **Retry deployment** or push a new commit to `main`.
3. Confirm the deployment log shows build only (no separate Wrangler Worker deploy).

## Local / CLI deploy (not the Git deploy command)

For manual uploads from your machine, use Wrangler **Pages** deploy (see [`CLOUDFLARE-DEPLOY.md`](./CLOUDFLARE-DEPLOY.md)), not `wrangler deploy`:

```bash
npm run build
npx wrangler pages deploy ./dist --project-name=topbestbuy
```

`wrangler.jsonc` in the repo root is for that CLI flow (`pages_build_output_dir`), **not** for the Git-connected **Deploy command** field.
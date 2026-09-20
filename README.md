# GitHub Pages route — homepage + privacy policy for the OAuth consent screen

**Account confirmed: `henryc444`** (checked live 2026-09-20 — the account exists; the
`henryc444.github.io` repo does **not** exist yet, and nothing is being served at
`https://henryc444.github.io/`). So step 1 below is the real work.

Two files, no build step, no dependencies:

| File | Becomes |
|---|---|
| `index.html` | `https://henryc444.github.io/` |
| `privacy.html` | `https://henryc444.github.io/privacy.html` |

## Why this is needed

Google will not let an **External** app switch to **In production** until Branding has a
**homepage URL** and a **privacy policy URL**, and the **Authorized domains** field then has to
cover their hostname. The requirement is not shown on the page — it appears in the hover tooltip
of the greyed-out *Publish app* button.

## Push it (replace `<USERNAME>` with your GitHub username)

```bash
cd ~/Documents/vault-personal/00-Reference/github-pages-oauth

git init -b main
git add index.html privacy.html
git commit -m "OAuth branding: homepage + privacy policy"

# option A — the simple way (repo named <USERNAME>.github.io, served at the root)
gh repo create <USERNAME>.github.io --public --source=. --push

# option B — plain git, no gh CLI
git remote add origin https://github.com/<USERNAME>/<USERNAME>.github.io.git
git push -u origin main
```

Then: repo → **Settings → Pages** → Source = *Deploy from a branch*, Branch = `main`, Folder =
`/ (root)` → Save. First build takes 1–2 minutes.

Verify with `curl -I https://<USERNAME>.github.io/` — expect `HTTP/2 200`.

## Then in the Google Cloud console

Project `monday-hermes` → **Google Auth Platform → 品牌 / Branding**
(`https://console.cloud.google.com/auth/branding?project=monday-hermes`):

| Field | Value |
|---|---|
| Application home page | `https://<USERNAME>.github.io/` |
| Application privacy policy link | `https://<USERNAME>.github.io/privacy.html` |
| Application terms of service link | *(leave empty)* |
| Authorized domains | `<USERNAME>.github.io` |

Save, then return to **Audience** → **Publish app** — the button should now be enabled.

## ⚠️ Risk to check at the Authorized-domains step

Google validates the domain against public-suffix rules. `github.io` is itself a public suffix, so
`<USERNAME>.github.io` *should* be treated as a registrable domain and accepted — this is the
commonly reported outcome, but it is not universally guaranteed and Google's validator has changed
behaviour over time.

- If it is accepted → publish, then re-authorise so the new refresh token is minted under Production.
- If it is rejected (red border, "Invalid domain") → do not fight it. The fallback is your own
  custom domain (any cheap registrar), or staying on Testing and re-approving once a week.

## Note

The pages are honest descriptions of a single-user personal tool — they name what Google data is
accessed, where tokens live, that nothing is shared, and how to revoke access. Do not add claims
about verification, certification, or "Google-approved" status: the app is explicitly unverified,
and the 100-user cap and unverified warning screen apply.


---

# Route 2 — no new repo (60 seconds)

Verified live 2026-09-20: Henry already owns **`sector-heatmap`**, GitHub Pages is **already
enabled** on it (`has_pages=true`), and `https://henryc444.github.io/sector-heatmap/` serves
content (HTTP 200, a "Sector Heatmap — 行業熱度" app). Pages therefore needs **no further setup** —
just two more files.

Use the renamed pair in `drop-into-sector-heatmap/` (cross-links already rewritten):

| File | Upload as | Becomes |
|---|---|---|
| `monday.html` | `monday.html` | `https://henryc444.github.io/sector-heatmap/monday.html` |
| `monday-privacy.html` | `monday-privacy.html` | `https://henryc444.github.io/sector-heatmap/monday-privacy.html` |

Console values for this route:

| Field | Value |
|---|---|
| Application home page | `https://henryc444.github.io/sector-heatmap/monday.html` |
| Application privacy policy link | `https://henryc444.github.io/sector-heatmap/monday-privacy.html` |
| Authorized domains | `henryc444.github.io` |

Trade-off: no repo creation and no Pages configuration at all — but the OAuth branding URLs then
depend on the `sector-heatmap` repo continuing to exist. Renaming or deleting that repo would break
the consent screen's branding. Route 1 (dedicated `henryc444.github.io` repo) is the more durable
choice; Route 2 is the faster one.

## Public Suffix List check (the Authorized-domains risk)

`github.io` **is** present in the Public Suffix List (line 13770 of the official list, fetched
2026-09-20). That is the condition that makes `henryc444.github.io` a *registrable domain* rather
than a bare public suffix — so it should pass Google's authorized-domain validation. This
materially lowers the risk flagged above, though Google's own validator remains the final word.

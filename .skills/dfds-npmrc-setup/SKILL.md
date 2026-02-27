---
name: dfds-npmrc-setup
description: Set up authentication for @dfds-frontend npm packages hosted on GitHub Packages. Use when pnpm install or npm install fails with 401/403 errors, when onboarding a new local dev environment, or when configuring GitHub Actions or Docker builds that pull @dfds-frontend packages.
metadata:
  author: dfds-frontend
  version: "1.0"
---

# DFDS Frontend npm Registry Setup

`@dfds-frontend` packages (Navigator, Compass UI, etc.) are published to GitHub Packages (`npm.pkg.github.com`). You need a token before `pnpm install` will work.

---

## Auth methods

### Method A — DFDS org member

Generate a **classic** Personal Access Token (PAT):

1. <https://github.com/settings/tokens> → **Generate new token (classic)**
2. Grant **`read:packages`** scope only
3. Copy it — you won't see it again

> Fine-grained tokens do **not** work with GitHub Packages. Classic PAT only.

### Method B — Shared token via 1Password

Find **"DFDS Frontend GitHub Packages Token"** in the shared 1Password vault.  
Contains a pre-created classic PAT with `read:packages`. Tokens rotate every 30 days.

---

## Local `.npmrc`

Create `.npmrc` in the **project root** (never commit — add to `.gitignore`):

```ini
@dfds-frontend:registry=https://npm.pkg.github.com
//npm.pkg.github.com/:_authToken=YOUR_GITHUB_PAT_HERE
always-auth=true
```

Verify:
```bash
pnpm install   # no 401/403 = success
```

---

## GitHub Actions — `NPMRC` secret

Docker/CI builds need the same credentials. Set once per repo:

```bash
gh secret set NPMRC \
  --repo <org>/<repo> \
  --body "$(printf '@dfds-frontend:registry=https://npm.pkg.github.com\n//npm.pkg.github.com/:_authToken=YOUR_GITHUB_PAT_HERE\nalways-auth=true\n')"
```

Use in workflow (`docker/build-push-action`):
```yaml
secrets: |
  npmrc=${{ secrets.NPMRC }}
```

In `Dockerfile` — mounts secret at build time, **never baked into the image**:
```dockerfile
RUN --mount=type=secret,id=npmrc,dst=/root/.npmrc \
    pnpm install --frozen-lockfile
```

---

## Troubleshooting

| Symptom | Fix |
|---|---|
| `401 Unauthorized` on `pnpm install` | Wrong or missing token in `.npmrc` |
| `403 Forbidden` | Token missing `read:packages` scope |
| Docker `npm ERR! code E401` | `NPMRC` secret missing or expired |
| 1Password token expired | Org admin must rotate + update the note |

---

## 1Password note template

```
Title: DFDS Frontend GitHub Packages Token

Token         : ghp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
Expires       : YYYY-MM-DD  ← set a calendar reminder
Scope         : read:packages (classic PAT only)
Created by    : <github-username>

─── .npmrc (paste into project root, never commit) ─────────────
@dfds-frontend:registry=https://npm.pkg.github.com
//npm.pkg.github.com/:_authToken=ghp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
always-auth=true

─── gh secret set command (once per repo) ───────────────────────
gh secret set NPMRC --repo <org>/<repo> \
  --body "$(printf '@dfds-frontend:registry=https://npm.pkg.github.com\n//npm.pkg.github.com/:_authToken=ghp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx\nalways-auth=true\n')"

─── Rotation checklist ──────────────────────────────────────────
1. New classic PAT at https://github.com/settings/tokens (read:packages)
2. Update token in this note + expiry date
3. Update .npmrc on all dev machines
4. Re-run gh secret set for every repo that uses this token
5. Reset calendar reminder
```

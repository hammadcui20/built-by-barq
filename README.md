# Built by Barq: built.barq.dev

A one-page portfolio of what Barq Dev has designed, built and shipped. It's for showing clients.
It's a single static `index.html` with no build step. All product data is in the `PRODUCTS` array near the bottom of the file.

```
index.html             the whole site
assets/barq-logo.svg   Barq wordmark from barq.dev (text follows the theme, bolt stays #FF6500)
assets/favicon-*.ico   barq.dev's favicons (dark one for light browsers, light one for dark)
CNAME                  built.barq.dev, which tells GitHub Pages the custom domain
.nojekyll              serve files as-is, no Jekyll build
INVENTORY.md           private notes (git-ignored, never pushed)
```

## Publish on GitHub Pages

### Why a separate repo

Yes, give it its own repo. GitHub Pages serves **one site per repo**, and each repo can have **one custom domain**.
barq.dev itself isn't on GitHub Pages (`barq-site` is hosted elsewhere), so there's nothing to add this to. A separate
repo also keeps deploys independent: pushing here can never break barq.dev, and the other way round.

### Public or private repo

GitHub Pages from a **private** repo needs a paid plan (GitHub Pro for a personal account, Team or Enterprise for an org).
On a free account the repo must be **public**. That's fine here, because the page is public anyway. Just keep anything confidential out of the repo: that's why
the product inventory (which names client repos) lives in `INVENTORY.md`, which `.gitignore` keeps out.

### Step 1: create the repo and push (once)

The repo is [`hammadcui20/built-by-barq`](https://github.com/hammadcui20/built-by-barq) (personal account). It was pushed from this folder with:

```bash
git init -b main
git add .
git commit -m "Built by Barq portfolio site"
git remote add origin https://github.com/hammadcui20/built-by-barq.git
git push -u origin main
```

`INVENTORY.md` is git-ignored and was **not** pushed (check any time with `git ls-files`).

If you later move the repo to the BarqDev org, change the GoDaddy CNAME value to `barqdev.github.io`; nothing else changes.

### Step 2: turn on Pages

Repo → **Settings → Pages**

- **Source:** Deploy from a branch
- **Branch:** `main`, folder `/ (root)` → **Save**

After about a minute the site is live at `https://hammadcui20.github.io/built-by-barq/`.

### Step 3: verify barq.dev on your account (recommended, once per domain)

This stops anyone else's GitHub repo from claiming a `*.barq.dev` subdomain.

1. Your profile → **Settings → Pages → Add a domain** → enter `barq.dev`.
2. GitHub shows a TXT record. Add it in GoDaddy (step 4 shows where). It looks like:

   | Type | Name | Value |
   |---|---|---|
   | TXT | `_github-pages-challenge-hammadcui20` | *(the code GitHub gives you)* |

3. Click **Verify**. It can take a few minutes for DNS to update.

### Step 4: point built.barq.dev at GitHub (GoDaddy)

barq.dev's DNS is managed at GoDaddy (nameservers `ns19/ns20.domaincontrol.com`).

GoDaddy → **My Products → barq.dev → DNS → Add New Record**:

| Type | Name | Value | TTL |
|---|---|---|---|
| CNAME | `built` | `hammadcui20.github.io` | 1 hour |

Use the **account** address `hammadcui20.github.io` (the owner of the repo), not the repo URL. GitHub routes to the right repo using the `CNAME` file.
Don't touch the existing `@`, `www`, `meet` or `video-forge` records.

### Step 5: connect the domain and HTTPS

1. Repo → **Settings → Pages → Custom domain**. It should already show `built.barq.dev` (from the `CNAME` file). If it's
   empty, type it and **Save**.
2. Wait for the DNS check to pass (usually minutes, up to 24 h).
3. Tick **Enforce HTTPS** once it's clickable. GitHub issues the certificate automatically, which can take up to an hour.

Check from a terminal:

```bash
dig +short built.barq.dev
```

It should print `hammadcui20.github.io.` followed by GitHub IPs (`185.199.108-111.153`).

### Updating the site later

Edit `index.html`, then commit and push. GitHub Pages redeploys in about a minute. There's no build step and no CI to manage.

### If something goes wrong

| Symptom | Fix |
|---|---|
| 404 on built.barq.dev | Pages not enabled, or the `CNAME` file is missing or misspelled in the repo |
| "Domain's DNS record could not be retrieved" | GoDaddy record not live yet, or it points to the repo URL instead of `hammadcui20.github.io` |
| "Enforce HTTPS" greyed out | Certificate still being issued: wait up to an hour, then remove and re-add the custom domain |
| Old version still showing | Hard refresh (Cmd+Shift+R); Pages caches for about 10 minutes |

## Add or edit a product

Add one object to `PRODUCTS` in `index.html`:

```js
{
  name: "Name", group: "live" | "shopify" | "internal" | "foundations",
  audience: "external" | "internal",
  status: "live" | "listed" | "soon" | "internal",
  color: "#hex", what: "One sentence.", tags: ["AI", "Productivity"],
  stack: "Django · React",
  links: [{ label: "Visit site", href: "https://…", primary: true }]
}
```

The tag chips, counts and stats update on their own.

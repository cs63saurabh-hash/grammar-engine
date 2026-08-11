# Pāṇini Grammar Parsing Engine — 2407042

A rule-based Sanskrit grammar parser (Pāṇini's Aṣṭādhyāyī modeled as a
CS rule engine). Type a Sanskrit word or sentence, get its case
(vibhakti) and semantic role (karaka) back.

Single `index.html` file — no build step, no server. Just open it in
a browser.

## Run locally

Double-click `index.html`, or open it in any browser.

## Deploy to GitHub

```bash
cd panini-grammar-engine
git remote add origin https://github.com/<your-username>/<your-repo-name>.git
git push -u origin main
```

Then: repo → **Settings → Pages** → Source: "Deploy from a branch" →
branch `main`, folder `/ (root)` → Save. Live in ~1 minute at:
`https://<your-username>.github.io/<your-repo-name>/`

# Deploy — GitHub + Vercel

## Step 1 — Push to GitHub (Terminal only — folders don't work via browser drag)

GitHub's web UI cannot upload folders. Use Terminal:

```bash
# 1. Open Terminal, cd into the unzipped folder
cd ~/Downloads/p2p-spend-intelligence

# 2. Initialise git
git init
git add .
git commit -m "P2P Spend Intelligence — procurement spend analytics"
git branch -M main

# 3. Create an EMPTY repo on github.com
#    → github.com → New repository
#    → Name: p2p-spend-intelligence
#    → Visibility: Public
#    → !! Do NOT tick README / .gitignore / license !!
#    → Create repository
#    → Copy the HTTPS URL shown on the next page

# 4. Link and push
git remote add origin https://github.com/YOUR_USERNAME/p2p-spend-intelligence.git
git push -u origin main
```

All folders (data/, sql/, scripts/) push fine via git.

---

## Step 2 — Deploy to Vercel (60 seconds)

1. Go to **https://vercel.com/new**
2. Click **Import** next to `p2p-spend-intelligence`
3. Configure screen:
   - Framework Preset → **Other**
   - Build Command → *(leave blank)*
   - Output Directory → *(leave blank)*
4. Click **Deploy**

Live URL: `https://p2p-spend-intelligence-xxx.vercel.app`
Every future `git push main` auto-redeploys.

---

## URLs after deploy

```
/               →  index.html      (walkthrough)
/dashboard      →  dashboard.html  (charts)
/sql-guide      →  sql-guide.html  (SQL course)
```

---

## Rebuild data locally (optional)

```bash
pip install faker pandas duckdb
python scripts/generate_data.py   # writes data/*.csv
python scripts/load_duckdb.py     # runs all 10 SQL analyses
```

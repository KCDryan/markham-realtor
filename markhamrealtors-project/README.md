# MarkhamRealtors.com

Static, SEO-focused Markham real estate agent directory for Cloudflare Pages. The site is built with HTML, Tailwind CSS and native JavaScript. Realtor data lives in `realtors.json` and is refreshed by a scheduled GitHub Actions workflow.

## Project structure

```text
markhamrealtors/
|-- .github/
|   `-- workflows/
|       `-- weekly_update.yml
|-- src/
|   `-- input.css
|-- .gitignore
|-- index.html
|-- package.json
|-- README.md
|-- realtors.json
|-- requirements.txt
|-- robots.txt
|-- sitemap.xml
`-- update_realtors.py
```

## Data architecture

Production discovery uses the Serper Maps API. This avoids direct automated scraping of regulator directories, review sites and Google Maps HTML. The updater applies a single ranking formula to eligible profiles, filters for accepted Markham-area postal prefixes, optionally reads the home page of an agent's own website when robots.txt allows it, and writes only `realtors.json`.

The local development fallback is deterministic seed data. GitHub Actions sets `REQUIRE_LIVE_DATA=1`, so CI refuses to publish the seed if the live API key is missing.

Before commercial publication, confirm that the terms of your chosen data provider permit the storage and republication needed by your directory. If you replace Serper with another licensed provider, keep the normalized output fields used by `payload_for()`.

## Local setup

Requirements:

- Git
- GitHub CLI (`gh`)
- Node.js 20 or newer
- Python 3.12 or newer

From the project folder:

```bash
python -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
python update_realtors.py
npm install
npm run build
python -m http.server 8080 --directory dist
```

Open `http://localhost:8080` in a browser. Without a Serper key, `update_realtors.py` intentionally uses the development seed.

## Create the GitHub repository

Run these commands from the project folder:

```bash
git init
git branch -M main
git add .
git commit -m "Initial MarkhamRealtors.com build"
gh auth login
gh repo create markhamrealtors --public --source=. --remote=origin --push
```

## Add the live-data secret

Create a Serper account and copy its API key. Then run:

```bash
gh secret set SERPER_API_KEY
```

The GitHub CLI securely prompts for the secret value. Do not commit the API key to the repository.

Run the production updater once immediately:

```bash
gh workflow run weekly_update.yml
gh run watch
```

The workflow also runs automatically every Sunday at 00:00 UTC. It commits `realtors.json` only when material directory data changes.

## Cloudflare Pages deployment

1. Add `markhamrealtors.com` to your Cloudflare account if it is not already there. For the apex domain, point the domain to Cloudflare nameservers.
2. In Cloudflare, open **Workers & Pages** and choose **Create application** > **Pages** > **Connect to Git**.
3. Connect GitHub and select the `markhamrealtors` repository.
4. Use these build settings:
   - Production branch: `main`
   - Framework preset: `None`
   - Build command: `npm run build`
   - Build output directory: `dist`
   - Root directory: leave blank
5. Deploy the project.
6. Open the Pages project > **Custom domains** > **Set up a domain** and enter `markhamrealtors.com`.

Every future commit to `main`, including the Sunday data-bot commit, triggers a new Cloudflare Pages deployment through the Git integration.

## Branch protection note

The workflow needs permission to push `realtors.json` to `main`. The YAML grants `contents: write`. If your repository has branch rules that block GitHub Actions from pushing directly, allow the GitHub Actions bot for this branch or change the workflow to open a pull request instead.

## SEO implementation

The page includes:

- semantic HTML5 sections and dynamic `<article>` cards
- optimized title, description, canonical, robots and OpenGraph tags
- WebSite and Organization JSON-LD
- ItemList JSON-LD whose items are typed as both LocalBusiness and RealEstateAgent
- no third-party review ratings in `AggregateRating` schema
- visible ranking methodology and eligibility criteria
- weekly crawl signal in `sitemap.xml`
- `robots.txt`
- native FAQ accordion using `<details>` and `<summary>`
- no framework runtime and no external web fonts
- compiled Tailwind CSS rather than Tailwind's development Play CDN

## Ranking formula

Each eligible profile receives a 100-point score:

- 60 points: Bayesian-adjusted review quality
- 25 points: log-scaled review volume
- 10 points: best observed local-search position
- 5 points: profile/contact completeness

A public business address must resolve to an accepted Markham-area postal prefix. Paid placement is not part of the ranking score.

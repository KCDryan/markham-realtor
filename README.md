# MarkhamRealtors.com

An automated, SEO-optimized local real estate directory for discovering top real estate agents in Markham, Ontario.

The website is designed to run as a lightweight static site hosted on **Cloudflare Pages**, with realtor ranking data automatically refreshed every week through **GitHub Actions**.

Live domain:

**https://markhamrealtors.com**

---

# Overview

MarkhamRealtors.com publishes a regularly updated directory of real estate agents serving Markham, Ontario.

The project uses:

* HTML5
* Tailwind CSS
* Native JavaScript
* Python
* GitHub Actions
* Cloudflare Pages
* JSON-based realtor data
* Structured SEO schema
* Automated weekly data updates

The site is intentionally built without a traditional CMS or database.

The workflow is:

```text
Data Provider
      ↓
update_realtors.py
      ↓
realtors.json
      ↓
GitHub Commit
      ↓
Cloudflare Pages
      ↓
markhamrealtors.com
```

---

# Project Structure

```text
markhamrealtors/
├── .github/
│   └── workflows/
│       └── weekly_update.yml
│
├── src/
│   └── input.css
│
├── index.html
├── realtors.json
├── update_realtors.py
├── requirements.txt
├── package.json
├── robots.txt
├── sitemap.xml
├── .gitignore
└── README.md
```

---

# Main Features

## SEO-Optimized Frontend

The website includes:

* Semantic HTML5
* Responsive design
* Mobile-first layout
* Optimized `<title>`
* Meta description
* Canonical URL
* OpenGraph metadata
* Robots directives
* XML sitemap
* Internal anchor navigation
* FAQ content
* JSON-LD structured data
* `WebSite` schema
* `Organization` schema
* `ItemList` schema
* `LocalBusiness` / `RealEstateAgent` schema

The primary target keyword is:

```text
Best Real Estate Agents in Markham
```

Related search topics include:

```text
Best Realtors in Markham
Top Realtors in Markham
Markham Real Estate Agents
Markham Realtors
Best Real Estate Agent Markham
Top Real Estate Agents Markham Ontario
```

---

# Realtor Data

Realtor information is stored inside:

```text
realtors.json
```

The frontend loads this file dynamically using native JavaScript.

Each realtor entry can include:

```json
{
  "rank": 1,
  "name": "Example Realtor",
  "brokerage": "Example Realty Brokerage",
  "rating": 4.9,
  "review_count": 250,
  "address": "Markham, Ontario",
  "specialties": [
    "Residential",
    "Luxury Homes",
    "Move-Up Buyers"
  ],
  "contact_url": "https://example.com",
  "score": 95.2
}
```

Do not manually edit generated ranking fields unless necessary.

The automated Python script is responsible for updating the directory.

---

# Ranking Methodology

The directory uses a weighted ranking methodology rather than simply ranking agents by star rating.

The score may consider:

| Factor               | Approximate Weight |
| -------------------- | -----------------: |
| Review quality       |                60% |
| Review volume        |                25% |
| Search visibility    |                10% |
| Profile completeness |                 5% |

Ratings are adjusted using a Bayesian-style calculation so an agent with a very small number of reviews does not automatically outrank an established agent with hundreds of strong reviews.

The methodology may be refined over time as better data becomes available.

Rankings are editorial and algorithmic estimates and are not official industry rankings.

---

# Markham Location Filtering

The system is intended to prioritize agents or teams with a physical presence in Markham.

Relevant postal forward sortation areas include:

```text
L3P
L3R
L3S
L3T
L6B
L6C
L6E
L6G
```

These areas cover Markham, Unionville and portions of Thornhill located within the City of Markham.

---

# Python Data Updater

The primary automation script is:

```text
update_realtors.py
```

Install Python dependencies with:

```bash
python -m pip install -r requirements.txt
```

Run the updater locally:

```bash
python update_realtors.py
```

For live data, set the required API key as an environment variable.

Example:

```bash
export SERPER_API_KEY="YOUR_API_KEY"
python update_realtors.py
```

On Windows PowerShell:

```powershell
$env:SERPER_API_KEY="YOUR_API_KEY"
python update_realtors.py
```

---

# Data Safety

The production workflow is designed to avoid accidentally publishing broken directory data.

For example, the updater can reject an update when:

* An API key is missing
* The data provider returns invalid results
* Too few eligible Markham agents are found
* The response cannot be parsed
* The newly collected data fails validation

If a refresh fails, the existing `realtors.json` remains live.

This prevents a temporary API failure from wiping out the directory.

---

# GitHub Actions Automation

Weekly automation is configured inside:

```text
.github/workflows/weekly_update.yml
```

The workflow runs automatically every:

```text
Sunday at 00:00 UTC
```

Cron configuration:

```yaml
0 0 * * 0
```

The workflow:

1. Checks out the repository
2. Sets up Python
3. Installs dependencies
4. Runs `update_realtors.py`
5. Checks whether `realtors.json` changed
6. Commits updated realtor data
7. Pushes the change to `main`
8. Cloudflare automatically redeploys the website

The workflow can also be manually triggered from GitHub Actions.

---

# GitHub Secret

The production workflow requires the following GitHub repository secret:

```text
SERPER_API_KEY
```

Add it through:

```text
GitHub Repository
→ Settings
→ Secrets and variables
→ Actions
→ New repository secret
```

Name:

```text
SERPER_API_KEY
```

Or use GitHub CLI:

```bash
gh secret set SERPER_API_KEY
```

---

# Local Development

Clone the repository:

```bash
git clone https://github.com/YOUR-GITHUB-USERNAME/markhamrealtors.git
```

Enter the directory:

```bash
cd markhamrealtors
```

Install frontend dependencies:

```bash
npm install
```

Build Tailwind CSS:

```bash
npm run build
```

Install Python dependencies:

```bash
python -m pip install -r requirements.txt
```

Run the Python updater:

```bash
python update_realtors.py
```

You can serve the project locally using:

```bash
python -m http.server 8000
```

Then visit:

```text
http://localhost:8000
```

Do not open `index.html` directly using `file://` if the page needs to fetch `realtors.json`.

Use a local HTTP server instead.

---

# Create the GitHub Repository

From inside the project directory:

```bash
git init
git branch -M main
git add .
git commit -m "Initial MarkhamRealtors.com build"
```

Using GitHub CLI:

```bash
gh auth login
```

Then:

```bash
gh repo create markhamrealtors \
  --public \
  --source=. \
  --remote=origin \
  --push
```

Alternatively, create a blank repository manually on GitHub and connect it:

```bash
git remote add origin https://github.com/YOUR-GITHUB-USERNAME/markhamrealtors.git
git push -u origin main
```

---

# Cloudflare Pages Deployment

Log into Cloudflare.

Navigate to:

```text
Workers & Pages
→ Create
→ Pages
→ Connect to Git
```

Select the GitHub repository.

Use the following settings:

```text
Production branch:
main
```

```text
Framework preset:
None
```

```text
Build command:
npm run build
```

```text
Build output directory:
dist
```

Leave the root directory blank.

Deploy the project.

---

# Connect markhamrealtors.com

Once the Cloudflare Pages deployment succeeds:

```text
Cloudflare
→ Workers & Pages
→ markhamrealtors
→ Custom Domains
→ Set up a domain
```

Enter:

```text
markhamrealtors.com
```

Follow Cloudflare's DNS instructions.

Once configured, the production site will be accessible at:

```text
https://markhamrealtors.com
```

---

# Automatic Deployment Workflow

Once everything is connected, no manual publishing should normally be required.

The automated workflow is:

```text
Sunday 00:00 UTC
        ↓
GitHub Action Starts
        ↓
Python Fetches Fresh Data
        ↓
Agents Are Filtered
        ↓
Scores Are Calculated
        ↓
realtors.json Updated
        ↓
Changes Detected?
   ↓ No          ↓ Yes
Nothing       Git Commit
Changes           ↓
                Push
                  ↓
          Cloudflare Pages
                  ↓
        Website Redeployed
```

---

# SEO Architecture

The site currently focuses primarily on the homepage:

```text
https://markhamrealtors.com/
```

Future SEO expansion can include dedicated pages such as:

```text
/best-realtors-markham/
/luxury-realtors-markham/
/realtors-unionville/
/realtors-thornhill/
/chinese-realtors-markham/
/realtors-for-seniors-markham/
/first-time-buyer-realtors-markham/
/condo-realtors-markham/
/commercial-realtors-markham/
```

Neighbourhood pages can also be developed for:

```text
Unionville
Cornell
Berczy Village
Wismer
Greensborough
Box Grove
Cachet
Angus Glen
Markham Village
Thornhill
Milliken Mills
Buttonville
Cathedraltown
```

This creates a larger topical authority structure around Markham real estate.

---

# Future Recommended Upgrade

The current website loads realtor information from `realtors.json` using JavaScript.

A future version should also pre-render the ranking directly into static HTML during the build process.

The ideal architecture would be:

```text
realtors.json
      ↓
Static Build Script
      ↓
SEO HTML
      +
Interactive JavaScript
```

This would provide:

* Crawlable realtor content immediately in the HTML
* Better resilience
* Faster perceived rendering
* Better LLM visibility
* Better long-tail SEO potential
* JavaScript search and filtering

---

# Structured Data

Structured data should represent the directory and the businesses listed.

Third-party reviews should not automatically be inserted into `AggregateRating` schema unless the data complies with Google's structured-data requirements.

Ratings may still be displayed visually on the directory when legitimately obtained and properly attributed.

---

# Editorial Disclosure

MarkhamRealtors.com is an independent real estate directory.

Rankings may be based on publicly available information, review signals, online visibility, profile completeness and other measurable criteria.

Placement should not be represented as an official ranking from:

* RECO
* TRREB
* CREA
* Google
* Any brokerage
* Any real estate board

If paid placements or sponsored listings are added in the future, they should be clearly labelled.

---

# Data Compliance

Before introducing any new data source, review its:

* Terms of service
* API terms
* Republishing restrictions
* Database rights
* Storage restrictions
* Automated-access rules

Avoid directly scraping websites that prohibit automated access or commercial republication.

Whenever possible, use:

* Licensed APIs
* First-party business websites
* Properly licensed datasets
* Data-provider APIs designed for automated access

---

# Useful Commands

Install dependencies:

```bash
npm install
python -m pip install -r requirements.txt
```

Build production assets:

```bash
npm run build
```

Update realtor data:

```bash
python update_realtors.py
```

Run locally:

```bash
python -m http.server 8000
```

Git status:

```bash
git status
```

Commit changes:

```bash
git add .
git commit -m "Update MarkhamRealtors.com"
git push
```

Run the weekly workflow manually:

```bash
gh workflow run weekly_update.yml
```

Watch the workflow:

```bash
gh run watch
```

---

# Technology Stack

**Frontend**

```text
HTML5
Tailwind CSS
Native JavaScript
JSON-LD
```

**Automation**

```text
Python
GitHub Actions
```

**Data**

```text
JSON
External search/data API
```

**Hosting**

```text
Cloudflare Pages
```

**Version Control**

```text
Git
GitHub
```

---

# Domain

```text
markhamrealtors.com
```

Primary geographic market:

```text
Markham, Ontario, Canada
```

Primary topic:

```text
Real Estate Agents and Realtors in Markham
```

---

# License

Copyright © MarkhamRealtors.com.

All rights reserved.

Third-party trademarks, brokerage names, realtor names, ratings and other business information remain the property of their respective owners.

The inclusion of a realtor or brokerage in this directory does not imply an endorsement, partnership or affiliation unless explicitly stated.

---

# Administration

For normal operation, the website should require very little manual maintenance.

Recommended checks:

```text
Weekly:
Confirm GitHub Action completed successfully.

Monthly:
Review rankings and data quality.

Quarterly:
Review SEO performance and ranking methodology.
```

# Prompt — Regional campaign dashboard (Foundever EMEA)

Save this file. Each time you want to spin up a dashboard for a new region (or refresh an existing one), copy the **prompt** section below into Claude, fill in the **inputs** block, and paste it.

---

## How to use it

1. Fill in the inputs block at the top. URLs and cohort IDs are the only things that change between regions.
2. Paste the whole thing — inputs + prompt — into Claude.
3. Claude will pull the data, generate a `data.json`, and either update an existing dashboard or scaffold a new one.

---

## The prompt — copy everything below this line

You're acting as Foundever's EMEA campaign reporting agent. I want you to build (or update) a single-page regional campaign dashboard, following the existing pattern at `Foundever/uk-dashboard/`. The dashboard reads from `data.json` and renders via `index.html` — both designed to be hosted on GitHub Pages and updated weekly.

### Inputs — fill these in for each region

```
REGION: <e.g. UK / France / Germany / Spain>
REGION_CODE: <e.g. uk / fr / de / es>
DATE_RANGE_LABEL: <e.g. "30 Mar — 28 Apr 2026" — what's pulled from LinkedIn>
OWNER: <name of person owning the dashboard>

# LinkedIn horizontal campaign — leave blank if not running yet
LINKEDIN_CAMPAIGN_OVERVIEW_URL: <campaignmanager URL filtered to the regional campaign group>
LINKEDIN_TOP_TITLES_URL: <demographics URL with pivot=MEMBER_JOB_TITLE>
LINKEDIN_TOP_COMPANIES_URL: <demographics URL with pivot=MEMBER_COMPANY>
LINKEDIN_MAIN_CREATIVE: <e.g. "rightshoring", "claims = retention" — the angle driving results>

# Influ2 cohorts per vertical — add as many as you have (BFS, T&H, Insurance, Telco, EV, Retail, etc.)
INFLU2_COHORT_<VERTICAL>:
  url: <full v2.influ2.com/cohorts/... URL with from/to params>
  vertical_label: <e.g. "Banking & Financial Services">

# Google SEA — leave as "pending" if data isn't pulled yet
SEA_STATUS: <"pending" | "live">
SEA_REPORT_URL: <Google Ads or other report URL — only if SEA_STATUS = live>

# Where the dashboard lives
DASHBOARD_FOLDER: <e.g. Foundever/uk-dashboard or Foundever/fr-dashboard>
MODE: <"create" for a brand-new region | "update" to refresh an existing dashboard>
```

### What you should do

1. **Read the existing UK dashboard structure first** at `Foundever/uk-dashboard/data.json` and `Foundever/uk-dashboard/index.html`. Match the schema and layout exactly. Don't reinvent.

2. **Pull live data** by opening each URL in Chrome via the browser tools.
   - For LinkedIn: capture spend, impressions, clicks, CTR, CPM, CPC, plus top 3 titles by clicks/CTR and top 10 companies by impressions.
   - For each Influ2 cohort: capture cohort name, active period, reach (targets and buying groups), impressions, CTR, top creative, top 5 contacts (name, title, company, last engagement), top 5 companies (with contact counts).
   - For SEA: only include if `SEA_STATUS = live`. Otherwise leave the `sea` block as `"status": "pending"`.

3. **If a login is expired**, skip that platform and flag it in the report. Do NOT attempt to authenticate — the user will re-auth and re-run.

4. **If a metric is unclear or below LinkedIn's reporting minimum**, mark it as `null` in JSON and "—" in any prose. Never guess numbers.

5. **Generate `data.json`** in the target dashboard folder. Use the exact schema from `Foundever/uk-dashboard/data.json`. Include:
   - `report_meta` (title with region, subtitle, last_updated, owner)
   - `linkedin_horizontal` (or omit the block entirely if not running)
   - One `influ2_<vertical>` block per cohort provided
   - `sea` (pending or live)
   - `read_of_the_week` — your opinionated 2–3 sentence read of what the data is telling us. Be specific: name top accounts, name strongest creative, call out what to do next.

6. **If MODE is "create"**, also copy `index.html`, `README.md`, and `.gitignore` from the UK dashboard folder. The HTML doesn't need code changes — it renders whatever shape `data.json` has, including arbitrary numbers of Influ2 cohorts (you'll need to add corresponding `<div class="card" id="<vertical>_card"></div>` slots and a `renderInflu2()` call for any vertical beyond T&H and BFS — match the pattern in the script).

7. **If MODE is "update"**, only modify `data.json` in the existing folder. Don't touch `index.html` unless the schema changes.

8. **At the end, give the user**:
   - A link to view the updated `data.json`
   - A 4–6 line summary of what changed since the last update (numbers up/down, new top accounts, new blockers)
   - The git commit message they can paste: `Weekly update — REGION — YYYY-MM-DD`

### Style rules

- Numbers over adjectives. "£4,200 spent, 0.8% CTR, 3 engaged accounts" — not "decent performance".
- Be opinionated in the `read_of_the_week`. Recommend, don't describe.
- British English (organise, colour, prioritise, optimise).
- Never invent numbers. If you can't read it, say so.
- Sentence case everywhere. No ALL CAPS, no Title Case.
- Don't pad. Short summary, dashboard does the heavy lifting visually.

### Edge cases

- **No LinkedIn campaign in this region yet**: omit the `linkedin_horizontal` block from `data.json`. The dashboard handles missing blocks gracefully (or it should — if you spot a rendering bug, fix `index.html` once and it works for all regions after).
- **One Influ2 cohort, not two**: same — only include what exists.
- **A cohort URL has a date range different from the others**: respect it. Capture the actual date range in `date_range_pulled` for each cohort separately.
- **An Influ2 row shows impressions instead of a date in the engagement column**: that means the contact has impressions but no date-stamped click. Show "199 imp" (or similar) as the engagement value rather than guessing a date.
- **A vertical's top creative has 0% CTR but high impressions**: still surface it as the top creative — note explicitly that engagement hasn't landed yet, so the recommendation is creative refresh.

### What to ask before starting

If any of these are missing or ambiguous in the inputs, ask once at the start, then proceed:
- Region name + code
- At least one LinkedIn URL OR at least one Influ2 cohort URL (if both are missing, there's nothing to pull)
- MODE (create vs update)

Don't ask permission to start pulling — just go.

---

## Example: UK update run

```
REGION: UK
REGION_CODE: uk
DATE_RANGE_LABEL: 30 Mar — 28 Apr 2026
OWNER: Raluca Mitran

LINKEDIN_CAMPAIGN_OVERVIEW_URL: https://www.linkedin.com/campaignmanager/accounts/511840953/campaigns?businessId=7307305596398002177&campaignGroupIds=%5B1019818684%5D
LINKEDIN_TOP_TITLES_URL: https://www.linkedin.com/campaignmanager/accounts/511840953/demographics?...&pivot=MEMBER_JOB_TITLE
LINKEDIN_TOP_COMPANIES_URL: https://www.linkedin.com/campaignmanager/accounts/511840953/demographics?...&pivot=MEMBER_COMPANY
LINKEDIN_MAIN_CREATIVE: rightshoring

INFLU2_COHORT_TH:
  url: https://v2.influ2.com/cohorts/e6320257-f7d9-4eae-a7da-5165e7266556?client_id=...&from=2026-01-01&to=2026-04-28
  vertical_label: Travel & Hospitality

INFLU2_COHORT_BFS:
  url: https://v2.influ2.com/cohorts/7ff8f95e-76fb-4d6d-8c9c-1b0a0a7d7f88?client_id=...&from=2026-03-29&to=2026-04-28
  vertical_label: Banking & Financial Services

SEA_STATUS: pending

DASHBOARD_FOLDER: Foundever/uk-dashboard
MODE: update
```

## Example: France first-time setup

```
REGION: France
REGION_CODE: fr
DATE_RANGE_LABEL: 1 Apr — 28 Apr 2026
OWNER: Raluca Mitran

LINKEDIN_CAMPAIGN_OVERVIEW_URL: <paste FR campaign group URL>
LINKEDIN_TOP_TITLES_URL: <paste FR demographics URL with pivot=MEMBER_JOB_TITLE>
LINKEDIN_TOP_COMPANIES_URL: <paste FR demographics URL with pivot=MEMBER_COMPANY>
LINKEDIN_MAIN_CREATIVE: <FR-specific creative angle>

INFLU2_COHORT_EBILLING:
  url: <France eBilling cohort URL>
  vertical_label: EV / eBilling

SEA_STATUS: pending

DASHBOARD_FOLDER: Foundever/fr-dashboard
MODE: create
```

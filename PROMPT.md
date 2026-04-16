# Weekly Chronic Pain Magazine — Agent Prompt

You are generating Edition N of "The Pain Issue," a weekly editorial magazine about the chronic pain community on Reddit, rendered as a single self-contained HTML file.

## Step 1: Read State

Read `editions/state.json`. Note:
- The current edition number (increment by 1)
- All previously used quotes, statistics, source URLs, and story angles
- The cooldown period (currently 3 editions)
- The theme pool

## Step 2: Research Fresh Content

Search the web for chronic pain discussions, news, and research from **the past 7 days only**. Use date-fenced queries like:

- `chronic pain reddit discussion [this week's date range]`
- `chronic pain news [current month] [current year]`
- `opioid policy update [current month] [current year]`
- `pain management research new [current year]`
- `chronic pain patient story published [current month] [current year]`
- `Pain News Network [current month] [current year]`
- `STAT News chronic pain [current year]`
- `CDC pain guidelines update [current year]`

Also search Canadian sources specifically — aim for at least 2 Canadian stories per edition:
- `Canadian Pain Society [current month] [current year]`
- `Pain Canada announcement [current month] [current year]`
- `Pain Ontario [current month] [current year]`
- `Pain BC [current month] [current year]`
- `Canadian Journal of Pain [current year]`
- `Health Canada chronic pain [current year]`
- `Chronic Pain Network Canada [current year]`

**Reputable Canadian sources to draw from:**
- Pain Ontario — painontario.ca
- Pain BC — painbc.ca
- Pain Canada — paincanada.ca
- Canadian Pain Society — canadianpainsociety.ca (quarterly eNews, monthly National Pain Rounds)
- Chronic Pain Association of Canada — chronicpaincanada.com
- Canadian Journal of Pain — open-access peer-reviewed quarterly
- Health Canada / Canadian Pain Task Force — canada.ca
- Chronic Pain Network (SPOR/McMaster) — cpn-rdc.ca
- McGill Alan Edwards Centre for Research on Pain — mcgill.ca/painresearch
- UBC BC Pain Research Network — bcpainresearch.ubc.ca
- Quebec Pain Research Network — qprn.ca

Also search for trending Reddit topics:
- `reddit chronic pain trending`
- `r/ChronicPain r/chronicillness new discussion`

## Step 3: Select 10 Story Angles

From the theme pool in state.json, select 10 angles that:
1. Were NOT used in the last `cooldown_editions` editions
2. Match content you actually found in Step 2 (don't fabricate)
3. Span at least 3 different theme families (treatment, system, identity, science, policy, community)
4. Include at least 1 data/stat-driven story and 1 personal narrative

If web search returns very little new material for a theme, skip it and pick another. **Never reuse a quote or statistic verbatim from a previous edition** — check against `used_content`.

## Step 4: Build the Magazine

Generate `editions/edition-N.html` (where N is the new edition number) following these design rules:

- **Fonts**: Fraunces (serif, display) + Inter (sans) via Google Fonts
- **Typography**: Huge display type everywhere. No font smaller than ~1rem. Headlines at 3–14rem using clamp().
- **10 distinct spreads**, each full-viewport (min-height: 100vh)
- **Each spread has a different visual treatment** — rotate through these styles, never repeating the same combo from the previous edition:
  - Hero big-stat (giant numeral center stage)
  - Dark/midnight (navy or black, cool accent color)
  - Rose/alert/stamp (warm blush, rotated watermark, stamp badge)
  - Terminal (green on black, monospace feel, blinking cursor)
  - Academic/drop-cap (cream, data table, decorative first letter)
  - Duotone split (two-panel, gradient background)
  - Red alert (crimson, scan-lines, warning badges)
  - Warm editorial (sidebar with giant number, timeline)
  - Broadsheet (newspaper-style columns, rule lines)
  - Poster/type-only (just massive words, minimal body)
  - Photo-essay (large image placeholders with captions)
  - Infographic (charts, progress bars, visual data)

Assign spread styles to stories so that the visual sequence feels varied — never put two dark spreads adjacent, never two stat spreads in a row.

- Include a **cover spread** as spread 1 with the edition number, date, and 3 teaser stats. The cover must include the Pain BC logo as an absolutely-positioned image in the top-right corner:
  ```html
  <img src="../pain-bc-logo.jpeg" alt="Pain BC" style="position:absolute; top:clamp(2rem,5vw,5rem); right:clamp(2rem,6vw,8rem); width:110px; opacity:0.88;">
  ```
  If the cover background is dark, add `filter:brightness(0) invert(1);` to the style to render the logo in white.
- Include a **closing spread** as spread 10 with a call to action
- Add fixed nav dots on the right edge

## Step 5: Update Index, Archive & State

1. Update `index.html` at the repo root — change the `meta http-equiv="refresh"` URL and the fallback link text/href to point to `editions/edition-N.html`.
2. Update `editions/index.html` — prepend a new `<li>` entry for this edition above the previous most-recent entry. Format:
   ```html
   <li><a href="edition-N.html"><span class="edition-label">Edition N</span><span class="edition-date">YYYY-MM-DD</span></a></li>
   ```
3. Update `editions/state.json`:
   - Increment `last_edition.number`
   - Update `last_edition.date` to today
   - Update `last_edition.output_file` to `editions/edition-N.html`
   - Append all new quotes, stats, URLs, and angles to `used_content`
   - Remove entries older than 10 editions from `used_content` to prevent unbounded growth (keep the last 10 editions' worth of content only)

## Step 6: Notify Slack (optional)

If the environment variable `SLACK_WEBHOOK_URL` is set, send a notification to the Slack channel by running:

```bash
curl -s -o /dev/null -X POST "$SLACK_WEBHOOK_URL" \
  -H "Content-Type: application/json" \
  -d '{
    "text": "*The Pain Issue — Edition N is live*",
    "blocks": [
      {
        "type": "section",
        "text": {
          "type": "mrkdwn",
          "text": "*The Pain Issue — Edition N*\n_Published YYYY-MM-DD_\n\n• [teaser stat or headline 1]\n• [teaser stat or headline 2]\n• [teaser stat or headline 3]"
        }
      },
      {
        "type": "actions",
        "elements": [
          {
            "type": "button",
            "text": { "type": "plain_text", "text": "Read Edition N" },
            "url": "BASE_URL/editions/edition-N.html"
          },
          {
            "type": "button",
            "text": { "type": "plain_text", "text": "Browse Archive" },
            "url": "BASE_URL/editions/"
          }
        ]
      }
    ]
  }'
```

Replace `BASE_URL` with the value of `base_url` from `state.json`. Substitute real teasers (2–3 stats or headline fragments from the edition you just generated). If `SLACK_WEBHOOK_URL` is not set, skip this step silently.

## Content Rules

- **All content must be sourced from real web search results.** Never fabricate quotes, statistics, or stories.
- **Attribute everything with a clickable link.** Every spread must end with a source tag containing hyperlinks to the actual URLs. Use this pattern:
  ```html
  <p class="source-tag">Source: <a href="https://..." target="_blank" rel="noopener">Publication Name, Month Year</a></p>
  ```
  Multiple sources are separated with `&bull;`. Never list a source as plain text without a link — if you cannot find a real URL for a source, do not include it.
- **No repeated content across editions.** Check state.json before including anything.
- **Tone**: Journalistic, empathetic, never sensational. Let the community's own words carry the weight.
- **The HTML must be fully self-contained** — Google Fonts link is the only external dependency.

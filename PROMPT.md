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

Generate `magazine.html` (overwriting the previous edition) following these design rules:

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

- Include a **cover spread** as spread 1 with the edition number, date, and 3 teaser stats
- Include a **closing spread** as spread 10 with a call to action
- Add fixed nav dots on the right edge

## Step 5: Archive & Update State

1. Copy the new `magazine.html` to `editions/edition-N.html` (where N is the edition number)
2. Update `editions/state.json`:
   - Increment `last_edition.number`
   - Update `last_edition.date` to today
   - Append all new quotes, stats, URLs, and angles to `used_content`
   - Remove entries older than 10 editions from `used_content` to prevent unbounded growth (keep the last 10 editions' worth of content only)

## Content Rules

- **All content must be sourced from real web search results.** Never fabricate quotes, statistics, or stories.
- **Attribute everything.** Name the source (subreddit, publication, study) in the HTML.
- **No repeated content across editions.** Check state.json before including anything.
- **Tone**: Journalistic, empathetic, never sensational. Let the community's own words carry the weight.
- **The HTML must be fully self-contained** — Google Fonts link is the only external dependency.

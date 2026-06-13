# Tableau Public — step-by-step build guide

Builds the credit-risk dashboard from the CSVs in `dashboard/data/`, assuming
zero prior Tableau experience. Expect 60–90 minutes end to end.

Every chart in this guide is the **same three moves**:

> **1.** Drag a category field (blue) onto **Columns** or **Rows**.
> **2.** Drag a number field (green) onto the other shelf — that draws the bars.
> **3.** Drag that same number onto **Color** and **Label** in the Marks card
>        to style it.

Once that clicks, the whole dashboard is repetition.

## 0. Setup (once)

1. Go to [public.tableau.com](https://public.tableau.com) → **Sign Up** for a
   free Tableau Public account (this account also hosts your published link).
2. Download and install **Tableau Public** (the free desktop app) from the
   same site, and sign in inside the app.

> Tableau Public has no local "Save" to your computer — saving publishes to
> your online profile. So build everything first, then save once at the end.

## 1. Know your screen (read this once)

When you open a worksheet, the window has four regions. The whole guide refers
to these by name:

- **Data pane** — far left. Lists the fields from your CSV. Fields are
  **blue** (text, e.g. `Grade` — Tableau calls these *dimensions*) or
  **green** (numbers, e.g. `Default Rate Pct` — *measures*). You drag fields
  *out* of here.
- **Columns shelf** and **Rows shelf** — two horizontal bars across the top of
  the chart area, stacked, labeled "Columns" and "Rows". What you put here
  becomes the x and y axes.
- **Marks card** — a tall card on the left of the chart area (just right of the
  Data pane), with buttons labeled **Color**, **Size**, **Label**, **Detail**,
  **Tooltip**. Drag a field onto one of these to style the marks by that field.
- **Canvas** — the big area on the right where the chart draws.

> **Undo is your friend.** To remove a field, drag its pill off the shelf and
> drop it on the empty canvas — it vanishes, nothing breaks. `Ctrl/Cmd+Z`
> undoes anything.

> **Why no SUM math is needed:** these CSVs are already one row per category
> (the SQL in `sql/04_dashboard_extracts.sql` did the aggregation). When
> Tableau wraps a field as `SUM(Default Rate Pct)`, it's summing a single
> value per bar — so the number shown is exactly the value in the CSV. Leave
> it as SUM.

## 2. Connect the data

1. Open Tableau Public → on the start page, under **Connect → To a File**,
   click **Text file** and pick `dashboard/data/default_rate_by_grade.csv`.
2. You land on the gray **Data Source** page (a preview of the table). You
   don't need to change anything here.
3. Click the **Sheet 1** tab at the bottom-left to open a blank worksheet.
4. For each later sheet that uses a *different* CSV: top menu **Data → New
   Data Source → Text file**, pick that CSV, then start a fresh worksheet.
   Each CSV is its own little data source — no joins, they're independent.

## 3. The views (one worksheet each)

After building each one, **double-click its sheet tab** at the bottom and
rename it to the bold name. To start the next sheet, click the **new-worksheet
icon** (a tab with a small starburst, bottom bar).

### Sheet 1 — **Default Rate by Grade** (the headline)

Data source: `default_rate_by_grade.csv`

1. Drag `Grade` (blue) from the Data pane onto the **Columns** shelf. → A, B,
   C … G appear across the bottom.
2. Drag `Default Rate Pct` (green) onto the **Rows** shelf. → seven bars draw.
3. Drag `Default Rate Pct` again onto the **Color** button in the Marks card.
   → click **Color → Edit Colors → Red**, so high risk reads hot.
4. Drag `Default Rate Pct` a third time onto the **Label** button. → each bar
   now prints its %.
5. *Sanity check:* grade A is shortest (~6.0%), grade G tallest (~49.9%).
6. **Optional pricing-vs-risk overlay** (the strongest single chart — adds the
   "pricing doesn't keep pace" story): drag `Avg Int Rate` onto the **Rows**
   shelf, dropping it to the *right* of the existing pill so you get a second
   chart stacked beside the first. Right-click the second (lower) axis →
   **Dual Axis**. Right-click it again → **Synchronize Axis**. In the Marks
   card you'll now see two sub-cards (one per measure) — click the
   `Avg Int Rate` sub-card and change its mark dropdown from Bar to **Line**.
   Result: bars = losses realized, line = rate charged; the grade-G gap
   (27.7% rate vs 49.9% default) is the punchline.
7. Title (double-click the title strip above the chart): *"Default risk is
   steeply graded — and pricing doesn't keep pace."*

### Sheet 2 — **Risk by Purpose**

Data source: `default_rate_by_purpose.csv`

1. Drag `Default Rate Pct` onto **Columns**, and `Purpose` onto **Rows**.
   (Number on Columns, category on Rows → horizontal bars.)
2. Sort longest-to-shortest: hover the chart, click the **sort-descending
   icon** that appears on the toolbar (or on the Purpose axis).
3. Drag `Default Rate Pct` onto **Color** (Red palette again).
4. Drag `N Loans` onto **Tooltip** so hovering a bar shows the loan count.
5. *Sanity check:* `small_business` is the top bar at 29.7%.
6. Title: *"Small-business loans default most."*

### Sheet 3 — **Risk by Income Band**

Data source: `default_rate_by_income_band.csv`

1. Drag `Income Band` onto **Columns**, `Default Rate Pct` onto **Rows**.
2. The bands are pre-numbered `1.`–`4.`, so they sort in order automatically.
3. Drag `Default Rate Pct` onto **Label**.
4. *Sanity check:* left bar (under $40k) 23.8%, right bar ($120k+) 15.6%.
5. Title: *"Income protects: 23.8% under $40k → 15.6% at $120k+."*

### Sheet 4 — **Default Rate by State** (map)

Data source: `default_rate_by_state.csv`

1. In the Data pane, right-click `State` → **Geographic Role →
   State/Province**. (A small globe icon appears on the field.)
2. Double-click `State`. → Tableau auto-draws the US map with a dot per state.
3. Drag `Default Rate Pct` onto **Color**. If you want filled states instead
   of dots, set the Marks mark-type dropdown to **Map**. Red palette.
4. Drag `N Loans` onto **Tooltip**.
5. *Sanity check:* MS / AR / AL shade darkest (~24–26%).
6. Title: *"Where defaults cluster (states with ≥5,000 loans)."*

### Sheet 5 — **Segment Explorer** (grade × home-ownership heatmap)

Data source: `segment_grade_home_ownership.csv`

1. Drag `Home Ownership` onto **Columns**, `Grade` onto **Rows**. → a grid of
   empty cells.
2. In the Marks card, change the mark-type dropdown (top of the card) to
   **Square**. → the cells fill in.
3. Drag `Default Rate Pct` onto **Color**, then onto **Label**.
4. *Sanity check:* the F-row / RENT-column cell burns red (~48.9%); the
   A-row / MORTGAGE cell stays coolest (~5.2%) — a ~9× spread.
5. Title: *"Risk stacks: renters out-default homeowners in every grade."*

### Sheet 6 — **Model Performance**

Data source: `model_metrics.csv`

1. Drag `Model` onto **Rows**.
2. Double-click, in turn, `Roc Auc`, then `Pr Auc`, `Ks`, `Precision`,
   `Recall`. → each lands as a column of numbers (Tableau builds a text
   table; the measures collect on a "Measure Values" shelf).
3. *Optional:* drag `Roc Auc` onto **Color** to shade the winning row.
4. *Sanity check:* the `Gradient boosting (all features)` row reads 0.716 /
   0.381 / 0.314.
5. Title: *"Models vs. the lender's own grade (test set: 269k loans)."*

### Sheet 7 — **Default Drivers**

Data source: `top_default_drivers.csv`

1. Drag `Coefficient` onto **Columns**, `Feature` onto **Rows**; sort
   descending (toolbar sort icon).
2. Drag `Coefficient` onto **Color** → **Edit Colors → Red-Green Diverging**,
   check **Use Full Color Range**, and reverse it so **positive = red**
   (raises risk) and **negative = green** (protective).
3. *Sanity check:* `grade_C` is the longest red bar (+0.329); `fico` is green
   (−0.182).
4. Title: *"What drives default — standardized logistic coefficients."*

## 4. Assemble the dashboard

1. Click the **New Dashboard** icon in the bottom bar (a grid-of-squares with
   a plus — next to the new-worksheet icon).
2. In the left panel, set **Size → Automatic** (the dashboard resizes to the
   viewer's browser — best for a portfolio link).
3. The left panel now lists your seven sheets under "Sheets." Drag them onto
   the canvas one at a time. A layout that reads well:
   - **Top-left, large:** Default Rate by Grade (the hero).
   - **Top-right:** Model Performance, with Default Drivers below it.
   - **Bottom row:** Risk by Purpose · Segment Explorer · State map.
   - Income Band is optional — add it as a small fourth tile or leave it out;
     5–6 views is the sweet spot, don't overcrowd.
   - To remove a tile: click it, then the **×** on its toolbar.
4. Add a title bar: drag a **Text** object (from "Objects", lower-left) to the
   very top → type *"Credit-Risk Default Prediction — 1.35M Lending Club
   loans"* with a one-line subtitle linking your GitHub repo.
5. Make it interactive: top menu **Dashboard → Actions → Add Action →
   Highlight**. Set **Source Sheets** = Default Rate by Grade, **Target
   Sheets** = Segment Explorer, **Run action on = Select**. Now clicking a
   grade bar highlights that grade's row in the heatmap.
6. Tidy each tile: click it, and use the tile dropdown → **Fit → Entire View**
   so nothing is cut off.

## 5. Publish and get the link

1. **File → Save to Tableau Public As…** → name it
   `credit-risk-default-prediction` → it uploads and opens in your browser.
2. On the published page, click **Edit Details** → paste a one-paragraph
   description (reuse the README TL;DR). Under workbook settings, turn **off**
   "Show workbook sheets as tabs" so viewers see just the dashboard.
3. The browser URL —
   `https://public.tableau.com/app/profile/<you>/viz/credit-risk-default-prediction/...`
   — is your share link (the **Share** button shows the same URL).
4. Paste that URL into both `<TABLEAU_LINK>` placeholders in `README.md`
   (the TL;DR line and the Dashboard section) and into the portfolio card.
5. Take a full-dashboard screenshot, save it as `dashboard/preview.png`
   (the README already points at that exact path), then commit and push.

## Numbers to sanity-check after building

If any of these don't match, a field landed on the wrong shelf:

- Grade A default rate **6.0%**, grade G **49.9%** (overall 20.0%).
- Small-business purpose **29.7%**.
- F × RENT segment **48.9%**; A × MORTGAGE **5.2%**.
- Best model row: gradient boosting (all features) **0.716 AUC / 0.314 KS**.

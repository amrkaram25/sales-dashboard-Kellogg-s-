# Sales Performance Dashboard

Power BI-style, fully client-side dashboard. Open `index.html`, drop a sales file, done.
No build step, no server. Libraries load from jsDelivr CDN (internet needed on first load).

## Run
1. Open `index.html` in Chrome / Edge / Firefox.
2. Drag & drop (or Browse) a `.xlsx`, `.xls`, `.xlsb` or `.csv` file. "Try demo data" works without a file.
3. Everything (KPIs, charts, rankings, forecast, tables) recalculates automatically. Filters and chart clicks cross-filter all visuals.

## Files
| File | Responsibility |
|---|---|
| `index.html` | Layout, vendor libraries |
| `css/style.css` | Dark/light themes, glass cards, responsive + print |
| `js/dataProcessor.js` | Upload parsing, header/column auto-detection (English + Arabic labels), cleaning, validation |
| `js/kpiEngine.js` | KPIs, grouping, ranking, forecast, confidence, insights, status colours |
| `js/filterEngine.js` | Date + 7 cascading multi-select filters, chips, chart cross-filtering |
| `js/charts.js` | Chart.js (9 charts + gauge) and Plotly (burn-up, Pareto) |
| `js/exportEngine.js` | PDF, PNG, summary Excel, grid Excel, print, fullscreen |
| `js/script.js` | Controller: upload, AG Grid tables, DataTables, notifications, wiring |

## Data rules (important)
* Header row is auto-detected (title rows above it are skipped). Extra columns are ignored.
* **Target layout**: your file stores Target on separate target-only rows (no invoice) per Executive + Customer + Division. Target is summed from those rows, never repeated per invoice. If a file instead repeats Target on invoice lines, it is counted once per Executive+Customer+Division (a notice is shown).
* Target rows have no date or material, so they ignore the date filter; with a **Material** filter active, targets are excluded (achievement shows N/A).
* Blank targets = 0. Negative GSV (returns) is kept. Exact duplicate rows are removed and reported in the validation log.
* Required columns: Invoice Date, Sold to Party Name, Division Name, GSV, KT Executive. Others fall back gracefully with a warning.

## Calculations
* Achievement % = Sales ÷ Target; Gap = Target − Sales (positive = shortfall)
* Reporting month = month of the latest invoice date in the selection; Elapsed days = day of month of that date
* Forecast = (Month-to-date sales ÷ Elapsed days) × Days in month; Required daily = Remaining target ÷ Remaining days
* Confidence = 60% month coverage + 40% stability of daily sales (High ≥70, Medium ≥45)
* Colours: ≥100% green, 90–99% yellow, 75–89% orange, <75% red
* Multi-month files: forecast/pace use the latest month; use the date filter to focus on one month.

## Notes
* Executive ranking / league table default to Achievement %; toggle to Sales.
* The combo charts (division, forecast) use a secondary % axis, as specified.
* Chart/PNG/PDF export uses html2canvas; very long dashboards are split across PDF pages.

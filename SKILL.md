---
name: earnings-analysis
description: Analyze 12 quarters of earnings, auto-save to vault.
version: 0.1.0
author: Kundi Wang, Hermes Agent
license: MIT
platforms: [linux, macos, windows]
---

# Earnings Analysis Skill

Analyze a company's last 12 quarters of earnings reports, produce a 13-section
plain-language report (12 quarterly sections + 1 overall evaluation), then
AUTO-SAVE it into the company's Obsidian directory note without being asked.
Kundi should never have to remind about the save.

## When to Use

- Kundi asks for an earnings analysis / earnings-history review of a ticker.
- Kundi asks "how has X been doing quarter by quarter" or "analyze the last
  year's earnings" (scale sections to the span asked, but keep the structure).
- Don't use for: a fresh full stock screen (use the screening pipeline), a DCF
  (use dcf-valuation / reverse-dcf), or portfolio trade reconciliation.

## Data Sources (in order of preference)

1. **Company IR press releases** — revenue/margins/EPS/guidance as reported.
2. **SEC EDGAR 10-Q/10-K** (`sec.gov/cgi-bin/browse-edgar`) — cash flow,
   balance sheet, segment detail, footnotes.
3. **stockanalysis.com/stocks/<TICKER>/financials/** — fast 12-quarter series
   for revenue, margins, EPS, FCF (cross-check at least revenue and EPS against
   IR before trusting).
4. **Earnings call transcripts** (Motley Fool, IR site) — guidance language,
   analyst Q&A, dodged questions.

Fetch via `web_extract` first; on block/empty results fall back to the camofox
browser (`browser_exec`), then `web_extract_plus`. Cross-check one number per
statement against a second source — never build 12 quarters off one aggregator
alone.

## Procedure

1. **Collect 12 quarters.** For each quarter capture: total revenue + YoY/QoQ,
   revenue by segment/geography if disclosed, gross margin, operating margin,
   GAAP EPS, adjusted EPS (note the reconciliation deltas — SBC addbacks,
   "one-time" exclusions), diluted share count, operating cash flow, free cash
   flow, total debt + cash, capex, buybacks/dividends, and the guidance given
   that quarter (next-quarter and full-year). Completion criterion: a filled
   table for 12 consecutive fiscal quarters with fiscal-quarter labels (e.g.
   FQ3'24) — if fewer than 12 are publicly available (recent IPO), use what
   exists and say so in Section 13.

2. **Analyze each quarter.** Work through the checklist below per quarter. The
   point is NOT restating numbers — it is what changed and why it matters.
   Per-quarter checklist:
   - Revenue: growth rate, which segment drove it, volume vs price, one-off vs
     recurring.
   - Margins: direction and size of change, cost vs pricing driver.
   - EPS: GAAP vs adjusted gap and whether the gap is growing.
   - Cash: OCF vs net income (a widening gap = check receivables/inventory),
     FCF conversion.
   - Balance sheet: debt/cash movement, buyback reality (share count falling
     or just offsetting SBC?).
   - Guidance: raised/lowered/maintained vs prior quarter, magnitude.
   - Call: what management emphasized, what analysts pressed, what got dodged.

3. **Write the report — 13 sections, fixed structure:**

   ```markdown
   # <TICKER> — Earnings Analysis (last 12 quarters)
   *As of YYYY-MM-DD. Fiscal quarters labeled FQx'YY.*

   ## 1. FQ<x> <year> — <plain-language headline>
   **Numbers:** revenue $X (+YoY%, QoQ%), gross margin X%, op margin X%,
   GAAP EPS $X / adj $X, OCF $X, FCF $X, debt $X, cash $X.
   **What changed:** <the 2-3 moves that matter this quarter>
   **What this means:** <one to three sentences, plain talk — see rule below>

   ## 2. ... (repeat for each of the 12 quarters)
   ...
   ## 13. Overall Evaluation
   **The story of these 12 quarters:** <3-5 sentences, plain talk: what kind of
   company does the data say this is — accelerating/stalling/cyclical/one-hit>
   **Trends that held all 12 quarters:** <list>
   **Trends that broke or reversed:** <list, with which quarter>
   **Management credibility:** did guidance match delivery, quarter by quarter?
   **Red flags:** accounting gaps, guidance misses, margin erosion, dilution.
   **What it means for the business:** <straight answer: is the business getting
   stronger, weaker, or cyclical — and what to watch next quarter>
   ```

4. **Plain-language rule (binding).** Every "What this means" line must be
   understandable to someone with zero finance background. Bad: "Gross margin
   compression of 180bps reflects input cost inflation outpacing pricing
   carry-through." Good: "They made less profit on each dollar of sales because
   their costs rose faster than the prices they could charge." No hedging, no
   analyst-speak, no unexplained jargon. If a term is needed (NRR, backlog),
   explain it in parentheses the first time.

5. **Auto-save (no reminder).** This step runs every time, unconditionally:
   a. Resolve the vault root `/Users/wangkundi/Desktop/Cowork/Hermes Memory/`.
   b. Write/update the company directory note `Stocks/<TICKER>.md` (create it
      if missing). Insert or update the earnings-analysis section; keep any
      existing thesis/screening content — this is an addition, not a rewrite.
      Link the note with `[[TICKER]]` from any newly-mentioned entities.
   c. Ensure `Stocks/Index.md` has a row for the ticker (add if missing).
   d. Append a dated one-line entry to `Research Log.md` ("<date> — earnings
      analysis for [[TICKER]], 12 quarters, saved to Stocks/<TICKER>.md").
      Re-read the log immediately before appending — sibling sessions append
      to it concurrently.
   e. Re-read the saved note after writing and verify the 13 sections are
      present and no existing content was deleted (check the patch/write diff).
   Do NOT run the full six-file financial save checklist — that applies when
   Kundi explicitly asks to save research; this skill's auto-save is scoped to
   the ticker note + Index + Research Log.

6. **Report back.** Deliver the full 13-section report in chat AND confirm the
   vault save with the file path. If any quarter's data could not be verified,
   say which and what source was used instead — never fill gaps with guesses.

## Pitfalls

- **Fiscal vs calendar years:** MSFT FQ2'24 = Oct-Dec 2023. Label fiscal
  quarters correctly or the whole timeline is off by a quarter.
- **Stock splits:** adjust per-share figures (EPS, buyback prices) to a
  consistent share basis across all 12 quarters or growth rates are fiction.
- **Adjusted EPS drift:** if the company keeps adding new "one-time" addbacks,
  flag it in Section 13 rather than averaging it away.
- **Guidance memory:** compare each quarter's guidance to the NEXT quarter's
  actuals to score management credibility — this is the highest-value output of
  the skill and the most commonly skipped step.
- One aggregator source alone = trust nothing. Cross-check at least revenue,
  EPS, and OCF against IR/EDGAR.

## Verification

- 12 (or all available) quarterly sections present, correctly labeled, in
  chronological order + Section 13 present.
- Every quarter has a "What this means" line in plain language.
- `Stocks/<TICKER>.md` saved, prior content preserved, `Stocks/Index.md` and
  `Research Log.md` updated (re-read after write).
- Any unverified numbers explicitly flagged, not silently filled.

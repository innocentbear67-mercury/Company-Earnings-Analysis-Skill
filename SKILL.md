---
name: earnings-analysis
description: Analyze 12 quarters of earnings, auto-save to vault.
version: 0.2.0
author: Kundi Wang, Hermes Agent
license: MIT
platforms: [linux, macos, windows]
---

# Earnings Analysis Skill

Analyze a company's last 12 quarters of earnings reports, produce a
Kundi-approved plain-English report (overview table + 12 quarterly sections +
big picture), then
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
   exists and say so in the big picture.

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

3. **Write the report — Kundi-approved format (binding, from LILA 9/9/2026).** Better formatting never means less information — keep every number, just make it readable. Structure:

   ```markdown
   # <TICKER> — 3 Years In Plain English
   > One line: what they sell + where. Price $X (date, position in 52wk range). Worth ~$XB, owes ~$XB.
   > How to read this: Money in = sales. Real cash = cash from daily work (OCF). Cash left = what remains after fixing/building (FCF). Owed = loans. In bank = cash on hand.
   ## All 12 quarters on one page
   | Time | Money in | vs last year | Real cash | Cash left | Owed / In bank |
   |---|---|---|---|---|---|
   | <Mon-Mon YYYY> | $XM | up/down X% | $XM | $XM | $XM / $XM |
   (... all 12 rows, calendar labels — never fiscal codes as the visible label ...)
   ## What happened, quarter by quarter
   ### 1. <Mon-Mon YYYY> — <plain headline>
   - **Kept:** Xc per $1 after running the network (= gross margin, no jargon). Per share: $X.
   - **What happened:** <2-4 bullets/facts: customers, costs, one-offs, guidance, storms/deals — with dollar amounts>
   - **Bottom line:** <1-2 sentences, zero jargon>
   (... repeat for all 12 ...)
   ## The big picture
   **One sentence:** <story of the 12 quarters>
   **Always true, all 12 quarters:** <numbered, with ranges>
   **What flipped:** <numbered, with which quarter each broke>
   **Can you trust the bosses?:** <forecasting vs operating, with receipts>
   **5 dangers:** <numbered, plain words, dollar amounts>
   **Next to watch (<date>):** <1-2 concrete questions>
   ---
   *Checked: <what was cross-checked>. No guessing. One note: <any presentation quirk, e.g. GAAP vs standardized>.*
   *Where I could be wrong: <strongest counterargument, 1-2 sentences>.*
   ```
   Fiscal-quarter labels (FQx'YY) go in the As-of line / vault note only — chat-facing headers use calendar months so a non-investor can follow.

4. **Plain-language rule (binding, from LILA 9/9/2026).** Zero jargon — a
   non-investor must understand every line. Fixed vocabulary: Money in = sales;
   Real cash = cash from daily work (OCF); Cash left = what remains after
   fixing/building (FCF); Owed / In bank = loans / cash on hand; Kept Xc per $1
   = gross margin. Chat-facing headers use calendar labels (Jul-Sep 2023),
   never fiscal codes. Bad: "Gross margin compression of 180bps reflects input
   cost inflation outpacing pricing carry-through." Good: "They kept less per
   $1 because costs rose faster than prices." No bps, no unexplained acronyms
   (OIBDA, NRR, FCF, OCF) — either translate to the fixed vocabulary or explain
   in parentheses on first use. Better formatting NEVER means less information
   — keep every number (revenue, YoY, margins, EPS, OCF, FCF, debt, cash),
   just make it readable.

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
   e. Re-read the saved note after writing and verify the overview table (12
      rows) + 12 quarterly sections + big picture are present and no existing
      content was deleted (check the patch/write diff).
   Do NOT run the full six-file financial save checklist — that applies when
   Kundi explicitly asks to save research; this skill's auto-save is scoped to
   the ticker note + Index + Research Log.

6. **Report back.** Deliver the full report in chat in the Kundi-approved
   format AND confirm the
   vault save with the file path. If any quarter's data could not be verified,
   say which and what source was used instead — never fill gaps with guesses.

## Pitfalls

- **Fiscal vs calendar years:** MSFT FQ2'24 = Oct-Dec 2023. Label fiscal
  quarters correctly or the whole timeline is off by a quarter.
- **Stock splits:** adjust per-share figures (EPS, buyback prices) to a
  consistent share basis across all 12 quarters or growth rates are fiction.
- **Adjusted EPS drift:** if the company keeps adding new "one-time" addbacks,
  flag it in the big picture rather than averaging it away.
- **Guidance memory:** compare each quarter's guidance to the NEXT quarter's
  actuals to score management credibility — this is the highest-value output of
  the skill and the most commonly skipped step.
- One aggregator source alone = trust nothing. Cross-check at least revenue,
  EPS, and OCF against IR/EDGAR.
- **Formatting ≠ less info (LILA 9/9/2026):** when Kundi says "nicer" or
  "simpler" he means readability, not fewer numbers. Keep all figures; change
  words and layout, never drop content.
- **Jargon creep:** fiscal codes (FQ3'24), bps, OIBDA/OCF/FCF unexplained, and
  "What this means"-style analyst headers all fail Kundi's bar. Calendar
  months + fixed vocabulary (Money in / Real cash / Cash left / Owed / Kept)
every time.

## Verification

- 12 (or all available) quarterly rows in the overview table + 12 quarterly
  sections in chronological order with calendar labels + big picture present.
- Every quarter has Kept / What happened / Bottom line in zero-jargon language;
  "How to read this" legend present.
- `Stocks/<TICKER>.md` saved, prior content preserved, `Stocks/Index.md` and
  `Research Log.md` updated (re-read after write).
- Any unverified numbers explicitly flagged, not silently filled.

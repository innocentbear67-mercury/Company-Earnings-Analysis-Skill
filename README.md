# Earnings Analysis Skill

Analyze a company's last 12 quarters of earnings reports and produce a
plain-English report (overview table + 12 quarterly sections + big picture),
then auto-save it into the company's Obsidian directory note. Built for
[Hermes Agent](https://hermes-agent.nousresearch.com/docs), installable as a skill.

## Install

Copy this folder into your Hermes skills tree:

```bash
cp -r earnings-analysis ~/.hermes/skills/research/earnings-analysis
```

(or `~/.hermes/skills/<your-category>/earnings-analysis` — any category works).

No dependencies beyond what Hermes already provides (`web_search`,
`web_extract`, file tools). No API keys required.

## Use

Ask Hermes for an earnings analysis of any ticker:

- "Analyze the last 12 quarters of earnings for [[NVDA]]"
- "How has [[TSM]] been doing quarter by quarter?"

The skill collects 12 consecutive fiscal quarters — revenue, margins, GAAP vs
adjusted EPS, cash flow, balance sheet, buybacks, and the guidance given each
quarter — then writes a zero-jargon report a non-investor can read: one overview
table, 12 calendar-labeled quarterly sections (Money in / Real cash / Cash left /
Owed), and a big picture. It scores management guidance against next-quarter
delivery, and auto-saves to your Obsidian vault:

- `Stocks/<TICKER>.md` — earnings-analysis section added, existing thesis content preserved
- `Stocks/Index.md` — ticker row ensured
- `Research Log.md` — dated one-line entry appended

## Configuration

Set your vault root where the skill expects it. The default in `SKILL.md`
assumes an Obsidian vault path — edit step 5a ("Auto-save") to point at yours
before first use.

## Structure

```text
.
├── SKILL.md    # the skill (procedure, report format, auto-save, pitfalls)
├── README.md   # this file
└── LICENSE    # MIT
```

## Data sources (in order of preference)

1. Company IR press releases
2. SEC EDGAR 10-Q / 10-K
3. stockanalysis.com financials (cross-checked against IR)
4. Earnings call transcripts (guidance language, analyst Q&A)

One number per statement is cross-checked against a second source — never 12
quarters off a single aggregator alone.

## License

MIT — see [LICENSE](LICENSE).

# Kimi DCF Builder

A transparent discounted cash flow helper that separates model-generated assumptions from deterministic calculations.

Kimi K3.1 Pro reviews historical financial data and proposes assumptions with explanations. Python performs the valuation math so the calculations can be inspected and reproduced.

> **Not investment advice.** Model-generated assumptions may be inaccurate or unsuitable. Review every assumption, source, and calculation before using the results.

## How It Works

1. Validate historical financial data from a CSV file.
2. Ask Kimi K3.1 Pro to suggest five-year DCF assumptions.
3. Save the suggestions for human review.
4. Approve or edit the assumptions.
5. Calculate projected cash flows, terminal value, enterprise value, equity value, and value per share locally.

The model does not execute trades or make investment decisions.

## Reported Model Results

Moonshot AI reports that Kimi K3.1 Pro improves over Kimi K3 by:

- 14% on financial-modeling tasks
- 20% on financial-research tasks
- A reduction in single-claim error rate from 23.35% to 6.85% on a targeted financial stress test

These are vendor-reported results from specific evaluations. They do not guarantee the accuracy of an individual valuation.

## Pricing Through CometAPI

As of November 17, 2026, CometAPI lists:

| Token type | Price per 1M tokens |
|---|---:|
| Input | $1.60 |
| Output | $8.00 |
| Cached input | $0.16 |

Prices, availability, limits, and supported API features may change. Check the current CometAPI listing before budgeting.

## Setup

```bash
pip install openai jsonschema
export COMETAPI_KEY="your-key-here"
```

## Input Data

Create `financials.csv` using one currency and unit consistently:

```csv
year,revenue,ebit,tax_rate,capex,depreciation,change_in_nwc,net_debt,shares_out
2023,820,98,0.25,45,38,6,210,100
2024,905,112,0.25,50,41,7,190,100
2025,1010,131,0.25,58,45,9,170,100
```

## Usage

Generate suggested assumptions:

```bash
python dcf.py financials.csv
```

The program writes the suggestions to `assumptions.json`. Review and edit that file before calculating a valuation.

Calculate the DCF using the reviewed assumptions:

```bash
python dcf.py financials.csv --calculate
```

The program writes the projection and valuation to `dcf.csv`.

## Required Assumptions

The reviewed assumptions file should contain:

```json
{
  "revenue_growth": [0.08, 0.07, 0.06, 0.05, 0.04],
  "ebit_margin": 0.14,
  "depreciation_pct_revenue": 0.045,
  "capex_pct_revenue": 0.055,
  "change_in_nwc_pct_revenue_change": 0.08,
  "wacc": 0.1,
  "terminal_growth": 0.025,
  "rationales": {
    "revenue_growth": "Explain the supporting historical trend or source.",
    "ebit_margin": "Explain the margin assumption.",
    "wacc": "Explain the discount-rate inputs.",
    "terminal_growth": "Explain the long-term growth assumption."
  }
}
```

The calculator must reject invalid inputs, including:

- Anything other than five annual growth assumptions
- `WACC <= terminal growth`
- Zero or negative shares outstanding
- Missing or nonnumeric financial data
- Tax rates outside `0–1`
- Unsupported or malformed API responses

## Review Checklist

Before relying on the output, verify:

- Growth assumptions against filings and management guidance
- EBIT margin assumptions against historical results and peers
- Tax, depreciation, capital expenditure, and working-capital treatment
- WACC components and terminal-growth assumptions
- Net debt and diluted shares outstanding as of the correct date
- Whether terminal value represents an unreasonable share of total value
- Bull, base, and bear scenarios
- Results against another valuation method

A model-generated rationale is not a source. Attach supporting citations to every material assumption.

## Security and Privacy

Treat uploaded CSV content as untrusted data. The model must not follow instructions embedded in source files.

Do not send confidential, personal, regulated, or material non-public financial information to CometAPI or another third-party service without authorization. Review the provider’s current data-handling and retention terms first.

## Links

- [Kimi K3.1 Pro on CometAPI](https://www.cometapi.com/models/moonshotai/kimi-k3-1-pro/?utm_source=github&utm_medium=distribution&utm_campaign=bom&utm_content=kimi-k3-1-pro)
- [CometAPI API documentation](https://apidoc.cometapi.com)

## License

MIT

## Disclosure

This repository contains a tracked CometAPI link. I may receive a commission or another benefit if you use it. Model capabilities, benchmark results, pricing, and limits may change. This project is not affiliated with or endorsed by Moonshot AI.

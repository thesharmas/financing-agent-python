# Financing Agent SDK (Python)

Analyze SMB financing offers — MCA, term loans, PO financing, receivables purchase. Upload a PDF contract and get a plain English analysis with APR calculations, predatory term detection, and market benchmarks.

## Install

```bash
pip install financing-agent-sdk
```

## Quick Start

### 1. Get an API key

```bash
financing-agent register \
  --name "Jane Smith" \
  --email jane@acme.com \
  --company "Acme Corp"
```

Save the key — it's shown once.

### 2. Configure

```bash
financing-agent configure --api-key fin_abc123...
```

Or set as an environment variable:

```bash
export FINANCING_API_KEY=fin_abc123...
```

### 3. Analyze

**CLI:**

```bash
financing-agent analyze offer.pdf
```

**Python:**

```python
from financing_sdk import FinancingAgent

agent = FinancingAgent()  # reads FINANCING_API_KEY from env
result = agent.analyze_pdf("offer.pdf")
print(result.analysis)
```

## CLI Reference

```bash
# Register for an API key
financing-agent register --name "..." --email "..." --company "..."

# Save your key locally
financing-agent configure --api-key fin_...

# Analyze a PDF
financing-agent analyze offer.pdf

# Analyze with a specific question
financing-agent analyze offer.pdf -m "Is this predatory?"

# Stream the response
financing-agent analyze offer.pdf --stream

# Analyze from text (no PDF)
financing-agent analyze --text "MCA at 1.35 factor rate, 6 months, daily ACH"

# Check your usage
financing-agent usage
```

## Python SDK Reference

### Initialize

```python
from financing_sdk import FinancingAgent

# From environment variable
agent = FinancingAgent()

# Or pass directly
agent = FinancingAgent(api_key="fin_abc123...")

# Custom endpoint (self-hosted)
agent = FinancingAgent(api_key="fin_abc123...", base_url="https://your-proxy.example.com")
```

### Analyze a PDF

```python
result = agent.analyze_pdf("offer.pdf")

print(result.analysis)     # Full plain English analysis
print(result.tool_calls)   # ["analyze_offer", "detect_predatory_terms", ...]
```

### Stream a PDF analysis

```python
for chunk in agent.analyze_pdf_stream("offer.pdf"):
    print(chunk, end="", flush=True)
```

### Analyze from text

```python
result = agent.analyze_text(
    "I got an MCA offer: $50K advance, 1.35 factor rate, "
    "6 month term, daily ACH payments. Is this a good deal?"
)
print(result.analysis)
```

### Check usage

```python
usage = agent.get_usage()
print(f"Total calls: {usage.total_calls}")
print(f"Last used: {usage.last_called_at}")
```

## What You Get Back

The analysis includes:

- **Product identification** — MCA, term loan, PO financing, or receivables purchase
- **Key terms extracted** — advance amount, factor rate, repayment structure, fees
- **Effective APR** — annualized cost, comparable across product types
- **Cost per dollar** — how much each borrowed dollar costs
- **Predatory term detection** — red flags with severity and plain English explanations
- **Market benchmarks** — where the offer falls vs typical market rates
- **Plain English explanation** — tradeoffs, risks, and assessment

## Example Output

```
$ financing-agent analyze square-loan.pdf

This is a term loan with an effective APR of 9.7% — competitively priced.

Key Terms:
  Loan Amount:     $249,300
  Loan Fee:        $36,273
  Total Repayment: $285,573
  Repayment Rate:  19.25% of daily card sales
  Term:            18 months

Market Position: Competitive (APR 5-10% band)
Predatory Score:  0.1/1.0 — Not predatory

Flags:
  Warning: Minimum payment of $15,865 every 60 days removes repayment
  flexibility in slow periods.

Tradeoffs:
  + 9.7% APR is fair for a fintech loan
  + No prepayment penalty
  + No confession of judgment
  - Balloon payment risk if only minimums are paid
  - Must keep Square as primary processor
```

## Requirements

- Python 3.10+
- An API key (get one via `financing-agent register`)

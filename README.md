# xShield Domain Risk Scan — GitHub Action

> Fail your CI pipeline if a domain's risk score is too high.
> Powered by [xShield AI](https://xshieldai.com) — 13 free threat intelligence sources in one scan.

---

## Quick Start

```yaml
- uses: ankrlabs/xshield-scan@v1
  with:
    domain: ${{ github.event.repository.name }}.com   # or hard-code your domain
    api-key: ${{ secrets.XSHIELD_API_KEY }}
```

## Full Example

```yaml
name: Security Scan

on:
  push:
    branches: [main]
  pull_request:

jobs:
  risk-scan:
    name: xShield Domain Risk Scan
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Scan domain for threats
        id: xshield
        uses: ankrlabs/xshield-scan@v1
        with:
          domain: 'yourdomain.com'
          api-key: ${{ secrets.XSHIELD_API_KEY }}
          fail-threshold: '70'       # fail if score > 70

      # Use outputs in later steps
      - name: Print risk report
        if: always()
        run: |
          echo "Risk score : ${{ steps.xshield.outputs.risk-score }}"
          echo "Risk level : ${{ steps.xshield.outputs.risk-level }}"
          echo "Full report: ${{ steps.xshield.outputs.report-url }}"
```

## Inputs

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `domain` | ✅ | — | Domain to scan, e.g. `example.com` |
| `api-key` | ✅ | — | xShield API key (`xsh_live_...`). Store as a repository secret. |
| `fail-threshold` | ❌ | `70` | Fail the job if the risk score exceeds this value (0–100) |
| `api-url` | ❌ | `https://xshieldai.com/api` | Override for self-hosted xShield instances |

## Outputs

| Output | Description |
|--------|-------------|
| `risk-score` | Numeric risk score, 0–100 |
| `risk-level` | `MINIMAL` · `LOW` · `MEDIUM` · `HIGH` · `CRITICAL` |
| `report-url` | URL to the full JSON risk report |

## Getting an API Key

1. Sign up at [xshieldai.com](https://xshieldai.com)
2. Go to **Settings → API Keys** → **Create New Key**
3. Copy the key (shown once) and add it to your repo:
   `Settings → Secrets → Actions → New secret → XSHIELD_API_KEY`

## What Gets Scanned

Each scan checks 13 free threat intelligence sources:

| Source | What it detects |
|--------|----------------|
| GreyNoise Community | Malicious / scanner IP classification |
| AlienVault OTX | IP/domain threat pulse count |
| Shodan | Open ports and exposed services |
| HaveIBeenPwned | Credential breach records |
| urlscan.io | Active phishing URLs |
| crt.sh | Lookalike SSL certificates |
| DNS validation | Registered typosquat domains |
| psbdmp.ws | Paste / data leak monitoring |
| SPF / DMARC / DNSSEC / CAA | DNS security audit |
| OpenPhish + SURBL + PhishStats | Active phishing feeds |
| ip-api.com + ASN | Bulletproof hosting / geopolitical risk |
| GitHub code search | Exposed secrets and credentials |
| Feodo Tracker + ThreatFox | Ransomware C2 indicators |

## Risk Score Thresholds

| Score | Level | Recommended action |
|-------|-------|--------------------|
| 0–14 | 🟢 MINIMAL | No action needed |
| 15–34 | 🟡 LOW | Monitor |
| 35–54 | 🟠 MEDIUM | Investigate open findings |
| 55–74 | 🔴 HIGH | Fix before merging to prod |
| 75–100 | 🚨 CRITICAL | Block merge — immediate action required |

## Pricing

| Tier | Scans / month | Price |
|------|--------------|-------|
| Free | 10 (IP-based, no key needed) | $0 |
| Starter | 500 | $99/mo |
| Pro | Unlimited | $499/mo |
| Enterprise | Custom SLA | Contact us |

[→ Compare plans](https://xshieldai.com/pricing)

## License

MIT © [ANKR Labs](https://ankrlabs.org)

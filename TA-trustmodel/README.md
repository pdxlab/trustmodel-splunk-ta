# TA-trustmodel — TrustModel AI Assurance Add-on for Splunk

**Version:** 0.1.0-alpha (pre-Splunkbase)
**Status:** EPIC TRUS-783 (Cisco-driven). Ticket TRUS-786.

This Technology Add-on receives TrustModel events via HTTP Event Collector and maps them to Splunk Common Information Model so they appear natively in:

- **Splunk Enterprise Security** — Risk-Based Alerting, Notable Events, Exposure Analytics
- **Splunk SOAR** — playbook triggers via Notable Events
- **Splunk Observability Cloud** — AI Agent Monitoring (via OTLP, separate path)

## Sourcetypes

| Sourcetype | Description | CIM Datamodel |
|---|---|---|
| `trustmodel:evaluation` | Completed evaluation (TrustScore 0–100 + dimension scores) | Risk |
| `trustmodel:guardrail` | Guardrail violation (blocked / flagged) | Alerts |
| `trustmodel:shadow_ai` | Shadow AI discovery from scanners | Inventory + Risk |
| `trustmodel:redteam` | Red-team probe finding | Alerts |
| `trustmodel:datascan` | DataScan AI-readiness finding | Risk |

## Install

### 1. Mint an HEC token

In Splunk Web → **Settings → Data Inputs → HTTP Event Collector**:

1. **New Token** with name `trustmodel`
2. Allowed sourcetypes: select all `trustmodel:*` (or "All")
3. Default index: `trustmodel` (create the index if needed: Settings → Indexes)
4. Save and copy the token UUID

### 2. Install the add-on

```bash
# From Splunk web: Apps → Manage Apps → Install app from file
# Upload the TA-trustmodel-0.1.0.tgz package built from this repo.
```

Or, for development:

```bash
cp -r TA-trustmodel $SPLUNK_HOME/etc/apps/
$SPLUNK_HOME/bin/splunk restart
```

### 3. Configure the TrustModel sender

#### Option A — Direct from SDK

```python
from trustmodel import TrustModelClient

client = TrustModelClient(api_key="tm-...")
client.splunk.configure(
    hec_url="https://your-splunk.cloud:8088",
    hec_token="paste-the-uuid",
    index="trustmodel",
)
client.splunk.test_connection()
```

#### Option B — Via aurora-gateway (multi-tenant)

In TrustModel Console: **Settings → Integrations → Splunk → Connect**. Paste your HEC URL + token. All evaluations, guardrail violations, and findings forward automatically.

#### Option C — Via auto_init() (zero-config)

```bash
export SPLUNK_HEC_URL=https://your-splunk.cloud:8088
export SPLUNK_HEC_TOKEN=paste-the-uuid
```

```python
import trustmodel
trustmodel.auto_init(api_key="tm-...")
```

### 4. Verify

In Splunk: `index=trustmodel sourcetype=trustmodel:* | head 20`

Expected: events with full CIM fields populated (`risk_object`, `signature`, `severity`, etc.).

## CIM Compliance

Field aliases in `default/props.conf` map TrustModel fields to CIM Alerts and Risk schemas:

```
trustmodel:evaluation.risk_object_type = "model"
trustmodel:evaluation.calculated_risk_score = trust_score
trustmodel:guardrail.signature, severity, category, action, src, dest
trustmodel:redteam.signature = "redteam:<probe_id>"
trustmodel:datascan.risk_object_type = "dataset"
trustmodel:shadow_ai.risk_object_type = "ai_system"
```

Full schema: see `TRUS-785` Confluence doc.

## ES Content Pack

This add-on ships the parsing layer only. Correlation searches, notable events, and dashboards are in the companion ES content pack `DA-ESS-TrustModel` — track under `TRUS-787` (follow-on this sprint).

## Cisco AI Defense + Splunk

This add-on is the trust-scoring tap inside the existing Cisco AI Defense → Splunk pipeline. See `TRUS-794` for the joint architecture.

## Support

- Docs: https://trustmodel.ai/splunk
- Issues: https://github.com/pdxlab/trustmodel-splunk-ta/issues
- Email: support@predixtions.com

## License

Copyright (c) 2026 Predixtions / TrustModel. Proprietary.

## Roadmap

- v0.1.0-alpha (this) — TA package, props/transforms/eventtypes/tags/macros
- v0.2.0 — AppInspect-clean, dashboards XML+JSON
- v0.3.0 — Splunkbase Vetted (Cloud), ES content pack auto-bundled
- v1.0.0 — GA on Splunkbase, full Cisco AI Defense partner content pack

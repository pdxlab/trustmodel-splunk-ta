# trustmodel-splunk-ta

[![Splunkbase](https://img.shields.io/badge/Splunkbase-pending-orange)](https://splunkbase.splunk.com/)
[![AppInspect](https://img.shields.io/badge/AppInspect-pending-orange)](https://dev.splunk.com/enterprise/docs/developapps/testvalidate/appinspect/)
[![Version](https://img.shields.io/badge/version-0.1.0--alpha-blue)](./TA-trustmodel/CHANGELOG.md)

**TrustModel AI Assurance Add-on for Splunk.** Receives TrustModel evaluation lifecycle events and finding events via HTTP Event Collector and maps them to Splunk Common Information Model so they light up natively in:

- Splunk Enterprise Security (Risk-Based Alerting + Exposure Analytics + Notable Events)
- Splunk SOAR (playbook triggers via Notable Events)
- Splunk Observability Cloud (separate OTLP path)

Companion add-on for the **Cisco AI Defense + Splunk** integration.

## Repository layout

```
trustmodel-splunk-ta/
├── TA-trustmodel/                  # The add-on package
│   ├── default/
│   │   ├── app.conf               # Package metadata + version
│   │   ├── inputs.conf            # HEC inputs declaration
│   │   ├── props.conf             # Sourcetype field extractions + CIM aliases
│   │   ├── transforms.conf        # Lookups (future enrichment)
│   │   ├── eventtypes.conf        # Eventtype definitions
│   │   ├── tags.conf              # CIM datamodel tags
│   │   ├── macros.conf            # Search macros
│   │   └── fields.conf            # Custom field declarations
│   ├── metadata/default.meta      # Permissions
│   ├── README.md                  # User-facing install + usage
│   └── CHANGELOG.md               # Per-release notes
├── README.md                       # This file (developer docs)
├── LICENSE
└── .github/                        # CI: AppInspect on every push (TBD)
```

## Build

```bash
make package
# Produces: TA-trustmodel-0.1.0.tgz
```

```bash
make appinspect
# Runs `splunk-appinspect inspect TA-trustmodel-0.1.0.tgz`
```

(Makefile + AppInspect CI follow in v0.2.0 — TRUS-795.)

## Install

End-user install instructions are in [`TA-trustmodel/README.md`](./TA-trustmodel/README.md).

## Sourcetypes (TRUS-785 v1.0)

Two sourcetypes only. Eval flavor (LLM, COTS, agentic_trace, DataScan) is carried in the `eval_source` field on every event, not as separate sourcetypes.

| Sourcetype | Description | CIM Datamodel |
|---|---|---|
| `trustmodel:eval` | Evaluation lifecycle (one per terminal-state run, all flavors) | none |
| `trustmodel:finding` | Detected risk / issue within an evaluation (PII, bias, proxy, hallucination, etc.) | Alerts (strict) |

Schema source-of-truth: TRUS-785 Confluence doc (TrustModel space → Integrations → Splunk → Event Schema v1.0).

## Related repositories

- [`pdxlab/aurora-gateway`](https://github.com/pdxlab/aurora-gateway) — `splunk_connector` Django app (TRUS-784: HEC exporter that emits these events)
- [`pdxlab/aurora-metrics-v2`](https://github.com/pdxlab/aurora-metrics-v2) — eval scoring + emit hooks
- [`pdxlab/trustmodel-soar-app`](https://github.com/pdxlab/trustmodel-soar-app) — Splunk SOAR app (TRUS-789, follow-on)

## License

Copyright (c) 2026 Predixtions / TrustModel. Proprietary.

## Owner

EPIC: TRUS-783 — Splunk Integration (Cisco-driven). Owner: @ankushgochke.

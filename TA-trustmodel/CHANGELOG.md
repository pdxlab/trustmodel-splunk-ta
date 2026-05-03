# Changelog

All notable changes to TA-trustmodel will be documented here. Format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/) and the project adheres to [SemVer](https://semver.org/).

## [Unreleased]

## [0.1.0-alpha] — 2026-05-03

### Added
- Initial Technology Add-on package skeleton (TRUS-786)
- 5 sourcetypes declared: `trustmodel:evaluation`, `trustmodel:guardrail`, `trustmodel:shadow_ai`, `trustmodel:redteam`, `trustmodel:datascan`
- CIM mapping via field aliases (Risk + Alerts + Inventory)
- Eventtypes + tags for CIM datamodel inclusion
- Search macros (`trustmodel_index`, `trustmodel_evaluation`, etc.)
- README with HEC setup + SDK examples
- Default permissions / metadata

### Pending
- AppInspect-clean validation
- Splunk Cloud Vetted certification
- Dashboard Studio JSON + Simple XML dashboards (TRUS-788)
- ES correlation searches in companion `DA-ESS-TrustModel` (TRUS-787)

# Ramify GA4 vs Ahrefs Audit (April 2026)

Public bundle of the audit materials used to investigate the GA4 vs Ahrefs discrepancy observed on `www.ramify.fr` in February-March 2026.

## Contents

- [`artifacts/report.md`](./artifacts/report.md): final audit report
- [`artifacts/monthly_comparison.csv`](./artifacts/monthly_comparison.csv): monthly comparison dataset
- [`artifacts/analysis.json`](./artifacts/analysis.json): consolidated analysis data
- [`artifacts/summary.json`](./artifacts/summary.json): summarized aggregates
- [`artifacts/daily_www_transition.csv`](./artifacts/daily_www_transition.csv): daily cut around the February-March transition
- [`artifacts/daily_www_organic_pageviews_transition.csv`](./artifacts/daily_www_organic_pageviews_transition.csv): daily `GA4 organic pageviews` vs `GSC clicks`
- [`artifacts/daily_www_organic_pageview_ratios.json`](./artifacts/daily_www_organic_pageview_ratios.json): aggregated ratios by date window
- [`artifacts/page_gap_analysis.csv`](./artifacts/page_gap_analysis.csv): page-level gap analysis
- [`artifacts/page_gap_analysis_with_pageviews.csv`](./artifacts/page_gap_analysis_with_pageviews.csv): page-level gap analysis including pageviews
- [`artifacts/pageview_gap_stats.json`](./artifacts/pageview_gap_stats.json): aggregate statistics for the pageview gap
- [`artifacts/runtime_probe_notes.md`](./artifacts/runtime_probe_notes.md): notes from runtime testing
- [`artifacts/runtime_probe_output.json`](./artifacts/runtime_probe_output.json): sanitized runtime probe output
- [`artifacts/returning_user_probe.json`](./artifacts/returning_user_probe.json): sanitized consent / returning-user probe output

## Sanitization

This public bundle excludes credentials and local tokens.

The runtime probe JSON files were sanitized before publication:

- cookie values were removed or redacted
- `_ga` values were removed
- full `g/collect` query strings were reduced to a small summary of relevant parameters

## Purpose

This repository is intended for review and verification by third parties.

It is not the source workspace used to run the investigation.

# Quantified Claude

A personal daily-automation spec for an AI agent that combines a free-text daily diary, a personal quantified-self spreadsheet (mood, sleep, activity, etc.), medical/genetic context, and environmental/behavioral context (weather, news, browsing, productivity tracking) into a single daily report: a life rating for that calendar date, a data-grounded mood outlook, and a warm, evidence-grounded "life on this date" reading. It originally also ingested Facebook "On This Day" memories; that stage was retired later in the amendments log (see Amendment 43) once the diary became the primary narrative source.

It's designed to run as a scheduled task inside [Claude Code](https://claude.com/product/claude-code) / the Claude Agent SDK — the agent itself does the browsing, data reconciliation, statistics, and report-writing each morning, unattended.

## What's in this repo

- **[SPEC.md](SPEC.md)** — the full specification, including an amendments log showing how the system evolved in real operation (voice changes, new data sources, safety guardrails, retry/ceiling policies, a shelved feature, a weekly rollup).
- **[example-run-log-entry.md](example-run-log-entry.md)** — an illustrative (synthetic, not real personal data) example of the kind of run-completion log the spec calls for.

This repo intentionally does **not** include: the actual daily output PDFs, the quantified-self data, the historical weather/context CSVs, or any Facebook memory content — those are personal data and stay private. What's published here is the *system design*, not the data it produces. A handful of amendments about medical-records integration, a genetic-context subsystem, and a couple of intimate self-tracked columns are deliberately summarized rather than reproduced in full in SPEC.md — the mechanism and governing rules for each are described, but specifics (diagnoses, medication names, genetic findings) are left out.

## Core ideas

- **Statistical honesty over vibes.** The system computes real regressions, anomaly z-scores, and historical-analog comparisons — but the spec bans presenting correlation as causation, presenting probabilistic patterns as fatalistic predictions, or ever recommending substance use or sleep reduction to "improve" a metric.
- **Strict data-alignment contracts.** Personal tracking spreadsheets are often filled in retrospectively (e.g., a "morning" entry describing yesterday). The spec calls out exactly which fields describe which calendar day, since getting this wrong quietly corrupts every downstream statistic.
- **Read-only, always.** Every external source (Facebook, email, productivity trackers, the spreadsheet itself) is accessed read-only. The agent is instructed to hard-stop and hand control back to a human on any login, credential, or security-challenge prompt — never to push through one.
- **Voice matters.** The report format evolved from a dry analytical layout into a "coach/oracle hybrid" — statistics inform the writing but stay in the background, in a small fine-print section, while the main report reads as a warm, specific, evidence-grounded reflection rather than a dashboard.
- **Iterate in the open.** The amendments log in SPEC.md is a real changelog of decisions made after the system was already running daily — new data sources getting added, a feature getting shelved after it kept failing, a collection ceiling getting accepted rather than fought.

## Why this exists

Mostly as an experiment in what a genuinely *personal*, daily-run agent looks like when the spec has to survive contact with real, messy data (spreadsheets with lag, flaky browser automation, connectors that invalidate themselves) — and in keeping the statistical and safety guardrails legible even as the output format got warmer and less technical.

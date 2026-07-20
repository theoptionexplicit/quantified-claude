# Example run-log entry

This is a synthetic, illustrative example of the kind of entry the spec's observability/auditability section calls for — it is **not** a real run, and none of the figures below are real personal data. It exists to show the shape and level of detail a run-log entry should have.

---

## Run: scheduled-YYYY-MM-DD — YYYY-MM-DD 05:00 (local)

**Status: PARTIAL SUCCESS.** Report date was not previously present (idempotency check passed cleanly). Outputs in `<DATA_DIR>/YYYY-MM-DD/`.

- **Facebook (Chrome extension):** connected and worked. Collected 9 memories in full, faithful order, with per-memory text records. Collection then hit the accepted collection ceiling (Amendment 9) — recorded as complete rather than retried into false completeness. Read-only throughout.
- **Quantified Self spreadsheet:** full sheet recovered via raw CSV export (avoiding the summarized reader's truncation). Applying the strict D/D+1 alignment contract, the latest fully aligned outcome day is two days prior (Mood 7, Constitution 7, down 1 from the day before — a meaningful but non-alarming single-day step, still at the 30-day baseline).
- **Weather/AQI:** OK. Current conditions clear, AQI in the "Moderate" range — best air quality in several weeks.
- **News:** OK via web search — a balanced set of US and international sources reviewed; overall tone tense but not extreme.
- **Behavioral context (browsing, email, productivity tracking):** all OK, aggregates only, no raw content retained. One source (a music-listening history) remains structurally unparseable, a known ongoing limitation.
- **context-history.csv:** one new row appended, no duplicate.
- **Analytics:** feature store refreshed incrementally. Adjusted regression (standardized OLS, several hundred complete cases): prior-day Mood and a physical-wellness measure dominant; an active-hobby variable and daily movement robust smaller positives; sleep, screen time, and a substance-use variable weak/non-significant. Anomaly z-scores computed against 30/90-day baselines — one measure notably elevated, flagged as an observation, not a prediction. Historical-analog matching gave a grounding counterpoint below the current reading. RL suggestion loop: prior suggestion row scored against its outcome day; today's suggestions logged.
- **PDF:** built, verified (correct filename, opens, text extractable, all pages rendered to images and visually inspected for layout issues before finalizing).
- **Pending for next run:** score remaining open intervention-log rows once the spreadsheet's entries catch up (they characteristically lag the calendar by a couple of days); a previously-attempted optional module remains shelved per an earlier decision.

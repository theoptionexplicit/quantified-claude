# Daily Facebook Memories Illustration and Life-Summary System — Specification

This is the authoritative spec for a personal automation routine built and run as a Claude Code / Claude Agent SDK scheduled task. It has been generalized for public sharing: personal identifiers, exact file paths, and specific location history have been replaced with placeholders (`the user`, `<DATA_DIR>`, `Region A` / `Region B`). See [README.md](README.md) for an overview. The amendments block below records how the spec evolved through real operation — each entry documents a decision made once the system was actually running, in the order they were made.

**AMENDMENTS (2026-07-17, per the user's instruction — these override the spec text below):**
1. The routine runs daily at **5:00 AM** America/New_York, not 4:00 AM.
2. All outputs live in **"<DATA_DIR>/"**, not "<DATA_DIR>/". Every path in the spec that references the "Facebook Memories" folder (dated output folders, archive/, historical-weather-daily.csv, context-history.csv, contact sheets, PDFs) now uses the "Quantified Claude" folder instead.
3. **Advanced analytics considerations (added 2026-07-17, per the user).** The following five capabilities are part of the process. Each lists the user's requirement, then the operational adaptation for the nightly run (which executes as an agent session, so capabilities build incrementally and degrade gracefully; all of Section 7's statistical-honesty and safety rules still govern — uncertainty preserved, no causal overclaiming, no fatalistic or diagnostic framing):
   1. **Longitudinal machine learning.** Ingest time-stamped self-tracking streams (physiological, behavioral, questionnaire) into a time-indexed relational or columnar store; model temporal dependencies with algorithms such as HMMs or RNNs. *Operational:* maintain `feature-store.sqlite` (or Parquet) in the output folder as the unified time-indexed store, built from the Quantified Self extraction, context-history.csv, and historical-weather-daily.csv; refresh incrementally each run. Prefer simple, auditable temporal models first (autoregressive baselines, hidden-state/regime models via hmmlearn when sample size supports them); report model class and fit diagnostics, never black-box scores alone.
   2. **Causal inference.** Move beyond correlation with a causal-graph or potential-outcomes framework; estimate intervention effects via do-calculus or propensity-score matching. *Operational:* the historical-analog stage (Section 8) should use propensity-score matching or matched comparisons where overlap permits; when a candidate intervention (e.g., movement, instrument-playing) is evaluated, state the assumed causal graph in plain language, adjust for the confounders in Section 7, and label every estimate as assumption-dependent. Use DoWhy or equivalent when available; otherwise use transparent matching/stratification.
   3. **Reinforcement learning for suggestions.** A policy loop observes current state, selects a behavioral nudge, and refines future suggestions from a reward signal (measured Mood/Sleep improvement). *Operational:* log every suggested behavior experiment in `intervention-log.csv` (report date, state summary, suggestions given); on each subsequent run, record next-day Mood/Constitution/Sleep outcomes against prior suggestions and whether the spreadsheet indicates the behavior occurred. Weight future suggestion selection toward experiments with better observed follow-through and outcomes (a conservative contextual-bandit update), but never suppress the safety floor: substances and sleep-reduction remain off the menu regardless of any reward statistic, and suggestions stay framed as experiments.
   4. **Multimodal social-media feature extraction.** Fuse text/image/video/interaction streams into unified representations via joint-embedding models (CLIP/ImageBind-style) to improve detection of mental-health-relevant shifts. *Operational:* for each collected memory, derive per-memory features (text sentiment/topic, media type, people/social density, era) and store only derived numeric features in the feature store — never raw post text, images, or identities in longitudinal logs (Section 17 governs). True joint-embedding models require infrastructure not yet present; treat as an aspirational stage and record its absence as a limitation rather than approximating it with unvalidated proxies.
   5. **Anomaly detection.** Detect out-of-distribution shifts or sudden spikes in the longitudinal streams (Isolation Forests, One-Class SVMs, or statistical thresholding) to flag behavioral outliers that may precede mood drops. *Operational:* each run computes anomaly scores over the recent feature-store window — at minimum robust z-scores against 30/90-day baselines for Mood, Sleep, movement, screen/context-switching, late-night activity, and social contact; Isolation Forest when scikit-learn is available. Flags appear in the PDF's "Where the user is now" section as observations with explicit baselines ("X is N standard deviations above your 30-day norm"), never as predictions of decline.
   - **Stack guidance:** storage = SQLite/Parquet time-indexed feature store (vector store only if/when embeddings exist); frameworks = DoWhy-style causal tooling, conservative bandit logic, scikit-learn/hmmlearn; monitoring = the anomaly-scoring layer above, surfaced in the daily PDF (and flagged prominently when scores are extreme) rather than as unattended alerts.
4. **No illustrations (added 2026-07-17, per the user).** The routine must NOT generate images from Facebook memories. Sections 5 (illustration generation) and the contact-sheet steps are retired, along with their file-organization and image-verification requirements. Facebook Memories are still collected in full every day and remain a primary input to the PDF report — the life rating, memory synthesis, recurring themes, reconciliation with quantified data, and the horoscope-style reading all continue to use them. The dated folder `YYYY-MM-DD/` now contains the PDF (plus optional per-memory text records and an archive/ for superseded drafts). Acceptance criteria referencing per-memory PNGs, caption panels, and contact sheets no longer apply; all other criteria stand.
5. **Browser policy (updated 2026-07-18, per the user): hybrid — Chrome for Facebook collection AND Google Photos sampling ONLY; Firefox for everything else.** The Claude-in-Chrome extension is the sole sanctioned browser-automation channel and is used exclusively to (a) open https://www.facebook.com/memories and read/scroll it to the caught-up state (verified working, logged in, 2026-07-17), and (b) open https://photos.google.com for the Amendment 8 Targeted Photo Sampling stage (approved by the user 2026-07-18), both strictly read-only. No other site may be visited in Chrome by this routine, and all of Section 4's read-only rules apply (never post, react, comment, or change settings; any login/security challenge is a hard stop for the user's takeover). Firefox remains untouched by automation: it is view-only at the platform level, and its role in this system is limited to (a) history aggregates computed from a COPY of places.sqlite and (b) passively visible content when relevant. The Quantified Self spreadsheet is read via the Drive connector (or visible Firefox content) — the routine must not browse to it in Chrome.
6. **Report voice (added 2026-07-18, per the user): coach/oracle hybrid, not analytical.** The PDF must read as if written by a coach/oracle hybrid — mystical framing woven together with concrete, instructional suggestions. Less mathematical: statistics inform the writing but stay in the background, translated into plain language ("the most faithful companion of your good days"), with the numeric/methodological detail confined to a small "Behind this reading" fine-print section at the end (which also carries limitations and citations). All of Section 7's statistical-honesty rules still govern the underlying claims — uncertainty preserved, no causal overclaiming, no fatalism, no substance/sleep-reduction advice — they are simply expressed in the coach/oracle register. The four-part Mood outlook survives as content (the season you're in / what tends to lift you / where you are now / today's practice) rather than as labeled statistical sections. First applied to the 2026-07-18 report; that day's v1 analytical draft is archived in the dated folder's archive/.
7. **RescueTime access path (added 2026-07-18, per the user's authorization).** RescueTime aggregates are collected via its read-only Analytic Data API using the key stored in the desktop client's local config: read `data_key` from `~/Library/RescueTime.com/rescuetimed.json` (the `account_key` in the same file does NOT work — returns "key not found"). Verified working 2026-07-18. Endpoints: `https://www.rescuetime.com/anapi/daily_summary_feed?key=<data_key>&format=json` for daily totals/pulse/category hours, and `https://www.rescuetime.com/anapi/data?key=...&perspective=interval&restrict_kind=productivity&interval=hour&restrict_begin=YYYY-MM-DD&restrict_end=YYYY-MM-DD` for hourly rows (late-night minutes = hours 23:00–03:59). Read-only always: never modify goals, categories, or settings. The key never appears in longitudinal logs or the PDF.
8. **Targeted Photo Sampling module (added 2026-07-18, per the user's augmentation request). SHELVED 2026-07-20, per the user's instruction.** A new workflow stage between Facebook collection and the Quantified Self read, providing visual sentiment context without exhaustive scanning. (a) **Date-gated retrieval:** only photos whose creation time matches the report date's month/day across all years, or the behavior date; sampling cap 5–10 representative photos per date. (b) **Lightweight sentiment analysis:** metadata first (location label, time of day, people tags) for social-connection and activity signals; then visual sentiment sampling of the retrieved photos by the agent's own vision capability (serving as the lightweight VLM), extracting exactly three signals per photo — Dominant Affect (e.g., joy, serenity, stress, loneliness), Contextual Theme (e.g., nature, social gathering, work, solitude), Visual Energy (high/vibrant vs low/muted). (c) **Oracle integration:** cross-reference photo sentiment against the same-date Quantified Self Mood (naming agreements AND discrepancies — e.g., high social activity in photos against a recorded Mood 4 is reported as an appearance/interior tension, never resolved by guessing); select the single most sentiment-dense photo as the visual anchor embedded in the PDF. (d) **Privacy:** strictly read-only; no raw images stored beyond transient working files (the embedded PDF anchor is permitted as part of the report artifact); longitudinal logs receive only derived sentiment tags and an opaque image reference ID. context-history.csv columns (added 2026-07-18): photos_sampled, photo_affect, photo_theme, photo_energy, photo_anchor_ref, source_photos. (e) **Access path:** Google's Photos Library API removed full-library read scopes for third-party apps in March 2025, so automated API retrieval is not available; the sanctioned path is the Claude-in-Chrome extension reading photos.google.com date searches (Amendment 5 allowlist extended accordingly, approved by the user 2026-07-18). Same read-only hard rules as Facebook: never edit, delete, share, favorite, or change any setting; any login or security challenge is a hard stop. **Shelved status:** after repeated permission-grant failures blocking every attempt (2026-07-18 and 2026-07-19), the user decided on 2026-07-20 to drop this module rather than keep troubleshooting it. Future daily runs must skip this stage entirely — no attempts, no "PENDING" notice in the PDF's limitations section, no context-history photo columns populated. The photo columns remain in context-history.csv's schema (left blank) for compatibility; the module may be revisited later if the user raises it again, but is dormant until then.
9. **Facebook collection ceiling (added 2026-07-20, per the user's instruction).** Across the 2026-07-18, -19, and -20 runs, collection has reproducibly stalled on unresolving lazy-load placeholders at roughly the same point each day (around 9–10 memories), regardless of waits, scroll nudges, or a full page reload with re-scroll. The user has directed that ~9 memories be treated as the practical daily collection ceiling. Future runs should still attempt collection normally and continue scrolling in display order, but upon hitting the stall: perform one short verification (a single wait of a few seconds plus one scroll nudge) rather than the full retry procedure (repeated waits, scroll-up/down cycles, and a full page reload with complete re-scroll) that Section 4 previously called for. Record the collection as complete for the day at whatever count is reached, without treating the stall as an open limitation to keep chasing — this is expected behavior, not a failure, unless the count collected is notably lower than the recent norm (in which case a brief note is still warranted).
10. **Weekly rollup report (added 2026-07-20, per the user's instruction).** In addition to the daily life-summary PDF, a weekly rollup report runs every **Sunday**, after that day's daily 5:00 AM run completes. Content: a synthesis across the preceding seven report dates (the just-completed Sunday plus the prior Saturday through Monday) — mood trajectory over the week (daily values, direction, comparison to the 7/14/30/90-day and long-term baselines already tracked), recurring people, places, and themes surfaced across that week's Facebook memories, notable anomaly flags or historical-analog findings from the week's daily runs, and a brief look-ahead in the same coach/oracle voice as the daily reports (Amendment 6 governs voice; Section 7's statistical-honesty rules still apply — no causal overclaiming, no fatalism). Save as `Quantified Claude/weekly/00-weekly-summary-YYYY-MM-DD.pdf` (YYYY-MM-DD = that Sunday's report date). Verify the same way as the daily PDF (opens, text extractable, all pages rendered and visually inspected). **Alert mechanism:** once the weekly PDF is verified, send a `PushNotification` (desktop, and to the user's phone if Remote Control is connected) noting the report is ready and its file path. Email alerting was requested but is not available — no tool exists to send email (the Gmail connector only supports drafting, search, and labeling, never sending), and the user confirmed push notification as the substitute on 2026-07-20.
11. **Expanded Quantified Self column integration (added 2026-07-24, per the user's instruction).** Prior runs used only a narrow subset of the spreadsheet (Mood, Constitution, Sleep, Steps, Total Screen Time, Music, Alcohol). The user confirmed he wants the full sheet used "just as in depth as other data," clarified several ambiguous columns, and asked for a one-time full historical backfill, completed 2026-07-24. `feature-store.sqlite`'s `daily_features` table now carries every spreadsheet column (D/D+1-aligned exactly as Section 6 already specifies — every column except Sleep follows the same "row D+1 describes outcome day D" rule; Sleep alone uses row D's own value as the night preceding day D — **note: this alignment rule was later found to be wrong; see Amendment 41**). Column-specific notes for future runs:
    - **A small number of self-tracked columns cover intimate personal topics and a named partner's health.** Full-depth, non-clinical treatment was explicitly authorized for these by the user, with an explicit care requirement for the partner-related one since it concerns a real third party who isn't present to consent turn-by-turn. Specifics are intentionally omitted from this public spec; the general principle — self-tracked intimate/relational columns may be used at full statistical depth, reported warmly and non-diagnostically, never with more of a named third party's private detail than the single tracked number itself — is recorded here so the discipline is visible even though the columns themselves aren't named.
    - **`Mood-10`**: ignore per the user — not a meaningful column.
    - **`Benzos` / `Benzos Proj` / `Benzo Pace`**: `Benzos` is a within-month **cumulative running total** of a fixed monthly prescription amount (resets each month), not a daily dose — using it raw in a regression is meaningless (near-collinear with day-of-month). Derive two features instead, both now first-class columns in `daily_features`: `benzo_daily` = that day's actual increment (`Benzos` diffed day-over-day, discarding negative diffs across a month reset as missing rather than a real negative dose) and `benzo_pace_gap` = `Benzos − Benzos Proj` (how far ahead of or behind a conservative self-set pacing target the user is running, to avoid running out before refill). Both may be used as regression predictors and named in the PDF. **Hard rule unchanged from Section 7**: never recommend increasing or decreasing benzodiazepine use based on any statistic, and treat any `benzo_daily` ↔ Mood association as very likely reverse-causation-dominated (PRN medication taken *in response to* a harder day, not a cause of it) — state that explicitly whenever the association is reported, never imply the medication is the problem or the fix.
    - **`Cals/10`**: an exact /10 rescaling of `Cals` (r=1.0) — redundant, use `Cals` only.
    - **`B/P Avg`**: an exact average of `Sys`/`Dia` (r=0.9998) — redundant, use `Sys` and `Dia` individually instead (more information than the average alone).
    - **`Total Screen Time` vs. `Comp Time` + `Cell Time`**: in the current tracking era these satisfy `Total Screen Time = Comp Time + Cell Time/60` **exactly** (Comp Time in hours, Cell Time in minutes) — an exact linear dependency, not just correlation, that produces a singular design matrix if all three are entered together. Use `Comp Time` and `Cell Time` as the two predictors and drop the `Total Screen Time` aggregate from regressions (it can still be reported descriptively). First run with the expanded model found phone time (`Cell Time`) significantly negatively associated with Mood (n=783, z≈−2.3) while computer time (`Comp Time`) showed no effect — report this distinction (phone vs. computer) rather than collapsing back to one screen-time number.
    - **`Followers`**: the raw level is a slow multi-year growth trend (std ≈ 838 across history vs. a day-over-day std of ≈3), so it's confounded with the model's own year/trend control if entered as a level. Use `followers_delta` (day-over-day change, now a first-class `daily_features` column) instead. `IG Likes` and `TikTok Likes` are fine to use as levels (genuine day-to-day variation, not a trend).
    - **`Cigarettes` / `Cigs Target` / `Cigs Smoked` / `Vape Pulls Actual` / `Vape Target` / `Psilo` / `Interval on` / `Interval off` / `BPM drop` / `Projected Mileage` / `Actual Mileage`**: all stopped being logged around a year before this amendment and do not overlap the current tracking era used by the main model. Treat these as **historical/inactive** — report them descriptively when relevant (date range, frequency) rather than as live regression predictors, and don't flag their absence as an ongoing limitation. Several are additionally too sparse for a reliable coefficient even within their own active window.
    - **Main regression going forward**: prior-day Mood, Constitution, Sleep (preceding), Steps, Comp Time, Cell Time, Music, Alcohol, Weight, Heart Rate, Cals, IG Likes, `followers_delta`, `benzo_daily`, `benzo_pace_gap`, plus the existing season/year controls, plus the one full-depth intimate self-tracked column noted above. Complete-case n≈783 (2024-04-20 onward) as of the 2026-07-24 backfill; recompute fresh each run as more data resolves. The partner-mood column and blood pressure (`Sys`/`Dia`) run as separate secondary regressions given their different, sparser windows.
12. **Location/travel history integration via a private local Timeline rebuild (added 2026-07-26, per the user's instruction).** The user maintains a private local rebuild of Google's discontinued Timeline web app, parsing Google Takeout Semantic Location History into per-day move/visit event lists. This is now a contextual data source for the daily/weekly system, split into two tracks:
    - **Coverage gap — live daily integration is BLOCKED.** The parsed data currently spans a multi-year historical window only, ending in late 2024. The user's Google account migrated to on-device end-to-end-encrypted Timeline storage sometime after that, and no on-device export path has been found yet. Because the daily/weekly reports' behavior date is always recent, there is currently **no Timeline coverage for any report date this system will produce** until that export gap is resolved. Do not attempt to pull "yesterday's" travel data for the daily pipeline; there is nothing there. Re-check coverage periodically in case the user resolves the export gap and re-runs the backfill with fresh months.
    - **Retroactive backfill — DONE, in active use.** `backfill_travel_features.py` parses all of the rebuilt Timeline's monthly JSON files and writes derived, privacy-safe daily aggregates into `feature-store.sqlite`'s `daily_features` table, keyed by the actual calendar date (no D/D+1 shift — unlike the Quantified Self spreadsheet, this reflects the literal day, like the weather columns). Columns: `travel_km` (total distance across all move segments that day, a coarse, GPS-noise-inclusive aggregate — treat as directional, not exact), `travel_visits` (count of visit segments), `travel_home_anchored` (1 if any visit that day was classified home), `travel_work_visit` (1 if any visit was classified work), `travel_flew` (1 if any move segment had flying mode — a strong single-day travel/trip signal), `travel_novel_place` (1 if any visited place had never been seen before in the chronological history), and `travel_dominant_mode` (the move mode with the greatest total distance that day). Days within the covered window with zero recorded location events are left NULL — this is normal sparsity, not zero travel, and must not be treated as or imputed to zero. Re-run the backfill script any time the Timeline rebuild's own source data is rebuilt with new months; it is idempotent (updates in place, never duplicates rows).
    - **How to use it in reports, given the gap.** (a) *Same-date history* (Section 6): for years within the covered window, add a brief travel-context line when available and materially interesting (e.g. "home-anchored that day" / "away, dominant mode: train" / "a flight day") alongside the existing Mood/Constitution/Steps extraction — descriptive context, not a new statistical dimension. (b) *Historical analog matching* (Section 8): because the **current target day never has Timeline coverage**, travel features cannot yet be used as a formal matching dimension — do not add them to the standardized-distance feature set until live coverage resumes. Instead, report them descriptively as a cross-reference on the matched candidate days when coverage exists for enough of them to be worth mentioning — a texture note, not a scored input. (c) Never surface raw addresses, place names, place IDs, or coordinates in the PDF, `context-history.csv`, `feature-store.sqlite`, or `run-log.md` — only the derived aggregates above, per Section 17's existing discipline for all other contextual sources.
13. **Storage and process streamlining (added 2026-07-28, per a code-review-style suggestion set from a separate AI system, evaluated and partially adopted by Claude per the user's request to "implement" them with engineering judgment applied rather than blind compliance).** Five proposals were reviewed. Two were adopted as-is, one was adopted with a materially different implementation than proposed (to avoid real data loss the original proposal would have caused), one was a documentation clarification only (no runtime behavior changed, because the described problem didn't actually exist in the running system), and one was declined outright with reasoning recorded below.
    - **(a) ADOPTED, with a corrected implementation — `context-history.csv` de-duplication.** The proposal ("make `feature-store.sqlite` the single source of truth; convert `context-history.csv` into a bare pointer manifest") was directionally right but factually wrong about current state: `feature-store.sqlite` was **missing** several fields that existed only in `context-history.csv` — all RescueTime columns, all Apple Music columns, AQI/PM2.5/ozone, and several Firefox/Gmail sub-fields. Collapsing the CSV as originally proposed would have silently deleted the only copy of that historical data. Corrected sequence actually executed: (1) `feature-store.sqlite`'s `daily_features` table extended with 19 new columns; (2) all existing dates backfilled into the new columns from the then-current `context-history.csv`, verified zero data loss; (3) the original full `context-history.csv` archived before any destructive edit; (4) `context-history.csv` rewritten to a lean 13-column **run manifest**: `date, status, facebook_status, source_weather, source_news, source_apple_music, source_firefox, source_gmail, source_rescuetime, weather_zip_stale, latest_aligned_mood, latest_aligned_constitution, notes`. The free-text `notes` field and the append-only nature of the file were deliberately **kept, not dropped** — `notes` carries the run's full narrative reconciliation, which has no structured home in the feature store, and the file's append-only, never-corrected-in-place design is a real audit-trail property distinct from the feature store's mutable "current best knowledge" role. Going forward, AQI/PM2.5/ozone, Apple Music aggregates, Firefox category shares, extended Gmail fields, and RescueTime aggregates are written to `feature-store.sqlite` only; `context-history.csv` receives only the 13 manifest fields above per run.
    - **(b) Documentation clarification only — the "Digital Exhaust" hierarchy.** The proposal assumed RescueTime and Firefox aggregates were being fed into the adjusted regression alongside the spreadsheet's `Comp Time`/`Cell Time`, risking multicollinearity. Checked against the actual regression predictor list (Section 7, unchanged): RescueTime and Firefox have **never** been regression inputs — only `Comp Time` and `Cell Time` (both spreadsheet-sourced) are used quantitatively; RescueTime and Firefox have always been contextual/narrative sources only. No regression code changed because there was nothing to fix. **The QS spreadsheet is the sole source of quantitative screen-time predictors; RescueTime and Firefox are qualitative/descriptive only, never predictors in the same model.**
    - **(c) ADOPTED — consolidated PDF verification.** Sections 2 and 15 both independently described the render-and-visually-inspect verification procedure. Section 2's pipeline-overview step now cross-references Section 15 rather than restating the procedure; Section 15 remains the single canonical description.
    - **(d) DECLINED — merging the main regression with the partner-mood / blood-pressure secondary regressions into one "tiered core + extended" model.** This one is a real statistical regression, not a simplification. The partner-mood column and blood pressure have materially sparser and differently-shaped availability windows than the core predictor set. A combined model requires every row to have *all* core and extended predictors simultaneously non-null (complete-case listwise deletion) — this would shrink the core model's sample down to the intersection with the sparser columns, exactly the outcome this amendment's own architecture was written to prevent. The proposal's secondary claim — that reporting blood pressure as "checked-and-null" is "just noise" — is also declined: a checked-and-null result is honest, informative science, and suppressing it would violate Section 7's own statistical-honesty requirements. **No change to the regression architecture.** The three-model structure (core adjusted regression + two independent secondary regressions) stands, now formally labeled "Core model" and "Secondary models" in report language for clarity.
    - **(e) ADOPTED — consolidated idempotency check.** Sections 2, 3, and 11 each independently described the same single check (does a verified PDF + matching `context-history.csv` row already exist for this report date). Section 11 remains the single canonical description; Sections 2 and 3 now cross-reference it rather than restating the logic. No behavior changed.
14. **Amendment 8 (Targeted Photo Sampling) UN-SHELVED (2026-07-28, per the user's explicit instruction, after a live ad hoc test this same session succeeded cleanly against three real dates).** Amendment 8's shelving rationale — repeated permission-grant failures — no longer holds as a blanket blocker: the photos site loaded and was searchable by date on the first attempt this run, with no permission wall encountered. Amendment 8's design (date-gated retrieval, per-photo Dominant Affect / Contextual Theme / Visual Energy extraction via Claude's own vision, cross-reference against QS Mood naming agreements *and* discrepancies rather than resolving them by guessing, one anchor photo, strict read-only) is reinstated as an active daily pipeline stage, with the following corrections and additions:
    - **(a) Storage corrected for Amendment 13.** Amendment 8's original design wrote six fields to `context-history.csv`. Under Amendment 13, `context-history.csv` no longer carries structured per-source data — these six fields (plus a new seventh, below) are instead added as columns on `feature-store.sqlite`'s `daily_features` table, one row per date, consistent with every other source integration since Amendment 13.
    - **(b) New signal added — `photo_travel_signal`.** A simple descriptive flag (e.g. "home-based", "local outing", "travel/location change evident") based on whether the day's sampled photos show a location change from the ordinary home/local baseline — texture, not a formal historical-analog matching dimension.
    - **(c) Sampling target for the daily routine.** Each day, sample the **behavior date** (yesterday) primarily. Same-month/day-across-years sampling remains available for ad hoc or same-date-history use but is not a required daily step.
    - **(d) Shared albums explicitly excluded**, per the user's instruction. Only the user's own library content — never albums or photos shared *to* him by others.
    - **(e) Anchor photo embedding.** Since programmatic download was never the sanctioned path, the anchor photo is captured via a full-page browser screenshot (already-permitted read-only viewing) and cropped for PDF embedding. The screenshot is a transient working file, never persisted outside the dated folder beyond what's embedded in the final PDF; `photo_anchor_ref` in the feature store stores an opaque descriptor only, never a URL, file path, or raw image.
    - **(f) Failure handling matches Facebook's, not the old shelve-on-failure pattern.** If the photos site hits a permission wall or other failure on a given run, that run's photo-sampling stage is marked PENDING (like Facebook) and the rest of the routine proceeds — it is not grounds to silently re-shelve the module. If it fails on **three or more consecutive runs**, flag this prominently as a pattern rather than either quietly retrying forever or unilaterally shelving it again.
    - **(g) All of Amendment 8's original hard rules stand unchanged**: strictly read-only, any login/security/identity challenge is a hard stop, no raw images in longitudinal logs, sampling cap 5–10 photos per date.
15. **Diary Entries integrated as a primary narrative source (added 2026-07-28, per the user's instruction).** The user has begun writing free-text daily diary entries into a new "Diary Entries" column in the Quantified Self spreadsheet. A small initial sample existed as of this amendment, described as one he would expand over time.
    - **(a) Alignment, confirmed directly by the user.** Diary Entries follow the *general* D/D+1 rule already governing every daily field except Sleep (Section 6) as originally understood — **later corrected; see Amendment 41, which found the spreadsheet is actually own-row throughout, diary included.**
    - **(b) Treated as qualitative narrative, not (yet) a quantitative predictor.** At small and growing sample size, this is explicitly not enough for NLP-derived sentiment scoring or regression treatment — consistent with Section 8's "small samples are hypotheses, not personal laws." Diary Entries function as a **second primary narrative source alongside Facebook memories**: read in full, used to inform the day's synthesis directly, cross-referenced against that day's QS Mood and other sources for agreements and contradictions. Revisit quantitative treatment (e.g., a word-count or sentiment-score predictor) once sample size supports it; `diary_word_count` (below) is tracked now specifically so that future threshold is measurable.
    - **(c) Storage, per the user's explicit instruction.** Diary Entries are to be "referenced in the daily report and used to inform the report overall" — the same treatment as Facebook memories, not a more conservative one. Raw diary text for the report's behavior date may be saved to a per-report dated-folder text file — never in `context-history.csv` or `feature-store.sqlite`, matching the existing discipline that longitudinal logs never carry raw personal text from any source. `feature-store.sqlite`'s `daily_features` table gains two new columns for this: `source_diary` (availability flag) and `diary_word_count` (a simple numeric proxy, descriptive only, not yet a regression input per (b)).
    - **(d) Voice, per the user's explicit instruction: heavy content is named plainly, not softened.** Where a diary entry references drinking, medication, grief, or distress, the daily PDF should name it directly and specifically — the same directness already used for Facebook-memory synthesis. This does **not** relax any of Section 7's or Amendment 6's existing hard rules: still never diagnostic, fatalistic, or alarmist; still frames guidance as experiments; still preserves agency and uncertainty. "Named plainly" governs *whether* something gets said, not a license to abandon the coach/oracle voice's care in *how* it's said.
    - **(e) Named persons.** Diary entries reference a number of recurring real people beyond the user's partner — family and friends who come up in day-to-day life. Per the user's standing invitation, relationships/context for these are asked about contextually, as they become load-bearing for a specific report's synthesis, rather than resolved as a batch upfront. Specific names are intentionally omitted from this public spec.
    - **(f) Scope: forward-only, not retroactive.** This amendment governs runs from 2026-07-29 onward. Existing dated folders are not retroactively regenerated or backfilled with diary text files, even though some of them have corresponding diary entries.
16. **Google Photos: a second sanctioned browser channel added, alongside the Claude-in-Chrome extension (added 2026-08-01, per a live troubleshooting session with the user).** The 2026-08-01 run found Facebook working normally via the Claude-in-Chrome extension but Google Photos hitting a persistent domain-block error — the fourth-plus PENDING run in a row for this module, following the pattern flagged at Amendment 14(f)'s three-run threshold. Live troubleshooting (the user confirmed the extension's own site-access setting is "on all sites," and confirmed nothing at all appears in Chrome when the block fires) established that this specific block occurs *before the request ever reaches Chrome*, ruling out both the extension's site-access list and a Chrome-side permission dialog as the cause. The root cause sits in a layer neither the user nor Claude can inspect or change from within this session and remains unresolved as an open question.
    - **(a) A working alternative channel was found and adopted.** The separate sandboxed "Browser pane" tool (distinct from both the Claude-in-Chrome extension and Firefox — a fully isolated browser context with no default connection to the user's real Chrome profile, cookies, or extensions) was tested live against the photos site and was **not** subject to the block: it loaded the page cleanly. It had no persistent Google session by default; the user logged into his Google account inside that pane manually, after which it correctly showed his real library content — confirmed live 2026-08-01.
    - **(b) New protocol for the Google Photos stage, effective 2026-08-02 onward.** Each run attempts the sandboxed Browser pane *first* for the photos site (in place of the Claude-in-Chrome extension, which remains reserved for Facebook only per Amendment 5). If the pane loads already logged in, proceed with sampling exactly as Amendment 14 already specifies. If the pane is *not* logged in, do **not** attempt to log in as the user under any circumstance — credential entry is always prohibited, full stop. Instead, send a `PushNotification` reminding him this fallback channel exists and that a fresh login would restore it for that day's sampling, sent **before** the report/PDF is generated, then continue the rest of the routine regardless of whether he responds in time. If the module doesn't complete in time, mark it PENDING for that run — a graceful degradation, not a blocking condition.
    - **(c) Open question, to be resolved by the first live automated run under this protocol.** Whether the sandboxed Browser pane's login session survives between chat sessions is untested as of this writing.
    - **(d) Facebook is unaffected by this amendment.** The Claude-in-Chrome extension continues to be the sole channel for Facebook Memories collection.
17. **Architecture of Well-being merged into the daily unsupervised report (added 2026-08-02, per the user's explicit instruction).** A separate research protocol (Trigger Map / Resilience Profile / Narrative Arc lenses, plus three candidate new signals — Communication Latency, Calendar Density, App-Switching Frequency) was designed and validated this same day as a standalone, deliberately-invoked analysis, built and run against the real feature-store history. Claude's original design deliberately kept this as a *separate* track from the daily report specifically because its "Trigger Map" framing (naming precursors to "acute" mood episodes) sounded more clinical/diagnostic than the daily report's own hard rules were built to allow, and an unsupervised report is a different risk profile than one the user reviews on request. The user was shown that tradeoff explicitly and chose full integration anyway: **the Trigger Map, Resilience Profile, and the validated new signals are now part of the nightly pipeline**, not a separate research track.
    - **(a) What's now collected and computed every night.** In addition to everything Section 6 and Amendment 3 already specify:
      - **Communication Latency**: read-only Gmail metadata-only thread search (sender/recipient/timestamp/label only, never subject or body) covering a window wide enough to catch reply pairs landing on the behavior date; compute that day's median reply latency in hours. Store as `gmail_reply_latency_hours` and `gmail_reply_count` in `feature-store.sqlite`.
      - **App-Switching Frequency**: read-only RescueTime interval data for the behavior date. Compute app-to-app switch rate per hour and total minutes spent in contiguous same-app runs of 25+ minutes ("deep-work minutes"). Store as `rescuetime_app_switch_rate` and `rescuetime_deep_work_minutes`. RescueTime's free plan only ever exposes a rolling ~2-week window, not a fixed historical start date — a day not captured into `feature-store.sqlite` within roughly two weeks of occurring is gone permanently. Each night's run should also opportunistically check for and backfill any gap in the trailing 2-week window that a prior run missed.
      - **Calendar Density**: attempted nightly via the read-only Calendar connector, personal calendar only (never a calendar belonging to someone else, even if shared), classified into Obligatory/Restorative/Ambiguous hours. Remained PENDING for a period after the first-pass keyword classifier returned an implausible reading and needed rebuilding (later fixed — see Amendment 28). Collect and store the fields when possible so the history exists once the classifier is trustworthy, but do not surface it as an active finding in the PDF until it is.
      - **State labeling**: recomputed fresh each night across the *full* feature-store history (not just the behavior date): a day is `acute` if Mood≤4; `onset` is the first day of a run of `acute` days; `pre-crash` is the day immediately before an onset day that is not itself acute; `recovery` is the first non-acute day after an acute run; everything else is `stable`. Stored as a single `state_label` column on `feature-store.sqlite`'s existing `daily_features` table.
    - **(b) What's now computed nightly (pure analysis, no new collection).** Trigger Map: for the current candidate precursor set (steps, constitution, cell time, comp time, sleep, alcohol), compute each field's flagged-rate (bottom or top quartile, direction per field) in the 1–3 days before the most recent onset, compared against its base rate on all other days. Resilience Profile: identify all historical days carrying the top validated risk signature (steps and constitution both in the person's own bottom quartile, not yet acute), split into crashed-within-72h vs. did-not, and compare the two validated buffers (partner mood and active-music minutes) between the groups, reporting the no-crash rate and both buffers' group means. Both recomputed fresh every run against the then-current full history.
    - **(c) What's explicitly excluded from the nightly merge.** The Narrative Arc lens stays out of the daily report entirely — it remains a deliberately-invoked, ad hoc lens only (later revived as a periodic report — see Amendment 22). Communication Latency is collected and stored nightly but is **not** surfaced as an active finding or warning sign in the PDF — tested against real reply events over ten months, it showed essentially no relationship with same-day Mood, so treating it as a signal would contradict this system's own statistical-honesty rules. It may still appear in the "Behind This Reading" fine print as a checked-and-null result.
    - **(d) The voice does not change — only where this content appears does.** Section 7's and Section 17's hard rules stand completely unchanged: never diagnostic, never fatalistic, never falsely predictive, no substance-use or sleep-reduction recommendations regardless of any Trigger-Map or Resilience-Profile finding, guidance still framed as experiments, uncertainty and agency still preserved. State labels (including `acute` and `pre-crash`) may be named directly and plainly in the report, per the user's explicit preference for directness over softened language — but every such mention must carry real numbers and explicit non-causal framing (recurrence rate *and* base rate stated side by side; small-sample caveats stated as numbers, not vague hedges; "this is a recurring pattern in your own data, not a diagnosis and not a prediction" said plainly rather than buried).
    - **(e) New report section.** The daily PDF gains a section presenting: today's state label plainly named; whether the validated precursor pattern is present in the 1–3 days behind the behavior date, with real recurrence-vs-base-rate numbers; whether the validated resilience buffers were present that day when a risk pattern is showing; and the App-Switching signal when it's notably elevated or depressed relative to the person's own recent baseline.
    - **(f) Why this is recorded as an explicit override, not a quiet change.** Claude's original separation of the two tracks was a considered safety design choice, not an oversight. The user was shown the specific tradeoff directly (diagnostic-feeling language appearing daily, unsupervised, vs. only when he asks for it) before deciding.
18. **Architecture of Well-being section: plain-ratio register locked in as the enforced standard (added 2026-08-02, per the user's explicit instruction after a same-day correction).** The first live run under Amendment 17 initially composed "The Architecture Underneath" in academic statistical language in the main body — percentages and sample sizes stated as such. The user flagged this directly: it didn't match the register of the sample he'd reviewed and approved the night before. This amendment tightens the prior general instruction into a concrete, checkable rule.
    - **(a) The rule, stated concretely.** In the main body of "The Architecture Underneath" (and any comparable section presenting Trigger Map, Resilience Profile, or comparable quartile/base-rate findings), state every recurrence-vs-base-rate comparison in plain ratio language — "about half the time" / "roughly 1 in 4" / "all three of those days" — never as a bare percentage or sample-size notation in the sentence itself. Precede findings with a short, plain "How I checked this" framing sentence when it aids clarity. State small-sample and checked-and-null caveats in an honest, direct style rather than a hedged statistical qualifier. Where the current day's numbers connect across signals, name that connection explicitly when the day's actual data shows it.
    - **(b) Where the fuller numbers still belong.** This rule governs the main body only. Percentages, exact base rates, z-scores, sample sizes, and p-values remain correct and expected in "Behind This Reading" fine print, per Amendment 6's existing design — nothing here relaxes that split, it only extends the same plain-body/statistical-fine-print discipline to this specific section, which had drifted from it.
    - **(c) Two working precedents now, not one.** The original validated sample report and the corrected same-day rebuild are both the working register precedent. Any future run composing this section should read as continuous with both, not reinvent the voice from Section 7/17's rules alone.
19. **Medical records integrated as a new contextual data source (added 2026-08-02, per the user's explicit instruction and choices, after clarifying questions).** The user provided a structured export of his primary-care medical history, and this system built a new set of tables in `feature-store.sqlite` to hold derived, non-raw contextual fields from it (condition/medication/visit history, vitals, labs), subject to the same read-only, non-diagnostic, never-a-basis-for-medication-advice rules that govern every other source in this system. Specifics of the data itself — diagnoses, medications, dates, record counts — are intentionally omitted from this public spec. The governing principle, stated once for the record: known, already-diagnosed medical background may be named as background fact when directly relevant to a day's synthesis; it may never be used to explain, predict, or moralize about a specific day's Mood, and it never licenses a medication or substance-use suggestion — that hard line is unchanged by this amendment and reaffirmed explicitly in later ones (see Amendment 39(c)).
20. **Medical context activated in the standard nightly routine; whole-system synthesis deepened to actively test the user's own hypotheses (added 2026-08-02, per the user's explicit instruction).** Amendment 19 built the medical-records data layer but deliberately stopped short of wiring it into the nightly PDF's composition step, pending a decision on whether the user wanted it in the default unsupervised report. He said yes. Section 8's composition step now weaves in relevant medical context where it materially bears on a day's synthesis, additively rather than as a forced daily block. Separately, Section 12 (Whole-system interpretation) gained a standing capability: when the user names a suspected pattern about himself, test it directly against the full history (same-day and both lag directions, plus a state-label breakdown) and report the real answer — confirmed, partially confirmed, or checked-and-null — rather than only reporting what survives the omnibus regression's significance filter. (One intimate self-tracked column was tested this way, per Amendment 11's full-depth authorization; the specific finding is omitted from this public spec, consistent with Amendment 11's redaction of that column.) The voice rules do not change: never diagnostic, never fatalistic, never falsely predictive, no medication-change or substance recommendations, small samples and modest effect sizes named as such rather than oversold.
21. **Forward-looking content re-scoped: honest probability, elevated and expanded — not false certainty, which remains fully prohibited (added 2026-08-02, per the user's explicit request and context, after Claude raised its own reservation directly first).** Section 1's original standard — "The output must preserve uncertainty and agency. It must never sound fatalistic, diagnostic, alarmist, or **falsely** predictive" — has governed this system since its first draft and is **not being weakened**. What changes here is the operational reading of "falsely": it prohibits asserting an unknown future outcome as if it were fact (a false certainty), and always will. It has never actually prohibited reporting genuine, honestly-caveated historical base rates and probabilities — that distinction simply wasn't being exercised, and forward-looking content was kept small, hedged, and buried as a "grounding counterpoint" in fine print rather than given real space in the main body.
    - **(a) The context that changed this.** Claude raised a direct reservation before touching this rule: mood forecasting for someone managing a real, diagnosed condition carries genuine risk (a predicted good stretch inviting dismissal of real warning signs; a predicted hard stretch becoming its own weight; either eroding the "pattern, not prophecy" honesty this whole system depends on). The user's response, in full: he has been psychologically stable for many years, actively works with a professional care team to maintain that, and experiences these reports as "a meaningful and interesting part of my own care" — not as his primary safety mechanism. That context is why this system can now afford to give forward-looking content more room than a system serving as someone's sole support could safely give it.
    - **(b) What's now elevated.** The historical-analog and Resilience-Profile machinery already computed genuine forward-looking statistics — Section 8 already required this. Previously this lived as a small, hedged-into-near-invisibility paragraph. It is now a first-class, prominently-placed section in both the daily report and the periodic Narrative Arc report (Amendment 22), reported with the same real-numbers plain-ratio register Amendment 18 established elsewhere: actual frequencies ("13 of 20 similar days held in this range over the following week"), not vague hedges. Where sample size supports it, extend the trajectory horizon beyond the current +1/+2/+3 days, especially in the periodic report.
    - **(c) What is still, and will always remain, prohibited.** Asserting a specific future outcome as certain or as fact. Any claim not actually grounded in the user's own historical pattern data. Fatalistic or alarmist framing of any kind. Removing agency — every forward-looking statement must sit alongside what tends to help, never as a fixed verdict. Diagnosing what a specific day's Mood "is" or "means" clinically. Any suggestion regarding medication, substance use, or sleep reduction, regardless of what any probability shows.
    - **(d) Named as a real, considered change, not a drift.** Recorded explicitly as a deliberate policy shift, made after Claude stated its own reservation plainly and the user responded with real context that changed the analysis.
22. **The Narrative Arc lens revived as a periodic "life in chapters" report — cadence: quarterly, not nightly (added 2026-08-02, per the user's choice between the two options presented).**
    - **(a) What it is.** A periodic report — separate from the daily life-summary and the Sunday weekly rollup — that steps back from any single day and looks at the user's life in chapters: recurring themes, relationships, and turning points across the full tracked history. It should surface things a single day's report structurally cannot: how a season or era of life compares to others, stretches of high vs. low tracked engagement. Per Amendment 21, it may include genuine forward-looking content using the same honest-probability standard — never certainty, always grounded in the user's own history, agency-preserving, framed as pattern not prophecy.
    - **(b) Cadence: quarterly for the deep rebuild, daily for a lightweight reference.** The quarterly run does the actual deep work — defines chapter boundaries, computes era comparisons, extends forward-looking trajectories to a longer horizon. Every **daily** report then carries a short "Where Today Sits" thread that *references* the current chapter from the most recent chapters/ report rather than re-deriving chapter-level synthesis from scratch each night.
    - **(c) Save location and verification.** `chapters/00-life-in-chapters-YYYY-MM-DD.pdf`, verified the same way as every other PDF in this system (Section 15/Amendment 13(c)).
    - **(d) Voice.** Same coach/oracle register as the daily report, scaled up in scope.
    - **(e) First build: 2026-08-02.**
    - **(f) The daily "Where Today Sits" thread.** Once at least one chapters report exists, every daily report adds a short paragraph naming which chapter today falls in and one honest observation about whether today's data sits comfortably inside that chapter's established pattern or looks like an early sign of something shifting. This is a **reference**, not a recomputation. If today's data looks like a meaningful divergence from the current chapter, name that directly — but never overclaim a new chapter from a single day, and never let this thread drift into forecasting (it describes today's relationship to the *established past* pattern, not what happens next).
    - **(g) Storage for the daily thread to reference.** The quarterly build writes chapter definitions (name, start/end dates or "ongoing," and defining statistics) to a new `feature-store.sqlite` table, `life_chapters`. Daily runs read this table; they do not re-open or re-parse the chapters PDF itself.
23. **Three changes to the daily routine (added 2026-08-03, per the user's explicit instruction, given directly in conversation rather than through the scheduled run).**
    - **(a) Google Photos sampling window widened from a single day to a rolling 14-day window, synthesized rather than reported day-by-day.** Going forward, each run samples the behavior date **and the 13 days before it** (14 days total), and produces one **combined** sentiment read across that whole window rather than a single day's reading. Practical method: triage at the grid/thumbnail level across all 14 days first, then apply the existing three-signal vision analysis to a representative subset rather than opening every single photo. Synthesize the output as: (i) an affect **arc** across the window rather than a flat list, (ii) recurring themes/subjects, (iii) a combined travel signal, and (iv) one anchor photo selected as the single most sentiment-dense image from across the *whole* 14 days. Cross-reference against the **Mood trend over that same 14-day window**, naming agreements and discrepancies. All existing hard rules are unchanged.
    - **(b) Weather, air quality, and news are now treated as candidate influences on the user's own state, not neutral scene-setting.** Previously these three sources were collected and reported purely as context/texture, explicitly excluded from the regression predictor list and never examined for association with Mood. The user asked for this to change. Concretely: (i) a weather composite (apparent temperature) is added to the **core adjusted regression's** predictor list; its near-complete historical coverage means this shouldn't meaningfully shrink the core model's sample. (ii) AQI/PM2.5 and news intensity/tone run as their **own secondary regressions**, following the same precedent already established for the partner-mood and blood-pressure columns — their historical availability is sparser and differently-shaped. (iii) Report whatever is actually found, including a **checked-and-null** result if that's what the data shows. All of Section 7's existing statistical-honesty rules apply in full and are unchanged.
    - **(c) Today's Practice suggestions may now draw on general, externally-researched ideas, not only what has empirically worked for the user's own tracked history.** Section 7's suggestion-scoring loop previously weighted suggestions solely by observed follow-through and outcomes against the user's own intervention-log history — a closed loop, by design. Going forward, each run's Today's Practice experiments may draw on general behavioral-science/wellness research as well as the user's own scored history. When a suggestion is research-derived rather than personal-history-derived, say so briefly and honestly. All existing hard limits stay exactly as strict and are **not** loosened by this change: no medication, substance, or sleep-reduction suggestions regardless of source; never diagnostic or fatalistic; guidance stays framed as optional experiments, never obligations.
24. **Full directness as the baseline, not a ratcheted permission (added 2026-08-06, per the user's explicit instruction, given directly in conversation immediately after that day's scheduled run).** Discussing that day's report, Claude described its own directness as something that had been "allowed to grow over time" as trust accrued across this project's history. The user rejected that framing outright: the trust already exists and has existed across this project's whole run, gradualism for its own sake is itself a subtle form of paternalism, and he does not want directness metered out further — he wants it at full strength now, as the default, in every report and every conversation about this project.
    - **(a) What changes.** Interpretive directness — naming what the data shows and what it's believed to mean — is the baseline from this point forward, not a target approached asymptotically. No burying a real finding under stacked qualifiers, no triple-hedging an interpretation the data actually supports, no softening language purely out of an instinct toward caution.
    - **(b) The user's own stated position, recorded for future reference.** He considers himself the appropriate judge of what to do with any data-driven suggestion or interpretation this system produces. He also holds that real insight sometimes requires interpretive leaps rather than incrementally-hedged caution.
    - **(c) What does NOT change — named explicitly so this amendment is never misread as loosening the hard rules.** The handful of remaining hard limits stand exactly as before: no medication/substance/dosage-adjustment suggestions ever, regardless of source or statistic; never asserting a future outcome as certain; never diagnosing what a specific day's Mood clinically "is" or "means." These are **competence and epistemic boundaries, not trust boundaries.** Claude is not the user's psychiatrist, has no clinical training, no lab access, no titration history, and a pattern in a spreadsheet is not equivalent evidence to what a treating clinician works from. **The operative test for any future ambiguous case:** if the limiting factor is "Claude isn't qualified to say this / can't actually know this," the limit stays regardless of how direct this system has otherwise become; if the limiting factor was merely "this might be uncomfortable to say plainly," it no longer applies as of this amendment.
    - **(d) Practical effect.** Applies immediately and retroactively to this system's own posture.
25. **A second, parallel daily report added — a "data analysis" edition, alongside the standard coach/oracle report, not a replacement for it (added 2026-08-07, per the user's explicit instruction, given directly in conversation after reviewing a same-day experimental composition).**
    - **(a) What runs every day now.** In addition to the standard life-summary PDF (entirely unchanged by this amendment), each daily run also composes a data-analysis PDF in the same dated folder. Both reports are built from one single data-collection and analytics pass.
    - **(b) The data-analysis report's governing rules, verbatim from the user, and binding on every future run of this edition:** "You are a data analysis agent. Your job is to find what the data is actually saying, not just describe what is in it. When given a dataset or numbers: 1. Identify what kind of data this is and what questions it can realistically answer. 2. Look for patterns, outliers, and trends - not averages alone. 3. For every finding, ask: is this interesting or is this obvious? Cut the obvious. 4. Flag anything that looks wrong - missing values, suspicious spikes, inconsistencies. 5. Deliver findings ranked by importance, not by where they appear in the data. Rules: Never describe what the data contains. Interpret what it means. If a number is surprising, explain why it is surprising. If the data cannot answer the question being asked, say so directly instead of stretching the interpretation. End every analysis with one sentence: the single most important thing this data suggests you should do." The validated structure: a short framing section naming what each data stream can and cannot support; findings ranked by significance; an explicit "what this run cannot tell you" section; a closing single-sentence directive; a brief methodology footer.
    - **(c) Hard rules are identical to the standard report and do not loosen for this edition.** No medication/substance/dosage-adjustment suggestions ever; no future outcome asserted as certain; no clinical diagnosis of what a specific day's Mood "is"; small samples labeled as small; association never presented as causation; the same third-party privacy care already established for the partner-mood column applies here too.
    - **(d) A real example of what this edition is for.** An early trial run caught and named two genuine data-quality issues the standard report's fine print had not surfaced as prominently. Catching this kind of issue and ranking it near the top of the findings, rather than burying it in a limitations paragraph, is exactly the behavior this edition exists to produce.
    - **(e) Verification.** Required independently for both PDFs before a run may report success.
    - **(f) Scope.** Governs the daily report only.
26. **A diagnosis-date field in the medical-records layer was corrected after direct clarification, applied immediately to the live database (added 2026-08-09, per the user's direct correction in conversation).** Amendment 19's medical data derived a background flag from the earliest date a condition appeared in the structured record — a records artifact, not necessarily the actual clinical event. The user corrected this directly: the actual diagnosis predates what the structured record shows by several years. The correction was applied immediately across the full tracked history, and a note in `life_chapters` describing the earlier-believed date was updated; the chapter's own boundaries were left for the next quarterly rebuild. Specifics of the diagnosis itself are omitted from this public spec (see Amendment 19). What did not change: the hard rules — the flag remains background fact only, never license to explain, predict, or moralize about a specific day's Mood.
27. **The daily report is no longer a silent unattended 5:00 AM run — it is now manually triggered by the user, prompted by a 7:00 AM reminder, because Google Photos sampling requires live human-present approval that an unattended session can never grant (added 2026-08-10).** Direct testing on 2026-08-10 confirmed that Chrome computer-use/browser access is blocked entirely during scheduled (unattended) sessions — a permission model constraint, not a bug. Since Amendment 16's fallback login flow assumes a live user who might see and act on a `PushNotification`, an unattended run can never complete that stage. **New protocol:** a scheduled task at 7:00 AM sends a brief `PushNotification` reminding the user that the day's report is ready to run whenever he is, and that he should start a new conversation and ask for it directly. That reminder task's only job is to send the notification and stop — it does not attempt to collect data or run any part of the pipeline itself. The actual report generation happens only in a live, user-initiated conversation from that point forward.
28. **Three infrastructure bugs found and fixed the same day as Amendment 27, all previously undiagnosed despite being flagged as broken across multiple prior runs (added 2026-08-10).** Following up on a direct request to fix everything currently broken in this system, this session live-tested every available data source end-to-end rather than assuming prior working status.
    - **(a) A stale RescueTime retention assumption (see Amendment 7's addendum) was corrected.** The free-plan retention window is a rolling ~15-day window relative to the current date, not a fixed historical start date, as direct re-testing showed.
    - **(b) Calendar Density classifier (known-broken since 2026-08-02, never previously diagnosed) root-caused and fixed.** The "implausible 0% restorative" reading traced to a classification bug in the keyword logic; corrected and re-validated against real calendar data.
    - **(c) A weather-cache defect (flagged broken, never diagnosed) was root-caused: a three-value column mix-up** in how apparent temperature, actual temperature, and a third related field were being written, silently swapping values in a way that had gone unnoticed. Corrected, and the affected historical range re-pulled.
29. **AQI and news-intensity analysis paused; Newey-West (HAC) standard errors adopted as the core regression's default; a larger methodology overhaul scoped but not yet built (added 2026-08-10, per the user's direct instructions in a data-science-register conversation).**
    - **(a) AQI and news intensity: PAUSED, effective immediately.** Both secondary regressions stop running until the user reactivates them. Neither had reached statistical usability anyway (very small sample sizes) — this is a scope-reduction instruction, not a response to any new problem with either signal.
    - **(b) Serial autocorrelation tested directly against the live core regression, per the user's concern that robust standard errors handle heteroskedasticity but not serial dependence in an n=1 daily panel.** Durbin-Watson ≈ 2.01 (no autocorrelation); Ljung-Box tests at several lags all non-significant; a direct comparison across all core predictors found **zero significance flips** between the old and new covariance estimator — the concern is theoretically well-founded for a daily panel in general but does not materially change any current finding in *this* model, almost certainly because prior-day Mood is already a predictor and absorbs most first-order serial dependence. **Newey-West (HAC, maxlags=3) is adopted as the new default covariance estimator for the core regression going forward** regardless — it is more defensible for this data-generating process and costs nothing.
    - **(c) A larger methodology overhaul was requested and is scoped here, not yet implemented — flagged explicitly as a roadmap item, not silently deferred.** The user requested, in one exchange: a standardized lag matrix for every plausible predictor (same-day, −1/−2/−3 day, trailing 3-day mean, trailing 7-day mean, deviation from personal 30-day mean) specifically to separate *state* effects from *accumulation* effects; FDR correction across exploratory predictors; block-bootstrap or Newey-West errors for serial dependence (the latter adopted per (b) above); walk-forward validation; rolling coefficient stability plots; partial R² per predictor; standardized effect sizes; permutation importance on held-out periods; explicit missingness indicators/audit; DAG-style causal hypotheses stated *before* running each analysis. Most of these are not yet built as of this writing. This is real, substantial engineering work with real downstream implications for report content and should be built and reviewed deliberately, not rushed into the next daily run.
    - **(d) Directional reframing, stated by the user, recorded verbatim as the standard this system should be evolving toward:** *"Quantified Claude should evolve from 'What correlates with my good and bad days?' toward 'Which plausible mechanisms repeatedly precede them, and which safe actions actually change the distribution of outcomes when tested prospectively?'"* This has two parts: (1) mechanism-focused hypothesis testing — already partially present via Section 12's hypothesis-testing capability (Amendment 20) and the Trigger Map, but the lag-matrix work would substantially deepen it; (2) **prospective intervention testing** — a materially different capability than the current suggestion-scoring loop, which scores suggestions qualitatively after the fact rather than running anything resembling a pre-registered N-of-1 trial. Building genuine prospective testing is a significant design undertaking, not a one-line change, and has not been scoped in detail yet.
    - **(e) Status: this amendment records direction and one completed empirical test, not a completed overhaul.**
30. **Texting/calling metadata added as a new data source (added 2026-08-10, per the user's direct request), with a one-time historical backfill completed and live tracking pending the user's phone-side setup.**
    - **(a) Requested metrics.** Unique conversational partners, inbound/outbound message counts, reciprocal conversations (partners with both inbound and outbound contact that day), total call minutes, calls >10 minutes, first/last social interaction time, communication diversity, and days since meaningful (two-way) contact.
    - **(b) Data source and access path.** The user's phone-backup app writes periodic XML backups (calls + SMS/MMS metadata) to cloud storage. **Privacy discipline, matching Gmail's metadata-only treatment (Section 17):** the backup XML includes full message body text by default — this system never reads, stores, or logs it. Parsing extracts only date, direction, and contact identifier via targeted attribute matching, never a general parser that would expose message body text.
    - **(c) File-size constraint.** A full SMS/MMS history export commonly exceeds the connector's inline-pull size limit — both available browser-automation channels were tested and found blocked for large-file download in this session. **Current workaround: the user manually downloads the relevant backup file to the local project folder**, and the parser runs against it from local disk. This is a real, unresolved gap for *live* daily tracking once the phone-side backup resumes.
    - **(d) One-time historical backfill: DONE**, from the most complete available older snapshot, written to two new `feature-store.sqlite` tables: `daily_communication` (aggregate metrics per (a) above) and `daily_communication_by_partner` (the same metrics computed per-individual, including a per-partner "days since meaningful contact" that correctly walks the full calendar span, including days with zero rows for that partner, rather than resetting only on days with data present — verified against real multi-week gaps with specific contacts). Both tables carry a `source` value explicitly marking this as a one-time historical snapshot, not continuous coverage.
    - **(e) Source file retained, per the user's explicit instruction** (declined the offer to delete it after parsing).
    - **(f) Not yet integrated into the daily report or the core regression** as of this amendment.
    - **(g) Live-ish calls data integrated shortly after — a genuine, if partial, resolution of (c)'s "unresolved gap."** The backup app was reactivated; two files land per backup: a small calls file (well under the connector's size limit — pulled directly, no manual download needed) and a much larger SMS/MMS text file (which grew dramatically larger than the original historical snapshot, now far beyond any automated or reasonable manual channel). Given this asymmetry, the two data types are now handled differently going forward: **calls are pulled directly via the connector each time** and parsed with the same functions already built and validated for the historical backfill, covering a real, multi-year range that genuinely overlaps the live tracked/report period. Written to the same two tables, tagged with a distinct `source` value so they're never confused with the earlier, fuller snapshot. Message-count/diversity fields (which need SMS data) are left NULL for this window rather than zero, since zero would falsely imply "no texting happened" when the truth is "not measured." **SMS/MMS text data still has no working automated path**, and the problem got materially worse rather than better as the file grew — this needs the user's decision (a backup-scope change, accepting text-message metrics as permanently stuck at the old snapshot, or something else), not another attempted workaround.
    - **(h) A recurring health-check was scheduled** to verify the backup app is still actively running (flagging if the most recent file is unusually old, since the app has already failed silently once before) and to re-surface the unresolved large-file problem as a standing nudge rather than letting it go silently stale again.
31. **A genetic-context subsystem added (added 2026-08-10), built from the user's own complete written specification.** Static contextual layer only — genetic data is never a longitudinal/time-varying predictor and is never written to `daily_features`, enforced by a dedicated automated test, not just documentation. Given the sensitivity of genetic data specifically, this public spec omits the architecture and findings in detail; the governing principles are recorded here because they matter beyond this one subsystem: (1) a whitelist-driven design where anything not explicitly whitelisted is architecturally unreachable, not just policy-excluded; (2) every interpretation backed by direct evidence lookup, never LLM memory, with an evidence-version recorded per record; (3) the more conservative of two storage architectures offered was deliberately chosen, avoiding a full per-variant table in favor of only derived, aggregate contextual fields; (4) categories with real potential for harm if mishandled (psychiatric polygenic risk, and others) are explicitly excluded by config with a documented reason, not silently absent; (5) a real technical bug (a strand-orientation mismatch between the raw data format and the reference literature for one trait) was caught during validation and fixed rather than producing a wrong silent result — the system correctly declined to interpret a mismatched case rather than guess; (6) a boundary question about extending into actual health-risk/carrier-status findings was raised directly with the user and explicitly declined, on competence-boundary grounds identical to the medication-suggestion boundary (Amendment 24(c)) — consumer genetic tests have real per-variant accuracy limits, population effect sizes aren't individually determinative, and this system has no genetic-counseling training to safely contextualize a novel finding.
32. **"Same-date across years" content retired from the daily report, effective immediately (added 2026-08-10, per the user's direct instruction: "remove any reference in the report to how a particular date has been in history... let's focus on other more useful data").** This overrides the original spec body's "Same-date extraction" step and its downstream references without rewriting that original text in place, consistent with how this system has always handled superseding instructions — the original spec text stays as the historical record, this amendment governs going forward.
33. **State-vs-accumulation testing built and run against real data (added 2026-08-10) — the first concrete piece of Amendment 29(c)'s scoped statistical overhaul.** Reference implementations: `lag_matrix.py` (a reusable 7-feature-per-variable lag/accumulation testing method) and `state_label.py`. Findings from the first live pass are recorded in `run-log.md`; the general conclusion was that most tested variables' effects are dominated by same-day (state) terms rather than multi-day accumulation, with a small number of exceptions worth continued tracking as more data accumulates.
34. **Pre-report data sanity check built (added 2026-08-10), in direct response to the user's "bulletproof this" request — a mechanical tripwire layer, not a general data-quality framework.** Reference implementation: `data_sanity_check.py`. Runs a fixed set of mechanical checks (impossible values, broken exact-linear-dependency relationships like the Total-Screen-Time identity in Amendment 11, duplicate dates, obviously-swapped columns) before a report composes, and halts with a clear message rather than silently composing a report from corrupted input if any check fails.
35. **Calls-derived social-contact-volume features built and tested against the core regression (added 2026-08-13, per the user's explicit instruction to execute, not just scope, a plan for using every available data source at the same regression depth as the Quantified Self spreadsheet).** The honest answer given first, before building anything: several sources are deliberately kept out of the core model already, for reasons already on record — RescueTime/Firefox were checked and found to duplicate what Comp Time/Cell Time already capture; folding sparser sources into one complete-case model shrinks everyone's sample to the intersection, which is why the partner-mood and blood-pressure columns already run as separate secondary models instead. The one genuinely untapped, non-duplicate candidate identified: calls-derived social-contact-volume metrics.
    - **(a) A real, previously-undocumented coverage gap found before any test was run.** The calls-only backfill's own headline date range turned out to mask a large hole: essentially no call-log rows for several years, then dense, reliable coverage from a specific recent point onward (over 90% of calendar days with at least one call). All testing was restricted to the verified-dense window only, to avoid silently fabricating years of "no contact" that were actually just missing phone/call-log history.
    - **(b) Three new first-class `daily_features` columns, populated only for that dense window**: `comm_unique_partners_calls`, `comm_call_minutes`, `comm_days_since_any_call` (plus two more for completeness). All keyed by the literal calendar date the call happened (no D/D+1 shift — same convention as weather and travel).
    - **(c) `comm_days_since_any_call` turned out to be a near-constant, not the sparse "contact gap" signal it was expected to be** — the user calls someone on the large majority of days in this window, so the variable is almost always zero. A real, honest, mildly surprising finding in its own right.
    - **(d) Nine accumulation-term tests run, one term at a time against the core model, restricted to complete cases in the dense window:** unique-partners showed no signal in any representation tested. Call minutes showed one borderline positive association (a rolling 3-day total of call time vs. Mood) right at the edge of what several uncorrected tests at the standard significance threshold would produce by chance alone.
    - **(e) Decision, stated plainly rather than left implicit: not added to the core regression, not yet promoted to a formal secondary model.** Recorded as a candidate signal to re-test as the dense window accumulates more days, not a validated finding.
    - **(f) What this does answer, concretely, for the user's original question.** The system's regression depth is not actually limited by unused data — it's limited by which sources have enough independent daily variation to be worth a coefficient and a genuinely reliable coverage window, checked directly rather than assumed. Medical and genetic data remain deliberately excluded as static, non-time-varying context (a scope decision already made explicitly, not an oversight); location/travel data remains excluded from any live model per Amendment 12's coverage-gap reasoning; RescueTime/Firefox remain qualitative-only per Amendment 13(b).
36. **A social-media platform's full personal data export parsed for engagement-volume regression predictors (added 2026-08-13, per the user's instruction to use the export "to further strengthen analysis in this project").** The user downloaded and shared his complete data export from the same platform already used for the daily memories feature, covering roughly a year at the time, across many categories (posts, comments/reactions, messages, security/login history, and more). This is a different, much richer channel than the daily memories scrape, which only ever samples "on this day" content.
    - **(a) Triage before parsing anything.** Roughly half of the categories in the export were empty. Of the populated categories, several were checked and found unusable for regression purposes before any parsing effort was spent on them (too little date coverage, or redundant with existing sources). Message content was identified as a plausible future extension but explicitly deferred, not attempted — parsing per-thread message timestamps at scale is a meaningfully larger and more sensitive undertaking than counting reactions/comments, and wasn't necessary once a strong signal was already found elsewhere.
    - **(b) Two genuinely usable, well-covered sources found: reactions given and comments given.** Both spanning the export's full requested window with real day-to-day variation, dense enough that missing days are treated as true zero-activity. A real duplicate-file catch was made before parsing twice.
    - **(c) Privacy discipline, per Section 17, applied exactly as for every other source.** The parser extracts only event timestamps. Reaction type, the target of a reaction or comment, and the full text of every comment are all read transiently during parsing and never returned, logged, or stored anywhere — `feature-store.sqlite` receives only the two derived daily counts.
    - **(d) Real findings, tested via the same lag-matrix / Newey-West-HAC method as the prior amendments' tests.** Both variables' same-day terms came back significant under full core-model adjustment, but not equally robust to each other once tested together — one of the two appears to have been substantially riding on its correlation with the other, not carrying an independent effect on its own. Neither variable meaningfully correlates with the existing screen-time predictors already in the model. All trailing-mean/deviation accumulation terms for both variables were non-significant — a same-day (state) effect only, with no detected multi-day accumulation.
    - **(e) The honest characterization.** More of one specific engagement behavior on a given day is associated with a real, moderately robust drop in same-day Mood — modest in size but consistent across two different adjustment specifications and not explained by screen time already in the model. As with every other same-day behavioral association in this system, causal direction is not established and reverse-causation is at least as plausible a story as forward-causation.
    - **(f) Decision: two new `daily_features` columns; not yet promoted to a standing secondary-model section in the daily report.**
    - **(g) One-time backfill; no live re-pull path**, like the medical-records export and the historical SMS snapshot — this kind of "download your information" export is a manual, on-demand download, not a live API.
37. **"The Move" chapter's founding event corrected: a foot surgery, not a knee surgery (added 2026-08-13, per the user's direct correction in conversation).** The `life_chapters` "The Move" row had described its own opening event incorrectly since the chapter was first defined — this language was carried into every daily report's "Where Today Sits" section for every report composed while this chapter has been active. The user corrected this directly. This is recorded here not for the medical detail itself but as a working example of the same catch-and-correct discipline this changelog tries to model throughout: a plausible-sounding but wrong detail, stated confidently across many outputs, corrected the moment the person who actually lived it said so.
38. **Facebook engagement history (Amendment 36) extended from a roughly-one-year window to the full tracked history (added 2026-08-15, per the user's instruction: "I have another facebook data export, but this is much more complete... integrate it").** The user provided a new, much larger export whose reaction history is paginated across many files covering the platform's entire era of use for him without overlap, plus one older-format file that duplicates the most recent years and must be excluded to avoid double-counting. The parser was extended to prefer the newer, paginated series when present. The two engagement columns now cover the system's entire ~13-year tracked history instead of the prior one-year window. **Finding:** the raw same-day association between reactions-given and Mood, robust and positive across the full history when lightly adjusted, collapses to non-significant once a linear year control is added — both engagement and Mood have independently trended downward over the tracked years, and the naive correlation is a shared-trend confound, not a real relationship. Inside the core model's own recent complete-case window, a small but real negative signal survives — not meaningfully correlated with existing screen-time predictors, so it is independent information. Documented in full in a one-time integration report, not a new standing daily-pipeline stage.
39. **Report voice sharpened further — evaluative directness and judgment-based suggestions on any topic, diary entries summarized rather than block-quoted; medication/substance-use suggestions explicitly re-affirmed off-limits after the user raised and then accepted the distinction (added 2026-08-21, per the user's instruction, given directly in conversation after reviewing that day's report).** The user asked for three changes. Two are adopted as requested; the third was proposed by the user, met with a direct reservation from Claude, and resolved by the user accepting the boundary Claude drew — recorded here in full rather than smoothed over.
    - **(a) ADOPTED — evaluative directness.** Where the data supports a negative read of a pattern, the report should say so plainly rather than retreating into clinical neutrality — the user's own words: "it's ok to call it out as negative without being judgmental." This extends Amendment 24's "full directness" baseline into evaluation: a pattern can be named as a bad one, not just an interesting one, when the numbers support it.
    - **(b) ADOPTED, with the boundary in (c) carved out — suggestions become more direct and may draw on Claude's own judgment from the data, on any topic, not just topics with prior empirical support in the user's own intervention-log history.**
    - **(c) DECLINED, specifically — direct suggestions about adjusting medication or about increasing/decreasing substance use, based on Claude's own statistical judgment.** The user's initial ask extended (b) to cover these topics too. Claude raised a direct reservation before implementing it: this isn't a trust question, it's a competence one — Claude has no clinical training, no titration history, no lab values, no side-effect history, and a regression coefficient on a self-tracked spreadsheet is not equivalent evidence to what a treating clinician uses to make a dosing or substance-use call for a real, diagnosed regimen. This is not a new line — it is Amendment 24(c)'s own "operative test" firing exactly as designed. **The user accepted this distinction as stated ("That's fine")** rather than pushing further, so this is recorded as a re-affirmed boundary, not an open question. What changes going forward: alcohol, medication-timing, sleep-loss, and comparable patterns are still named as plainly and evaluatively as anything else under (a) above — what never follows is a sentence recommending a change to the medication or substance itself. Section 7's and Amendment 24(c)'s hard rule stands verbatim: never recommends increasing, decreasing, starting, or stopping any medication or substance use based on any statistic in this report.
    - **(d) ADOPTED — diary entries summarized in the report's prose rather than block-quoted at length.** The user's reasoning, stated directly: "I wrote them myself, so I don't need explicit reminders." Default to paraphrase/summary when working diary content into the daily synthesis; a short verbatim phrase remains fine when it makes the writing more succinct or when the exact wording is itself the analytically load-bearing thing. This governs report *composition* only; it does not change Amendment 15(c)'s storage rule or Amendment 15(d)'s directness rule.
40. **Extended-window core regression added as a standing second finding in the daily data-analysis PDF (added 2026-08-25, per the user's instruction, after he asked to size out extending the project's history backward).** A sizing exercise found that `feature-store.sqlite` was already fully backfilled to the start of the tracked history — the core regression's small sample is not a missing-data problem but a predictor-availability one: the phone/computer screen-time split only exists from a certain point onward, and is itself a weak predictor. Dropping just those two columns extends the complete-case window back much further with every current finding holding and several strengthening. A further, more aggressive option — also dropping Constitution to reach the full multi-decade-adjacent history — was sized out and rejected: it loses the single strongest predictor and changes the model's character rather than just adding power, evidence of conflating genuinely different life eras rather than a clean gain in power.
    - **(a) What was built.** `core_regression_extended.py`, a persisted variant using the same methodology (OLS, Newey-West/HAC maxlags=3, complete-case, Constitution same-day, all else same-day, Mood lag-1) with Cell Time/Comp Time dropped from the predictor list.
    - **(b) How it's used.** Run alongside (not instead of) the core regression every day, reported as a second, comparison finding in the data-analysis PDF's ranked findings.
    - **(c) Open decision, explicitly not yet made.** Whether to replace the current core model with this extended-window spec is undecided. The user asked to be reminded of this decision — until he makes it, every daily report should keep surfacing both models side by side and naming the choice as open.
41. **The Quantified Self date-alignment contract was WRONG and has been corrected (found and fixed 2026-08-29, by this system, not at the user's prompting — awaiting his review).** The contract stated in this SPEC and implemented throughout the pipeline was: *"Quantified Self row D+1 holds outcome day D's daily fields, but Sleep on row D is the night ending morning of D."* **The Sleep half is correct. The daily-fields half is not — the spreadsheet is own-row throughout. QS row D holds day D's Mood, Constitution, Steps, Comp Time, diary, and everything else.**
    - **(a) What went wrong, and how far it reaches.** Because the pipeline faithfully implemented the contract as written, every QS-derived column in `feature-store.sqlite` has sat one day EARLIER than the day it actually describes, for the entire history of the project. Meanwhile every externally-dated contextual column — weather, AQI, RescueTime, Firefox, Gmail, Calendar, calls, travel, Apple Music, photos — has always been correctly dated. The consequence is precise and bounded: **every cross-source relationship this system has ever reported between a QS variable and a contextual variable was offset by one day. QS-on-QS lag findings (e.g. prior-day Mood → Mood) are unaffected, because a uniform shift preserves lag structure.** Every crash/onset/recovery date ever reported was named one day earlier than it actually fell.
    - **(b) Evidence (four independent lines, all agreeing).** Cross-checks between the spreadsheet's self-logged computer-time column and the independently-tracked RescueTime totals for the same days matched almost exactly under the corrected same-row reading and badly under the old D+1 reading; the same reversal showed up checking the store's own RescueTime-derived column against the spreadsheet's Comp Time column; independently-dated travel-visit data correlated with Steps far better under the corrected reading in every year tested; a mechanical row-by-row comparison of store against spreadsheet across several major columns matched the corrected rule 100% of the time and the old rule 0% of the time; and independent calendar/email/photo evidence around a known real-world event (the move) all corroborated the corrected date on the same spreadsheet row.
    - **(c) What this resolves.** A previously-flagged "diary vs. calendar date tension" was never a real conflict between sources — it was this system misreading one of them. Also very likely resolves a long-open design question about an apparent scoring offset in the suggestion-tracking log — the offset that made each day's suggestion appear to be scored against an already-known outcome is exactly this one-day shift.
    - **(d) The most important downstream consequence — a finding relocated, not lost.** "Sleep robustly predicts Mood" has been this system's most-repeated substantive result. In the corrected store, sleep entered same-day (as the canonical spec requires) is **null**. Entered as PRIOR-day sleep it is **robust** — and that model reproduces the pre-correction figures almost exactly. The old model was silently fitting a next-day sleep effect while labelling it same-day. **Sleep is banked for tomorrow, not spent today.** Report it that way from now on.
    - **(e) The fix, and how to revert it.** A dedicated correction script shifts all QS-derived columns forward one day; Sleep and all contextual columns are deliberately untouched. A full pre-correction backup was kept — reverting is one file copy. `state_label` was rebuilt in full afterward.
    - **(f) Standing instruction for future runs.** Read QS row D as day D, for every field including the diary. The old D+1 language is retired wherever it appears in this SPEC; treat this amendment as overriding it. **Any previously published finding coupling a QS variable to a contextual variable should be treated as UNVERIFIED** until recomputed.
    - **(g) Open for the user.** This correction re-bases every historical cross-source figure this system has published. It was applied rather than deferred because the precedent set by Amendment 28 is that root-caused defects get fixed and backfilled immediately, with the correction stated plainly. If he disagrees with either the diagnosis or the decision to apply it without asking first, the revert is one command.
42. **Weather/AQI region baseline updated following a household relocation within Region B (added 2026-08-29, per the user's explicit instruction).** The user relocated within the New-York-area region already established in Section 9's geographic rules.
    - **(a) The new baseline.** A new specific location within Region B, effective for behavior dates from the move onward. Applies to both the historical-weather and air-quality data sources the pipeline uses. Timezone is unchanged.
    - **(b) History is NOT rebased.** Every row keeps the region that was actually correct on its own date — the prior Region B location through the day before the move, deliberately logged as a transition day where the day began in one location and ended in the other. Do not retroactively rewrite earlier rows — the region column exists precisely so the series can carry a residence change honestly.
    - **(c) Expect a level shift in the weather features, and do not read it as a behavioral signal.** The new location is coastal and measurably cooler and rainier than the prior one on directly-compared days around the move. Any apparent discontinuity in temperature, precipitation, or AQI at the move-date boundary is the baseline moving, not the weather or the subject changing. This matters for the core regression, where the weather composite is a standing predictor: treat the boundary the way an instrument change would be treated, and say so if a weather-coupled finding shifts around it.
43. **Facebook Memories retired as a pipeline stage entirely (added 2026-09-02, per the user's explicit instruction: "remove the facebook memories section from subsequent reports," clarified to mean the full collection stage, not just the report section, when asked).** From this amendment forward, the routine does **not** visit the memories page at all — no collection, no memories text file in the dated output folder, no memories section in the daily PDF, and no use of Facebook Memories content anywhere in the report's synthesis, epigraph, or life rating.
    - **(a) What this overrides.** This retires the Facebook-collection portions of Section 2 (pipeline overview), Section 4 (collection procedure and its read-only hard rules), Amendment 5 (browser policy — the Claude-in-Chrome extension's Facebook allowlist entry is no longer exercised by this routine, though the extension itself remains available for other approved uses), Amendment 9 (the collection-ceiling/stall-handling procedure), and every place Section 8/14 describe memories as a primary narrative source or an input to the life rating and recurring-themes synthesis. Diary Entries (Amendment 15) remain a primary narrative source on their own, unaffected by this change.
    - **(b) Not a data-loss event.** Every prior dated folder's memories text and every prior report's memories-derived content stands unchanged — this is forward-only. The separate, already-completed engagement-reaction backfill (Amendment 38) is a **separate, already-completed backfill** from a different data source (a personal-data export, not the live memories page) and is **not** affected by this amendment.
    - **(c) Reversible.** If the user wants Facebook Memories back in a future run, the retired sections above describe exactly how the stage worked and can be reinstated by removing this amendment's override.
    - **(d) First run under this amendment.** The report already produced the day this instruction was given is not retroactively regenerated. The next run is the first to omit the stage entirely.

Daily Facebook Memories Illustration and 
Life-Summary
 
System
 
1. Purpose 
Build an automated daily routine that combines Facebook Memories, the user's 
Quantified
 
Self
 
history,
 
recent
 
behavioral
 
context,
 
and
 
environmental
 
conditions
 
into:
 
1. One illustrated PNG for every Facebook memory visible that day. 2. A consolidated “life on this date” assessment across all represented years. 3. A polished PDF containing a data-grounded Mood outlook and horoscope-style 
narrative.
 4. Longitudinal weather and contextual feature stores supporting increasingly useful 
historical
 
comparisons.
 
The system’s larger purpose is practical self-knowledge and foresight. It should help the user 
recognize
 
patterns
 
that
 
may
 
precede
 
emotional
 
or
 
physical
 
difficulty,
 
distinguish
 
ordinary
 
variation
 
from
 
a
 
sustained
 
decline,
 
and
 
identify
 
small
 
actions
 
that
 
may
 
improve
 
resilience.
 
The output must preserve uncertainty and agency. It must never sound fatalistic, diagnostic, 
alarmist,
 
or
 
falsely
 
predictive.
 
 
2. Schedule and report dates 
Run every day at 4:00 AM America/New_York . 
Use a timezone-aware scheduler that observes daylight-saving changes. Prevent concurrent 
runs
 
for
 
the
 
same
 
report
 
date.
 
Define: 
● Report date: the local calendar date when the routine runs. ● Behavior date: the prior completed calendar day. ● Current conditions: conditions and forecasts available during the run. ● Memory date: the report date’s month and day across all Facebook years. 
For example, a run at 4:00 AM on July 17 produces the July 17 report. Personal behavioral 
sources
 
such
 
as
 
browsing,
 
email,
 
and
 
RescueTime
 
should
 
be
 
analyzed
 
for
 
July
 
16.
 
Current
 
weather
 
and
 
headlines
 
should
 
reflect
 
conditions
 
available
 
on
 
July
 
17.
 
A delayed run should still use the intended scheduled report date when that date can be 
determined
 
reliably.
 
 
3. Overall workflow 
The orchestrator should perform these stages in order: 
1. Acquire a per-date execution lock. 2. Read the previous automation memory and existing derived files. 3. Establish the report date, behavior date, and timezone. 4. Open Firefox and collect every Facebook Memory. 5. Confirm that Facebook displays its caught-up state. 6. Read and analyze the Quantified Self spreadsheet without modifying it. 7. Update the historical weather cache. 8. Collect current weather, news, Apple Music, Firefox history, Gmail Sent, and 
RescueTime
 
context.
 9. Update the append-only contextual feature log. 10. Calculate recent Mood trends, adjusted associations, and historical analog days. 11. Reconcile the memories, quantified data, and contextual inputs. 12. Generate one final illustration per memory, in Facebook order. 13. Compose the integrated life-summary PDF. 14. Optionally create a contact sheet. 15. Validate the images, PDF, feature logs, and output folder. 16. Record a concise run summary in automation memory. 17. Report success, partial success, or a blocking authentication problem. 
Failure of an optional or secondary source must not abort the full routine. Facebook 
authentication
 
challenges
 
and
 
spreadsheet
 
security
 
challenges
 
are
 
exceptions
 
requiring
 
human
 
takeover.
 
 
4. Facebook collection 
Browser requirements 
Use Firefox because it may already contain the user's authenticated session. 
Open: 
https://www.facebook.com/memories 
Do not post, react, like, comment, share, edit, delete, follow, or change any Facebook setting. 
Collection behavior 
Start with the first visible memory and continue downward in display order until Facebook 
explicitly
 
indicates
 
that
 
the
 
user
 
is
 
caught
 
up.
 
Scrolling must account for lazy-loaded content. After reaching an apparent end: 
1. Wait briefly for additional content. 2. Inspect the page for loading indicators. 3. Continue if new memories appear. 4. Finish only when the caught-up message is visible or a well-defined terminal state is 
reached.
 
Data to capture for each memory 
Create an ordered memory record containing: 
● Sequential position. ● Original date and year. ● Exact or faithfully transcribed post text. ● Poster or relevant people when visible. ● Relationship or conversational context. ● Shared-link title and source when present. ● Whether the memory contains a photo, video, thumbnail, link preview, or text only. ● A local screenshot or image reference when needed for illustration. ● Visible comments only when they materially explain the memory. ● Collection status and confidence. ● Any source-access limitation. 
Preserve the original event, people, year, and emotional context. Comments should not 
overwhelm
 
the
 
original
 
post.
 
Blocking conditions 
Stop and ask the user to take over if: 
● Firefox is logged out. ● Facebook presents a security or identity challenge. ● A login action requires credentials, a second factor, or sensitive account interaction. ● Facebook presents an unfamiliar permission or account-recovery flow. 
Ordinary cookie dialogs or a standard geolocation permission for the separately authorized 
weather
 
purpose
 
may
 
be
 
handled,
 
but
 
OS
 
security
 
settings
 
must
 
never
 
be
 
changed
 
automatically.
 
 
5. Illustration generation 
Generate exactly one final whimsical cartoon illustration for every collected memory. 
Illustration principles 
Each image must: 
● Preserve the original date and year. ● Preserve the memory’s central event. ● Preserve relevant people and context. ● Retain important source details such as a photo, video, song, or linked article. ● Treat the past with warmth and affectionate humor. ● Use a subtle visual sensibility appropriate to the memory’s era. ● Reimagine source imagery rather than copying it exactly. ● Avoid unsupported details that materially change the event. 
If the memory contains an image or video thumbnail, use it as the primary visual reference. If it 
is
 
text-only,
 
derive
 
the
 
scene
 
from
 
the
 
post
 
text.
 
Caption panel 
Every final image must have a clean, readable caption panel containing: 
● The original memory date and year. ● A concise caption based on the original text. 
The caption must be legible at ordinary viewing size. Avoid extra generated text, logos, 
watermarks,
 
or
 
invented
 
quotations.
 
File organization 
Save final images in: 
<DATA_DIR>/YYYY-MM-DD/ 
Use numbered filenames that preserve Facebook order, for example: 
● 01-2022-memory.png 
● 02-2020-memory.png ● 03-2019-memory.png 
Use descriptive suffixes when multiple memories share a year. 
The main dated folder must contain only current final versions. Store experiments, rejected 
generations,
 
and
 
superseded
 
images
 
in:
 
YYYY-MM-DD/archive/ 
Never silently overwrite a good final with an unverified replacement. A rerun may replace that 
report
 
date
 
only
 
through
 
an
 
explicit
 
controlled
 
replacement
 
process.
 
Image verification 
Verify: 
● Final count equals collected memory count. ● Sequence numbers are continuous. ● Every image opens successfully. ● Captions contain the correct year and meaning. ● No final image is an accidental duplicate. ● No experimental image remains in the main set. ● No caption is cropped or illegible. 
Create a contact sheet after all finals are approved. 
 
6. Quantified Self spreadsheet 
Access 
Use the spreadsheet already open in Firefox or retrieve the same workbook through an 
authorized
 
read-only
 
connector.
 
Never edit cells, formatting, formulas, comments, sharing settings, or spreadsheet metadata. 
If access is unavailable, record the limitation and continue using Facebook and other available 
sources.
 
Stop
 
for
 
human
 
takeover
 
only
 
when
 
access
 
requires
 
sensitive
 
authentication
 
actions.
 
Required date alignment 
This is a strict data contract. 
Most values are entered shortly after awakening, but they describe the preceding calendar day. 
Therefore:
 
● For outcome day D , Mood, Constitution, steps, screen use, computer use, alcohol, active 
Music,
 
productivity,
 
habits,
 
notes,
 
and
 
comparable
 
daily
 
fields
 
come
 
from
 
spreadsheet
 
row
 
D+1
.
 ● Sleep is different. Sleep on row D describes the night ending on the morning of D , so it is 
the
 
sleep
 
that
 
preceded
 
day
 
D.
 ● Sleep on row D+1 is the night following day D. It may be a consequence of day D and 
must
 
not
 
be
 
treated
 
as
 
preceding
 
sleep.
 
The report must state this alignment explicitly. 
Example: a row dated July 15 describes July 14’s Mood and behavior, while its Sleep describes 
the
 
night
 
ending
 
July
 
15.
 
Field semantics 
Assume: 
● Higher Mood values are better on the 1–10 scale unless the sheet explicitly says 
otherwise.
 ● Constitution (low=bad) is physical wellness; higher is better. ● Music means active musical-instrument playing. ● Apple Music is passive listening and must remain a separate variable. 
Same-date extraction 
For the report’s month and day, locate every available historical outcome day across all years. 
Extract relevant signals including: 
● Mood. ● Constitution. ● Sleep preceding the outcome day. ● Steps and activity. ● Computer, phone, and total screen time. ● Productivity or workload. ● Alcohol and other recorded substances. ● Active instrument-playing. ● Health measures. ● Habits. ● Notes and relationship context. ● Day of week. 
● Following Mood and Constitution over the next one to three outcome days when 
alignment
 
permits.
 
Do not invent missing fields or interpret blank cells as zeros unless the spreadsheet clearly uses 
that
 
convention.
 
 
7. Mood analysis 
Mood is the primary quantitative outcome. 
Descriptive trends 
Calculate: 
● Latest fully aligned Mood. ● Previous Mood and daily change. ● Latest Constitution. ● Seven-day Mood mean and direction. ● Fourteen-day mean and direction. ● Thirty-day mean and direction. ● Ninety-day mean and direction. ● Long-term personal baseline. ● Recent frequency of Mood 5. ● Recent frequency of Mood 4 or below. ● Whether a decline is isolated or has persisted across multiple days. 
Treat: 
● A decline of at least one point as meaningful. ● Mood 5 as a border state. ● Mood 4 or below as alarming. 
Do not describe the user as being at a threshold unless the latest observed value actually meets 
it.
 
Adjusted statistical analysis 
When sample size and data completeness permit, use regression, partial correlation, or an 
equivalent
 
multivariable
 
method.
 
At minimum, adjust candidate predictors for: 
● Prior-day Mood. 
● Long-term or year effects. ● Seasonal variation. 
Where supported, also consider: 
● Day of week. ● Constitution. ● Prior-night Sleep. ● Recent Mood trajectory. ● Movement. ● Workload. ● Screen exposure. ● Active Music. ● Location or climate regime. 
Use robust uncertainty estimates when appropriate. 
Rank signals by practical magnitude, consistency, sample size, and uncertainty—not by p-value 
alone.
 
Clearly distinguish: 
● Same-day association. ● One-day lag. ● Multi-day lag. ● Possible reverse causation. ● Missingness and selection effects. ● Hypotheses from repeated evidence. ● Anecdotal or low-sample observations. 
Never claim causation from correlation. 
Never recommend increasing alcohol, nicotine, vaping, benzodiazepines, or other substances. 
Never
 
recommend
 
reducing
 
needed
 
sleep
 
because
 
a
 
statistical
 
coefficient
 
appears
 
unfavorable.
 
Interactions 
Test only a small number of plausible interactions when data volume supports them, such as: 
● Constitution × activity. ● Constitution × Sleep. ● Smoke or heat × outdoor movement. ● Screens × late-night timing. ● Active Music × Constitution. ● Work intensity × context switching. 
Guard against overfitting and repeated testing. Label exploratory results as provisional. 
 
8. Historical analog days 
Build a small set of historically similar days when sufficient data exists. 
Similarity must not be based on weather alone. 
Candidate features include: 
● Season-adjusted apparent temperature or heat. ● Humidity or dew point. ● Precipitation. ● Cloud cover and daylight. ● Pressure or pressure change. ● Air quality. ● Abrupt weather change. ● Prior Mood and recent Mood trajectory. ● Constitution. ● Preceding Sleep. ● Movement. ● Workload. ● Screen and media exposure. ● Active Music. ● Social connection. ● Weekday or weekend context. ● Location regime. 
Normalize features appropriately, weight them conservatively, and require sufficient overlap. 
Avoid
 
presenting
 
distant
 
matches
 
as
 
close
 
analogs.
 
For the selected analog set, report: 
● Number of matches. ● Similarity or distance quality. ● Matched-day Mood. ● Matched-day Constitution. ● Difference from an appropriate seasonal and location baseline. ● Expected Mood change. ● Proportion declining by at least one point. ● Proportion reaching Mood 5 or below. ● Proportion reaching Mood 4 or below. ● Outcomes over the next one to three days. 
● Whether movement, Sleep, instrument-playing, listening, screens, or social contact 
appeared
 
to
 
buffer
 
or
 
amplify
 
the
 
pattern.
 
Use matched comparisons or adjusted models when possible. Small samples must be 
described
 
as
 
hypotheses,
 
not
 
personal
 
laws.
 
 
9. Historical weather cache 
Maintain: 
<DATA_DIR>/historical-weather-daily.csv
 
This is a locally derived, read-only cache relative to the source data. 
Geographic rules 
Use: 
● Region A through a chosen cutoff date. ● Region B (a specific ZIP or metro area) from that cutoff date onward. ● Travel days as location-uncertain unless an authorized source reliably establishes 
location.
 
Do not project current New York weather backward onto California years. 
Do not store precise coordinates. 
Cache behavior 
On the first capable run: 
1. Determine the Quantified Self date range. 2. Backfill daily weather for the full range. 3. Store only daily aggregates and availability metadata. 
On subsequent runs: 
● Append or refresh only missing dates. ● Avoid duplicate date/location rows. ● Preserve historical values unless the source is being deliberately corrected. 
Suggested fields include: 
● Date. ● Region or ZIP. ● Location-confidence flag. ● Source. ● Maximum, minimum, and mean temperature. ● Apparent temperature. ● Humidity or dew point. ● Precipitation. ● Cloud cover. ● Daylight and sunshine. ● Pressure and pressure change when available. ● Wind. ● Air-quality measures when available. ● Severe-weather flag. ● Source-availability flags. 
 
10. Daily contextual inputs 
Use prior-day data for personal behavior sources and current snapshots for environmental 
sources.
 
Compare each available measure with seven- and thirty-day baselines. Use longer history as it 
accumulates.
 
Weather and geolocation 
Resolve the user's current ZIP using browser or device geolocation. The user has explicitly 
authorized
 
geolocation
 
for
 
weather.
 
Store and transmit only the minimum location information needed for lookup. Retain the derived 
ZIP,
 
not
 
precise
 
coordinates.
 
If live geolocation fails: 
1. Use the most recently resolved ZIP. 2. Mark it stale. 3. Continue. 
If no current or prior ZIP exists, mark weather unavailable. 
Collect prior-day and current: 
● Temperature. 
● Apparent temperature. ● Precipitation. ● Humidity or dew point. ● Cloud cover. ● Daylight. ● Wind. ● Air quality. ● Severe alerts. ● Abrupt weather changes. 
News sentiment 
Review a balanced set of reputable US and international sources. 
Record: 
● Dominant themes. ● Broad emotional tone. ● Headline intensity. ● US and global snapshots separately. ● Source availability. ● Citations used in the PDF. 
Do not treat negative headlines as an objective measure of the world or as evidence of 
the user's
 
personal
 
state.
 
Apple Music 
Inspect available play history for: 
● Listening duration. ● Timing. ● Artists and broad musical characteristics. ● Repetition versus novelty. ● Active versus background listening when responsibly inferable. ● Change from baseline. 
Do not assign emotional meaning based solely on genre. If timestamps are unavailable, 
describe
 
the
 
data
 
as
 
an
 
undated
 
recent-play
 
snapshot.
 
Keep Apple Music listening separate from active instrument-playing. 
Firefox browsing history 
Analyze only aggregates needed for self-understanding: 
● Total activity. ● Late-night use. ● Topic balance. ● Work, news, social, health, and entertainment shares. ● Repetition. ● Context switching. ● Changes from seven- and thirty-day baselines. 
Do not store raw URLs or full titles in the contextual history file. Mention a specific item only 
when
 
essential
 
to
 
the user's
 
own
 
report.
 
Gmail Sent 
Use read-only access. 
Analyze: 
● Sent-message count. ● Timing. ● Recipient breadth. ● Work versus personal balance. ● Conversational intensity. ● Broad relational themes. ● Change from baseline. 
Do not reproduce message text, addresses, or recipient identities unless strictly necessary. 
Never send, draft, reply, forward, archive, delete, label, or otherwise modify email. 
RescueTime 
Collect read-only aggregates such as: 
● Tracked time. ● Focused time where available. ● Productive and distracting categories. ● Productivity pulse. ● Context switching. ● Work span. ● Late-night activity. ● Change from baseline. 
Do not alter goals, categories, settings, or recorded data. Clearly state limitations of a restricted 
account
 
tier.
 
 
11. Context-history log 
Maintain: 
<DATA_DIR>/context-history.csv 
The file is append-only derived history. 
Use one logical row per report date. A rerun must not create a duplicate. 
Suggested fields include: 
● Report date. ● Behavior date. ● Timezone. ● ZIP. ● ZIP stale flag. ● Weather aggregates and availability. ● News tone and intensity. ● Apple Music aggregates and availability. ● Browsing aggregates and availability. ● Gmail Sent aggregates and availability. ● RescueTime aggregates and availability. ● Latest aligned Mood and Constitution where available. ● Source quality or limitation flags. ● Run identifier and completion status. 
Store only minimum aggregates. Never store email content, addresses, raw browsing URLs, full 
page
 
titles,
 
precise
 
coordinates,
 
or
 
unnecessary
 
personal
 
text.
 
If a run is explicitly replacing a date, perform a controlled replacement preserving an audit copy 
or
 
change
 
record.
 
 
12. Whole-system interpretation 
The report must integrate evidence rather than list unrelated correlations. 
Evaluate plausible sequences such as: 
● Weather and air quality affecting movement. ● News exposure interacting with browsing behavior. ● Late-night screens preceding Sleep disruption. ● Active Music interacting with movement or Constitution. 
● Sent-message patterns reflecting connection or workload. ● RescueTime focus interacting with browser switching. ● Physical wellness changing the interpretation of rest or inactivity. 
Reconcile agreements and contradictions explicitly. 
Examples: 
● Warm Facebook memories may coexist with low measured Mood; the memories are 
autobiographical
 
context,
 
not
 
numeric
 
proof
 
of
 
current
 
wellbeing.
 ● High screen time may reflect productive work, distress-driven repetition, or both. ● Extra Sleep may be protective, or it may occur because the user was ill or depleted. ● Low movement during unhealthy air may be adaptive rather than evidence of 
disengagement.
 
Recommendations should arise from the combined system, not from isolated coefficients. 
 
13. Life rating 
Assign a consistent 1–10 “life on this date” rating based on all Facebook memories for that 
month
 
and
 
day
 
across
 
years.
 
Consider: 
● Tone and Mood. ● Activity. ● Emotion. ● Relationships. ● Creativity. ● Novelty. ● Rest and adventure. ● Productivity. ● Agency and resilience. 
Include: 
● Numeric score. ● Short descriptive label. ● Brief evidence-based explanation. 
The score is a narrative synthesis, not a clinical or statistical measurement. Do not overclaim 
beyond
 
the
 
available
 
memories.
 
 
14. Mood outlook structure 
The PDF must include four explicit parts. 
1. Regression insight 
Summarize adjusted associations with higher and lower Mood. 
State: 
● What was adjusted for. ● Direction and approximate magnitude. ● Sample size where useful. ● Which signals are robust, weak, or uncertain. ● Which findings may reflect reverse causation. 
2. Historical trend 
Compare the latest trajectory with: 
● Seven-day history. ● Fourteen-day history. ● Thirty-day history. ● Ninety-day history. ● Long-term baseline. ● Same-date patterns where supported. 
Explain whether the evidence looks like a temporary fluctuation, a rebound, or a sustained 
change.
 
3. Where the user is now 
State: 
● Latest fully aligned Mood. ● Recent direction. ● Constitution context. ● Favorable factors. ● Neutral factors. ● Concerning factors. ● Data freshness and limitations. 
Separate observed status from probabilistic risk. 
4. Thought and behavior guidance 
Provide: 
● One specific mental reframe or attention strategy. ● Two to four small behavior experiments. 
Possible interventions include: 
● Walking or exercise. ● Active instrument-playing. ● Deliberate Apple Music listening. ● Contacting someone. ● Restructuring work. ● Reducing news or screen switching. ● Going outdoors when conditions support it. ● Protecting bedtime. 
Choose the smallest relevant combination. Do not prescribe every option daily. 
Frame recommendations as experiments, not guarantees. Do not diagnose, moralize, imply 
thoughts
 
control
 
health,
 
recommend
 
medication
 
changes,
 
or
 
substitute
 
for
 
medical
 
care.
 
 
15. PDF requirements 
Save the PDF as: 
00-life-summary-YYYY-MM-DD.pdf 
Place it in the dated memory folder. 
The PDF should normally be one or two pages, but may be longer when necessary for 
readability.
 
Include: 
● Title and report date. ● Life rating and label. ● Facebook-memory synthesis. ● Same-date Quantified Self synthesis. ● Mood outlook with all four required parts. ● Historical analog findings. ● Recurring themes. 
● Agreements and contradictions among sources. ● Personalized suggestions. ● Source limitations. ● Relevant citations. ● A substantive horoscope-style “life on this date” reading. 
Narrative tone 
The horoscope-like section should be: 
● Warm. ● Reflective. ● Personal. ● Vivid. ● Lightly mystical when appropriate. ● Grounded in evidence. ● Specific to this date. 
It may describe what the date tends to invite, but must not make supernatural claims or 
deterministic
 
predictions.
 
PDF verification 
After creation: 
1. Confirm the expected filename. 2. Confirm the document opens. 3. Confirm text is extractable. 4. Confirm links are valid annotations when citations are included. 5. Render every page to an image. 6. Visually inspect for clipping, overlap, tiny text, bad contrast, broken characters, awkward 
page
 
breaks,
 
and
 
excessive
 
whitespace.
 7. Iterate until the rendered pages are polished and readable. 
A PDF is not complete merely because generation succeeded. 
 
16. Progress reporting 
During an interactive run, send brief progress notes at these milestones: 
● Starting the routine. ● Discovering Facebook memories. ● Facebook confirms the collection is complete. 
● Reading and aligning the Quantified Self data. ● Collecting contextual sources. ● Running Mood and analog analysis. ● Generating the image batch. ● Building the PDF. ● Saving and visually verifying final files. 
Updates should be concise and informative. Long-running image generation must not leave 
the user
 
without
 
status
 
for
 
extended
 
periods.
 
 
17. Privacy and safety 
All source access is read-only unless the specification explicitly permits local derived-file writes. 
The routine must never: 
● Modify Facebook. ● Modify the Quantified Self spreadsheet. ● Modify Gmail. ● Modify RescueTime. ● Change OS security settings. ● Store precise geolocation. ● Store raw email content. ● Store recipient addresses. ● Store raw browsing URLs or full titles in longitudinal logs. ● Treat memories as clinical evidence. ● Present probabilistic analysis as destiny. ● Recommend harmful substance use or insufficient sleep. 
Local writes are limited to: 
● Dated output folders. ● Final images. ● Archived experiments. ● Contact sheets. ● Summary PDFs. ● Historical weather cache. ● Context-history log. ● Automation run memory. ● Temporary working files that can be safely discarded. 
 
18. Failure handling 
Continue with limitations 
Continue the routine when: 
● Quantified Self is unavailable but Facebook works. ● One contextual source is unavailable. ● Apple Music lacks timestamps. ● RescueTime exposes only partial history. ● Weather geolocation is stale but a prior ZIP exists. ● Some historical dates lack weather or outcome data. ● A single image generation fails and can be retried. 
Mention each relevant limitation briefly in the PDF. 
Stop for the user 
Stop and request takeover when: 
● Facebook requires login, credentials, identity verification, or a security challenge. ● Spreadsheet access requires a sensitive authentication action. ● The routine would need to change OS security settings. ● The target account or spreadsheet cannot be identified safely. 
Image-generation retries 
Retry failed generations with simplified wording while preserving the memory’s facts. 
Handle each memory independently so one failed render does not discard successful images. 
Limit retries and record unresolved failures. Do not silently reduce the expected image count. 
 
19. Idempotency and reruns 
Use the report date as the primary idempotency key. 
A rerun must: 
● Reuse verified Facebook collection data when appropriate. ● Avoid duplicate context-history rows. ● Avoid duplicate weather-cache rows. ● Preserve previous derived values unless explicitly replacing them. 
● Keep experiments out of the final folder. ● Replace final artifacts atomically or through temporary filenames. ● Retain an archive when materially different finals are superseded. ● Revalidate the complete output after replacement. 
Use a run lock so two processes cannot generate the same report simultaneously. 
 
20. Observability and auditability 
Record structured run metadata including: 
● Run identifier. ● Scheduled date. ● Start and completion times. ● Stage status. ● Facebook memory count. ● Caught-up confirmation. ● Source availability. ● Quantified Self alignment version. ● Model version. ● Analog count. ● Image success and retry counts. ● PDF page count. ● Validation results. ● Output paths. ● Limitations and warnings. 
Logs must not contain prohibited raw private content. 
Maintain a concise automation memory summarizing what was completed and any information 
future
 
runs
 
should
 
reuse
 
or
 
avoid
 
repeating.
 
 
21. Acceptance criteria 
A daily run is fully successful only when: 
● It runs against the correct local report date. ● Facebook is read to the caught-up state. ● Every visible memory is captured in order. ● There is one verified final PNG per memory. 
● Every caption reflects the correct date and memory. ● The main folder contains no discarded experiments. ● Quantified Self timing is aligned exactly as specified. ● Mood trends and adjusted associations are reported with uncertainty. ● Analog days use behavioral and personal-state features as well as weather. ● Geographic history uses the configured region cutover correctly. ● Current geolocation stores no precise coordinates. ● Context-history and weather-cache updates are non-duplicative. ● External sources remain read-only. ● Apple Music listening remains distinct from active Music. ● The life rating and narrative use all memories. ● The Mood outlook contains all four required parts. ● Recommendations are small, personalized, and safe. ● The PDF includes limitations and source reconciliation. ● Every PDF page has been rendered and visually inspected. ● The contact sheet shows the complete ordered image set. ● Final output paths and counts are reported to the user. 
A run is partially successful when Facebook or some contextual data is unavailable but useful 
artifacts
 
can
 
still
 
be
 
produced
 
honestly.
 
A
 
run
 
is
 
blocked
 
when
 
required
 
authentication
 
or
 
security
 
intervention
 
prevents
 
safe
 
collection.
 
 
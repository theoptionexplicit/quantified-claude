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
 
 
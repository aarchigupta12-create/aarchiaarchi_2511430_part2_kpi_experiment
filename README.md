# aarchiaarchi_2511430_part2_kpi_experiment
# Part 2 — KPI Experiment: Onboarding & Activation Campaign

## Business Problem
A subscription product tested a new onboarding/activation campaign (Treatment)
against the existing experience (Control). Decide whether to launch, reject,
continue testing, or launch only for selected segments — accounting for
guardrails (refunds, support load, revenue quality), not just conversion rate.

## Dataset Used
`data/experiment_data.csv` — 1,408 users (693 Control / 715 Treatment), with
funnel events, revenue, support tickets, refunds, and engagement score.

## Tools Used
Python (pandas, numpy, scipy), Google Colab

## Steps Performed
1. Checked data quality — no missing values of concern (`days_to_convert` is
   only populated for converters, which is expected, not a defect).
2. Measured the full funnel (landing page → trial → onboarding → paid) for
   both groups with a two-proportion z-test at each stage.
3. Checked guardrail metrics: support tickets, refund rate, and — critically —
   revenue per converted user (not just aggregate revenue).
4. Broke conversion lift down by region, device type, traffic source, and
   plan type to check whether the effect is uniform across segments.

## Key Outputs
| Metric | Control | Treatment | Result |
|---|---|---|---|
| Converted to paid | 3.2% | 7.0% | **+120%, p = 0.0011 (significant)** |
| Completed onboarding | 15.6% | 21.3% | +36%, p = 0.006 (significant) |
| Support tickets / user | 0.22 | 0.37 | **Worse, p < 0.0001 (significant)** |
| Refund rate | 0.0% | 0.4% | Worse, p = 0.088 (borderline) |
| Revenue per converted user | ₹1,630 | ₹770 | **Worse, p = 0.079 (borderline, small n)** |
| Overall revenue per user | ₹51.75 | ₹53.88 | No significant difference (p = 0.92) |

Segment lift in `converted_to_paid`:
- **Social traffic source is the one segment where Treatment underperforms**
  Control (7.7% → 6.0%).
- **Basic plan** shows almost no lift (3.6% → 3.8%), while Free and Premium
  plans see 2–3x lift.
- Mobile and Tablet see far larger relative lift than Desktop.

## Business Insights
The new campaign clearly drives more people through the funnel and into a
paid conversion — the effect is large and statistically significant at every
stage. But the conversions it wins are lower quality: revenue per converting
user is roughly half of Control's, support ticket volume rises significantly,
and refund requests — which never happened in Control — start appearing in
Treatment. Read together, this looks like the campaign is successfully
lowering the bar to convert, but is pulling in some users who aren't a great
product fit (more support burden, lower spend, occasional refunds).
The effect also isn't uniform: it actively underperforms for users acquired
through Social, and does almost nothing for the Basic plan segment.

## Recommendation: Launch for selected segments, hold on Social
- **Launch Treatment for:** Email, Organic, Paid Search, and Referral traffic
  sources, and for Free/Premium plan users — these show strong, statistically
  supported conversion lift.
- **Do not launch for:** users from the Social channel, where Treatment
  underperforms Control on the primary metric.
- **Hold at Control for:** Basic plan users, where Treatment shows no
  meaningful lift, so there's no upside to offset the guardrail risk.
- **Before a full rollout**, address the guardrail signals — investigate why
  support tickets and refunds are rising, and monitor revenue per converted
  user with more data before assuming the small-sample gap will close.

This is a "launch for selected segments" decision rather than a blanket
launch or reject, because the primary metric win is real but concentrated,
and the guardrail risk is real but not yet fully proven at this sample size.

## Assumptions Made
- `days_to_convert` being empty for non-converters is treated as "not
  applicable," not missing data.
- Revenue-per-converted-user is treated as the most decision-relevant revenue
  metric, since overall revenue-per-user is diluted by the ~93–97% of users
  who never convert in either group.
- Segment cuts are treated as directional evidence for a rollout strategy,
  even where individual segment sample sizes are too small for their own
  significance test.

## Known Limitations
- The revenue-per-converted-user and refund-rate differences are directionally
  concerning but not fully statistically significant at this sample size
  (only 22 Control and 50 Treatment conversions) — a larger or longer test
  would give more confidence before a full launch decision.
- Segment-level lifts are not individually significance-tested (sample sizes
  per segment are too small); they are used as directional signal only.
- No data on long-term (>30 day) retention or lifetime value, so this
  decision is based on 30-day signals only.

## Screenshots
See `screenshots/` for the funnel/guardrail statistical output and the
segment-level breakdown tables.

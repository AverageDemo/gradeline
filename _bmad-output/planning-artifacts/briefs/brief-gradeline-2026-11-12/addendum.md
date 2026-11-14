---
title: "Product Brief Addendum: Gradeline"
created: 2026-11-12
updated: 2026-11-14
---

# Addendum: Gradeline

Material that is too detailed for the brief but is needed downstream (PRD, architecture, grading-engine design).

## Contents

- [A. Confluence factor catalogue](#a-confluence-factor-catalogue-research-2026-11-12)
- [B. TradingView / Pine v6 platform constraints](#b-tradingview--pine-v6-platform-constraints-research-2026-11-12)
- [C. Landscape and delivery](#c-landscape-and-delivery-research-2026-11-12)
- [D. User's reference methodology](#d-users-reference-methodology-from-discovery-2026-11-14)
- [E. Reference educator methodology](#e-reference-educator-methodology-research-2026-11-14)
- [F. 20-day SMA "dog leash"](#f-20-day-sma-dog-leash-research-2026-11-14)
- [G. Trade journal landscape](#g-trade-journal-landscape-research-2026-11-14)
- [H. Downstream decisions](#h-downstream-decisions-for-prd-and-architecture)
- [I. Technical risks to settle in architecture](#i-technical-risks-to-settle-in-architecture-moved-from-the-brief)

## A. Confluence factor catalogue (research, 2026-11-12)

> **Note:** the "Scene of the Crime" definition in this section (impulse origin) differs from the one in §E (first breakout from a consolidation). The PRD must choose one; see §H.

Key: **Pine** = Y (computable) / P (partial) / S (subjective). **Wt** = the researcher's judgment (H/M/L), not a practitioner consensus. **Beg** = beginner friendliness.

| Family | Factor | Pine | Wt | Beg | Notes |
|---|---|---|---|---|---|
| Structure | Trend (HH/HL) | Y | H | Easy | `ta.pivothigh/low`. Lags by the pivot lookback. |
| Structure | Swing points | Y | M | Easy | |
| Structure | Break of structure (BOS) | Y | H | Med | Definitions vary: wick or close. |
| Structure | Change of character (CHoCH) | P | M | Hard | Depends on which swing counts. |
| Levels | Classic / Camarilla / Fib pivots | Y | L–M | Easy | Camarilla R3/S3 = fade, R4/S4 = breakout (sources disagree). |
| Levels | PDH / PDL / PDC | Y | H | Easy | |
| Levels | Premarket high / low | Y | H (equities) | Easy | Needs extended-hours data. |
| Levels | Opening range (5/15/30m) | Y | H | Easy | Strongest academic support (Zarattini/Barbon/Aziz 2024). |
| Levels | Round numbers | Y | L | Easy | |
| Levels | VWAP and σ bands | Y | H (intraday) | Easy | `ta.vwap` |
| Levels | Anchored VWAP | P | M | Med | The anchor is chosen by hand. |
| Levels | Volume profile POC / VAH / VAL | P | M–H | Hard | Approximated from lower-timeframe bars. |
| Magnets | Scene of the Crime (SOC) | P | M | Med | Origin zone of a sharp, impulsive move or breakdown. Price returns to it because the fast move left it under-traded and trapped traders are anchored there. Auction-theory / Market Profile vocabulary. **No known originator. Do not attribute it to anyone.** Pine: flag a bar with range > k×ATR and store where the move began. |
| ICT/SMC | Fair value gap | Y | M | Med | 3-candle imbalance. |
| ICT/SMC | Order block | P | M | Hard | |
| ICT/SMC | Liquidity sweep | Y | H | Med | Wick through a swing or PDH, then close back inside. |
| ICT/SMC | Premium / discount | P | L–M | Med | The dealing-range choice is subjective. |
| MAs | EMA 9/20/50/200 stack | Y | M | Easy | **Measures the same thing as trend.** |
| MAs | Pullback to 9/20 EMA | Y | M | Easy | |
| Momentum/Vol | RVOL | Y | H | Easy | Evidence-backed ("Stocks in Play"). |
| Momentum/Vol | Volume spike | Y | M | Easy | |
| Momentum/Vol | RSI divergence | P | L–M | Med | |
| Momentum/Vol | MACD | Y | L | Easy | Mostly repeats the MAs. |
| Context | Higher-timeframe bias / multi-timeframe alignment | Y | H | Med | `request.security` on confirmed bars only, to avoid repainting. |
| Context | Session window / killzones | Y | gate | Easy | Better as a gate than as points. |
| Risk | ATR, R:R to the next level, stop distance | Y | veto | Easy | R:R should be a required gate (e.g. ≥ 2R). |

### How existing tools score
- Equal-weight counts (LuxAlgo calls this "the robust default"; % of factors aligned).
- Weighted points mapped to grades (Confluence Framework Pro: A+ to C. Market State Engine V2: 4×25 points, A at 60, A+ at 75, A++ at 90).
- User-ticked checklists (AlexG, "The Checklist").
- Vetoes / disqualifiers that set the score to zero (LuxAlgo).
- TrendSpider Multi-Factor Alerts: stacked conditions, apparently AND-only with no score.
- Trade Ideas Holly: a signal engine, not a grader.
- **None publishes evidence that higher grades lead to better outcomes.**

### Pitfalls the grading design must handle
1. Correlated factors counted twice (EMA stack, trend, MACD and VWAP side are often one signal). Group factors into families and cap each family.
2. Overfitting through weights. Default to equal weights.
3. More factors means fewer trades. That only helps if the factors are independent.
4. Subjective factors (order block, CHoCH, SOC origin, anchors) should be labelled as such.
5. Repainting (pivot lookback, higher-timeframe data on an unconfirmed bar).
6. False confidence. A grade is evidence, not a guarantee. This ties to the README disclaimer.

### Candidate model (research suggestion, not decided)
Required gates (R:R, session window, no veto) → optional factors scored by family with a cap per family → A+/A/B/C.

### Sources
LuxAlgo confluence guide; TradingView public scripts (Market State Engine V2, Confluence Framework Pro, Smart Confluence PRO); marketcalls SOC article; TrendSpider MFA docs; Concretum ORB paper; QuantConnect replication. Full URLs are in the research digest in the session transcript.

## B. TradingView / Pine v6 platform constraints (research, 2026-11-12)

- **Version:** Pine Script v6 is current (since Dec 2024, with 2026 updates). The alert APIs are the same as v5. Dynamic `request.*()` calls are on by default.
- **Alerts:** `alert(msg, freq)` sends a JSON string built at runtime (placeholders are NOT expanded; build values with `str.tostring`). Users create one alert per chart, choosing "Any alert() function call". Use `alert.freq_once_per_bar_close` to avoid repainting. `alertcondition()` only takes fixed strings with placeholders.
- **The alert freezes the script's inputs at creation time.** Changing criteria on the TradingView side means recreating the alert. This argues for keeping grading weights on the Gradeline side, not in Pine inputs.
- **Limits:**
  - `alert()` message and request body: 40,960 characters.
  - Auto-disable at more than 15 triggers in 3 minutes.
  - Active alerts: Essential 20, Plus 100, Premium 400, Ultimate 1,000.
  - Alerts expire after 2 months below Premium.
  - Script budget: 40 unique `request.*()` calls (64 on Ultimate), 64 plots, ~100k tokens. Multi-timeframe factors use up the request budget.
- **Webhooks:**
  - Paid plan (Essential or higher) and 2FA required.
  - POST to ports 80/443, IPv4 only.
  - Timeout of 3 seconds; **no documented retries**.
  - No custom headers, so authenticate with a per-install token in the body plus an IP allowlist (52.89.214.238, 34.212.75.30, 54.218.53.128, 52.32.178.7).
- **Distribution:** an open-source published script fits best. Publication is permanent and moderated by house rules. Users add it from Community scripts or by pasting it into the Pine Editor.
- **Beginner friction:**
  - paid plan and 2FA
  - one alert per symbol/timeframe chart
  - recreate the alert after changing inputs
  - 2-month expiry
  - picking the wrong condition entry
  - the Message field is ignored for `alert()`

## C. Landscape and delivery (research, 2026-11-12)

### Comparables
- Execution routers (no grading): TradersPost, PineConnector, WunderTrading, 3Commas, Alertatron.
- Delivery only: NotiAlarm.
- Journals (after the fact): TraderSync, Tradervue, Edgewonk.
- Confluence grading exists only on the chart (Pine scripts such as Setup Quality Scorecard and Adaptive Signal System) or as a manual-checklist Chrome extension. Open-source forwarders (fabston/TradingView-Webhook-Bot, ~1.4k stars) don't score.
- **Gap:** nothing lets a trader define criteria outside Pine, combine several alerts as a setup forms over time, and push a scored, explained card to the phone.
- TradingView's native push is free. Gradeline must add multi-alert state, a grade with the reason behind it, filtering (only A setups reach the phone) and a reviewable history.

### Delivery options
| Option | Cost | Rich card | Open-source fit |
|---|---|---|---|
| APNs + native iOS app | $99/yr + push server | Best | Poor (someone has to fund and run it) |
| CloudKit (Mac to iOS) | Dev account | Good | Medium; reported latency and lost-push problems |
| ntfy | Free, self-hostable | Partial (title, tags, priority, 3 actions; markdown shows raw on phones) | Excellent |
| Pushover | $4.99 once | Moderate | Good, but proprietary |
| Telegram / Discord | Free | Good (embeds, buttons) | Good |
| Bark | Free, iOS only | Basic | Good |

Researcher suggestion: a provider interface (this matches the global provider-pattern rule) with ntfy, Pushover, Telegram and Discord adapters at v1, and a native iOS app later.

### Is a Mac the right hub?
- A Mac behind NAT needs a tunnel. Tailscale Funnel is free and gives a stable `*.ts.net` URL; a Cloudflare named tunnel needs a domain; free ngrok URLs change on restart.
- **A sleeping laptop loses alerts silently**, because there are no retries. Desktop Macs and a Mac mini with keep-awake are fine.
- Options:
  - (a) Mac-only. Document the always-on requirement.
  - (b) Portable grading engine with two shells: a macOS app and a headless/Docker receiver.
  - (c) A tiny cloud relay (e.g. Cloudflare Worker) that answers TradingView, queues alerts and maybe pushes directly. Robust, but no longer purely local-first.

## D. User's reference methodology (from discovery, 2026-11-14)

- Timeframes scanned: 1m, 5m, 4h, D, W.
- **The higher timeframe overrides the lower.** The HTF sets bias and context; the LTF is the entry trigger. That makes grading hierarchical: an LTF factor that disagrees with HTF bias should be vetoed or discounted, not just score fewer points.
- Looks back in time for trend and pattern context: scene of the crime, change of character.
- Draws on an external reference educator's methodology. That research is complete and is written up in §E. The educator is deliberately not named.
- Pine budget note: 5 timeframes × several factors through `request.security` must fit within 40 unique requests. Bundle each timeframe's factors into one tuple per timeframe.

### AI grading (BYO key), raw notes
- The user wants optional AI grading with the user's own API keys.
- Must go through a provider interface (global provider-pattern rule) and support several LLM vendors.
- Open questions are tracked in the brief discussion: deterministic or AI as the source of truth, what the AI sees (factor JSON only, or charts/OHLCV), cost per alert, and latency (fine, since the relay queues).

## E. Reference educator methodology (research, 2026-11-14)

> **Privacy rule:** this educator is never named or identified in VCS, docs, code or the product. Built-in setups are framed as the user's own method, informed by common community concepts. They must never imply endorsement.

- **Scene of the Crime, reference definition:**
  - The zone where price **first** breaks out of a consolidation (an accumulation/distribution area).
  - A return to it acts as support for a bounce; a breakdown zone acts as resistance.
  - Filters: first breakout from that consolidation; breakout ran **≥ 7 bars**; trade the **first** retest; best for intraday/short-term bounces; works on all timeframes.
  - An alternative community definition (origin of an impulse or news move) is in §A. Gradeline must state which definition each factor uses.
  - No known originator for the term.
- **Level emphasis:**
  - unfilled gaps (targets and support)
  - 50-day MA
  - 200-week MA
  - 100-month MA
  - prior pivots
  - places where several long-term levels line up
  - a first test holds best
- **CHoCH** comes from ICT/SMC (Wyckoff roots), a different school; the user blends the two. **Higher timeframe overrides lower** is the user's own rule.
- **Pine can compute:**
  - gaps and whether they are filled
  - the 50D, 200W and 100M MAs (`request.security`)
  - pivots
  - prior D/W/M highs and lows
  - SOC approximately: a range-break detector plus "first breakout", "≥ 7 bars of follow-through" and "first retest"
- **Discretionary, out of scope:** how the consolidation is drawn, Elliott/Gann counts, proprietary cycle and turn dates.
- **Inferred rubric (our reading, not anyone's published rules):**
  1. first test
  2. several long-term levels lining up
  3. SOC conditions met
  4. price on the right side of the 50D/200W MAs
- Sources: kept out of the repo on purpose (they would identify the educator).

## F. 20-day SMA "dog leash" (research, 2026-11-14)

- **Reference MAs are all SIMPLE on close:** 20D, 50D, 200W, 100M. Allow the MA type as a setting; tests show neither SMA nor EMA is superior.
- **Lore:**
  - The rubber-band metaphor is common; "dog leash" looks like an informal variant.
  - 20 is about one trading month, and the 20 SMA is the Bollinger midline.
  - Bollinger: a band touch is not a signal, and strong trends "walk the bands".
- **Evidence:**
  - Short-horizon support: stocks further below their 20/50-day MA beat those above over 1–2 weeks (Alajbeg, Bubaš & Vasić 2017). This is consistent with the 1-week to 1-month reversal effect (Jegadeesh 1990; Lehmann 1990).
  - It is non-linear: the most extreme laggards did worst.
  - It conflicts with 3–12 month momentum (Jegadeesh & Titman 1993).
  - One unverified backtest: stocks more than 5.5 ATR above the 20MA kept trending rather than reverting.
  - **"Always returns" is not supported.** It is mostly true in choppy or mildly trending stocks and fails in the strongest moves.
  - Part of every "return" is arithmetic: in a trend the 20 SMA lags about 9.5 days and catches up with price while price moves sideways.
- **Factors:**
  - **Extension** = (close − SMA20)/ATR14. Over 2–3 ATR = stretched; the threshold is lore and should be tunable.
  - **Pullback** = a touch or near-touch while the SMA slopes in the trend direction (continuation entry).
  - **Slope** = (SMA20 − SMA20[5])/ATR.
  - **%B.**
- **Pine:** `request.security(..., "D", ta.sma(close,20)[1], lookahead=barmerge.lookahead_on)` gives a non-repainting daily value; gate on `barstate.isconfirmed`; pin session and adjustment settings. Unverified against the docs.
- **Product implication:** Gradeline's outcome tracking can produce real numbers: P(touch the 20 SMA within N days | ATR extension, slope) for the user's own symbols.

## G. Trade journal landscape (research, 2026-11-14)

- **Fields:**
  - **Must-have:** symbol, side, entry and exit time and price, size, P&L, setup tag, notes.
  - **Common:** fees, stop, target, R-multiple, mistake tags, screenshots.
  - **Power-user:** emotion tags, MFE/MAE plus timing, custom stats, voice notes, options fields.
- **Calendar view (standard everywhere):** each day cell shows net P&L and trade count (green, red or grey), plus weekly and monthly totals. P&L is usually booked on the exit day.
- **Stats:**
  - win rate, expectancy, profit factor, average R, equity curve
  - broken down by setup, symbol, time of day, day of week, hold time and side
  - MFE/MAE reports
- **How data gets in:** broker sync (IBKR, Schwab, TradeStation, NinjaTrader, Tradovate, Webull, Fidelity, E*Trade), generic CSV, or manual entry (painful). **Lowest friction:** prices filled in automatically, the user adds only tags and notes. Gradeline already has setup, entry, stop, target and outcome from the alert, so a took/skipped toggle plus an optional fill price is enough.
- **Gradeline's gap:**
  - Edgewonk ("Missed Trades") and TradeZella ("missed" tag) need missed trades entered by hand, with the outcome estimated.
  - **No journal has an objective record of what happened to skipped setups, or ties a pre-trade grade to results.**
  - Key reports to build: grade × taken/skipped → outcome, and "R left on the table" from skips.
- **Interop:**
  - There is no standard schema. The shared core is Date/Time, Symbol, Side, Qty, Price, plus optional Commission and Fees.
  - Suggested Gradeline export, one row per alert: alert_id, symbol, side, grade, alert_time, entry, stop, target, outcome, MFE_R, MAE_R, taken, fill_price, exit_price, exit_time, qty, fees, pnl, r_multiple, setup, mistakes, notes.
  - Optionally also export Tradervue's generic format.

## H. Downstream decisions (for PRD and architecture)

- **AI context per alert:**
  - Recent raw bars: W 52, D 60, 4h 60, 5m 1–2 sessions, 1m the last hour.
  - Long history (200W/100M SMA, gaps, pivots, SOC zones) is pre-computed in Pine and sent as conclusions.
  - About 15–18k characters per alert, under TradingView's 40,960 limit. About 5–10k input tokens on the user's key.
- **AI authority:**
  - The rules grade; the AI explains only.
  - The AI also records its *independent* grade for every alert, without acting on it, so that its authority can later be widened on evidence.
  - Next step if the evidence supports it: a bounded nudge of at most one grade step with a written reason, never past a gate.
  - The AI grades against the user's configured rubric and never invents criteria.
- **Grading split:**
  - The Pine companion is generic. It detects factors and emits JSON.
  - All weights, gates and grade logic live in Gradeline, because the alert fixes the script's inputs when it is created.
- **Outcome capture:** the companion follows its own setups and sends follow-up outcome alerts (target, stop, timeout, MFE/MAE). Verify against the 15-alerts-in-3-minutes limit.
- **Setup editor UX:** templates by default, Advanced mode for custom setups. The create page reopens in whichever mode was used last.
- **Lock-screen card (approved mock):**
  ```
  NVDA · Leash Pullback · A−
  W↑ D↑ 4h↑ · 1st test of SOC · 0.4 ATR from 20D
  ⚠ Below 50D on 4h
  R:R 2.6 → PDH 142.80
  ```
- **Providers:** push and AI both sit behind provider interfaces. v1 uses ntfy and Anthropic.
- **Terminology:** always "plan levels", never "recommendations".
- **Relay:** each user deploys their own (a one-click Cloudflare deploy inside onboarding). It authenticates with a per-install token in the body plus TradingView's IP allowlist, and answers in under 3 seconds.
- **Open for PRD:** which definition of Scene of the Crime each template uses (§A impulse-origin vs §E first-breakout). The PRD must choose one.

## I. Technical risks to settle in architecture (moved from the brief)

- Packing past higher-timeframe bars into a single alert within Pine's request budget.
- Outcome tracking via follow-up alerts without tripping TradingView's limit of 15 alerts in 3 minutes.
- Two-month alert expiry below the Premium plan.
- The companion script's settings being fixed into the alert when it's created (so the companion must be generic and the weights must live in Gradeline).

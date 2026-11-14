---
title: "Product Brief: Gradeline"
status: final
created: 2026-11-12
updated: 2026-11-14
---

# Product Brief: Gradeline

## Executive Summary

Gradeline is an open-source macOS app that grades a trading setup as it forms and sends the verdict to the trader's phone. A free Pine Script v6 companion on TradingView reads five timeframes, from the weekly down to the 1-minute, and sends what it finds the moment a setup appears. Gradeline checks it against the trader's own rules (gates, factor families, the higher timeframe in charge) and pushes a card that fits on a lock screen: the grade, the factors that decided it, any warnings, and plan levels with R:R.

The problem is narrow but real. A disciplined trader can't fully check every alert across five timeframes in the seconds a decision allows, so they skip setups they couldn't verify and never learn whether those were winners. Gradeline makes the check fast and consistent, then follows every alert, taken or skipped, to its outcome. That gives the trader evidence no journal offers: whether their A setups actually beat their C setups, including the ones they passed on.

Grades are deterministic. An optional AI, using the trader's own Anthropic API key, explains each grade in plain English but can never change it. Everything is local first: each trader runs their own relay, history lives on their Mac, and Gradeline never places orders. It is built first for the author's own intraday equities and futures trading, and released openly for any trader who already uses TradingView and wants an edge from quality, not quantity.

## The Problem

Checking an alert properly means reading five timeframes, where the higher ones override the lower, plus scanning history for context: where price broke out of a consolidation, whether this is the first test of a level, how stretched it is from the 20-day average. That is slow, varies with fatigue, and fails when three alerts land in four minutes.

TradingView alerts say a condition fired, not whether the setup is any good. Confluence grading exists only inside individual indicators. Nothing checks an alert against a trader's own criteria and says, in time to act, whether it's an A or a C and why.

For a disciplined trader the cost is unusual. Their rule is "if I don't know, I don't do," so unchecked setups get skipped and their results are never learned. **It costs them an edge they can't measure.** A beginner has the opposite problem: they don't know what to check, so they take the alert at face value.

## The Solution

1. **Define a setup.** In the Mac app the trader adjusts a template (for example "20D leash pullback" or "SOC first retest") or builds from scratch in **Advanced mode**; the app reopens in the mode last used. A setup says which factors count, which are hard gates (for example, the higher timeframe must agree, or R:R must be at least 2), and how much each factor family is worth. Gradeline generates the webhook URL and a ready-to-use alert.
2. **Detect on TradingView.** The free, open-source Pine Script v6 companion does the heavy reading: SMAs on close (20D "leash", 50D, 200W, 100M), pivots, prior highs and lows, unfilled gaps, scene-of-the-crime zones, structure breaks. It sends its findings plus recent price bars from each timeframe as one webhook.
3. **Catch it reliably.** Each trader's own small cloud relay, deployed in one guided click, answers TradingView right away and queues the alert, so nothing is lost while the Mac sleeps. No one else's server sees their alerts.
4. **Grade it.** The rules engine applies the trader's gates and weights. The higher timeframe overrides the lower, and related factors share a cap so they can't double-count. With an Anthropic API key added, the AI explains the grade but can't change it.
5. **Push it.** A card, filtered to the grades the trader cares about, shows symbol, setup and grade; trend on each higher timeframe; deciding factors; warnings; and R:R to the next target. Entry, stop and target are always called **plan levels**: mechanical results of the trader's own rules, not advice. v1 uses ntfy, which is free and open source; other channels come later behind the same interface.
6. **Track the outcome.** The companion follows every graded alert, skipped ones included, to its target, stop or a timeout, with no extra market data.
7. **Journal it, whenever.** On a Mac calendar the trader marks which alerts they took, with an optional fill price and note. Nothing is asked at trade time. Gradeline compares actual results with the graded plan: A versus C grades, taken versus skipped, R left on the table. Trades export to CSV. All history stays on the Mac, and with an AI key the AI draws on the trader's own past outcomes.

## What Makes This Different

- **It grades the setup, not just the trigger.** Webhook tools route alerts into trades or forward them to chat. Gradeline checks an alert against criteria the trader defines outside Pine, across five timeframes, and pushes a graded, explained card.
- **Changing your criteria never touches TradingView.** An alert keeps the script settings from when it was created, but Gradeline holds the weights and gates, so setups can be tuned without recreating alerts.
- **It knows what you skipped.** Outcomes are tracked automatically, so Gradeline can answer *did the A setups I passed on work?* Competing "missed trade" features rely on manual entry and guesswork.
- **Deterministic and honest.** The same setup always gets the same grade, and a grade is evidence, never a promise.
- **Templates come from a working trader's process:** the higher timeframe in charge, SMAs on close, first retests of a consolidation breakout zone. They haven't been backtested yet; Gradeline's own outcome tracking will be the first to prove or disprove them, on each trader's own symbols.
- **Open source and local first.** Free, though webhooks need a paid TradingView plan.

**What is *not* a moat:** none of this is technically hard to copy. The edge is the combination (multi-timeframe grading, automatic outcome tracking, a journal that includes skipped setups), its openness, and how fast it's iterated.

## Who This Serves

**Primary: the intraday US-equities and futures trader.** They trade morning-session setups like opening-range breaks and pullbacks to moving averages, and already pay for TradingView. Success means acting on A setups with confidence, skipping C setups without wondering, and finally having data on whether skipped setups would have worked. The first such user is the author.

**Secondary: the newer trader who already uses TradingView.** They know the platform but not what separates a good setup from a bad one. Templates give them a working method, and the AI explanation teaches the why behind each grade. Success means learning what an A setup looks like, then building their own in Advanced mode.

**Prerequisites (by design):** a Mac, a paid TradingView plan (Essential or higher, needed for webhooks) with two-factor authentication on, and a phone with ntfy installed. Gradeline guides the rest, including the relay.

**Not for (v1):** people who want trades placed automatically (Gradeline never places orders, as a permanent principle); forex and crypto traders; people with no Mac; and traders whose method can't be computed from chart data, such as discretionary wave counts or proprietary cycle dates.

## Scope

**In v1:**
- A native macOS app with a setup editor (templates by default, Advanced mode for custom setups, remembers the last mode used) and onboarding that walks the trader through deploying their own relay
- An open-source Pine Script v6 companion that detects factors across W, D, 4h, 5m and 1m, sends factor results and recent bars, and follows its own alerts to their outcome
- A self-deployed cloud relay (one-click Cloudflare deploy)
- A deterministic grading engine: gates, factor families with caps, the higher timeframe overriding the lower
- AI explanations of grades through the user's own Anthropic API key (optional; the AI explains, never changes the grade; its own opinion is recorded)
- Push delivery through ntfy, filtered by grade
- Automatic outcome tracking for every alert, taken or skipped
- A thin journal: a calendar, taken/skipped with optional fill price and note, grade-vs-outcome and skipped-setup reports, CSV export
- US equities and futures

**Out of v1 (later):** more push channels (Pushover, Telegram, Discord, a native iOS app), more AI providers (OpenAI, local models), a shared hosted relay, suggestions for rule changes, an opt-in community outcome pool, broker import, full journal features (psychology tags, screenshots, the full stats suite), forex and crypto templates.

**Never:** placing orders or connecting to brokers to execute.

**Technical risks** that architecture must settle are listed in the addendum (§I).

## Success Criteria

**The product works:**
- A graded alert reaches the phone within **5 seconds** of TradingView firing.
- **No alerts are lost**, including while the Mac is asleep.
- Replaying the same alert always produces the same grade.
- A trader who already uses TradingView goes from install to their first graded alert in **under 20 minutes**.

**It earns its place in the author's trading:**
- The author trades every session from Gradeline's grades instead of checking charts by hand.
- After **3 months**, the outcome history shows whether A grades really beat C grades, and gives real numbers for each setup, such as how often price returned to the 20-day SMA.

Adoption is deliberately not a v1 goal.

## Vision

If Gradeline works, it becomes the open-source standard for checking a setup before taking it: a community-contributed library of setups tested against real outcomes, not marketing claims. Each trader's history shows which of their setups have an edge, and Gradeline suggests rule changes the trader approves or rejects. Over time an opt-in, anonymised outcome pool shows how setups perform across traders and markets. Delivery grows to a native iOS card and any channel a trader prefers, and AI works with any provider, including local models. Gradeline never places orders and never claims to know the future: it makes a trader's own judgment faster, more consistent and measurable.

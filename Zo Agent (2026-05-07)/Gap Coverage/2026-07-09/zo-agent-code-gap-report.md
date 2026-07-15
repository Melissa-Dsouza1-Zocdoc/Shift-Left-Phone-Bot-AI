# Zo Agent — Code Gap Report (Plain-English Edition)

**Date:** 2026-07-09
**What this is:** A caller-by-caller look at what the Zo voice agent handles, what it is missing, and what the developers should fix or confirm. Written so it can be read on its own — no code knowledge needed.
**Repo under test:** [Zocdoc/zo-agent](https://github.com/Zocdoc/zo-agent)
**Based on:** the test mapping + coverage report in [Zo Agent (2026-05-07)](https://github.com/Melissa-Dsouza1-Zocdoc/Shift-Left-Phone-Bot-AI/tree/main/Zo%20Agent%20(2026-05-07)), cross-checked against the actual agent code.
**Order:** we walk the code the same way a phone call does — starting at the very beginning (a call comes in) and moving forward. Delivered in batches of 10 cases.

---

## How to read this

Each row is one scenario. It tells you: what the caller does, what Zo does today, whether a test already exists, what is missing, and what the developer should do.

**Category markers** (so related cases stay together): each batch is grouped under plain category headings, for example — Call Entry & Setup, Greeting & Goal Routing, DOB, Name, Phone, Gender, Insurance, Date/Time, Provider, Booking, Reschedule/Cancel, Auth/Identity, Transfer, Language, PHI/Privacy, CSAT.

**Existing test?**
- ✅ **Excellent** — covered well
- 🟡 **Needs improvement** — a test exists but misses an important angle
- 🛠 **Needs validation** — the code should reject/guard something but doesn't
- 🔴 **Missing** — nothing tests this today

**Priority:** 🔴 High · 🟠 Medium · 🟢 Low

**Action:**
- `Dev fix` — code change needed
- `Talk to dev` — confirm the intended behavior first, then decide
- `Add test` — code looks fine, only a test is missing
- `Initiative` — bigger effort, needs planning

---

# Batch 1 — Call comes in → Greeting

This is the very start of every call: the phone rings, Zo answers, plays the "this call may be recorded" message, greets the caller, figures out what they want, and either books them or transfers them to a human.

## Call Entry & Setup
*(code: `src/agent.py` — the entry point that answers the call and sets everything up)*

| # | What the caller does | What Zo does today | Existing test? | What's missing → what could go wrong | Priority | Action |
|---|----------------------|--------------------|:--------------:|--------------------------------------|:--------:|--------|
| 1 | Calls in and should hear the **"this call may be recorded"** notice before anything else | Zo tries to play the recording notice. **If that audio fails to play, Zo just skips it and continues the call** (the error is logged and swallowed). | 🔴 Missing | Only the *list of notice messages* is tested, not what happens when the notice fails to play. A caller could be recorded **without ever being told** → a legal/compliance problem, not just a bug. | 🔴 High | `Talk to dev` — should a failed recording notice block or transfer the call instead of continuing silently? Then `Add test`. |
| 2 | Any normal caller (nothing special) | Zo talks to the LLM. Because of a line meant only for a test phone number, **retries to the LLM are turned off for _every_ call** (`agent.py:1127` sits outside the "test number only" check). | 🔴 Missing | If the LLM has one hiccup, there is no automatic retry for real callers → more dropped/mishandled turns than expected. Looks like a copy-paste/indent slip. | 🔴 High | `Talk to dev` — confirm this was meant to apply only to the test number, then `Dev fix`. |
| 3 | Talking to Zo when the **voice system hits a fatal error** (LLM/voice pipeline dies mid-call) | Zo plays a pre-recorded "system error" message, then transfers to a human. If that pre-recorded audio file is missing, it falls back to reading the message out loud. | 🟡 Needs improvement | The happy path is handled well, but there is no test for the **missing-audio-file fallback** or for the case where **no transfer number exists** at that moment. | 🟠 Medium | `Add test` for the missing-asset and no-number fallbacks. |
| 4 | Has a **bad connection** — the call keeps trying to reconnect | Zo waits up to 10 seconds; if it still hasn't reconnected, it gives up and cleanly ends the job (so it doesn't hang forever and leak memory). | 🔴 Missing | Good safety behavior, but **nothing tests it**. If someone changes the 10-second logic later, a call could silently hang for hours again (this actually happened before — a job retried for ~38 hours). | 🟠 Medium | `Add test` for the reconnect give-up timer. |
| 5 | Presses **"1" for Spanish** during the opening message | Zo captures the key-press and switches the call to Spanish before the greeting. | 🟡 Needs improvement | The Spanish *language wording* is tested, but the **key-press handling itself** is not — e.g. pressing 1 more than once, or pressing it late in the call. Behavior is undefined. | 🟠 Medium | `Talk to dev` on intended behavior for repeat/late presses, then `Add test`. |

## Greeting & Goal Routing
*(code: `src/agents/greeting_agent.py` — the first agent the caller talks to; figures out intent and routes)*

| # | What the caller does | What Zo does today | Existing test? | What's missing → what could go wrong | Priority | Action |
|---|----------------------|--------------------|:--------------:|--------------------------------------|:--------:|--------|
| 6 | Says something Zo's LLM turns into an **invalid intent** (not in the allowed list) | Zo replies with an internal "invalid, try again" instruction and asks the LLM to pick again. | 🔴 Missing | There is **no limit** on how many times this can repeat. If the LLM keeps returning an invalid intent, the caller could be stuck in a silent loop with no escape to a human. | 🔴 High | `Talk to dev` — add a max-attempts escape to transfer, then `Add test`. |
| 7 | Wants to **schedule** and the LLM sets an appointment type | Zo saves the appointment type. The value is only *supposed* to be `in_person` or `telemedicine`, but **any text is accepted** — nothing checks it. | 🛠 Needs validation | A wrong/garbage appointment type flows into the booking flow → could route the caller to the wrong kind of visit, or fail deeper in the flow where it's harder to diagnose. | 🟠 Medium | `Dev fix` — validate appointment type against the allowed two values. |
| 8 | Reaches a practice set to **auto-transfer**, but that practice has **no "catch-all" phone number** configured | Zo can't find a number, logs an error, bumps a metric, and **deletes the call (hangs up) — the caller hears nothing and is dropped**. | 🔴 Missing | The normal transfer-number fallback is well tested, but this **"drop the caller silently" branch is not**. A misconfigured practice = callers hung up on with no message. | 🔴 High | `Talk to dev` — should this play an apology/last-resort message first? Then `Add test`. |
| 9 | Keeps being **vague** about why they called ("I just need help", "talk to someone") turn after turn | Zo asks a clarifying question to route them. | 🟡 Needs improvement | A test covers the **first** vague answer, but not what happens when the caller **stays vague repeatedly** — today Zo would re-ask forever with no transfer escape. | 🔴 High | `Talk to dev` — confirm there's a give-up-and-transfer rule, then `Add test`. |
| 10 | Asks **several FAQs** (address, hours, parking…) before saying why they called | Zo answers them; it allows up to **25** of these back-and-forths at the greeting before giving up and transferring. | 🟡 Needs improvement | General banter-limit tests exist, but not the **greeting-specific limit of 25** or the exact boundary (25th vs 26th turn), so an off-by-one change could slip through. | 🟠 Medium | `Add test` for the 25-turn boundary at the greeting. |

---

# Batch 2 — Screening questions → Handoff into booking

Right after the caller says what they want, Zo runs a quick **screening step**: it calls the practice's rule engine (`/booking/accumulate`) to see if this practice needs any extra questions answered first (e.g. "cosmetic or medical?", "which clinic?"). Depending on the answers, the practice's rules can send the caller straight to a human, end the call, or let Zo carry on into booking. Once screening is done, Zo starts **looking up the patient by date of birth**. This batch walks that stretch.

## Intent Screening
*(code: `src/tasks/initial_screening_task.py` — asks any practice-specific screening questions before booking)*

| # | What the caller does | What Zo does today | Existing test? | What's missing → what could go wrong | Priority | Action |
|---|----------------------|--------------------|:--------------:|--------------------------------------|:--------:|--------|
| 11 | Reaches a practice, and the **screening rule-engine call fails** (network blip / server error) at the very start of booking | Zo catches the failure and **transfers the caller to a human** instead of guessing. | 🔴 Missing | Sensible fallback, but **nothing tests it**. If someone later changes this to "continue anyway," callers at practices with mandatory screening rules could get booked incorrectly and no test would catch it. | 🟠 Medium | `Add test` for the accumulate-failure → transfer path. |
| 12 | Picks an option whose rule says **"this clinic books elsewhere — end the call"** (a hang-up rule) | Zo speaks the practice's goodbye message and hangs up. | 🔴 Missing | Untested — **and there's a subtle gap**: the hang-up only happens if the goodbye message has text. If the practice configures the rule with a **blank/empty message**, Zo *ignores the hang-up* and quietly continues into patient look-up instead of ending the call. | 🔴 High | `Talk to dev` — should an empty-message hang-up still end the call? Then `Add test`. |
| 13 | Answers a **specialty-type question** ("are you here for cosmetic or medical?") | Zo tags that answer as *"this is a specialty, not the reason for the visit"* so it still asks the caller why they're coming in. | 🔴 Missing | The tag only fires for **two hard-coded question IDs** (`dermatologist_or_allergist_schedule`, `cosmetic_or_medical`). If the practice team adds a **new** specialty question on the server, Zo won't tag it → the LLM may treat "cosmetic" as the visit reason and skip asking what's actually wrong. | 🔴 High | `Talk to dev` — this hard-coded list will drift out of sync with the server. Then `Add test`. |
| 14 | Gives a screening answer that tells the rules to **route future transfers to a specific number** (a transfer-number override) | Zo quietly saves that number so that if the caller later asks for a human, it dials the right place. | 🔴 Missing | Untested. Also: if the override arrives with a **missing number or missing type**, it's silently dropped, and if two overrides of the same type arrive, the **last one wins** — none of this is verified, so a routing rule could silently fail to take effect. | 🟠 Medium | `Add test` for override-stashing (valid, missing-field, and last-write-wins). |
| 15 | Goes through a practice that asks **several screening questions in a row** | Zo asks them one at a time, submitting each answer and pulling the next question until there are none left. | 🔴 Missing | Untested. There's **no cap** on how many questions can be chained, and a malformed question list from the server isn't guarded — a bad rule config could keep the caller answering questions with no exit. | 🟠 Medium | `Talk to dev` on a sane max, then `Add test` for the multi-question loop. |
| 16 | Reaches a practice with **mandatory screening**, but the screening step itself **crashes** (not the network call — the task logic) | Zo logs the error and **continues into booking with no screening at all** (`unified_new_appointment_agent.py:402`). | 🔴 Missing | This is the opposite of case 11: here a failure means the caller is booked **skipping the practice's required questions entirely**. Untested, and possibly the wrong default for practices where screening decides routing/eligibility. | 🔴 High | `Talk to dev` — should a screening crash transfer to a human instead of silently skipping it? Then `Add test`. |

## Handoff & Patient Look-up
*(code: `src/agents/unified_new_appointment_agent.py` + `src/agents/base_agent.py` — starts booking and finds the patient by DOB)*

| # | What the caller does | What Zo does today | Existing test? | What's missing → what could go wrong | Priority | Action |
|---|----------------------|--------------------|:--------------:|--------------------------------------|:--------:|--------|
| 17 | Any caller at a practice where the **screening feature flag is OFF** (`unified_use_accumulate`) | Zo **skips the whole screening step** and goes straight into booking. | 🔴 Missing | Tests exercise the flag-ON path, but the **flag-OFF path is not covered**. Two very different behaviors ship at once; a regression in the "no screening" path would go unnoticed until a real call. | 🟠 Medium | `Add test` for the flag-OFF (no-screening) path. |
| 18 | Gives a **future or impossible date of birth** ("March 15, 2099", or a newborn born today) | Zo **does reject it** — the code refuses any DOB before 1910 or dated yesterday-or-later, and tells the LLM it's likely a mishearing (`search_patients:617`). | 🟡 Needs improvement | The guard *exists* (good!), but the only test covers an **invalid month (13)**. The **future-date, born-today, and pre-1910 boundaries are not tested**, so a change to that guard could silently let a bad DOB through into booking. *(This was the DOB example you flagged — the validation is there; the test isn't.)* | 🟠 Medium | `Add test` for future / born-today / pre-1910 DOB boundaries. |
| 19 | Is (or is booking for) a **minor** — DOB works out to under 18 | If the minor-detection flag is on, Zo flags the record as a minor and attaches special routing instructions for the LLM (`base_agent.py:557`). | 🟡 Needs improvement | Minor **booking policy** is tested at the final booking step (self → transfer, for-someone-else → proceed), but the **age-18 boundary** in patient look-up (someone turning 18 *today*) and the **flag-OFF path** (no minor handling at all) are not. An off-by-one in the age math wouldn't be caught. | 🟠 Medium | `Add test` for the exactly-18-today boundary and the flag-OFF path. |
| 20 | Wants to book at a practice/call-center where **that booking is blocked** (new-patient or existing-patient booking turned off), or asks for a **provider who can't be found** | Zo transfers the caller to a human with the right reason code. | 🔴 Missing | The block logic has **two different modes** (a newer per-practice setting vs. the old hard-coded new-patient blocklist; existing-patient booking was *never* blocked in the old mode) and the **provider-not-found transfer** — none of these transfer paths are directly tested. A misconfiguration could book someone who should have been blocked. | 🟠 Medium | `Add test` for each booking-blocked mode + provider-not-found transfer. |

---
*Batch 2 of N — Screening & Booking Handoff. Next batch continues forward in the code: provider/date resolution and building the availability search (`search_providers`, `resolve_day_to_date`, `resolve_date_range`, availability handling).*

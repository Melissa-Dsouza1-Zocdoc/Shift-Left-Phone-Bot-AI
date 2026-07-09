# Zo Agent — Code Gap Report (Plain-English Edition)

**Date:** 2026-07-09
**What this is:** A caller-by-caller look at what the Zo voice agent handles, what it is missing, and what the developers should fix or confirm. Written so it can be read on its own — no code knowledge needed.
**Repo under test:** [Zocdoc/zo-agent](https://github.com/Zocdoc/zo-agent)
**Based on:** the test mapping + coverage report in [Zo Agent (2026-05-07)](https://github.com/Melissa-Dsouza1-Zocdoc/Shift-Left-Phone-Bot-AI/tree/main/Zo%20Agent%20(2026-05-07)), cross-checked against the actual agent code.
**Order:** we walk the code the same way a phone call does — starting at the very beginning (a call comes in) and moving forward. Delivered in batches of 10 cases.

---

## How to read this

Each row is one scenario. It tells you: what the caller does, what Zo does today, whether a test already exists, what is missing, and what the developer should do.

**Category markers** (so related cases stay together):
☎️ Call Entry & Setup · 👋 Greeting & Goal Routing · 🎂 DOB · 👤 Name · 📞 Phone · ⚧ Gender · 🏥 Insurance · 📅 Date/Time · 🩺 Provider · 📖 Booking · 🔁 Reschedule/Cancel · 🔐 Auth/Identity · ☎️ Transfer · 🗣 Language · 🔒 PHI/Privacy · ⭐ CSAT

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

## ☎️ Call Entry & Setup
*(code: `src/agent.py` — the entry point that answers the call and sets everything up)*

| # | What the caller does | What Zo does today | Existing test? | What's missing → what could go wrong | Priority | Action |
|---|----------------------|--------------------|:--------------:|--------------------------------------|:--------:|--------|
| 1 | Calls in and should hear the **"this call may be recorded"** notice before anything else | Zo tries to play the recording notice. **If that audio fails to play, Zo just skips it and continues the call** (the error is logged and swallowed). | 🔴 Missing | Only the *list of notice messages* is tested, not what happens when the notice fails to play. A caller could be recorded **without ever being told** → a legal/compliance problem, not just a bug. | 🔴 High | `Talk to dev` — should a failed recording notice block or transfer the call instead of continuing silently? Then `Add test`. |
| 2 | Any normal caller (nothing special) | Zo talks to the LLM. Because of a line meant only for a test phone number, **retries to the LLM are turned off for _every_ call** (`agent.py:1127` sits outside the "test number only" check). | 🔴 Missing | If the LLM has one hiccup, there is no automatic retry for real callers → more dropped/mishandled turns than expected. Looks like a copy-paste/indent slip. | 🔴 High | `Talk to dev` — confirm this was meant to apply only to the test number, then `Dev fix`. |
| 3 | Talking to Zo when the **voice system hits a fatal error** (LLM/voice pipeline dies mid-call) | Zo plays a pre-recorded "system error" message, then transfers to a human. If that pre-recorded audio file is missing, it falls back to reading the message out loud. | 🟡 Needs improvement | The happy path is handled well, but there is no test for the **missing-audio-file fallback** or for the case where **no transfer number exists** at that moment. | 🟠 Medium | `Add test` for the missing-asset and no-number fallbacks. |
| 4 | Has a **bad connection** — the call keeps trying to reconnect | Zo waits up to 10 seconds; if it still hasn't reconnected, it gives up and cleanly ends the job (so it doesn't hang forever and leak memory). | 🔴 Missing | Good safety behavior, but **nothing tests it**. If someone changes the 10-second logic later, a call could silently hang for hours again (this actually happened before — a job retried for ~38 hours). | 🟠 Medium | `Add test` for the reconnect give-up timer. |
| 5 | Presses **"1" for Spanish** during the opening message | Zo captures the key-press and switches the call to Spanish before the greeting. | 🟡 Needs improvement | The Spanish *language wording* is tested, but the **key-press handling itself** is not — e.g. pressing 1 more than once, or pressing it late in the call. Behavior is undefined. | 🟠 Medium | `Talk to dev` on intended behavior for repeat/late presses, then `Add test`. |

## 👋 Greeting & Goal Routing
*(code: `src/agents/greeting_agent.py` — the first agent the caller talks to; figures out intent and routes)*

| # | What the caller does | What Zo does today | Existing test? | What's missing → what could go wrong | Priority | Action |
|---|----------------------|--------------------|:--------------:|--------------------------------------|:--------:|--------|
| 6 | Says something Zo's LLM turns into an **invalid intent** (not in the allowed list) | Zo replies with an internal "invalid, try again" instruction and asks the LLM to pick again. | 🔴 Missing | There is **no limit** on how many times this can repeat. If the LLM keeps returning an invalid intent, the caller could be stuck in a silent loop with no escape to a human. | 🔴 High | `Talk to dev` — add a max-attempts escape to transfer, then `Add test`. |
| 7 | Wants to **schedule** and the LLM sets an appointment type | Zo saves the appointment type. The value is only *supposed* to be `in_person` or `telemedicine`, but **any text is accepted** — nothing checks it. | 🛠 Needs validation | A wrong/garbage appointment type flows into the booking flow → could route the caller to the wrong kind of visit, or fail deeper in the flow where it's harder to diagnose. | 🟠 Medium | `Dev fix` — validate appointment type against the allowed two values. |
| 8 | Reaches a practice set to **auto-transfer**, but that practice has **no "catch-all" phone number** configured | Zo can't find a number, logs an error, bumps a metric, and **deletes the call (hangs up) — the caller hears nothing and is dropped**. | 🔴 Missing | The normal transfer-number fallback is well tested, but this **"drop the caller silently" branch is not**. A misconfigured practice = callers hung up on with no message. | 🔴 High | `Talk to dev` — should this play an apology/last-resort message first? Then `Add test`. |
| 9 | Keeps being **vague** about why they called ("I just need help", "talk to someone") turn after turn | Zo asks a clarifying question to route them. | 🟡 Needs improvement | A test covers the **first** vague answer, but not what happens when the caller **stays vague repeatedly** — today Zo would re-ask forever with no transfer escape. | 🔴 High | `Talk to dev` — confirm there's a give-up-and-transfer rule, then `Add test`. |
| 10 | Asks **several FAQs** (address, hours, parking…) before saying why they called | Zo answers them; it allows up to **25** of these back-and-forths at the greeting before giving up and transferring. | 🟡 Needs improvement | General banter-limit tests exist, but not the **greeting-specific limit of 25** or the exact boundary (25th vs 26th turn), so an off-by-one change could slip through. | 🟠 Medium | `Add test` for the 25-turn boundary at the greeting. |

---
*Batch 1 of N — Call Entry & Greeting. Next batch continues forward in the code: intent screening and the handoff into the booking flow.*

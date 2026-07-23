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
<!--
Continuation of zo-agent-code-gap-report.md — Batches 3-7.
Generated 2026-07-23 against Zocdoc/zo-agent main @ 1c6de1c.
-->

> ## ⚠️ Architecture note (read before Batch 3)
>
> Batches 1-2 were written on 2026-07-09 against an older `unified_new_appointment_agent.py`
> design (`search_patients`, `resolve_day_to_date`, etc.). **That code no longer exists on
> `main`.** Current `main` (@ `1c6de1c`) is a **DAG-based flow engine**: `FlowController`
> (`src/flow/flow_controller.py`) auto-builds a dependency graph from each agent's declared
> input/output fields and topologically sorts to pick the next step. The `schedule` goal runs,
> in priority order: `AuthenticationAgent` → `SelectLocationAgent` → `SelectProviderAgent` →
> `SelectTimeslotAgent` → `SelectInsuranceAgent` → `ConfirmAppointmentAgent`. Cancellation runs
> through `CancellationAgent` + `AuthenticatePatientTask`.
>
> **Reality check — much of the schedule flow is scaffolding, not shipped logic.** Location,
> provider, timeslot, and insurance agents currently **store whatever free-text string the LLM
> passes**, backed by unused `SAMPLE_*` constants and prompts that hard-code fake options
> (Manhattan/Brooklyn; "Dr. Ava Patel"). `ConfirmAppointmentAgent.submit_confirmed` **books
> nothing** — it flips a boolean. And the schedule flow's `AuthenticationAgent` sets
> `authenticated=True` with **no identity verification at all** (a separate, weaker path than
> the real `AuthenticatePatientTask` used by cancellation). So for the mid-flow batches below,
> many rows are marked **`Talk to dev`** = "confirm this is intentional WIP" rather than
> "production bug." The genuinely-live paths (name/phone/DOB collection, cancellation auth,
> transfer, flow-controller routing) carry real priority.

---

# Batch 3 — Caller says their name and phone number

After the caller's identity step, Zo collects and reads back the caller's **name** and **phone
number**. These tasks (`collect_first_name_task.py`, `collect_last_name_task.py`,
`collect_phone_task.py`) are live in the authentication path, so gaps here affect real callers.

## Name
*(code: `src/tasks/collect_first_name_task.py`, `src/tasks/collect_last_name_task.py`)*

| # | What the caller does | What Zo does today | Existing test? | What's missing → what could go wrong | Priority | Action |
|---|----------------------|--------------------|:--------------:|--------------------------------------|:--------:|--------|
| 21 | Spells or says a first/last name that Zo should clean up and read back | Zo strips whitespace, rejects if empty, title-cases it, and completes (`collect_first_name_task.py:34-42`; last-name identical). | 🔴 Missing | The unit "tests" are **placebos** — `test_collect_name_tasks.py:38-64` only assert `"John".strip() == "John"`; they build the task fixture then throw it away (`_ = first_name_task`). They test Python's built-in `str.strip()`, **not `confirm_first_name`**. The normalization and empty-guard have *zero* real coverage — the tool could be fully broken and every test still passes green. | 🔴 High | `Add test` — actually invoke the task's tool. |
| 22 | Confirms a name like **"McDonald", "MacLeod", or "DeLuca"** | `name.title()` runs unconditionally (`collect_first_name_task.py:40`, `collect_last_name_task.py:41`). `.title()` turns "McDonald"→"Mcdonald", "DeLuca"→"Deluca". | 🟡 Needs improvement | Eval tests (`test_collect_name_flow.py:38,157`) cover simple/apostrophe/hyphen only — no Mc/Mac/De case. The mangled name is stored and later used as a **matching key** against the EHR record (`authenticate_patient_task.py:214,243`) → can miss the right patient and force a needless transfer. | 🟠 Medium | `Dev fix` — smarter casing or preserve caller casing. |
| 23 | STT emits junk as a name ("123", "uh", a 200-char run-on) | Only guard is empty-after-strip (`collect_first_name_task.py:35-37`); any non-empty string is title-cased and accepted. | 🔴 Missing | No character/length validation and no test for garbage input → bogus names silently stored and used as a match key. | 🟠 Medium | `Talk to dev` — decide what "obviously invalid" means, then `Add test`. |

## Phone
*(code: `src/tasks/collect_phone_task.py`)*

| # | What the caller does | What Zo does today | Existing test? | What's missing → what could go wrong | Priority | Action |
|---|----------------------|--------------------|:--------------:|--------------------------------------|:--------:|--------|
| 24 | Gives a 10-digit number that is length-valid but **not a real assignable US number** (area code `000`, `1234567890`) | Zo accepts on `phonenumbers.is_possible_number` (`collect_phone_task.py:81`), which only checks *length* — it never calls `is_valid_number`. | 🛠 Needs validation | `test_collect_phone_task.py:29-38` only checks length boundaries, cementing the weaker check. An impossible number formats cleanly to E.164, is stored as the contact, and is used in `search-patients` (`authenticate_patient_task.py:282-289`) → guaranteed no-match / unreachable callback. | 🟠 Medium | `Talk to dev` on `is_valid_number`, then `Add test`. |
| 25 | Speech contains **letters** ("call my cell, a-b-c…") that STT passes through | `phonenumbers.parse` maps letters to T9 digits, so "555-abc-4567" becomes `+15552224567` and is accepted (`collect_phone_task.py:78-85`). | 🟡 Needs improvement | `test_letters_convert_to_t9_digits` (`:40-43`) asserts this as *desired*, locking in a likely-wrong default. Garbled STT with stray letters silently becomes a plausible **wrong** number. | 🟢 Low | `Talk to dev` — is T9 conversion ever wanted here? |
| 26 | Number on file is malformed, hitting the read-back fallback | On `NumberParseException`, code slices `e164[-10:]` and formats it (`collect_phone_task.py:103-106`); for a short/garbage string this emits nonsense like `() -`. | 🔴 Missing | `TestFormatReadable` (`:56-57`) only tests two well-formed inputs; the except branch and the invalid-`caller_phone` constructor branch (`:26-32`) are untested → Zo could read back "Is your number ( ) -?". | 🟢 Low | `Add test` for the fallback/except paths. |
| 27 | Keeps giving **empty/inaudible names or invalid phone numbers** turn after turn | On bad input the tools just return a re-prompt string and never `complete()` (`collect_first_name_task.py:37,44`; `collect_phone_task.py:56,60`). There is **no attempt counter** forcing `transfer_to_representative`. | 🔴 Missing | No unit/eval test drives repeated invalid turns to prove the loop ever exits — termination depends entirely on the LLM eventually choosing to transfer. A caller stuck on bad STT can be re-prompted indefinitely → effectively a silent trap. | 🔴 High | `Initiative` — add a bounded max-retry → transfer, then `Add test`. |

---

# Batch 4 — "Who am I talking to?" — Identity & Authentication

Two very different auth mechanisms live in this codebase. **Cancellation** uses the real
`AuthenticatePatientTask` (searches the EHR, runs `PatientMatcher`). **Scheduling** uses a much
weaker `AuthenticationAgent` that verifies nothing. This batch covers both, plus the DOB parse.

## Auth — the schedule flow
*(code: `src/agents/authentication_agent.py`)*

| # | What the caller does | What Zo does today | Existing test? | What's missing → what could go wrong | Priority | Action |
|---|----------------------|--------------------|:--------------:|--------------------------------------|:--------:|--------|
| 28 | Reaches the scheduling flow and speaks **any** last name + **any** DOB (need not be a real patient) | `submit_authentication` just stores the caller-supplied `last_name`/`date_of_birth` and sets `authenticated=True` (`authentication_agent.py:47-49`). It never calls `search-patients` or `PatientMatcher`. | 🔴 Missing | The only reference is an eval asserting the tool is *called* (`test_shared_handoff_flow.py:57`) — nothing asserts identity is *verified*. Anyone can "authenticate" for scheduling with fabricated data → impersonation / booking under another patient's identity. Two divergent auth paths in one codebase is itself a latent bug. | 🔴 High | `Talk to dev` — is this intentional (scheduling doesn't need verified identity)? If not, wire it to the real task. |
| 29 | (Framework) A future agent sets `requires_authentication = True` but the dev forgets to call `_authenticate_patient()` | The flag exists at `base_agent.py:56` and `cancellation_agent.py:21` but is **never read in any conditional** — verified by grep. Auth only happens because `CancellationAgent.on_enter` manually calls it (`cancellation_agent.py:36`). | 🔴 Missing | `test_base_agent_auth.py` tests `_authenticate_patient()` directly but never that the flag *causes* auth. A protected agent could silently serve unauthenticated callers — the flag gives false safety. | 🔴 High | `Dev fix` — enforce the flag in the framework, or delete it. |

## Auth — the cancellation flow (real matcher)
*(code: `src/tasks/authenticate_patient_task.py`, `src/matching/patient_matcher.py`)*

| # | What the caller does | What Zo does today | Existing test? | What's missing → what could go wrong | Priority | Action |
|---|----------------------|--------------------|:--------------:|--------------------------------------|:--------:|--------|
| 30 | A **real** patient calls during a zo-tools outage / network blip | `_search_patients` catches every exception and returns `[]` (`authenticate_patient_task.py:305-308`), which flows to `ZERO_MATCHES` → `_transfer("No matching patient found")` (`:186-188`). No retry, no distinct "system error" reason. | 🔴 Missing | No test exercises the search call or its exception path (only `MatchStatus` outcomes are tested). Real patients are told "we couldn't find you" during infra blips, and metrics can't tell an outage apart from a genuine no-match. | 🔴 High | `Dev fix` — retry + distinct system-error transfer reason. |
| 31 | zo-tools returns a record **missing `patient_id`/`first_name`/`last_name`** (partial-data EHRs like Denticon) | The list comprehension uses hard-required keys `p["patient_id"]`… (`authenticate_patient_task.py:296-298`). A `KeyError` on **one** record is caught by the broad `except` at `:305`, discarding the **entire** result set → `[]` → transfer. (Other fields correctly use `.get()` — inconsistent.) | 🔴 Missing | No test feeds a malformed search response. One bad record blocks auth for a legitimate caller in a multi-patient response, with no diagnostic signal. | 🟠 Medium | `Dev fix` — `.get()` + skip-bad-record instead of dropping all. |
| 32 | The API returns a single candidate whose **phone/DOB doesn't actually equal** what the caller gave | `_complete_success` authenticates whenever exactly one candidate exists (`authenticate_patient_task.py:312-336`) — it never re-checks DOB/phone locally. `filter_by_phone` (`patient_matcher.py:41-45`) is the guard that would confirm this and is **never called** anywhere in `src/`. | 🟡 Needs improvement | `filter_by_phone` is thoroughly tested (`test_patient_matcher.py:51-89`) but the tested method is dead code; nothing asserts the task re-verifies the returned match. Wrong-patient auth = **PHI disclosure** if the API's matching is looser than assumed. | 🟠 Medium (🔴 if API match is fuzzy) | `Talk to dev` — add defense-in-depth re-verification. |
| 33 | Last name transcribed with an accent/hyphen variance ("Muñoz"→"Munoz", "O'Brien"→"OBrien") | `filter_by_first_name`/`filter_by_last_name` do `lower().strip()` **exact** equality only (`patient_matcher.py:56-57,68-69`, both carry `TODO: fuzzy/phonetic`). A mismatch → `ZERO_MATCHES` → transfer. | 🟡 Needs improvement | Tests cover only case/whitespace (`test_patient_matcher.py:95-153`), not accents/hyphens/phonetics. Legitimate patients repeatedly fail disambiguation → transfer; disproportionately affects non-Anglo names (equity concern). | 🟠 Medium | `Initiative` — fuzzy/phonetic matching. |
| 34 | Reaches auth in a session where `patient_id` was **already populated** (earlier agent / warm transfer / carried-over state) | `on_enter` returns cached success purely from `getattr(userdata, "patient_id", …)` (`authenticate_patient_task.py:118-133`) — no DOB/phone re-confirmation, no check the *current* caller matches the cached identity. | 🟡 Needs improvement | `test_on_enter_already_authenticated` (`test_authenticate_patient_task.py:28-71`) confirms the shortcut *works* but never tests it as a **trust boundary** (stale/foreign `patient_id`). Combined with the weak schedule auth (case 28), a weakly-set `patient_id` could be promoted to full cross-agent auth. | 🟠 Medium | `Talk to dev` — should cached identity ever skip re-verification? |

## DOB parse
*(code: `src/tasks/collect_dob_task.py`)*

| # | What the caller does | What Zo does today | Existing test? | What's missing → what could go wrong | Priority | Action |
|---|----------------------|--------------------|:--------------:|--------------------------------------|:--------:|--------|
| 35 | An older patient gives a **two-digit year** ("born in twenty-five" = 1925) | `_parse_and_validate` maps `year <= current_year % 100` → `2000+year` (`collect_dob_task.py:103-108`). In 2026, "25"→2025, which passes the ≤today range check → a 1925 DOB silently becomes 2025. | 🔴 Missing | The 1900 floor / today ceiling are checked, but the **pivot boundary itself** has no unit test for `_parse_and_validate`. Silent wrong-DOB for a subset of callers → unexplained `ZERO_MATCHES` and transfers; a regression here wouldn't be caught. | 🟢 Low | `Add test` for the two-digit-year pivot + today/1900 boundaries. |

---

# Batch 5 — Location → Provider → Timeslot → Insurance (the booking core)

**Scaffolding warning applies to this whole batch.** All four agents accept free-text and store
it without validation, backed by unused `SAMPLE_*` constants and prompts that hard-code fake
options. These are less "bugs" than "unfinished integration" — but they ship in the `schedule`
flow today, so a real caller hits them.

## Location & Provider
*(code: `src/agents/select_location_agent.py`, `src/agents/select_provider_agent.py`)*

| # | What the caller does | What Zo does today | Existing test? | What's missing → what could go wrong | Priority | Action |
|---|----------------------|--------------------|:--------------:|--------------------------------------|:--------:|--------|
| 36 | Reaches location or provider selection on a scheduling call | `submit_location`/`submit_provider` store whatever string the LLM passed and hand off (`select_location_agent.py:46-59`, `select_provider_agent.py:45-58`). | 🔴 Missing | **Neither agent has any test** — no unit, no eval, no import anywhere under `tests/`. Two core scheduling steps are entirely uncovered; any regression ships undetected. | 🔴 High | `Add test` (happy-path + error paths). |
| 37 | Names a provider who doesn't work here / is roster-excluded, or a location the practice doesn't have | Free-text is stored with **no lookup** — neither agent calls `get_practice_info`, `all_providers`, or `nearby_locations`; no existence check, no exclusion check, no ambiguity handling (`select_provider_agent.py:52-58`; `select_location_agent.py:53-59`). | 🔴 Missing | Provider-not-found, location-not-found, ambiguous multi-match, and excluded-provider cases are silently accepted and pushed downstream — exactly the VR/exclusion mismatch class that breaks scheduling. | 🔴 High | `Talk to dev` — confirm WIP; the real integration must validate + handle exclusions. |
| 38 | Asks "what locations/providers do you have?" | Prompts hard-code fake options — `select-location-agent.yaml:9` offers "Manhattan, Brooklyn, Queens, Staten Island"; `select-provider-agent.yaml:9` offers "Dr. Ava Patel, Dr. Jordan Lee, Dr. Morgan Kim". The `SAMPLE_LOCATIONS`/`SAMPLE_PROVIDERS` constants are dead code. | 🔴 Missing | Nothing asserts these lists are dynamic. Zo will offer NYC boroughs and three fictitious doctors to **every** practice regardless of reality → callers get fabricated options. | 🔴 High | `Dev fix` — replace with practice-driven data (or gate the flow off until wired). |

## Timeslot & Insurance
*(code: `src/agents/select_timeslot_agent.py`, `src/agents/select_insurance_agent.py`)*

| # | What the caller does | What Zo does today | Existing test? | What's missing → what could go wrong | Priority | Action |
|---|----------------------|--------------------|:--------------:|--------------------------------------|:--------:|--------|
| 39 | Asks for "Thursday 4 PM" — a slot already booked or nonexistent | `submit_timeslot` stores whatever string the LLM passes (`select_timeslot_agent.py:45-54`). `SAMPLE_TIMESLOTS` (`:12-16`) is hardcoded and unused; there is **no availability lookup**. | 🔴 Missing | No test calls `submit_timeslot`. Caller is promised an unavailable slot; two callers can both "get" the same slot → double-booking with no detection. | 🔴 High | `Talk to dev` — confirm WIP; real flow needs an availability check. |
| 40 | Says "I have Cigna" at a practice that doesn't accept Cigna | `submit_insurance` stores the raw string and hands off (`select_insurance_agent.py:45-54`). No comparison against accepted carriers, even though `PracticeInfo` exposes `allowed_networks`. `SAMPLE_INSURANCE_OPTIONS` is unused. | 🔴 Missing | No test invokes `submit_insurance`. Out-of-network caller proceeds to "confirmed" → surprise billing / denied claims. | 🔴 High | `Talk to dev` — confirm WIP; real flow needs a no-match / not-accepted path. |

## Base-agent behaviors these steps inherit
*(code: `src/agents/base_agent.py`)*

| # | What the caller does | What Zo does today | Existing test? | What's missing → what could go wrong | Priority | Action |
|---|----------------------|--------------------|:--------------:|--------------------------------------|:--------:|--------|
| 41 | Any step runs while **zo-tools is degraded** (5xx/timeout/auth error) when loading dynamic tools | `_load_dynamic_tools` catches non-404 as a `logger.warning` and the bare `Exception` at `base_agent.py:366-370`, then continues with **no metric** — unlike `handoff_to_agent`, which increments a metric on failure (`:217,242`). | 🟡 Needs improvement | `test_base_agent_dynamic_tools.py:107,129` confirm it doesn't crash but assert nothing about observability or degraded-mode behavior. If a real search/booking tool is delivered dynamically, an outage silently strips it — no alert, no fallback. | 🟠 Medium | `Dev fix` — emit a metric + degraded-mode handling; add tests. |
| 42 | A dynamic lookup tool **errors mid-call** (zo-tools 4xx with a body) | The executor's `except Exception` builds `f"Error executing {name}: {e!s}"` and returns it to the LLM (`base_agent.py:508-515`); for `ApiResponseError`, `str(e)` can surface the raw upstream `response_body` (`base.py:36-39,227-232`). | 🟡 Needs improvement | `test_tool_executor_error_handling` (`test_base_agent_dynamic_tools.py:200`) only checks the string contains "Error executing" — not that internal details/**PHI in a response body** are scrubbed. Error text (possibly PHI) can flow into the LLM prompt and be spoken to the caller. | 🟠 Medium | `Dev fix` — sanitize error text before returning to the LLM. |

---

# Batch 6 — Confirmation, Cancellation & the (missing) booking write

This is where the call should *commit* something. Today, on the schedule side it doesn't — and
on the cancellation side the actual cancel depends entirely on un-inspected dynamic tools.

## Confirmation
*(code: `src/agents/confirm_appointment_agent.py`)*

| # | What the caller does | What Zo does today | Existing test? | What's missing → what could go wrong | Priority | Action |
|---|----------------------|--------------------|:--------------:|--------------------------------------|:--------:|--------|
| 43 | Picks provider/insurance/timeslot and says "yes, book it" | `submit_confirmed` sets `booking_confirmed = True` and hands off (`confirm_appointment_agent.py:48-56`). It calls **no booking endpoint** — `zotools.py` exposes only `get_agent_schema`/`get_practice_info`; there is no availability/booking API anywhere in `src/`. The docstring "finalizes booking" (`:15`) is inaccurate. | 🔴 Missing | No test exercises `submit_confirmed` (only the boolean/routing is checked). Zo can tell the caller they're booked when **nothing was written** → phantom appointments, no-shows never on the schedule. | 🔴 High | `Talk to dev` — is booking intentionally stubbed/WIP? If so track as `Initiative`; the docstring should not claim it finalizes. |
| 44 | Corrects a detail mid-call ("actually, Dr. Lee"), then the LLM confirms early | `submit_confirmed` takes **no arguments** (`confirm_appointment_agent.py:48-52`) — it captures no snapshot of what the caller actually agreed to. | 🔴 Missing | No test for the confirm tool or a wrong-details branch. Wrong-details "confirmations" with no audit trail of caller assent. | 🟠 Medium | `Add test` + `Talk to dev` on capturing the confirmed values. |

## Cancellation
*(code: `src/agents/cancellation_agent.py`)*

| # | What the caller does | What Zo does today | Existing test? | What's missing → what could go wrong | Priority | Action |
|---|----------------------|--------------------|:--------------:|--------------------------------------|:--------:|--------|
| 45 | Verified caller says "cancel my appointment Friday" | `get_output_fields()` declares `["cancelled_appointment_id"]` (`cancellation_agent.py:32-33`), but the agent only authenticates and, on success, says "I can help cancel" (`:54-57`). **No static tool sets `cancelled_appointment_id`** — the actual cancel depends entirely on dynamic tools from zo-tools. If those aren't configured, nothing cancels and the DAG output is never produced. | 🟡 Needs improvement | `test_cancellation_agent.py` covers auth success/failure/transfer thoroughly but never an actual cancellation, nor that `cancelled_appointment_id` gets set. Caller is told cancellation is possible but nothing cancels; the flow can't complete. | 🔴 High | `Talk to dev` — confirm the dynamic-tool contract; `Add test` for the completed-cancel path. |
| 46 | Caller with **multiple** upcoming appointments says "cancel my appointment" | No logic to list appointments, pick one, or read back which one is being cancelled before acting (`cancellation_agent.py:35-57`); selection/execution is delegated blindly to un-inspected dynamic tools. | 🔴 Missing | No test for multi-appointment selection or a confirm-before-cancel step → risk of cancelling the **wrong** appointment with no read-back safeguard. | 🟠 Medium | `Talk to dev` — add disambiguation + confirm-before-cancel. |
| 47 | The (dynamic) cancel/book call **fails** (zo-tools 500 / timeout / EHR error) | `_create_tool_executor` catches every exception and returns `"Error executing {name}: {e}"` as a normal tool result (`base_agent.py:508-515`) — no retry, no metric, no user-facing failure path. | 🛠 Needs validation | `test_tool_executor_error_handling` only asserts the string contains "Error executing" — not that the caller is informed or that the flow doesn't proceed as if successful. A failed cancel/book is invisible; the LLM may hallucinate success. | 🔴 High | `Dev fix` — surface failures to the caller / transfer; add coverage. |
| 48 | Identity verification fails during cancellation, and the SIP transfer then **errors** | On auth failure the agent awaits `execute_sip_transfer` with **no try/except** (`cancellation_agent.py:47-51`); a raised transfer error propagates uncaught. | 🟡 Needs improvement | `test_cancellation_agent.py:122-156` asserts transfer is called and ordered after playout, but never that transfer raising is handled. Caller just told "I'll transfer you" is dropped on failure → dead-air hangup. | 🟢 Low | `Add test` + graceful fallback. |

---

# Batch 7 — The flow engine & transfer (cross-cutting)

These sit under every call. A bug here doesn't affect one step — it affects **every** call on a
goal. This is the most load-bearing code in the repo and several branches are untested.

## Flow controller / routing
*(code: `src/flow/flow_controller.py`)*

| # | What happens | What Zo does today | Existing test? | What's missing → what could go wrong | Priority | Action |
|---|--------------|--------------------|:--------------:|--------------------------------------|:--------:|--------|
| 49 | Mid-call, the caller asks to **jump to a downstream agent** whose only unmet dependency is a field no agent in the flow produces | `resolve_target_agent` builds `candidate_producers` only from missing fields that have an in-flow producer, then calls `min(candidate_producers, …)` (`flow_controller.py:129-136`). If all missing fields are external/unproduced, that list is empty and `min()` raises `ValueError: min() arg is an empty sequence`, propagating through `handoff_to_agent` (`base_agent.py:237-238`). | 🔴 Missing | `TestDependentRouting` (`test_flow_controller.py:398-441`) covers "deps met" and "unmet dep *with* an in-flow producer" — never "unmet dep with **no** producer". One ill-timed handoff → unhandled exception → dropped call. | 🔴 High | `Dev fix` — guard the empty case; `Add test`. |
| 50 | Caller completes a booking, then in the same call says "book **another** one" (or switches goals mid-flow) | `SessionState` fields persist for the whole call (`session_state.py:34-41`); `get_next_agent` treats any agent whose outputs are already non-`None` as complete (`flow_controller.py:170-189`), so a second attempt looks "complete" and returns straight to `GreetingAgent` (`:185-189`). **Nothing clears fields** on goal change or completion. | 🔴 Missing | `test_after_confirmation_flow_complete` (`test_flow_controller.py:217-234`) asserts completion returns Greeting but never a repeat/second run or goal switch with stale state. Caller wanting a second action is silently bounced to greeting; stale location/provider/insurance leak into the next goal. | 🔴 High | `Talk to dev` — define state-reset semantics on goal change/completion. |
| 51 | (Code/config error) An agent declares a **field name that doesn't exist** on `SessionState` | `_get_field` raises `ValueError("Field '…' does not exist…")` (`flow_controller.py:42-47`), invoked for every agent on every routing decision (`:49-57,107,118-122`) → a single bad field name breaks routing for **every** call on that goal at runtime. | 🔴 Missing | No test exercises the unknown-field path, and there's no startup/registration-time validation that field declarations exist on `SessionState`. A typo passes type-checking and takes down a whole goal — surfacing only at call time. | 🟠 Medium | `Add test` + startup validation of field declarations. |
| 52 | (Config error) Two agents in a goal declare the **same output field** | `_build_graph` overwrites `field_producer`, so the later agent silently wins (`flow_controller.py:64-71`); same silent overwrite in `resolve_target_agent` (`:113-116`). | 🔴 Missing | `TestBuildGraph` (`test_flow_controller.py:74-124`) covers normal/empty/external-dep cases but never a duplicate producer → a duplicate declaration silently reorders/skips a step, hard to diagnose. | 🟢 Low | `Add test`; consider erroring on duplicate producers. |
| 53 | Two agents each reroute to the other across turns (LLM ignores the "don't hand off" guidance) | Loop prevention is **prompt-only** — injected chat messages at `base_agent.py:182,261-268`. `max_tool_steps=1` (`agent.py:217`) only blocks chained calls *within* a turn, not cross-turn ping-pong. No counter/hard cap in `handoff_to_agent`/`get_next_agent`. | 🔴 Missing | `test_base_agent.py` covers single-hop routing and self-handoff "stay put" (`:214-227`) but never a multi-turn A↔B loop. An LLM ignoring advisory text → handoff loop with no code-side circuit breaker. | 🟠 Medium | `Talk to dev` — add a hard loop cap. |

## Transfer to a human
*(code: `src/utils/call_transfer.py`, `src/agents/greeting_agent.py`)*

| # | What the caller does | What Zo does today | Existing test? | What's missing → what could go wrong | Priority | Action |
|---|----------------------|--------------------|:--------------:|--------------------------------------|:--------:|--------|
| 54 | Asks for a non-automatable goal (pricing, reschedule, confirm, catch-all) at a practice with **no matching transfer number** (or SIP transfer fails) | `GreetingAgent.initialize_call_goal` awaits `transfer_to_human(...)` with **no try/except** (`greeting_agent.py:64-70`); if it raises `RuntimeError("…no transfer number…")` (`call_transfer.py:60-63`), the exception propagates **after** the caller already heard "Let me transfer you." | 🟡 Needs improvement | `test_greeting_agent.py:57-66` covers only `pricing` success; the `reschedule`/`confirm`→APPOINTMENT and `catch_all`→CATCH_ALL mappings (`greeting_agent.py:72-79`) and the transfer-raises branch are untested → silent hang-up; a wrong mapping would go unnoticed. | 🟠 Medium | `Add test` for each mapping + the transfer-failure branch. |
| 55 | The SIP participant has already **dropped** when Zo attempts the transfer | `execute_sip_transfer` loops for a SIP participant; if none found it silently `return`s in `"mock_room"`, else raises `RuntimeError` — **after** the hand-off line is spoken (`call_transfer.py:75-86,131-136`). | 🔴 Missing | Every `test_call_transfer.py` test injects a SIP participant (`:57-62`); neither the silent-return nor the raise branch is covered. In prod, a missing participant raises post-utterance → caller hears the line, then the call fails. | 🟠 Medium | `Add test` for both no-participant branches. |
| 56 | Needs a transfer at a practice with **more than one** number of the requested type | `transfer_to_human` logs `logger.error("pick_transfer_location task not implemented")` and unconditionally uses `transfer_numbers[0]` (`call_transfer.py:64-70`). | 🟡 Needs improvement | `test_call_transfer.py:121-174` confirms "uses first + warns" — codifying a known-incomplete stopgap. Caller can be routed to the wrong department/location with no signal. | 🟠 Medium | `Initiative` — implement transfer disambiguation. |

---

*Batches 3-7 of N — the rest of the schedule/cancel flow + cross-cutting engine. Generated
2026-07-23 against `main` @ `1c6de1c`. Headline: the mid-booking flow (location→provider→
timeslot→insurance→confirm) is scaffolding — free-text stores, fake prompt options, and a
no-op "confirm" — so its rows are mostly `Talk to dev` (confirm WIP). The real, shippable risks
are in the **live** paths: placebo name tests (21), unbounded collect loops (27), the
verification-free schedule auth (28), the never-enforced `requires_authentication` flag (29),
search-swallows-all-errors (30), and the flow-controller crash/stale-state/whole-goal-outage
branches (49-51).*

---

# Appendix A — Worked example: how to actually test case 36

Case 36 says the location/provider agents have **no tests**. Here's a concrete,
runnable example that closes the location half of that gap, so "Add test" isn't
abstract. It follows the existing pattern in `tests/test_greeting_agent.py`.

**What we're testing:** `SelectLocationAgent.submit_location` (`select_location_agent.py:46-59`).
The two things worth asserting are (1) the caller's answer gets **stored**, and
(2) the flow **hands off** to the next step. We patch `get_next_agent` so we test
*this* agent alone — the flow controller has its own tests.

Create `tests/agents/test_select_location_agent.py`:

```python
"""Tests for SelectLocationAgent (closes case 36 for the location step)."""

from unittest.mock import MagicMock

import pytest

from src.agents.select_location_agent import SelectLocationAgent
from src.flow import flow_registry
from src.session_state import SessionState


@pytest.fixture
def context():
    """A fake RunContext whose session.userdata is a real SessionState."""
    context = MagicMock()
    context.session.userdata = SessionState(
        call_goal="schedule",
        to_phone_number="+16468591511",
    )
    return context


# --- Metadata / wiring (cheap, no async) ---
def test_agent_name() -> None:
    agent = SelectLocationAgent.__new__(SelectLocationAgent)
    assert agent.agent_name == "select-location-agent"


def test_get_output_fields() -> None:
    assert SelectLocationAgent.get_output_fields() == ["location"]


def test_registered_for_schedule_goal() -> None:
    assert SelectLocationAgent in flow_registry.GOAL_AGENTS["schedule"]


# --- Behavior ---
@pytest.mark.asyncio
async def test_submit_location_stores_value_and_hands_off(context, mocker):
    """HAPPY PATH: the caller's location is saved and the flow advances."""
    agent = SelectLocationAgent()
    expected_next = MagicMock()
    mock_next = mocker.patch.object(agent, "get_next_agent", return_value=expected_next)

    result = await agent.submit_location(context, location="Brooklyn")

    assert context.session.userdata.location == "Brooklyn"   # (1) stored
    assert result is expected_next                            # (2) handed off
    mock_next.assert_called_once_with(context)


@pytest.mark.asyncio
async def test_submit_location_accepts_any_string_documents_missing_validation(
    context, mocker
):
    """DOCUMENTS THE GAP (case 37): no validation today — empty string is stored.

    When validation is added, THIS test will fail and force a conscious update.
    """
    agent = SelectLocationAgent()
    mocker.patch.object(agent, "get_next_agent", return_value=MagicMock())

    await agent.submit_location(context, location="")

    assert context.session.userdata.location == ""
```

**Run it** (from the repo root):

```bash
make test                                        # whole unit suite
# or just this file, matching CI:
uv run pytest tests/agents/test_select_location_agent.py -v
```

**How to read each test:**

| Test | What it proves | If it ever fails… |
|------|----------------|-------------------|
| `test_agent_name` / `test_get_output_fields` / `test_registered_for_schedule_goal` | The agent is wired into the `schedule` flow and declares the `location` output the DAG routes on. | Someone renamed the agent, its output field, or unregistered it — routing would silently break. |
| `test_submit_location_stores_value_and_hands_off` | The core job: the caller's answer is saved to `SessionState.location` and control moves on. | The submit logic or handoff broke — a real caller's location would be dropped. |
| `test_submit_location_accepts_any_string...` | Pins the *current* no-validation behavior on purpose. | Validation was added (good!) — update this test to expect a re-prompt instead. |

**The provider half:** copy this file to `tests/agents/test_select_provider_agent.py`,
swap `SelectLocationAgent`→`SelectProviderAgent`, `submit_location`→`submit_provider`,
`location`→`provider`, and `"select-location-agent"`→`"select-provider-agent"`.

> Note: these are **unit** tests (mocked, fast). They prove the plumbing works, not
> that the LLM picks the right location from speech — that belongs in an *evaluation*
> test under `tests/evaluations/` (see `test_collect_dob_flow.py` for the pattern).

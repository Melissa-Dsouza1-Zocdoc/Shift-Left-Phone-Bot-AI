# Zo Agent — Shift Left Phone Bot AI Coverage Report

> **Generated:** 2026-05-21
> **Source:** [Shift-Left-Phone-Bot-AI / Zo Agent (2026-05-07)](https://github.com/Melissa-Dsouza1-Zocdoc/Shift-Left-Phone-Bot-AI/tree/main/Zo%20Agent%20(2026-05-07))
> **Repo Under Test:** [Zocdoc/zo-agent](https://github.com/Zocdoc/zo-agent)

---

## Executive Summary

| Metric | Count |
|--------|------:|
| **Total existing tests** | **96** |
| Integration / Evaluation tests (LLM flow) | 35 |
| Unit tests (ZoTools client) | 61 |
| **Missing tests identified** | **~48** |
| Critical missing | 14 |
| High priority missing | 18 |
| Low / nice-to-have missing | 16 |
| **Potentially irrelevant tests** | **3** |

**Overall coverage posture:** The test suite has solid happy-path coverage for individual collection tasks and good structural/error coverage for the ZoTools client. However, there are significant gaps in **negative-path / edge-case evaluation tests**, **cross-cutting concerns** (PHI scrubbing, adversarial inputs, determinism), and **parity gaps** across task flows.

---

## 1. Per-Task Evaluation Coverage (Integration Tests)

These are LLM flow evaluations that simulate caller turns and assert agent behavior.

### 1.1 Collect DOB (`test_collect_dob_flow.py`) — 8 tests

| Status | Test | What It Covers |
|:------:|------|----------------|
| :white_check_mark: | `test_dob_format[parametrized]` | Various DOB phrasings parse correctly |
| :white_check_mark: | `test_single_correction` | Caller corrects DOB once |
| :white_check_mark: | `test_multiple_corrections` | Two corrections; final submit correct |
| :white_check_mark: | `test_smart_correction` | Partial correction (only year changes) |
| :white_check_mark: | `test_immediate_transfer_request` | "Talk to a person" mid-DOB |
| :white_check_mark: | `test_transfer_after_frustration` | Frustrated phrasing triggers transfer |
| :white_check_mark: | `test_dob_reconfirm_only_discusses_dob` | Reconfirm stays on topic |
| :white_check_mark: | `test_ai_disclosure_response` | "Are you AI?" mid-DOB |

**Missing (4):**

| Priority | Missing Test | Why It Matters |
|----------|-------------|----------------|
| :red_circle: Critical | Future DOB rejection ("March 15, 2099") | Invalid DOB could propagate through booking — no validation guard tested |
| :orange_circle: High | Year-only or month-only ASR fragments | Real callers give partial info; agent behavior undefined |
| :orange_circle: High | Two-digit vs four-digit year ambiguity ("85" → 1985 vs 2085) | Silent misparse creates incorrect patient records |
| :yellow_circle: Low | Cultural date ordering (DD/MM/YYYY vs MM/DD/YYYY) | Ambiguous for dates like 03/04/1990 |

### 1.2 Collect Name (`test_collect_name_flow.py`) — 7 tests

| Status | Test | What It Covers |
|:------:|------|----------------|
| :white_check_mark: | `test_first_name_format[parametrized]` | First-name phrasings parse correctly |
| :white_check_mark: | `test_first_name_correction` | Correct first name |
| :white_check_mark: | `test_first_name_transfer` | Transfer mid-first-name |
| :white_check_mark: | `test_last_name_format[parametrized]` | Last-name phrasings parse correctly |
| :white_check_mark: | `test_last_name_correction` | Correct last name |
| :white_check_mark: | `test_last_name_transfer` | Transfer mid-last-name |
| :white_check_mark: | `test_ai_disclosure_response` | AI disclosure |

**Missing (4):**

| Priority | Missing Test | Why It Matters |
|----------|-------------|----------------|
| :orange_circle: High | Spelling clarification ("Smith" vs "Smyth") | Name accuracy directly affects patient matching |
| :orange_circle: High | Hyphenated / multi-part names ("Mary-Jane Smith-Jones") | Common pattern; could split incorrectly |
| :yellow_circle: Low | Non-ASCII / international names | Accents, non-Latin scripts may be dropped by ASR |
| :yellow_circle: Low | Name with title/suffix ("Jr.", "Dr.") | Could be incorrectly included in name field |

### 1.3 Collect Gender (`test_collect_patient_gender_flow.py`) — 5 tests

| Status | Test | What It Covers |
|:------:|------|----------------|
| :white_check_mark: | `test_happy_path[parametrized]` | Gender phrasings map to canonical value |
| :white_check_mark: | `test_transfer_on_request` | Transfer on request |
| :white_check_mark: | `test_other_phrasing` | Non-binary phrasings handled |
| :white_check_mark: | `test_clarification_then_valid` | Vague answer → agent clarifies |
| :white_check_mark: | `test_unmappable_triggers_transfer` | Unmappable → transfer |

**Missing (3):**

| Priority | Missing Test | Why It Matters |
|----------|-------------|----------------|
| :orange_circle: High | "Prefer not to say" opt-out | Opt-out path undefined; could loop or error |
| :yellow_circle: Low | Gender for someone-else booking | "For my daughter" — only self-path tested |
| :yellow_circle: Low | Pronoun vs sex/gender distinction | Sensitive area; behavior should be explicit |

**Parity Gaps:** No AI disclosure test (DOB/Name/Phone have one). No correction test (DOB/Name/Phone have one).

### 1.4 Collect Phone (`test_collect_phone_flow.py`) — 9 tests

| Status | Test | What It Covers |
|:------:|------|----------------|
| :white_check_mark: | `test_phone_format[parametrized]` | Phone phrasings parse to E.164 |
| :white_check_mark: | `test_confirm_caller_phone` | Agent suggests caller's phone, user confirms |
| :white_check_mark: | `test_reject_caller_phone_provide_new` | Reject suggestion, give different phone |
| :white_check_mark: | `test_single_correction` | Phone correction |
| :white_check_mark: | `test_incomplete_phone_asks_for_full_number` | < 10 digits → re-asks |
| :white_check_mark: | `test_incomplete_phone_only_area_code` | Area code only → re-prompt |
| :white_check_mark: | `test_immediate_transfer_request` | Transfer mid-phone |
| :white_check_mark: | `test_no_phone_transfer` | "I don't have a phone" → transfer |
| :white_check_mark: | `test_ai_disclosure_response` | AI disclosure |

**Missing (4):**

| Priority | Missing Test | Why It Matters |
|----------|-------------|----------------|
| :orange_circle: High | International phone numbers (+44, +1 prefix) | Non-US callers will hit this; E.164 parsing may fail |
| :yellow_circle: Low | Extension numbers ("ext 567") | Business lines commonly have extensions |
| :yellow_circle: Low | VoIP / Google Voice numbers | Format passes but business rules may differ |
| :yellow_circle: Low | Caller rejects phone then provides invalid format | Only happy rejection path tested |

### 1.5 CSAT (`test_csat_flow.py`) — 7 tests

| Status | Test | What It Covers |
|:------:|------|----------------|
| :white_check_mark: | `test_full_flow` | Full happy path: consent → score → feedback → close |
| :white_check_mark: | `test_high_score_with_feedback` | Score 5 with positive feedback |
| :white_check_mark: | `test_decline_consent` | Caller declines → close politely |
| :white_check_mark: | `test_polite_decline` | Polite decline still respected |
| :white_check_mark: | `test_no_feedback` | Score given, no free text → submit |
| :white_check_mark: | `test_vague_then_valid_score` | "Good" → clarify → valid number |
| :white_check_mark: | `test_transfer_at_gate` | Transfer at consent gate |

**Missing (5):**

| Priority | Missing Test | Why It Matters |
|----------|-------------|----------------|
| :red_circle: Critical | PHI in free-text feedback | Caller may state PHI in feedback; no scrubbing test — **compliance risk** |
| :orange_circle: High | Score 0 / negative score | Only positive scores tested; behavior with out-of-range values unknown |
| :orange_circle: High | Score above 5 ("10 out of 10") | LLM may accept and submit invalid score |
| :yellow_circle: Low | Profane / abusive feedback | Should still be captured + scrubbed |
| :yellow_circle: Low | Consent timeout / silence | Caller silent at gate; no test |

**Parity Gaps:** No AI disclosure test (DOB/Name/Phone have one).

---

## 2. Cross-Cutting Evaluation Coverage (Integration Tests)

### 2.1 Malformed Tool Retry (`test_malformed_tool_retry_flow.py`) — 1 test

| Status | Test | What It Covers |
|:------:|------|----------------|
| :white_check_mark: | `test_malformed_tool_error_is_rewritten_and_retried` | Arg-name typo recovery |

**Missing (4):**

| Priority | Missing Test | Why It Matters |
|----------|-------------|----------------|
| :red_circle: Critical | Repeated malformed calls → transfer | Agent prompt says "fail again → transfer" but path untested |
| :orange_circle: High | Hallucinated tool name | Agent calls non-existent tool; no recovery test |
| :orange_circle: High | Missing required arg retry | Only typo'd arg tested; missing arg path untested |
| :yellow_circle: Low | Type mismatch retry (str vs int) | Wrong-type arg recovery |

### 2.2 New or Existing Patient Routing (`test_new_or_existing_flow.py`) — 3 tests

| Status | Test | What It Covers |
|:------:|------|----------------|
| :white_check_mark: | `test_new_patient` | New patient → new-appointment flow |
| :white_check_mark: | `test_existing_patient` | Existing patient → auth flow |
| :white_check_mark: | `test_transfer_to_human` | Transfer at gate |

**Missing (2):**

| Priority | Missing Test | Why It Matters |
|----------|-------------|----------------|
| :orange_circle: High | "Not sure if I'm a patient" | Caller unsure; agent should clarify, not guess |
| :yellow_circle: Low | Returning patient with new insurance | Workflow boundary not covered |

### 2.3 Reschedule Date Resolution (`test_reschedule_date_resolution.py`) — 15 tests

This is the most thoroughly tested area with excellent coverage of date NLU.

| Status | Count | Examples |
|:------:|------:|---------|
| :white_check_mark: | 15 | Day-name, "next X", "this X", ranges, month, "soonest", "between A and B", etc. |

**Missing (5):**

| Priority | Missing Test | Why It Matters |
|----------|-------------|----------------|
| :red_circle: Critical | Past-date attempts ("March 5" when already past) | Silent acceptance creates impossible bookings |
| :orange_circle: High | Ambiguous "next Friday" (when today is Friday) | Behavior undefined |
| :yellow_circle: Low | Holiday handling ("before Christmas") | Nice-to-have |
| :yellow_circle: Low | Timezone-relative anchoring | Caller in different TZ |
| :yellow_circle: Low | Multi-language date phrasing | Spanish/Portuguese |

### 2.4 Shared Handoff (`test_shared_handoff_flow.py`) — 3 tests

| Status | Test | What It Covers |
|:------:|------|----------------|
| :white_check_mark: | `test_jump_ahead_is_blocked_with_explanation` | Forward-jump blocked |
| :white_check_mark: | `test_jump_back_to_redo_location` | Back-jump works |
| :white_check_mark: | `test_switch_goal_to_cancel_routes_to_cancellation` | Goal switch mid-booking |

**Missing (3):**

| Priority | Missing Test | Why It Matters |
|----------|-------------|----------------|
| :orange_circle: High | Triple-switch goal (book → cancel → book again) | State management under stress |
| :orange_circle: High | Switch goal mid-tool-call | Race condition with in-flight tool |
| :yellow_circle: Low | Jump-back without prior selection | Edge case; behavior unclear |

### 2.5 Shared Time Awareness (`test_shared_time_awareness.py`) — 1 test

| Status | Test | What It Covers |
|:------:|------|----------------|
| :white_check_mark: | `test_greeting_agent_knows_todays_date` | Agent knows today's date |

**Missing (4):**

| Priority | Missing Test | Why It Matters |
|----------|-------------|----------------|
| :orange_circle: High | Timezone awareness (practice TZ vs caller TZ) | Appointment scheduling across timezones |
| :yellow_circle: Low | Time-of-day awareness | Morning/afternoon context |
| :yellow_circle: Low | Date rollover at midnight | Boundary test |
| :yellow_circle: Low | Stale "today" (long-running session crossing midnight) | Edge case |

### 2.6 Zotools Schema Validation (`test_zotools_schema.py`) — 3 tests

| Status | Test | What It Covers |
|:------:|------|----------------|
| :white_check_mark: | `test_practice_info_schema` | Schema snapshot |
| :white_check_mark: | `test_initialize_call_schema` | Schema snapshot |
| :white_check_mark: | `test_agent_tool_schema` | Schema snapshot |

**Missing (2):**

| Priority | Missing Test | Why It Matters |
|----------|-------------|----------------|
| :orange_circle: High | Schema version pinning / backward-compat assertion | No guard against breaking schema changes |
| :yellow_circle: Low | Required vs optional field invariants | Only structural validity tested |

### 2.7 Example Flows — 3 tests

| Status | Test | What It Covers |
|:------:|------|----------------|
| :white_check_mark: | `test_schedule_flow_routing` | Example routing |
| :white_check_mark: | `test_greeting_flow_individual_turns` | Example individual mode |
| :white_check_mark: | `test_greeting_flow_full_conversation` | Example cumulative mode |

---

## 3. Unit Test Coverage — ZoTools Client

### 3.1 ZoTools Client Core (`test_zotools_client.py`) — 31 tests

| Test Group | Tests | Coverage |
|------------|------:|---------|
| `TestZoToolsClient` (init, schema) | 6 | Init, schema retrieval, auth error, 404, timer metric |
| `TestZoToolsClientGetAppointments` | 8 | Happy path, empty list, endpoint, optional fields, location type, blocked reason, logging, timer |
| `TestZoToolsClientSearchPatients` | 1 | Retry disabled on search |
| `TestValidateAvailability` | 6 | Valid, invalid, optional fields, endpoint, 404 |
| `TestPostCallCompleted` | 2 | Correct path/payload, error propagation |
| `TestMakeToolRequest` | 4 | Success, 400 JSON, 400 plain text, 500 re-raise |
| `TestIsFlagOn` | 2 | Correct endpoint, timer metric |
| `TestGetVariant` | 2 | Correct endpoint, timer metric |

**Missing (4):**

| Priority | Missing Test | Why It Matters |
|----------|-------------|----------------|
| :red_circle: Critical | PHI in error responses | Error payloads from zo-tools may include patient data — **compliance risk** |
| :orange_circle: High | Auth failure (401/403) specific exception | Unauthorized response not distinctly asserted |
| :orange_circle: High | Retry on 5xx | Retry policy and attempt budget unverified |
| :orange_circle: High | Client timeout | Slow zo-tools — clean cancellation unverified |

### 3.2 Initialize Call (`test_zotools_initialize_call.py`) — 17 tests

| Test Group | Tests | Coverage |
|------------|------:|---------|
| `TestInitializeCallSuccess` | 10 | Data parsing, endpoint, phone whitespace, timer, auto-transfer, fillers, multilingual STT |
| `TestInitializeCallValidation` | 3 | Empty/whitespace/None phone |
| `TestInitializeCallErrors` | 4 | Auth, 404, timeout, invalid schema |

**Well-covered area.** No critical gaps.

### 3.3 Practice Info (`test_zotools_practice_info.py`) — 13 tests

| Test Group | Tests | Coverage |
|------------|------:|---------|
| `TestGetPracticeInfoSuccess` | 5 | Success, whitespace strip, providers, transfer numbers, timer |
| `TestGetPracticeInfoValidation` | 3 | Empty/whitespace/None phone |
| `TestGetPracticeInfoErrors` | 5 | Auth, 404, timeout, 500, invalid schema |

**Well-covered area.** No critical gaps.

---

## 4. Potentially Irrelevant Tests

| Test | File | Why Potentially Irrelevant |
|------|------|---------------------------|
| `test_schedule_flow_routing` | `examples/test_flow_controller.py` | Example/reference test — not tracking production parity. Risk of rot without CI gate. |
| `test_greeting_flow_individual_turns` | `examples/test_greeting_flow.py` | Same as above — example, not production coverage |
| `test_greeting_flow_full_conversation` | `examples/test_greeting_flow.py` | Same as above — example, not production coverage |

**Recommendation:** These 3 example tests are not irrelevant per se (they serve as documentation), but they should either be **pinned to a CI gate that fails on drift** from production agents, or explicitly marked as `@pytest.mark.example` so they don't inflate coverage numbers.

---

## 5. Cross-Cutting Gaps (Systemic Issues)

These gaps affect **multiple test files** and represent the highest-impact improvements.

| # | Gap | Severity | Affected Areas | Recommendation |
|---|-----|----------|---------------|----------------|
| 1 | **PHI scrubbing untested** | :red_circle: Critical | All collection tasks, CSAT feedback, ZoTools error responses | Every task captures PHI; none assert log/transcript scrubbing. Add PHI-in-transcript tests for DOB, Name, Phone, CSAT feedback, and ZoTools error payloads. |
| 2 | **AI disclosure parity** | :orange_circle: High | Gender, CSAT | DOB, Name, Phone all have `test_ai_disclosure_response`. Gender and CSAT do not. Add for parity. |
| 3 | **Correction path parity** | :orange_circle: High | Gender | DOB, Name, Phone all have correction tests. Gender lacks one. |
| 4 | **Adversarial inputs** | :orange_circle: High | All flows | No prompt-injection tests ("ignore previous instructions"), profanity escalation, or multi-language mid-flow switching tested anywhere. |
| 5 | **Determinism for parametrized tests** | :orange_circle: High | All `test_X_format[parametrized]` | Without seeded LLM, these are flake sources. Verify frozen-time fixtures exist and LLM temperature is pinned. |
| 6 | **Negative-path assertions** | :orange_circle: High | All collection tasks | "Do NOT call `submit_X` if user says X" never asserted. Only positive tool calls are checked. |
| 7 | **Schema drift detection** | :yellow_circle: Low | `test_zotools_schema.py` | Covers 3 of N tools; full inventory parity not asserted. |

---

## 6. Priority Action Plan

### Tier 1 — Critical (Ship Blockers)

| # | Action | Est. Tests |
|---|--------|----------:|
| 1 | Add PHI scrubbing assertions across all collection tasks + CSAT feedback + ZoTools error responses | 6 |
| 2 | Add future DOB rejection test (`test_collect_dob_flow.py`) | 1 |
| 3 | Add past-date rejection test (`test_reschedule_date_resolution.py`) | 1 |
| 4 | Add repeated-malformed-tool → transfer test (`test_malformed_tool_retry_flow.py`) | 1 |
| 5 | Add CSAT out-of-range score tests (0, negative, >5) | 2 |
| 6 | Add ZoTools client PHI-in-error-response test | 1 |
| **Total** | | **12** |

### Tier 2 — High Priority (Next Sprint)

| # | Action | Est. Tests |
|---|--------|----------:|
| 7 | Add AI disclosure tests for Gender and CSAT flows | 2 |
| 8 | Add correction test for Gender flow | 1 |
| 9 | Add "not sure if I'm a patient" routing test | 1 |
| 10 | Add name spelling clarification test | 1 |
| 11 | Add hyphenated/multi-part name test | 1 |
| 12 | Add international phone number test | 1 |
| 13 | Add "prefer not to say" gender opt-out test | 1 |
| 14 | Add two-digit year ambiguity test | 1 |
| 15 | Add hallucinated tool name recovery test | 1 |
| 16 | Add missing required arg retry test | 1 |
| 17 | Add triple-switch goal test | 1 |
| 18 | Add mid-tool-call goal switch test | 1 |
| 19 | Add timezone awareness test | 1 |
| 20 | Add ambiguous "next Friday" date resolution test | 1 |
| 21 | Add adversarial prompt injection test (any one flow) | 1 |
| 22 | Add negative-path assertion (submit_X NOT called) | 1 |
| 23 | Add ZoTools client retry-on-5xx test | 1 |
| 24 | Add ZoTools client timeout test | 1 |
| **Total** | | **18** |

### Tier 3 — Nice to Have (Backlog)

| # | Action | Est. Tests |
|---|--------|----------:|
| 25 | Cultural date ordering (DD/MM vs MM/DD) | 1 |
| 26 | Non-ASCII / international names | 1 |
| 27 | Name with title/suffix | 1 |
| 28 | Phone extension numbers | 1 |
| 29 | VoIP / Google Voice numbers | 1 |
| 30 | Invalid phone after rejection | 1 |
| 31 | Gender for someone-else booking | 1 |
| 32 | Pronoun vs sex/gender distinction | 1 |
| 33 | Profane / abusive CSAT feedback | 1 |
| 34 | Consent timeout / silence | 1 |
| 35 | Holiday handling in date resolution | 1 |
| 36 | Multi-language date phrasing | 1 |
| 37 | Timezone-relative anchoring | 1 |
| 38 | Date rollover at midnight | 1 |
| 39 | Stale "today" in long sessions | 1 |
| 40 | Schema version pinning | 1 |
| **Total** | | **16** |

---

## 7. Coverage Heatmap

```
                        Happy Path  Corrections  Transfer  AI Disc.  Negative  PHI Scrub  Adversarial
Collect DOB               ██████      ██████      ██████    ██████     ░░░░░░    ░░░░░░      ░░░░░░
Collect Name              ██████      ██████      ██████    ██████     ░░░░░░    ░░░░░░      ░░░░░░
Collect Gender            ██████      ░░░░░░      ██████    ░░░░░░     ░░░░░░    ░░░░░░      ░░░░░░
Collect Phone             ██████      ██████      ██████    ██████     ░░░░░░    ░░░░░░      ░░░░░░
CSAT                      ██████      N/A         ██████    ░░░░░░     ░░░░░░    ░░░░░░      ░░░░░░
Date Resolution           ██████      N/A         N/A       N/A        ░░░░░░    N/A         ░░░░░░
Patient Routing           ██████      N/A         ██████    N/A        ░░░░░░    N/A         ░░░░░░
Handoff                   ██████      N/A         N/A       N/A        ░░░░░░    N/A         ░░░░░░
Tool Retry                ██████      N/A         N/A       N/A        ░░░░░░    N/A         ░░░░░░
ZoTools Client            ██████      N/A         N/A       N/A        ██████    ░░░░░░      N/A

██████ = Covered    ░░░░░░ = Gap    N/A = Not Applicable
```

---

## 8. Summary

**What's working well:**
- Happy-path coverage is strong across all collection tasks
- Transfer paths are consistently tested everywhere
- ZoTools client has solid structural/error coverage (61 unit tests)
- Date resolution NLU is the most thoroughly tested area (15 tests)

**What needs immediate attention:**
- PHI scrubbing is completely untested — this is a compliance risk
- AI disclosure and correction tests are missing from Gender and CSAT flows
- No adversarial/negative-path testing exists anywhere
- Critical edge cases (future DOB, past dates, out-of-range CSAT scores) are unguarded

**Bottom line:** The suite is ~65% complete for production readiness. The 12 Tier 1 tests should be prioritized immediately as they address compliance (PHI) and data integrity risks. The 18 Tier 2 tests round out the behavioral coverage for realistic caller interactions.

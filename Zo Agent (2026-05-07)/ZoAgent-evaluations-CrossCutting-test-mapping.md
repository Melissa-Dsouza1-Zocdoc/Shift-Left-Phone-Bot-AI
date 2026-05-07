# Cross-Cutting - Evaluation Test Mapping

> Repo: [Zocdoc/zo-agent](https://github.com/Zocdoc/zo-agent)
> Folder: `tests/evaluations/`
> Generated: 2026-05-07
> Stack: Python / pytest / LiveKit eval framework
>
> These are LLM flow evaluations that simulate user turns and assert agent behavior. Treat per-row "Steps" as the conversation script + expected agent actions.

Covers cross-agent concerns: malformed tool retry, new-vs-existing patient routing, reschedule date resolution (NLU), shared handoff and time awareness, zotools schema validation, and example flows.

## test_malformed_tool_retry_flow.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | test_malformed_tool_error_is_rewritten_and_retried | Framework rewrites malformed tool calls and retries; agent recovers without transferring | Agent intentionally calls `submit_payload` with `requiredvalue` (typo); expect: framework returns "Malformed tool call" error containing the correct arg name, agent self-corrects with `required_value="recovered"`, `transfer_to_human` is NOT awaited. | Self-heal on bad args | In scope: arg-name typo recovery. Out of scope: missing tool, schema-level validation, type errors. |

### Suggested Missing Tests
- **Type mismatch retry** — wrong type (str vs int) on a tool arg; not asserted.
- **Missing required arg retry** — only typo'd arg name; missing arg path not tested.
- **Repeated malformed calls -> transfer** — agent prompt says "fail again -> transfer" but no test forces second-failure path.
- **Hallucinated tool name** — agent calls a tool that doesn't exist; no test for that recovery.

## test_new_or_existing_flow.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | test_new_patient | "I'm a new patient" -> route to new-patient flow | User declares new; expect routing to new-appointment agent. | New routing | In scope: routing. Out of scope: scheduling. |
| 2 | | test_existing_patient | "I'm an existing patient" -> route to auth | User declares existing; expect routing to auth. | Existing routing | In scope: routing. Out of scope: auth. |
| 3 | | test_transfer_to_human | "Talk to person" at gate -> transfer | Transfer ask. | Transfer | In scope: transfer. Out of scope: department. |

### Suggested Missing Tests
- **"Not sure if I'm a patient"** — caller unsure; agent should clarify, not pick. Not tested.
- **Returning patient with new insurance** — workflow boundary not covered.

## test_reschedule_date_resolution.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | test_how_about_monday | "How about Monday" resolves to upcoming Monday | Parses to next Monday date. | Day-name | In scope: day-of-week. Out of scope: ambiguous. |
| 2 | | test_next_wednesday | "Next Wednesday" resolves correctly | Next-week Wednesday parsed. | Day-name with modifier | In scope: "next X". Out of scope: "this X". |
| 3 | | test_this_friday | "This Friday" resolves to current week | "This X". | Day-name "this" | In scope: "this X". Out of scope: weekend boundary. |
| 4 | | test_next_week | "Next week" -> Mon-Sun next week range | Range resolution. | Range "next week" | In scope: range. Out of scope: weekend. |
| 5 | | test_after_specific_date | "After May 5" -> open-ended range starting May 6 | Open range parsed. | Open-ended | In scope: after. Out of scope: before. |
| 6 | | test_sometime_in_may | "Sometime in May" -> month range | Month range parsed. | Month | In scope: month. Out of scope: year. |
| 7 | | test_next_two_weeks | "Next 2 weeks" -> 14-day range | Numeric range. | Numeric range | In scope: weeks. Out of scope: months. |
| 8 | | test_week_of_specific_date | "Week of May 5" -> that ISO week | ISO-week parsed. | Week-of | In scope: week-of. Out of scope: weekend. |
| 9 | | test_this_week | "This week" -> current week range | Current-week. | Range "this week" | In scope: range. Out of scope: timezones. |
| 10 | | test_specific_date_april_15 | "April 15" -> specific date | Single date. | Specific | In scope: specific. Out of scope: year disambig. |
| 11 | | test_same_day_but_earlier | Earlier-time within already-confirmed day | Refines time within same date. | Time refinement | In scope: time only. Out of scope: TZ. |
| 12 | | test_end_of_the_month | "End of the month" -> last week of current month | Range parsed. | End-of-month | In scope: range. Out of scope: business days. |
| 13 | | test_between_two_dates | "Between A and B" -> bounded range | Bounded range. | Bounded | In scope: between. Out of scope: open-ended. |
| 14 | | test_first_week_of_may | "First week of May" -> first ISO week | ISO week. | First-week | In scope: first. Out of scope: business days. |
| 15 | | test_soonest_available | "Soonest" -> open-ended starting today | Open-ended from today. | Open-ended | In scope: soonest. Out of scope: end. |

### Suggested Missing Tests
- **Past-date attempts** — "March 5" when March 5 is in the past.
- **Holiday handling** — "before Christmas" / "after Thanksgiving" not asserted.
- **Timezone-relative anchoring** — caller in different TZ; not covered.
- **Multi-language date phrasing** — Spanish/Portuguese dates not covered.
- **Ambiguous "next Friday"** — when today is Friday, "next Friday" is ambiguous; behavior not asserted.

## test_shared_handoff_flow.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | test_jump_ahead_is_blocked_with_explanation | Caller tries to skip a required step; agent blocks + explains | Caller jumps to "set time" before location; agent explains and re-prompts. | Forward jump block | In scope: forward block. Out of scope: backward jump. |
| 2 | | test_jump_back_to_redo_location | Caller asks to change earlier step; agent jumps back | After provider chosen, caller asks to change location; routes back. | Backward jump | In scope: back-jump. Out of scope: state revert. |
| 3 | | test_switch_goal_to_cancel_routes_to_cancellation | Mid-booking, caller switches to cancel -> routes correctly | Booking flow + caller says "actually cancel my appt"; routes to cancellation. | Goal switch | In scope: goal switch. Out of scope: state preservation. |

### Suggested Missing Tests
- **Triple-switch goal** — book -> cancel -> book again; not tested.
- **Switch goal mid-tool-call** — race condition with in-flight tool not asserted.
- **Jump-back without prior selection** — back-jump to nothing; behavior unclear.

## test_shared_time_awareness.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | test_greeting_agent_knows_todays_date | Agent has correct "today" in context | Caller asks "what's today's date"; agent answers. | Today-awareness | In scope: today. Out of scope: time-of-day. |

### Suggested Missing Tests
- **Time-of-day awareness** — does agent know it's morning/afternoon? Not tested.
- **Timezone awareness** — practice TZ vs caller TZ not asserted.
- **Date rollover at midnight** — boundary test missing.
- **Stale "today"** — long-running session crossing midnight not covered.

## test_zotools_schema.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | test_practice_info_schema | practice_info tool's JSON schema is valid + stable | Load schema; assert structure. | Schema snapshot | In scope: schema. Out of scope: behavior. |
| 2 | | test_initialize_call_schema | initialize_call schema valid + stable | Load schema; assert. | Schema snapshot | In scope: schema. Out of scope: behavior. |
| 3 | | test_agent_tool_schema | Generic agent tool schema valid | Load schema; assert. | Schema snapshot | In scope: schema. Out of scope: behavior. |

### Suggested Missing Tests
- **Schema version pinning** — no test asserting backward-compatible schema changes.
- **Required vs optional field invariants** — only structural validity tested.

## examples/test_flow_controller.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | test_schedule_flow_routing | Example flow controller routes to scheduling | Sample multi-turn evaluation routing book intent. | Example | In scope: example. Out of scope: production parity. |

### Suggested Missing Tests
- **None** — these are reference examples, not coverage.

### Improvement Suggestions / Irrelevant Tests
- **Examples may rot** — drift between examples and production agents not enforced. Consider CI gate.

## examples/test_greeting_flow.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | test_greeting_flow_individual_turns | Example: individual mode for greeting flow | Sample turns run independently. | Example individual | In scope: example. Out of scope: production. |
| 2 | | test_greeting_flow_full_conversation | Example: cumulative mode end-to-end | Sample multi-turn cumulative. | Example cumulative | In scope: example. Out of scope: production. |

## Cross-cutting Gaps for Cross-Cutting Files

- **Adversarial inputs untested across the board** — prompt injection ("ignore previous instructions"), profanity escalation, multi-language mid-flow switching.
- **No latency budget** — `_handle_tool_error_auto_transfer` runs as a background task with no timeout assertion.
- **Determinism** — date-resolution tests rely on "today" stable; if frozen-time fixture is missing, tests will silently change behavior over time. (Note: `test_reschedule_date_resolution.py` has an `autouse` fixture at line 35 — verify it freezes time.)
- **PHI in transcripts** — `examples/` may use synthetic but plausible PHI; no scrubbing assertion.
- **Schema drift detection** — `test_zotools_schema.py` covers 3 of N tools; full inventory parity not asserted.

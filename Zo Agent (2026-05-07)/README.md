# ZO Agent - Test Mapping Index

> Test mapping for the [Zocdoc/zo-agent](https://github.com/Zocdoc/zo-agent) repository (the LiveKit voice-agent backend — Python / pytest, calls into the `zo-tools` server for domain operations).
> Generated: 2026-05-07
> Format follows the [provider-calendar test mapping](https://github.com/Mervis-Mascarenhas-Zocdoc/Shift-Left-Appointment-Management/blob/main/2026-05-06/provider-calendar-test-mapping.md) template.

## How to read these files

Each mapping file contains, per spec file (`test_*.py`):

- A numbered table of every test function with `Test Name`, `What It Tests`, `Steps`, `Summary`, `Scope`
- A **Suggested Missing Tests** section listing concrete tests the team should consider adding
- An **Improvement Suggestions / Irrelevant Tests** section flagging redundancy, weak assertions, or tests worth deleting

The `Do we need these?` column is intentionally left blank — it is a triage column for the team to fill during review.

## Files

### Root unit tests (`tests/test_*.py`)
| Theme | File | Notes |
|-------|------|-------|
| Base agent | [ZoAgent-tests-BaseAgent-test-mapping.md](ZoAgent-tests-BaseAgent-test-mapping.md) | base_agent + banter_limit, blocked_events, dynamic_tools, language, llm_node, tool_events (~110 tests) |
| Root agent files | [ZoAgent-tests-RootAgents-test-mapping.md](ZoAgent-tests-RootAgents-test-mapping.md) | test_*_agent.py at the repo root: agent, auto_transfer, booking_confirm, greeting, manage_appointment, unified_manage_appointment (~141 tests) |
| Root task files | [ZoAgent-tests-RootTasks-test-mapping.md](ZoAgent-tests-RootTasks-test-mapping.md) | test_*_task.py at the repo root: collect_dob/name/phone/gender, confirm_full_name, new_or_existing, csat, resolve_booking_issue (~77 tests) |
| Call lifecycle | [ZoAgent-tests-CallLifecycle-test-mapping.md](ZoAgent-tests-CallLifecycle-test-mapping.md) | initialize_call, call_completed, csat, transfer, call summary, audio (~128 tests) |
| Helpers (A) | [ZoAgent-tests-HelpersA-test-mapping.md](ZoAgent-tests-HelpersA-test-mapping.md) | Time, sip, prompt loader, variation loader, schema, shared_prompts (~split, ~60KB) |
| Helpers (B) | [ZoAgent-tests-HelpersB-test-mapping.md](ZoAgent-tests-HelpersB-test-mapping.md) | preflight, sentry, session_state, synthetic_tool_items, user_input |
| Misc - Infra | [ZoAgent-tests-MiscA-Infra-test-mapping.md](ZoAgent-tests-MiscA-Infra-test-mapping.md) | Datadog metrics, ab service, feature flags, sentry, audit, conftest |
| Misc - Flow | [ZoAgent-tests-MiscB-Flow-test-mapping.md](ZoAgent-tests-MiscB-Flow-test-mapping.md) | Booking validation, select_agents, process_tool_response, turn metrics |
| Misc - Patient/Tools | [ZoAgent-tests-MiscC-PatientAndTools-test-mapping.md](ZoAgent-tests-MiscC-PatientAndTools-test-mapping.md) | Patient info, practice_info, tool_endpoint_execution, tool_schema |
| Zotools client | [ZoAgent-tests-ZotoolsClient-test-mapping.md](ZoAgent-tests-ZotoolsClient-test-mapping.md) | zotools_client, zotools_initialize_call, zotools_practice_info (~61 tests) |

### Subfolder unit tests (`tests/{subfolder}/`)
| Folder | File | Notes |
|--------|------|-------|
| `tests/agents/` | [ZoAgent-tests-subfolder-agents-test-mapping.md](ZoAgent-tests-subfolder-agents-test-mapping.md) | authenticate_patient, manage_appointment, select_location/provider/visit_reason, collect_patient_gender_tool, unified_new_appointment (~70 tests) |
| `tests/tasks/` | [ZoAgent-tests-subfolder-tasks-test-mapping.md](ZoAgent-tests-subfolder-tasks-test-mapping.md) | authenticate_patient, collect_dob, collect_name, collect_phone, confirm_full_name, new_or_existing (~70 tests) |
| `tests/matching/` | [ZoAgent-tests-matching-test-mapping.md](ZoAgent-tests-matching-test-mapping.md) | patient_matcher (~7 tests) |
| `tests/observability/` | [ZoAgent-tests-observability-test-mapping.md](ZoAgent-tests-observability-test-mapping.md) | call_cost_tracker, cost, event_bus, events, utils (~31 tests) |

### LLM evaluations (`tests/evaluations/`)
| Theme | File | Notes |
|-------|------|-------|
| Framework | [ZoAgent-evaluations-Framework-test-mapping.md](ZoAgent-evaluations-Framework-test-mapping.md) | runner, state-manager, turn, expectations, expectation-validation, fixtures (~39 tests + 4 fixture refs) |
| Per-agent flows | [ZoAgent-evaluations-PerAgent-test-mapping.md](ZoAgent-evaluations-PerAgent-test-mapping.md) | auth, authenticate-patient, greeting, greeting-stability, manage-appointment, resolve-booking-issue, select-location/provider/visit-reason (~125 flows) |
| Per-task flows | [ZoAgent-evaluations-PerTask-test-mapping.md](ZoAgent-evaluations-PerTask-test-mapping.md) | collect_dob, collect_name, collect_gender, collect_phone, csat (~36 flows) |
| Unified flows | [ZoAgent-evaluations-Unified-test-mapping.md](ZoAgent-evaluations-Unified-test-mapping.md) | 15 unified_* files for the unified-new-appointment agent (~70 flows) |
| Cross-cutting | [ZoAgent-evaluations-CrossCutting-test-mapping.md](ZoAgent-evaluations-CrossCutting-test-mapping.md) | malformed_tool_retry, new_or_existing, reschedule_date_resolution, shared_handoff, shared_time_awareness, zotools_schema, examples/ (~26 flows) |

### Benchmarks (`tests/benchmarks/`)
| File | Notes |
|------|-------|
| [ZoAgent-benchmarks-test-mapping.md](ZoAgent-benchmarks-test-mapping.md) | thinking_off / thinking_on parametrized runs, 5 conversation scenarios x 5 reps, real Vertex Gemini-2.5-flash (2 runs) |

## Cross-cutting findings

The following gaps surfaced repeatedly across mapping files and were independently flagged by multiple themes. Treat them as systemic rather than per-spec.

1. **PHI redaction is unverified across the entire stack.** Three of four mapping themes flagged this independently:
   - Root tests: `test_collect_*_task.py` only assert `allow_interruptions` flags — nothing verifies captured PHI (DOB, name, phone) is redacted before logging, metrics, Sentry, or call-completed summaries.
   - Observability subfolder: `to_dict()` and `LLMRequestRecord` PHI scrubbing is not enforced.
   - Evaluations: fixtures contain real-shaped DOBs (`"November 3rd, 1982"`), names (`"Dr. Patel"`), and phones (`"+15551234567"`) that flow into transcripts and assertion error messages with no scrubbing test on persisted snapshots.

   This is a Zocdoc PHI policy concern, not just a coverage gap.

2. **Empty / setup-only test files in load-bearing areas.** The following exist as `test_*.py` but contain zero `test_` functions despite covering critical seams: `test_select_agents.py`, `test_ab_service.py`, `test_booking_validation.py`, `test_zotools_initialize_call.py`, `test_zotools_practice_info.py`, `test_tool_endpoint_execution.py`, `test_turn_metrics_accumulator.py`, `test_datadog_metrics_consumer.py`, `test_resolve_booking_issue_task.py`, `test_synthetic_tool_items.py`, `test_base_agent_language.py`, `test_agent_dynamic_tools_integration.py`. Coverage tooling will count these as "covered" while the seams (agent routing, feature flags, booking validation, call init, metrics) have zero behavior assertions.

3. **Voice-pipeline failure modes untested.** Across base agent, task, and call-lifecycle suites: barge-in, silence timeout, ASR misrecognition, LLM truncation / empty response, tool-call mid-cancellation, and SIP transfer retry on 480/486 are all absent. Tests are heavily happy-path with `MagicMock` / `AsyncMock` standing in for real streaming/cancellation behavior.

4. **No determinism / seeding controls in evaluations.** `eval_llm` has no documented seed or temperature clamp; `state_variations.py` even self-annotates a "known to cause flakiness" entry. The `_no_thinking` fixture variant suggests known flake-reduction is happening ad hoc. Without a framework-level seed test, all parametrized format tests (DOB, name, phone) and stability tests are silent flake sources.

5. **Adversarial inputs absent across all 49 evaluation files.** No prompt injection ("ignore previous instructions"), role manipulation, mid-flow language switching beyond Spanish-greeting cases, or profanity escalation tests. Greeting has good frustration coverage but unified flows have none.

6. **Tautological assertions instead of behavior verification.** Subfolder `tasks/test_collect_name_tasks.py` asserts `str.strip()` outcomes rather than driving the task and asserting tool calls / state transitions. Cosmetic input handling is verified; the task's actual behavior contract is not.

7. **Async cancellation paths universally untested.** None of the agent, task, or call-lifecycle async tests assert that an inbound cancellation propagates to in-flight LLM, tool, or SIP operations.

8. **Benchmarks are unbounded.** `tests/benchmarks/` runs against the real Vertex Gemini-2.5-flash endpoint with no regression thresholds (only error logs), no DataDog / CI tracking, no p99, and warm-up that covers only one agent. As written, this is a load test that emits a local markdown file — not a guardrail against latency regressions.

9. **Reschedule parity gap.** New-appointment flows have dedicated evaluation files for auth, gender, insurance, name-spelling, timeslot, and visit-reason. The reschedule path only covers timeslot, directives, and hybrid. A reschedule audit would likely surface coverage holes for insurance / gender / name-change-on-reschedule.

10. **Tool-registration completeness for dynamic zo-tools surfaces is not asserted.** Subfolder agent flagged that `agents/` tests don't verify that all tools registered via the dynamic-tools mechanism are reachable, schema-valid, and exposed to the LLM in the right state.

## Output location note

These files were generated under sandbox restrictions in `/tmp/ZO Agent/`. To move them into the Shift-Left repo, run from outside the sandbox:

```bash
cp -R "/tmp/ZO Agent" ~/Downloads/Shift-Left-Appointment-Management/
```

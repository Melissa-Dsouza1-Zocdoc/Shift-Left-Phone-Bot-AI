# Framework - Evaluation Test Mapping

> Repo: [Zocdoc/zo-agent](https://github.com/Zocdoc/zo-agent)
> Folder: `tests/evaluations/`
> Generated: 2026-05-07
> Stack: Python / pytest / LiveKit eval framework
>
> These are LLM flow evaluations that simulate user turns and assert agent behavior. Treat per-row "Steps" as the conversation script + expected agent actions.

This document covers the eval framework's own self-tests plus the shared fixtures. These files validate that the runner, expectation matchers, snapshotting, and turn data structures behave correctly. They are NOT flow tests against the production agent.

## framework.py (framework / fixture file)

Provides `run_evaluation_with_variations()` — a helper that re-runs a list of `EvaluationTurn` objects across a list of state variation dicts, building a fresh agent class per variation. Used by `test_greeting_stability.py` to detect brittleness when only practice name changes.

## fixtures/helpers.py (framework / fixture file)

Single helper `make_location()` builds a `Location` schema with sensible defaults (NY, 123 Main St). Used to seed mock practice configs.

## fixtures/state_variations.py (framework / fixture file)

Defines `PRACTICE_NAME_VARIATIONS` (short, standard, standard_longer, medium, long) plus `get_greeting_agent_variations()`. Notably the "standard_longer" entry is annotated as "known to cause flakiness" — flakiness in production prompts is being tracked here.

## fixtures/test_agents.py (framework / fixture file)

Large (~1620 lines) factory module containing per-agent test subclasses with `with_X()` builder helpers: `_AuthAgent`, `_DOBCollectionAgent`, `_PhoneCollectionAgent`, `_PhoneCollectionAgentWithCallerPhone`, `_FirstNameCollectionAgent`, `_LastNameCollectionAgent`, `_GenderCollectionAgent`, `_ManageAppointmentAgent`, `_SelectVisitReasonAgent`, `_SelectLocationAgent`, `_SelectProviderAgent`, `_SelectInsuranceAgent`, `_GreetingAgent`, `_UnifiedRescheduleAgent`, `_UnifiedNewAppointmentAgent`, `_UnifiedNewAppointmentAuthAgent`, `_CSATAgent`. These wrap production agents with mock state injection (mock_patients, caller_phone, blocked_reason, single/multi locations, etc.).

## test_runner.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | test_turn_result_creation_success | TurnResult dataclass with passed=True | Construct TurnResult(turn_name="test", passed=True) and assert fields. | TurnResult ctor happy path | In scope: TurnResult dataclass. Out of scope: actual eval execution. |
| 2 | | test_turn_result_creation_failure | TurnResult dataclass with errors list | Construct TurnResult with passed=False and errors list. | TurnResult error propagation | In scope: error field. Out of scope: error sourcing. |
| 3 | | test_turn_result_with_final_state | TurnResult preserves final_state snapshot | Construct TurnResult with a final_state dict. | TurnResult holds session state | In scope: final_state field. Out of scope: state correctness. |
| 4 | | test_evaluation_has_agent_class | MultiTurnEvaluation requires an agent_class | Instantiate SimpleFlow and check agent_class attr. | Evaluation defines agent | In scope: class attribute discovery. Out of scope: agent boot. |
| 5 | | test_evaluation_has_turns | MultiTurnEvaluation must declare turns | Read turns ClassVar and verify list. | Evaluation defines turns | In scope: ClassVar discovery. Out of scope: turn execution. |
| 6 | | test_evaluation_llm_instance_optional | llm_instance is optional | Confirm absence of llm_instance does not error. | LLM optional on ctor | In scope: optional attribute. Out of scope: judge LLM behavior. |
| 7 | | test_run_individual_mode_single_turn | Individual mode runs a single turn against fresh state | Run SimpleFlow turns one-at-a-time, check Message expect satisfied. | Per-turn isolation | In scope: individual mode. Out of scope: cumulative carryover. |
| 8 | | test_run_individual_mode_forces_initial_state | Individual mode applies each turn's initial_state | Run MultiTurnFlow's 2nd turn directly with patient_id=123 seed; assert state applied. | initial_state injection | In scope: state forcing. Out of scope: state semantics. |
| 9 | | test_run_individual_mode_continues_on_failure | Individual mode does NOT abort on a failed turn | Force one turn to fail (mock); confirm subsequent turns still run. | Failure tolerance individual | In scope: continuation policy. Out of scope: error reporting format. |
| 10 | | test_run_cumulative_mode_single_session | Cumulative mode reuses one session across turns | Run SimpleFlow in cumulative mode, assert single session lifetime. | Session continuity | In scope: cumulative mode. Out of scope: deep-state inspection. |
| 11 | | test_run_cumulative_mode_stops_on_failure | Cumulative aborts on first failure (default) | Mock turn 1 fail; assert turn 2 not executed. | Default stop-on-fail | In scope: default behavior. Out of scope: skip reasoning. |
| 12 | | test_run_cumulative_mode_continues_on_failure | continue_on_failure=True overrides default | Pass continue_on_failure flag and assert all turns run. | Override flag honored | In scope: opt-in continuation. Out of scope: result aggregation. |
| 13 | | test_run_cumulative_mode_captures_final_state | Cumulative captures last session_state | Inspect TurnResult.final_state on last turn. | Final state retained | In scope: state capture. Out of scope: state schema. |

### Suggested Missing Tests
- **Determinism / seeding** — no test verifies the LLM is seeded or temperature-clamped; cumulative mode flakes are likely.
- **Timeout handling** — no test for what happens when an LLM call exceeds a budget.
- **Concurrent run isolation** — no test that two MultiTurnEvaluations running in parallel don't share state.
- **Logging / PHI scrubbing** — no test that transcripts/logs are scrubbed of PHI before persistence.

### Improvement Suggestions / Irrelevant Tests
- **Mock-heavy unit feel** — these are pure unit tests of dataclasses; consider relabeling with `@pytest.mark.unit` to separate from `@pytest.mark.evaluation` flow tests.

## test_state_manager.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | test_snapshot_capture | Snapshot copies chat_context items + session_state | Build context, snapshot, mutate; assert snapshot unchanged. | Snapshot is deep | In scope: capture. Out of scope: serialization. |
| 2 | | test_snapshot_restore | Restore replays the snapshot back into session | Restore snapshot, compare to original. | Restore round-trip | In scope: restore. Out of scope: restore mid-turn. |
| 3 | | test_snapshot_isolation | Mutating restored object doesn't leak into snapshot | Restore, then mutate restored copy; original snapshot intact. | Isolation guarantee | In scope: isolation. Out of scope: thread safety. |
| 4 | | test_snapshot_deep_copy_items | Chat items are deep copied (not shared refs) | Mutate restored item; original unchanged. | Deep copy semantics | In scope: deep copy. Out of scope: large-payload perf. |
| 5 | | test_apply_state_dict | apply_state_dict sets all fields | Apply full dict; verify session_state. | Full apply | In scope: apply path. Out of scope: validation. |
| 6 | | test_apply_state_dict_partial | Partial dict only sets named fields | Apply 1 field; others unchanged. | Partial apply | In scope: selective apply. Out of scope: schema. |
| 7 | | test_apply_state_dict_invalid_field | Invalid field raises | Apply non-existent field; assert error. | Strict apply | In scope: error path. Out of scope: error formatting. |
| 8 | | test_apply_state_dict_empty | Empty dict is a no-op | Apply {}; state unchanged. | No-op apply | In scope: empty path. Out of scope: ordering. |
| 9 | | test_get_state_dict | get_state_dict returns all set fields | Set fields, read dict, verify. | Round-trip dict | In scope: getter. Out of scope: serialization. |
| 10 | | test_get_state_dict_excludes_none | None-valued fields are excluded | Verify omitted None keys. | None pruning | In scope: getter prune. Out of scope: explicit-None preservation. |
| 11 | | test_get_state_dict_all_none | All-None state returns empty dict | Confirm {}. | Empty dict | In scope: edge case. Out of scope: schema. |
| 12 | | test_snapshot_with_empty_context | Snapshot of empty context succeeds | Capture empty; restore empty. | Empty snapshot | In scope: empty path. Out of scope: large context. |
| 13 | | test_snapshot_restore_with_empty_snapshot | Restoring empty snapshot clears state | Restore {}; verify cleared. | Empty restore | In scope: clear path. Out of scope: partial clear. |

### Suggested Missing Tests
- **Snapshot of in-flight tool calls** — does a snapshot mid tool call replay correctly?
- **Cross-agent snapshot transfer** — when agent_class changes, does state restore still work?

## test_turn.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | test_turn_creation | EvaluationTurn ctor with name+input+expects | Construct and validate. | Happy ctor | In scope: ctor. Out of scope: execution. |
| 2 | | test_turn_requires_name | Missing name raises | Construct without name; assert error. | Required field | In scope: validation. Out of scope: messages. |
| 3 | | test_turn_requires_user_input | Missing user_input raises | Construct without input; assert error. | Required field | In scope: validation. Out of scope: messages. |
| 4 | | test_turn_default_values | Defaults for optional fields | Construct minimal turn; verify defaults. | Defaults | In scope: defaults. Out of scope: typing. |
| 5 | | test_turn_with_description | description field round-trips | Set description; verify. | Description supported | In scope: field. Out of scope: usage. |
| 6 | | test_turn_with_multiple_expectations | expects supports list | Pass 2+ expects; verify list intact. | Multiple expects | In scope: list. Out of scope: validation order. |

## test_expectations.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | test_tool_call_creation | ToolCall ctor with name+args | Construct ToolCall; verify. | Ctor happy path | In scope: ctor. Out of scope: validation. |
| 2 | | test_tool_call_without_args | ToolCall args optional | Construct without args. | Optional args | In scope: optional. Out of scope: matching. |
| 3 | | test_tool_call_with_any | ANY sentinel acts as wildcard | Construct ToolCall with ANY; verify. | Wildcard sentinel | In scope: ANY support. Out of scope: matching semantics. |
| 4 | | test_message_creation | Message expectation ctor | Construct Message(role, contains). | Ctor | In scope: ctor. Out of scope: matching. |
| 5 | | test_message_default_role | Default role is assistant | Construct Message; verify role. | Default role | In scope: default. Out of scope: validation. |
| 6 | | test_message_with_judge | judge_intent field accepted | Construct with judge string. | LLM-judge support | In scope: field. Out of scope: judging. |
| 7 | | test_function_call_output_creation | FunctionCallOutput expectation | Construct; verify. | Tool result expect | In scope: ctor. Out of scope: matching. |
| 8 | | test_agent_handoff_creation_with_string | Handoff to named agent (string) | Construct AgentHandoff("X"). | String form | In scope: ctor. Out of scope: matching. |
| 9 | | test_agent_handoff_creation_with_type | Handoff to agent class | Construct AgentHandoff(SomeAgent). | Type form | In scope: ctor. Out of scope: matching. |
| 10 | | test_state_change_creation | StateChange expectation | Construct StateChange(field=val). | Ctor | In scope: ctor. Out of scope: matching. |
| 11 | | test_any_sentinel_repr | ANY repr is readable | Verify repr/str. | Debug helper | In scope: repr. Out of scope: behavior. |

## test_expectation_validation.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | test_validate_tool_call_name_only | ToolCall matches by name only | Set up FC mock, validate. | Name match | In scope: name. Out of scope: args. |
| 2 | | test_validate_tool_call_with_exact_args | ToolCall matches with exact args dict | Validate against matching args. | Exact arg match | In scope: exact match. Out of scope: ANY. |
| 3 | | test_validate_tool_call_with_any_wildcard | ANY wildcard accepts any value | Use ANY in expected args; pass. | Wildcard arg | In scope: ANY. Out of scope: schema. |
| 4 | | test_validate_tool_call_args_mismatch | Mismatched args fails validation | Provide different args; assert fail. | Negative path | In scope: arg diff. Out of scope: error format. |
| 5 | | test_validate_tool_call_missing_arg | Missing required arg fails | Drop a key; assert fail. | Missing arg | In scope: missing. Out of scope: extra arg. |
| 6 | | test_validate_message_role_only | Message matches by role | Validate assistant msg. | Role match | In scope: role. Out of scope: text. |
| 7 | | test_validate_message_with_contains | contains substring match | Check substring within message text. | Contains | In scope: substring. Out of scope: regex. |
| 8 | | test_validate_message_contains_not_found | Missing substring fails | Provide text without substring; fail. | Negative substring | In scope: failure path. Out of scope: judge fallback. |
| 9 | | test_validate_message_with_judge | LLM judge invoked when judge_intent set | Use mock LLM, verify judge called. | Judge wired | In scope: judge call. Out of scope: judge accuracy. |
| 10 | | test_validate_message_judge_without_llm_fails | judge_intent without llm raises | Pass judge_intent but no LLM. | Config error | In scope: validation. Out of scope: messages. |
| 11 | | test_validate_handoff_with_type | AgentHandoff matches by class | Validate against class arg. | Type match | In scope: type form. Out of scope: instances. |
| 12 | | test_validate_handoff_with_string_match | AgentHandoff matches by class name string | String form. | String match | In scope: string. Out of scope: case-insensitive. |
| 13 | | test_validate_handoff_string_mismatch | Wrong handoff string fails | Mismatch string. | Negative path | In scope: failure. Out of scope: messages. |
| 14 | | test_validate_state_change_success | StateChange satisfied when field updated | Verify state diff matches. | State assert | In scope: positive. Out of scope: nested. |
| 15 | | test_validate_state_change_mismatch | StateChange fails on wrong value | Wrong value. | Negative path | In scope: failure. Out of scope: nested fields. |
| 16 | | test_validate_state_change_multiple_fields | Multiple state field expects | Validate two fields at once. | Multi-field | In scope: AND-logic. Out of scope: OR-logic. |
| 17 | | test_validate_state_change_without_injection_fails | StateChange without injected state errors | Run without injection; fail. | Wiring guard | In scope: misconfig. Out of scope: messages. |

### Suggested Missing Tests
- **Negative ToolCall** — assert agent did NOT call a tool; no test for "must not call X" semantics.
- **Order assertions** — no test that ToolCall A precedes ToolCall B in a single turn.
- **Latency / timing** — no expectation type for response-time budgets.
- **PHI redaction in failure messages** — assertion errors may include user input; no scrubbing test.

## test_fixtures.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | test_eval_llm_fixture | eval_llm pytest fixture wires up | Request fixture, verify non-None. | Fixture available | In scope: fixture. Out of scope: model name. |
| 2 | | test_enable_eval_logging_fixture | enable_eval_logging fixture toggles logging | Use fixture, verify logger config. | Logging fixture | In scope: fixture. Out of scope: log content. |
| 3 | | test_eval_logging_disabled_by_default | Without fixture, eval logging is off | Inspect default state. | Default off | In scope: default. Out of scope: handlers. |

### Suggested Missing Tests
- **PHI scrubbing in eval logs** — if eval logging is on, transcripts may contain DOB/phone/name; no test verifies redaction.

## Cross-cutting Framework Gaps

- **No determinism guarantees** — no fixture or test ensures the eval LLM is seeded or temperature-clamped; flakes will silently degrade signal.
- **No latency / cost budgets** — every flow runs unbounded calls; CI cost is unmonitored.
- **No PHI scrubbing test** — patient names, DOB, phone numbers appear in fixtures and would land in logs/snapshots.
- **No "must not" expectation** — only positive expectations are tested; negative-path coverage is limited to args mismatch.
- **No schema validation of EvaluationTurn at collection time** — invalid turns surface only at runtime.

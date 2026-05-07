# Agents - Unit Test Mapping

> Repo: [Zocdoc/zo-agent](https://github.com/Zocdoc/zo-agent)
> Folder: `tests/agents/`
> Generated: 2026-05-07
> Stack: Python / pytest / LiveKit Agents

## test_authenticate_patient_agent.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | test_agent_name | Agent metadata `agent_name` matches the prompt slug. | Instantiate `AuthenticatePatientAgent` -> assert `agent_name == "authenticate-patient-agent"`. | Static metadata smoke test. | In scope: name property. Out of scope: prompt content. |
| 2 | | test_get_dependencies | Agent declares `call_goal` as its only dependency. | Call `AuthenticatePatientAgent.get_dependencies()` -> assert `== ["call_goal"]`. | Static dependency contract. | In scope: dependency list. Out of scope: dependency resolution. |
| 3 | | test_get_output_fields | Agent advertises `authenticated` as its output field. | Call `get_output_fields()` -> assert `== ["authenticated"]`. | Static output contract. | In scope: output schema. Out of scope: write semantics. |
| 4 | | TestAuthenticatePatient::test_success_stores_fields_in_session | Successful auth populates session userdata (patient_id, names, DOB, phone, locations, appointments). | Patch session + `AuthenticatePatientTask` returning success + `_fetch_appointments`; await `_authenticate_patient()`; assert userdata fields and appointments stored. | Happy-path session hydration. | In scope: side effects on session. Out of scope: AuthenticatePatientTask internals. |
| 5 | | TestAuthenticatePatient::test_appointment_fetch_phone_selection | `_fetch_appointments` uses authenticated phone when set, falls back to caller phone. | Parametrized over (auth_phone, caller_phone, expected); mock fetch; assert `mock_fetch.call_args.kwargs["caller_phone"] == expected`. | Phone-selection branch coverage. | In scope: which phone is forwarded. Out of scope: zo-tools API behavior. |
| 6 | | TestAuthenticatePatient::test_transfers_when_appointments_fail | Auth success but `_fetch_appointments` returns None -> result downgraded to transfer. | Patch task to return success and fetch to return None; await; assert `success=False`, `transfer_requested=True`, reason mentions appointment retrieval. | Failure-on-fetch downgrade. | In scope: result mutation. Out of scope: actual transfer execution. |
| 7 | | TestAuthenticatePatient::test_failure_does_not_store_patient_id | Auth failure leaves session.userdata.patient_id `None`. | Patch task to return failure; await; assert `userdata.patient_id is None` and reason preserved. | Negative-path session safety. | In scope: no session hydration on fail. Out of scope: transfer flow. |
| 8 | | TestAuthenticatePatient::test_raises_when_no_call_id | Missing `call_id` raises `RuntimeError`. | Make session with `call_id=None`; expect `RuntimeError("call_id not set")`. | Defensive precondition. | In scope: precondition guard. Out of scope: where call_id is set. |
| 9 | | TestAuthenticatePatient::test_already_authenticated_returns_cached_result | If `patient_id` is already on session, task is not called; cached result returned. | Pre-populate `userdata.patient_id`; assert mock task not called and result reflects existing patient. | Idempotency / cache shortcut. | In scope: short-circuit. Out of scope: cache invalidation. |
| 10 | | TestAuthenticatePatient::test_task_exception_propagates | Exceptions from `AuthenticatePatientTask` propagate. | Patch task to raise `RuntimeError`; await and assert pytest.raises. | Error propagation. | In scope: exception passthrough. Out of scope: retry logic (none). |
| 11 | | TestOnEnter::test_skips_auth_when_already_authenticated | `on_enter` skips auth and just generates a reply when already authenticated. | Pre-set `userdata.authenticated=True`; patch lifecycle deps; assert `_authenticate_patient` not called and `generate_reply` called once. | Lifecycle short-circuit. | In scope: on_enter branch. Out of scope: reply text. |
| 12 | | TestOnEnter::test_runs_auth_when_not_authenticated | `on_enter` runs auth when not yet authenticated. | Patch lifecycle with success result; await `on_enter`; assert `_authenticate_patient` called once. | Lifecycle dispatch. | In scope: branch selection. Out of scope: post-auth handoff. |
| 13 | | TestOnEnter::test_sets_authenticated_on_success | After successful auth, `userdata.authenticated` is True. | Run `on_enter` with success; assert `userdata.authenticated is True`. | Auth flag persistence. | In scope: flag write. Out of scope: handoff. |
| 14 | | TestOnEnter::test_transfers_on_auth_failure | Auth failure triggers transfer with default failure utterance. | Patch task with failure; assert `_execute_transfer` called with CATCH_ALL + AUTH_FAILURE reason and "can't be certain" utterance. | Transfer wiring on failure. | In scope: transfer args. Out of scope: actual SIP transfer. |
| 15 | | TestOnEnter::test_user_requested_transfer_uses_friendly_utterance | When caller asks for human, friendly utterance used (not auth-failure framing). | Failure result with `transfer_category=USER_REQUESTED`; assert `_execute_transfer` called with `AUTH_TRANSFER_USER_REQUESTED` and `USER_REQUESTED` reason. | Transfer-category UX. | In scope: utterance + reason switch. Out of scope: how user request is detected. |
| 16 | | TestProceedToNextAgent::test_calls_get_next_agent | `proceed_to_next_agent` delegates to `get_next_agent`. | Patch `get_next_agent` to return mock; call `proceed_to_next_agent(context)`; assert delegation and return value. | Routing delegation. | In scope: handoff plumbing. Out of scope: routing logic. |

### Suggested Missing Tests
- **Concurrent on_enter invocation** - assert no double-auth if `on_enter` triggered twice (e.g., reentrant lifecycle).
- **DOB-only failure category** - distinct utterance/reason path when failure is DOB mismatch vs. no match.
- **Language switching during auth** - verify utterance localization when `userdata.language` changes mid-call.
- **Async cancellation** - ensure `_authenticate_patient` cleanly handles `asyncio.CancelledError` (caller hangup).
- **`_fetch_appointments` partial failure** - some patient IDs fail; tests only cover all-or-nothing.

### Improvement Suggestions / Irrelevant Tests
- **Heavy patching** - `_patch_session` + `_patch_on_enter_lifecycle` patch four+ collaborators per test; consider a `Harness` fixture to reduce setup duplication and surface coupling.
- **Deterministic LLM mocking** - none of the tests verify that the underlying LLM is mocked deterministically; document or share mocks via conftest to prevent flake when an integration adapter is used.
- **`test_task_exception_propagates`** is generic; consider parametrizing with the actual exception types raised by `AuthenticatePatientTask` to lock down contract.

## test_collect_patient_gender_tool.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | TestCollectPatientGenderGuard::test_returns_error_when_no_patient_id | Tool errors out cleanly when `new_patient_id` is unset. | Make agent + session without patient_id; patch session property; call wrapped tool; assert JSON `status=="error"` and message references `insert_patient`. | Pre-call guard. | In scope: guard branch. Out of scope: insert_patient flow. |
| 2 | | TestCollectPatientGenderSuccess::test_returns_collected_gender | Successful collection returns JSON with `status=="collected"` and gender. | Set `new_patient_id`; patch task to return `GenderResult(success=True, gender="Female")`; call tool; parse JSON and assert fields. | Happy path. | In scope: tool output contract. Out of scope: prompt logic. |
| 3 | | TestCollectPatientGenderSuccess::test_includes_gender_in_routing_instructions | Routing instructions string echoes the collected gender. | Patch task with `gender="Male"`; call tool; assert `"Male" in routing_instructions`. | Downstream routing hint. | In scope: routing string. Out of scope: routing execution. |
| 4 | | TestCollectPatientGenderTransfer::test_executes_transfer_on_task_transfer_request | Task signaling transfer triggers `_execute_transfer` with appointment number + USER_REQUESTED reason. | Patch task with `transfer_requested=True`; spy on `_execute_transfer`; call tool; assert called with expected args and JSON `status=="transfer"`. | Transfer fan-out. | In scope: transfer args. Out of scope: SIP transfer. |
| 5 | | TestCollectPatientGenderTransfer::test_executes_transfer_when_gender_is_none | Task returning `success=True, gender=None` triggers system-error transfer. | Patch task accordingly; assert transfer with `CANNOT_DETERMINE_GENDER` reason and system-error utterance. | Fallback transfer. | In scope: edge case handling. Out of scope: task fix. |

### Suggested Missing Tests
- **Re-entry after transfer** - if tool invoked again post-transfer, ensure it short-circuits or no-ops.
- **Language switching mid-collection** - verify gender prompt locale follows session language change.
- **Tool-call retry after LLM hallucination** - call with malformed kwargs (e.g., `is_booking_for_self="maybe"`).

### Improvement Suggestions / Irrelevant Tests
- **`__wrapped__` access** - tests bypass the LiveKit `@function_tool` decorator. Add at least one test that exercises the registration path so the tool is verified as discoverable by the agent.
- **`_FakeGenderTask` boilerplate** - move to a shared test fixture with the matching `_FakeConfirmTask` from `test_unified_new_appointment_agent.py`.

## test_manage_appointment_agent.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | test_agent_exists | Class importable and not None. | Assert `ManageAppointmentAgent is not None`. | Import smoke. | In scope: import. Out of scope: behavior. |
| 2 | | test_agent_name | `agent_name` is `"manage-appointment-agent"`. | Instantiate; assert. | Metadata. | In scope: name. Out of scope: prompt. |
| 3 | | test_get_upcoming_appointments_excludes_cancelled_and_old | Filter excludes cancelled and >1h-old appointments. | Build past/recent/cancelled/future appointments; call `_get_upcoming_appointments(prefer_cache=False)`; assert only recent + future returned. | Time/status filter. | In scope: filter logic. Out of scope: timezone calc. |
| 4 | | test_select_appointment_to_reschedule_valid | Selecting an appointment writes `reschedule_appointment_id` and returns next agent. | Provide single-appt session; call tool with `appt_001`; assert state mutation and non-None return. | Reschedule selection. | In scope: state mutation. Out of scope: validation rules (TODO in code). |
| 5 | | test_confirm_appointment_details | Confirms details and triggers SMS via `_send_appointment_sms`. | Patch SMS method True; await `_confirm_appointment_details`; assert SMS called and `say` called. | SMS-on-confirm. | In scope: side effects. Out of scope: SMS provider. |
| 6 | | test_confirm_appointment_details_publishes_event | Successful confirm publishes `AppointmentDetailsConfirmedEvent`. | Set EventBus, subscribe handler; call confirm; drain bus; assert one event with correct call_id and practice_id. | Observability hook. | In scope: event emission. Out of scope: downstream consumers. |
| 7 | | test_confirm_appointment_details_continues_on_sms_failure | SMS failure does not raise; confirmation message still spoken. | Patch SMS False; assert `say` still called, no exception. | Graceful SMS failure. | In scope: error tolerance. Out of scope: retry. |
| 8 | | test_confirm_includes_sms_line_when_sms_succeeds | Spoken message includes "I sent the details in a text." when SMS succeeds. | Patch SMS True; assert message text. | Conditional copy. | In scope: utterance assembly. Out of scope: TTS. |
| 9 | | test_confirm_excludes_sms_line_when_sms_fails | Spoken message omits SMS line when SMS fails. | Patch SMS False; assert text excludes line. | Conditional copy. | In scope: utterance assembly. Out of scope: TTS. |
| 10 | | test_confirm_transfers_when_missing_start_time_description | Missing `start_time_description` triggers transfer with VALIDATION_FAILURE. | Build appointment with None description; assert `_execute_transfer` called with appointment number + validation reason; `say` not called. | Defensive transfer. | In scope: transfer args. Out of scope: schema validation. |
| 11 | | test_end_conversation | `end_conversation` delegates to `end_call_gracefully` with `DEFAULT_GOODBYE`. | Patch helper; await; assert called with goodbye list. | End-call delegation. | In scope: delegation. Out of scope: shutdown sequence. |
| 12 | | test_on_enter_proceeds_when_authenticated | Authenticated + has appointments -> `generate_reply` (LLM) used, not `say`. | Patch lifecycle; await on_enter; assert `generate_reply` once, `say` not. | Standard entry. | In scope: branch. Out of scope: prompt. |
| 13 | | test_on_enter_transfers_when_no_appointments | Authenticated but zero upcoming -> soft transfer (ZO-615). | Empty appointments; assert `_execute_transfer` with NO_APPOINTMENTS_ACTION_TRANSFER + VALIDATION_FAILURE. | Empty-state transfer. | In scope: branch + utterance. Out of scope: ticket. |
| 14 | | test_on_enter_transfers_when_not_authenticated | If `authenticated` is None (default), guard transfers immediately. | Set unauthenticated state; assert transfer with `NOT_AUTHENTICATED` reason. | Safety guard. | In scope: guard. Out of scope: auth flow. |
| 15 | | test_on_enter_filters_out_past_appointments_before_prompt_load | Past appointments stay on session but prompt context only sees upcoming. | Build past + future; patch BaseAgent.on_enter; await; assert original list unchanged and prompt kwarg `upcoming_appointments==[future]`. | Prompt-context filtering. | In scope: kwargs to base on_enter. Out of scope: prompt rendering. |
| 16 | | test_on_enter_transfers_when_authenticated_false | `authenticated=False` explicitly -> safety transfer. | Set state; assert `_execute_transfer` called. | Safety guard explicit-false. | In scope: branch. Out of scope: auth flow. |

### Suggested Missing Tests
- **Mixed status appointments** - confirm with mix of confirmed + checked-in only acts on confirmed.
- **Multiple upcoming appointments confirm** - currently coverage skews to single-appt; add 2+ case.
- **Reschedule with already-cancelled appt id** - select a cancelled id; ensure rejected.
- **EventBus failure during confirm** - swallow handler errors so caller flow is not blocked.
- **Re-entry idempotency** - calling `_confirm_appointment_details` twice should not double-publish event or re-send SMS.
- **Locale-aware `start_time_description`** - some practices may have non-English descriptions.

### Improvement Suggestions / Irrelevant Tests
- **`patch_agent_lifecycle` only used twice** - either reuse it everywhere or inline; right now most tests build their own ad-hoc session mock.
- **`test_agent_exists`** - low value vs. import linting; consider deletion.
- **Large appointment factories** - extract `_build_appointment` to conftest so other agent tests can reuse.

## test_select_location_agent.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | TestSelectLocationAgent::test_agent_name | Agent name slug matches prompt. | Instantiate via `__new__`; assert. | Metadata. | In scope: name. Out of scope: prompt. |
| 2 | | TestSelectLocationAgent::test_get_dependencies | Agent depends on `call_goal`. | Call class method. | Metadata. | In scope: deps. Out of scope: resolution. |
| 3 | | TestSelectLocationAgent::test_get_output_fields | Agent outputs `location_id`. | Call class method. | Metadata. | In scope: outputs. Out of scope: writes. |
| 4 | | TestSelectLocationAgentRegistration::test_registered_for_schedule_goal | Agent registered for schedule flow. | Look up `flow_registry.GOAL_AGENTS["schedule"]`; assert membership. | Flow registration. | In scope: registry. Out of scope: ordering. |
| 5 | | TestSelectLocationAgentRegistration::test_priority_is_10 | Location runs before provider and insurance. | Compare indices in `GOAL_AGENTS["schedule"]`. | Priority ordering. | In scope: relative order. Out of scope: index value. |
| 6 | | TestSubmitLocation::test_confirmed_updates_session_state | Confirmed validation writes `location_id` to userdata. | Patch `_validate_location` -> confirmed; await `submit_location`; assert state. | Tool happy path. | In scope: state mutation. Out of scope: API call. |
| 7 | | TestSubmitLocation::test_confirmed_returns_next_agent | Tool returns next agent on confirm. | Patch validate + `get_next_agent`; assert returned. | Handoff. | In scope: returned object. Out of scope: handoff timing. |
| 8 | | TestSubmitLocation::test_excluded_transfers_with_number | Excluded with `transfer_number` -> `transfer_to_number` called. | Patch validate to excluded+number; spy transfer; assert args. | Transfer-with-number branch. | In scope: dispatcher. Out of scope: SIP. |
| 9 | | TestSubmitLocation::test_excluded_without_number_falls_back_to_human | Excluded without number -> `transfer_to_human` (CAPABILITY_RESTRICTION). | Patch validate excluded only; assert `transfer_to_human` args. | Fallback transfer. | In scope: dispatcher. Out of scope: SIP. |
| 10 | | TestSubmitLocation::test_not_found_returns_message | `not_found` -> tool returns message; `location_id` stays None. | Patch validate not_found; assert returned message and state untouched. | Not-found branch. | In scope: return value. Out of scope: validation. |
| 11 | | TestSubmitLocation::test_confirmed_saves_previous_location_id | When prior `location_id` exists, it's saved to `previous_location_id` before overwrite. | Pre-set `location_id`; submit new; assert previous saved. | Audit/undo support. | In scope: history field. Out of scope: undo flow. |
| 12 | | TestSubmitLocation::test_confirmed_without_prior_location | `previous_location_id` stays None when no prior selection. | Submit; assert `previous_location_id is None`. | Audit/undo edge. | In scope: history field. Out of scope: undo flow. |
| 13 | | TestBuildLocationContext::test_no_locations | Empty location list -> message includes "no configured locations". | Call helper with `[]`; assert string. | Helper edge. | In scope: helper string. Out of scope: prompt format. |
| 14 | | TestBuildLocationContext::test_single_location | Single location info string includes name/city/state. | Build location; call helper; assert "single location" + content. | Helper happy path. | In scope: helper. Out of scope: prompt. |
| 15 | | TestBuildLocationContext::test_multiple_same_state | Multiple in same state -> "2 locations" without "multiple states". | Two NY locations; assert string. | Helper. | In scope: branch. Out of scope: prompt. |
| 16 | | TestBuildLocationContext::test_multiple_multi_state | Multi-state -> includes "multiple states" plus state codes. | NY+NJ; assert string. | Helper. | In scope: branch. Out of scope: prompt. |
| 17 | | TestBuildLocationContext::test_location_count_in_context | Reports correct count for 3 and 5 locations. | Parametrized over (3, 5). | Helper count formatting. | In scope: counter format. Out of scope: prompt. |

### Suggested Missing Tests
- **Validation API timeout** - when `_validate_location` raises -> ensure caller-friendly error/transfer instead of crash.
- **Excluded with malformed `transfer_number`** - non-E.164 string should still safely fall back.
- **Auto-skip when only 1 bookable location** (parity with `SelectProviderAgent.on_enter` auto-skip).
- **Language toggle** - confirm `_build_location_context` handles non-English location names without mojibake.

### Improvement Suggestions / Irrelevant Tests
- **`agent = SelectLocationAgent.__new__(SelectLocationAgent)`** repeated everywhere; lift to fixture.
- **Helper tests** vs **tool tests** are mixed; consider splitting `_build_location_context` into its own module + test file.
- **No on_enter coverage** - location agent has no entry-time auto-skip tests; either add or document why not needed.

## test_select_provider_agent.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | TestSelectProviderAgent::test_agent_name | `agent_name == "select-provider-agent"`. | Instantiate; assert. | Metadata. | In scope: name. Out of scope: prompt. |
| 2 | | TestSelectProviderAgent::test_get_dependencies | Depends on `call_goal`. | Class method call. | Metadata. | In scope: deps. Out of scope: resolution. |
| 3 | | TestSelectProviderAgent::test_get_output_fields | Outputs `provider`. | Class method call. | Metadata. | In scope: outputs. Out of scope: writes. |
| 4 | | TestSelectProviderAgentRegistration::test_registered_for_schedule_goal | Provider in schedule flow. | Membership check. | Flow registry. | In scope: registry. Out of scope: ordering. |
| 5 | | TestSelectProviderAgentRegistration::test_priority_after_location_before_insurance | Index check: location < provider < insurance. | Index compare. | Priority. | In scope: relative order. Out of scope: index. |
| 6 | | TestSubmitProvider::test_confirmed_updates_session_state | Confirmed -> writes `provider`, `provider_id`, `needs_booking_validation=True`. | Patch validate; submit; assert state. | Happy path. | In scope: state mutation. Out of scope: API. |
| 7 | | TestSubmitProvider::test_confirmed_returns_next_agent | Confirmed returns next agent. | Patch get_next_agent; assert. | Handoff. | In scope: return value. Out of scope: handoff timing. |
| 8 | | TestSubmitProvider::test_confirmed_saves_previous_provider | Saves prior provider/provider_id before overwrite. | Pre-set; submit new; assert previous fields. | Audit/undo. | In scope: history. Out of scope: undo flow. |
| 9 | | TestSubmitProvider::test_any_provider_skips_validation | `provider="any"` skips validation entirely. | Patch get_next_agent only; submit; assert no validate call and `needs_booking_validation=True`. | Wildcard fast-path. | In scope: branch. Out of scope: scheduler logic. |
| 10 | | TestSubmitProvider::test_excluded_transfers_with_number | Excluded + transfer_number -> `transfer_to_number`. | Patch transfer; assert args. | Transfer dispatch. | In scope: args. Out of scope: SIP. |
| 11 | | TestSubmitProvider::test_excluded_without_number_falls_back_to_human | Excluded without number -> `transfer_to_human` (CAPABILITY_RESTRICTION). | Patch transfer; assert args. | Fallback. | In scope: args. Out of scope: SIP. |
| 12 | | TestSubmitProvider::test_not_found_returns_message | Not found -> message returned, `provider` stays None. | Patch validate; assert. | Not-found. | In scope: return. Out of scope: validation API. |
| 13 | | TestSubmitProvider::test_age_restricted_transfers_with_number | Age-restricted + transfer_number -> `transfer_to_number`. | Patch transfer; assert utterance and args. | Age-restriction dispatch. | In scope: args. Out of scope: SIP. |
| 14 | | TestSubmitProvider::test_age_restricted_without_number_falls_back_to_human | Age-restricted without number -> `transfer_to_human` (CAPABILITY_RESTRICTION). | Patch transfer; assert. | Fallback. | In scope: args. Out of scope: SIP. |
| 15 | | TestSubmitProvider::test_provider_at_other_location_returns_message | Provider at other location -> tool returns message and `provider` stays None. | Patch validate; assert. | Branch. | In scope: return. Out of scope: cross-location flow. |
| 16 | | TestValidateProviderRequest::test_includes_date_of_birth_when_set | `_validate_provider` includes DOB in request body when set. | Patch ZoToolsClient; await; assert request body has `date_of_birth`. | Request construction. | In scope: request body. Out of scope: API behavior. |
| 17 | | TestValidateProviderRequest::test_omits_date_of_birth_when_not_set | DOB absent when not set. | Patch ZoToolsClient; assert body omits `date_of_birth`. | Request construction. | In scope: request body. Out of scope: API. |
| 18 | | TestBuildProviderContext::test_no_providers | Empty list -> "no bookable providers". | Helper call; assert. | Helper edge. | In scope: string. Out of scope: prompt. |
| 19 | | TestBuildProviderContext::test_single_provider | Single provider returns "single provider" + name + title. | Helper call; assert. | Helper happy. | In scope: string. Out of scope: prompt. |
| 20 | | TestBuildProviderContext::test_multiple_providers | Multiple providers -> "2 providers" + names. | Helper call; assert. | Helper. | In scope: string. Out of scope: prompt. |
| 21 | | TestBuildProviderContext::test_filters_out_facilities | `is_facility=True` providers excluded. | Helper call; assert. | Filter. | In scope: filter. Out of scope: schema. |
| 22 | | TestBuildProviderContext::test_filters_out_resources | `is_resource=True` providers excluded. | Helper call; assert. | Filter. | In scope: filter. Out of scope: schema. |
| 23 | | TestBuildProviderContext::test_all_filtered_out | All filtered -> "no bookable providers". | Helper call; assert. | Filter edge. | In scope: branch. Out of scope: prompt. |
| 24 | | TestBuildProviderContext::test_provider_without_title | No title -> name only, no parens. | Helper call; assert no `()`. | Display formatting. | In scope: display. Out of scope: prompt. |
| 25 | | TestBuildProviderContext::test_provider_count_in_context | Count formatting for 3 and 5 providers. | Parametrized helper. | Counter format. | In scope: format. Out of scope: prompt. |
| 26 | | TestBuildProviderContextIPA::test_single_provider_with_ipa | Spoken name uses IPA when provided. | Provider with IPA fields; assert spoken matches IPA, not raw last_name. | Pronunciation. | In scope: spoken-name selection. Out of scope: TTS. |
| 27 | | TestBuildProviderContextIPA::test_multiple_providers_with_ipa | IPA used for one provider while another uses raw name. | Build mixed; assert both representations present. | Mixed pronunciation. | In scope: per-provider IPA. Out of scope: TTS. |
| 28 | | TestOnEnterAutoSkip::test_single_real_provider_auto_selects | Single bookable provider auto-selected; no `generate_reply`. | Patch session + BaseAgent.on_enter; await; assert provider stored. | Auto-skip. | In scope: branch. Out of scope: super on_enter. |
| 29 | | TestOnEnterAutoSkip::test_multiple_providers_generates_reply | >1 provider -> `generate_reply` called and `provider` stays None. | Patch + await; assert. | Standard ask flow. | In scope: branch. Out of scope: prompt. |
| 30 | | TestOnEnterAutoSkip::test_single_provider_after_filtering | Auto-skip when only 1 real after filtering facilities/resources. | Build mixed; await; assert provider stored. | Filter+skip combo. | In scope: branch. Out of scope: filter logic. |
| 31 | | TestExcludedProviderAutoTransfer::test_excluded_single_match_transfers | Single `is_excluded` provider in search -> `transfer_to_human`. | Patch base executor; spy transfer; assert called and result is transfer string. | Tool wrapper transfer. | In scope: wrapper. Out of scope: API. |
| 32 | | TestExcludedProviderAutoTransfer::test_excluded_all_matches_transfers | All-excluded providers -> transfer. | Patch executor; assert. | Wrapper. | In scope: wrapper. Out of scope: API. |
| 33 | | TestExcludedProviderAutoTransfer::test_mixed_excluded_passes_through | Mixed excluded/non-excluded -> raw result returned to LLM, no transfer. | Patch executor; assert no transfer. | Wrapper passthrough. | In scope: wrapper. Out of scope: LLM behavior. |
| 34 | | TestExcludedProviderAutoTransfer::test_no_matches_passes_through | Empty providers list returns unchanged. | Patch executor; assert. | Wrapper edge. | In scope: wrapper. Out of scope: API. |
| 35 | | TestExcludedProviderAutoTransfer::test_error_string_passes_through | Error string from base executor passes through unchanged. | Patch executor returning string; assert. | Wrapper error passthrough. | In scope: wrapper. Out of scope: error format. |
| 36 | | TestExcludedProviderAutoTransfer::test_non_search_tool_not_wrapped | Non-`search_providers` tool not wrapped (returns base executor as-is). | Use other tool def; assert identity. | Wrapper scope. | In scope: wrapping condition. Out of scope: registration. |

### Suggested Missing Tests
- **DOB invalid format in validation request** - `date_of_birth="not-a-date"` should be sanitized or omitted, not forwarded.
- **Network error from `_validate_provider`** - currently no test for client exceptions.
- **`provider` and `provider_id` mismatch** - LLM hallucinates IDs not in registry; ensure validation catches.
- **Tool registration completeness** - assert `submit_provider`, `transfer_low_confidence`, etc., are registered as `@function_tool`.
- **Locale-aware spoken name** - non-Latin characters in `first_name_ipa`.
- **`needs_booking_validation` reset semantics** - on retry after rejection, should it stay True?

### Improvement Suggestions / Irrelevant Tests
- **`SelectProviderAgent.__new__(SelectProviderAgent)`** is everywhere; consolidate into a `bare_agent` fixture.
- **Test count is high (36)** - file would benefit from splitting `TestExcludedProviderAutoTransfer` and `TestBuildProviderContext` into sibling files.
- **Excluded auto-transfer wrapper** does not cover wrapping for new tool versions; consider parametrizing with future tool names.

## test_select_visit_reason_agent.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | test_visit_reason_id_defaults_to_none | Default session has `visit_reason_id is None`. | Build state; assert. | State default. | In scope: default. Out of scope: state schema. |
| 2 | | test_timeslot_agent_requires_visit_reason_id | `SelectTimeslotAgent.get_dependencies()` includes `visit_reason_id`. | Class method check. | Cross-agent contract. | In scope: dependency declaration. Out of scope: timeslot logic. |
| 3 | | TestSelectVisitReasonAgentMetadata::test_agent_name | Agent name slug. | Instantiate; assert. | Metadata. | In scope: name. Out of scope: prompt. |
| 4 | | TestSelectVisitReasonAgentMetadata::test_get_dependencies | Depends on `call_goal`. | Class method call. | Metadata. | In scope: deps. Out of scope: resolution. |
| 5 | | TestSelectVisitReasonAgentMetadata::test_get_output_fields | Outputs `visit_reason_id`. | Class method call. | Metadata. | In scope: outputs. Out of scope: writes. |
| 6 | | TestSelectVisitReasonAgentRegistration::test_registered_for_schedule_goal | Registered in `schedule` flow. | Membership check. | Registry. | In scope: registry. Out of scope: ordering. |
| 7 | | TestSelectVisitReasonAgentRegistration::test_runs_before_location_agent | Visit reason runs before location. | Index compare. | Priority. | In scope: relative order. Out of scope: index. |
| 8 | | TestSubmitVisitReason::test_updates_visit_reason_id | Submitting writes `visit_reason_id` to userdata. | Patch get_next_agent; await; assert state. | Tool happy. | In scope: state mutation. Out of scope: validation. |
| 9 | | TestSubmitVisitReason::test_returns_next_agent | Returns next agent on submit. | Patch + assert return. | Handoff. | In scope: return. Out of scope: handoff timing. |
| 10 | | TestTransferLowConfidence::test_calls_transfer_to_human | `transfer_low_confidence` invokes `transfer_to_human` with appointment number + CAPABILITY_RESTRICTION. | Patch transfer; await; assert args. | Low-confidence escape hatch. | In scope: dispatch args. Out of scope: SIP. |

### Suggested Missing Tests
- **Validation of `visit_reason_id`** - non-existent or malformed id should be rejected before commit.
- **Repeat submission** - submitting the same reason twice; ensure no duplicate writes / next agent still returned.
- **Auto-skip when practice has only one visit reason** (parity with provider auto-skip).
- **Language switching during submission** - utterance updates with locale.
- **Tool registration completeness** - verify both `submit_visit_reason` and `transfer_low_confidence` are LLM-callable function tools.

### Improvement Suggestions / Irrelevant Tests
- **Smallest file (88 lines)** - looks under-tested vs. peer agents; raise floor.
- **No on_enter coverage** - if visit-reason agent has lifecycle behavior, add at least one.

## test_unified_new_appointment_agent.py

> NOTE: This is by far the largest file (>2800 lines, 70+ tests). Tests are grouped by class for readability; the ordering matches source.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | TestSearchAvailabilityRemoval::test_no_local_search_availability_attr | Regression guard: agent must not have a local `@function_tool` named `search_availability` (now served by zo-tools). | Instantiate agent; check `search_availability` attribute either absent or lacks `__livekit_tool__`. | Regression. | In scope: tool surface. Out of scope: zo-tools. |
| 2 | | TestDateHelperTools::test_resolve_day_to_date_confirmed | Day name + correct date -> `confirmed`. | Compute target date; call tool; parse JSON. | Date helper happy. | In scope: tool result. Out of scope: timezone library. |
| 3 | | TestDateHelperTools::test_resolve_day_to_date_corrected_emits_metric | Wrong date for day -> `corrected` and `date_resolver.corrected` metric incremented with tool tag. | Patch metrics; call with stale date; assert metric. | Metric emission. | In scope: metric tag. Out of scope: metric backend. |
| 4 | | TestDateHelperTools::test_resolve_day_to_date_invalid_day_emits_error_metric | Invalid day name -> `error` and `date_resolver.error` metric. | Patch metrics; assert. | Error metric. | In scope: metric tag. Out of scope: backend. |
| 5 | | TestDateHelperTools::test_resolve_date_to_day | Valid ISO date -> day name. | Call tool; assert `day_name=="Monday"`. | Reverse helper. | In scope: tool. Out of scope: parser. |
| 6 | | TestDateHelperTools::test_resolve_date_to_day_invalid_format | Free-form date string -> `error`. | Call with `"April 2nd"`; assert. | Parser strictness. | In scope: error path. Out of scope: free-form parsing. |
| 7 | | TestResolveDateRange::test_next_week | "next week" resolves to Mon-Sun (next week). | Compute expected; call tool; parse range; assert. | Range happy. | In scope: range math. Out of scope: tz lib. |
| 8 | | TestResolveDateRange::test_resolve_date_range_does_not_accept_original_appointment_date | Method must reject `original_appointment_date` kwarg. | Call with kwarg; expect TypeError. | Signature guard. | In scope: arg surface. Out of scope: reschedule flow. |
| 9 | | TestGetReferenceDate::test_uses_practice_timezone | `_get_reference_date` returns today in practice tz. | Compute expected; assert. | TZ-aware "today". | In scope: tz handling. Out of scope: time mocking. |
| 10 | | TestTrackDateResolverResult::test_confirmed_emits_no_metric | `confirmed` status -> no metric. | Patch metrics; call tracker; assert not called. | Negative metric path. | In scope: metric. Out of scope: backend. |
| 11 | | TestTrackDateResolverResult::test_corrected_emits_metric_with_tool_tag | Corrected -> increments with `tool:` tag. | Patch metrics; assert call args. | Metric tag. | In scope: tag format. Out of scope: backend. |
| 12 | | TestTrackDateResolverResult::test_error_includes_error_tag | Error result includes `error:<reason>` tag. | Patch metrics; assert. | Tag format. | In scope: tag. Out of scope: backend. |
| 13 | | TestSearchPatientsDobValidation::test_no_match_confirms_dob_with_dash_readback | Zero-match search returns `no_match_confirm_dob` with readback string and persists DOB. | Patch session + zotools_client; call wrapped tool; assert JSON + userdata. | Search flow + readback. | In scope: tool result. Out of scope: zo-tools. |
| 14 | | TestSearchPatientsDobValidation::test_invalid_dob_does_not_store_on_session | Invalid month -> userdata DOB unchanged. | Call with month=13; assert state untouched. | Validation. | In scope: state guard. Out of scope: error UX. |
| 15 | | TestSearchPatientsDobValidation::test_invalid_month_returns_error_without_calling_api | API not called for invalid month. | Patch zotools; call; assert search_patients not called. | Short-circuit. | In scope: API guard. Out of scope: API. |
| 16 | | TestSubmitBookingZoToolsCompat::test_preview_posts_new_body_and_reads_directives_speak | Preview path posts to expected zo-tools path and reads directive speak text. | Patch booking env; await; assert request shape and speak text. | Zo-tools preview compat. | In scope: contract. Out of scope: zo-tools API. |
| 17 | | TestSubmitBookingZoToolsCompat::test_commit_sets_booking_confirmed_when_appointment_id_present | Commit with appointment_id -> `booking_confirmed=True`. | Patch env; await; assert state. | Commit happy. | In scope: state. Out of scope: API. |
| 18 | | TestSubmitBookingZoToolsCompat::test_commit_failure_leaves_booking_unconfirmed | Commit failure -> `booking_confirmed=False`. | Patch failure; assert. | Commit failure. | In scope: state. Out of scope: retry. |
| 19 | | TestSubmitBookingZoToolsCompat::test_yes_publishes_submitted_timeslot_even_if_commit_fails | Yes-confirmation path emits `CallEventStringPublished` even if commit later fails. | Patch env; capture events; assert publication. | Telemetry guarantee. | In scope: event emission. Out of scope: commit. |
| 20 | | TestSubmitBookingZoToolsCompat::test_no_does_not_publish_submitted_timeslot | Caller says no -> no submitted event published. | Confirm decision NO; assert no event. | Telemetry negative. | In scope: event suppression. Out of scope: confirm task. |
| 21 | | TestSubmitBookingZoToolsCompat::test_transfer_request_short_circuits_commit | Transfer requested mid-confirm -> commit never called. | Confirm result transfer; assert commit not called. | Short-circuit. | In scope: branch. Out of scope: transfer. |
| 22 | | TestSubmitBookingDirectiveErrorHandling::test_preview_transfer_directive_triggers_transfer | Preview directive `transfer` -> transfer triggered. | Patch env; assert transfer. | Directive routing. | In scope: directive. Out of scope: SIP. |
| 23 | | TestSubmitBookingDirectiveErrorHandling::test_preview_transfer_with_speak_speaks_then_transfers | Preview directive with speak -> speak first, then transfer. | Patch env; assert order. | Directive UX. | In scope: order. Out of scope: TTS. |
| 24 | | TestSubmitBookingDirectiveErrorHandling::test_preview_no_speak_no_transfer_triggers_fallback_transfer | Preview missing speak/transfer -> fallback transfer. | Assert fallback. | Defensive. | In scope: fallback. Out of scope: API. |
| 25 | | TestSubmitBookingDirectiveErrorHandling::test_preview_transfer_uses_directive_transfer_number | Directive-provided transfer number is honored. | Assert phone number passed. | Number routing. | In scope: routing. Out of scope: SIP. |
| 26 | | TestSubmitBookingDirectiveErrorHandling::test_commit_transfer_directive_triggers_transfer | Commit directive transfer -> transfer triggered. | Assert. | Commit-directive. | In scope: branch. Out of scope: SIP. |
| 27 | | TestSubmitBookingDirectiveErrorHandling::test_commit_errors_directive_triggers_transfer | Commit `errors` directive -> transfer triggered. | Assert. | Errors-directive. | In scope: branch. Out of scope: SIP. |
| 28 | | TestSubmitBookingDirectiveErrorHandling::test_commit_transfer_takes_precedence_over_errors | Both transfer + errors -> transfer wins. | Assert. | Precedence. | In scope: precedence. Out of scope: API. |
| 29 | | TestSubmitBookingDirectiveErrorHandling::test_commit_no_appointment_id_no_indicators_triggers_transfer | Commit without appointment_id and no indicators -> fallback transfer. | Assert. | Defensive. | In scope: fallback. Out of scope: API. |
| 30 | | TestSubmitBookingDirectiveErrorHandling::test_commit_success_with_no_speak_uses_fallback | Commit success without speak -> fallback utterance. | Assert. | Fallback UX. | In scope: utterance. Out of scope: TTS. |
| 31 | | TestSubmitBookingMissingPatientFieldsRecovery::test_single_missing_field_returns_to_llm | Single missing field -> error returns to LLM (no transfer). | Assert. | Recoverable. | In scope: branch. Out of scope: LLM behavior. |
| 32 | | TestSubmitBookingMissingPatientFieldsRecovery::test_multiple_missing_field_errors_transfers | Multiple missing fields -> transfer. | Assert. | Multi-error. | In scope: branch. Out of scope: SIP. |
| 33 | | TestSubmitBookingMissingPatientFieldsRecovery::test_mixed_errors_transfers | Missing field + other errors -> transfer. | Assert. | Mixed-error. | In scope: branch. Out of scope: SIP. |
| 34 | | TestSubmitBookingMissingPatientFieldsRecovery::test_non_recoverable_error_still_transfers | Non-recoverable error -> transfer. | Assert. | Non-recoverable. | In scope: branch. Out of scope: SIP. |
| 35 | | TestSubmitBookingMissingPatientFieldsRecovery::test_single_missing_field_emits_retry_metric | Single missing field emits retry metric. | Patch metrics; assert. | Metric. | In scope: metric tag. Out of scope: backend. |
| 36 | | TestSubmitBookingMissingPatientFieldsRecovery::test_multiple_missing_fields_emits_transfer_metric | Multiple missing -> transfer metric. | Patch metrics; assert. | Metric. | In scope: metric tag. Out of scope: backend. |
| 37 | | TestSubmitBookingMissingPatientFieldsRecovery::test_missing_description_uses_fallback | Missing description in error -> fallback wording. | Assert. | Defensive UX. | In scope: utterance. Out of scope: TTS. |
| 38 | | TestSubmitBookingMissingPatientFieldsRecovery::test_null_description_uses_fallback | `description=None` -> fallback wording. | Assert. | Defensive. | In scope: utterance. Out of scope: TTS. |
| 39 | | TestSubmitBookingMissingPatientFieldsRecovery::test_non_dict_error_entry_not_treated_as_missing_fields | Non-dict error entry -> skipped from missing-fields path. | Assert. | Type robustness. | In scope: type guard. Out of scope: API. |
| 40 | | TestSubmitBookingMissingPatientFieldsRecovery::test_single_missing_field_does_not_set_booking_confirmed | Recoverable error path leaves `booking_confirmed=False`. | Assert. | State guard. | In scope: state. Out of scope: API. |
| 41 | | TestSubmitBookingMissingPatientFieldsRecovery::test_mixed_errors_does_not_emit_missing_fields_metric | Mixed errors -> no `missing_fields` metric. | Patch metrics; assert. | Metric. | In scope: metric. Out of scope: backend. |
| 42 | | TestBuildLocationContext::test_single_physical_no_virtual | Single physical location formatting. | Helper call; assert. | Helper happy. | In scope: helper. Out of scope: prompt. |
| 43 | | TestBuildLocationContext::test_multiple_physical_no_virtual | Multiple physical locations same state. | Helper call. | Helper. | In scope: helper. Out of scope: prompt. |
| 44 | | TestBuildLocationContext::test_multi_state_physical | Physical multi-state. | Helper. | Helper branch. | In scope: helper. Out of scope: prompt. |
| 45 | | TestBuildLocationContext::test_physical_plus_virtual | Mixed physical + virtual. | Helper. | Helper. | In scope: helper. Out of scope: prompt. |
| 46 | | TestBuildLocationContext::test_virtual_only | Virtual only. | Helper. | Helper. | In scope: helper. Out of scope: prompt. |
| 47 | | TestBuildLocationContext::test_virtual_multi_state | Virtual multi-state. | Helper. | Helper. | In scope: helper. Out of scope: prompt. |
| 48 | | TestBuildLocationContext::test_virtual_single_state_has_per_state_summary | Virtual single state shows per-state summary. | Helper. | Helper. | In scope: helper. Out of scope: prompt. |
| 49 | | TestBuildLocationContext::test_virtual_multi_state_has_per_state_summary | Virtual multi-state per-state summary. | Helper. | Helper. | In scope: helper. Out of scope: prompt. |
| 50 | | TestBuildLocationContext::test_virtual_state_over_five_omits_ids | Virtual states >5 omits IDs. | Helper. | Helper edge. | In scope: helper. Out of scope: prompt. |
| 51 | | TestBuildLocationContext::test_no_locations | Empty list. | Helper. | Helper edge. | In scope: helper. Out of scope: prompt. |
| 52 | | TestBuildLocationContext::test_location_summary_excludes_location_details | Summary string excludes detail-level info. | Helper. | Helper. | In scope: helper. Out of scope: prompt. |
| 53 | | TestSearchLocationsRemoval::test_no_local_search_locations_attr | Regression guard: no local `search_locations` `@function_tool`. | Inspect attr. | Regression. | In scope: tool surface. Out of scope: zo-tools. |
| 54 | | TestSearchProvidersZoToolsCompat::test_name_search_posts_expected_path_and_body | Name-based provider search posts correct path + body. | Patch client; await; assert request. | Compat. | In scope: contract. Out of scope: API. |
| 55 | | TestSearchProvidersZoToolsCompat::test_list_all_mode_sends_none_fields | List-all mode sends None for filter fields. | Patch client; assert request. | Compat. | In scope: contract. Out of scope: API. |
| 56 | | TestGetAppointmentsPatientResolution::test_single_found_patient_no_ids | Single found patient resolved without explicit IDs. | Patch agent state; await; assert. | Resolution branch. | In scope: branch. Out of scope: API. |
| 57 | | TestGetAppointmentsPatientResolution::test_multiple_found_patients_no_ids_returns_error | Multiple found + no explicit IDs -> error. | Assert. | Branch. | In scope: branch. Out of scope: API. |
| 58 | | TestGetAppointmentsPatientResolution::test_no_found_patients_returns_error | No found -> error. | Assert. | Branch. | In scope: branch. Out of scope: API. |
| 59 | | TestGetAppointmentsPatientResolution::test_explicit_patient_ids_filters | Explicit IDs filter to matching found patients. | Assert filter. | Branch. | In scope: filter. Out of scope: API. |
| 60 | | TestGetAppointmentsPatientResolution::test_unrecognized_patient_ids_skipped | Unrecognized IDs skipped silently. | Assert filter. | Branch. | In scope: filter. Out of scope: API. |
| 61 | | TestGetAppointmentsPatientResolution::test_all_unrecognized_patient_ids_returns_error | All unrecognized -> error. | Assert. | Branch. | In scope: branch. Out of scope: API. |
| 62 | | TestGetAppointmentsPatientResolution::test_global_location_ids_passed_through | Global location IDs forwarded as-is. | Assert request. | Forward. | In scope: forward. Out of scope: API. |
| 63 | | TestGetAppointmentsPatientResolution::test_no_location_ids_passes_none | No location IDs -> None forwarded. | Assert. | Forward. | In scope: forward. Out of scope: API. |
| 64 | | TestFetchAppointments::test_single_patient_success | Single-patient fetch returns appointments. | Assert. | Happy. | In scope: aggregation. Out of scope: API. |
| 65 | | TestFetchAppointments::test_multiple_patients_aggregated | Multi-patient fetch aggregates. | Assert. | Aggregation. | In scope: aggregation. Out of scope: API. |
| 66 | | TestFetchAppointments::test_global_location_ids_override_patient_locations | Global location IDs override patient locations. | Assert request. | Override. | In scope: override. Out of scope: API. |
| 67 | | TestFetchAppointments::test_no_global_location_ids_uses_patient_locations | No globals -> use patient locations. | Assert request. | Default. | In scope: forward. Out of scope: API. |
| 68 | | TestFetchAppointments::test_none_location_ids_in_patient_defaults_to_empty | Patient with `location_ids=None` -> defaults to []. | Assert. | Defensive. | In scope: defaulting. Out of scope: schema. |
| 69 | | TestFetchAppointmentsFiltering::test_response_includes_is_in_past_flag | Response decorates each appointment with `is_in_past`. | Assert flag. | Decorator. | In scope: shape. Out of scope: clock. |
| 70 | | TestFetchAppointmentsFiltering::test_caps_past_appointments_to_three | Past appointments capped at 3. | Assert count. | Cap. | In scope: cap. Out of scope: ordering algorithm. |
| 71 | | TestFetchAppointmentsFiltering::test_cancelled_appointments_excluded_from_results | Cancelled excluded. | Assert filter. | Filter. | In scope: filter. Out of scope: status enum. |
| 72 | | TestGetPatientDemographicRequirements::test_required_and_asked_fields_listed | Required + asked fields appear in output. | Assert. | Output. | In scope: helper. Out of scope: API. |
| 73 | | TestGetPatientDemographicRequirements::test_not_asked_fields_excluded | Not-asked fields excluded. | Assert. | Filter. | In scope: filter. Out of scope: API. |
| 74 | | TestGetPatientDemographicRequirements::test_no_patient_information_returns_fallback | Missing patient info section -> fallback. | Assert. | Fallback. | In scope: fallback. Out of scope: API. |
| 75 | | TestGetPatientDemographicRequirements::test_existing_patient_uses_existing_patient_levels | Existing-patient path uses existing-patient levels. | Assert. | Path. | In scope: branch. Out of scope: API. |
| 76 | | TestGetPatientDemographicRequirements::test_gender_required_appears_in_output | Required gender appears. | Assert. | Field. | In scope: field. Out of scope: API. |
| 77 | | TestGetPatientDemographicRequirements::test_gender_not_asked_excluded_from_output | Not-asked gender excluded. | Assert. | Field. | In scope: field. Out of scope: API. |
| 78 | | TestInsertPatient::test_posts_correct_body_and_returns_patient_id | Insert posts correct body and returns new patient id. | Patch client; assert. | Insert happy. | In scope: contract. Out of scope: API. |
| 79 | | TestInsertPatient::test_api_error_transfers_to_staff | Insert API error -> transfer to staff. | Patch client error; assert transfer. | Error path. | In scope: branch. Out of scope: API. |
| 80 | | TestInsertPatient::test_duplicate_insert_returns_existing_id | Duplicate insert returns existing patient id. | Patch client duplicate; assert. | Idempotency. | In scope: branch. Out of scope: API. |
| 81 | | TestInsertPatient::test_minor_booking_for_self_triggers_transfer | Minor booking for self -> transfer. | Assert. | Policy. | In scope: policy. Out of scope: API. |
| 82 | | TestInsertPatient::test_minor_booking_for_other_proceeds_normally | Minor booking for other proceeds. | Assert. | Policy. | In scope: policy. Out of scope: API. |
| 83 | | TestInsertPatient::test_adult_patient_not_transferred | Adult booking proceeds. | Assert. | Policy. | In scope: policy. Out of scope: API. |
| 84 | | TestInsertPatient::test_invalid_dob_format_returns_error | Invalid DOB format -> error. | Assert. | Validation. | In scope: validation. Out of scope: API. |
| 85 | | TestInsertPatient::test_missing_patient_id_in_response_transfers_to_staff | Missing patient_id in response -> transfer. | Assert. | Defensive. | In scope: branch. Out of scope: API. |
| 86 | | TestUpdatePatient::test_posts_only_provided_fields | Update sends only provided fields. | Assert request body. | Contract. | In scope: contract. Out of scope: API. |
| 87 | | TestUpdatePatient::test_api_error_transfers_to_staff | Update API error -> transfer. | Assert. | Error path. | In scope: branch. Out of scope: API. |
| 88 | | TestUpdatePatient::test_zip_code_serialized_as_zip | `zip_code` is serialized as `zip` in body. | Assert. | Serialization. | In scope: contract. Out of scope: API. |
| 89 | | TestUpdatePatient::test_patient_id_mismatch_returns_error | Mismatched patient_id in response -> error. | Assert. | Defensive. | In scope: branch. Out of scope: API. |
| 90 | | TestUpdatePatient::test_requires_insert_patient_first | Update without prior insert -> error. | Assert. | Order. | In scope: precondition. Out of scope: API. |
| 91 | | TestUpdatePatient::test_zero_optional_fields_sends_only_patient_id | Zero optionals -> body with only patient_id. | Assert. | Edge. | In scope: contract. Out of scope: API. |
| 92 | | TestSearchPatientsZeroMatchAlternatePhone::test_zero_match_after_alternate_phone_transfers | Zero matches after alternate-phone search -> instructs transfer. | Patch client; await; assert JSON status + routing instructions. | Final transfer fallback. | In scope: tool result. Out of scope: SIP. |

### Suggested Missing Tests
- **Tool registration completeness** - assert the agent advertises every tool that zo-tools declares; current regression guards only check for absence of stale local tools.
- **LLM hallucination of tool args** - `submit_booking` with malformed kwargs (e.g., bogus `provider_id`) -> graceful handling.
- **Async cancellation (caller hangup)** - cancel mid-`submit_booking` and assert resources cleaned up.
- **PHI scrubbing** - DOB, name, and phone should not appear in error logs/transfer reasons.
- **Concurrency** - two simultaneous `search_patients` calls; ensure no race on `_found_patients`.
- **Language switching** - DOB readback ("November 3, 1982") must localize per session language.
- **Twin DOB collision** - `search_patients` with two patients sharing DOB but different first names; current "single match" assumption is risky.
- **Daylight-saving boundary for `resolve_date_range`** - verify DST transitions don't shift Mon-Sun off.

### Improvement Suggestions / Irrelevant Tests
- **File is too large (>2800 lines)** - split into `test_unified_new_appointment_agent_booking.py`, `..._patients.py`, `..._date_helpers.py`, `..._location_context.py` to make ownership clearer.
- **`_FakeConfirmTask` / `_FakeGenderTask` duplication** - share via conftest.
- **Heavy module-level patching** of `UnifiedNewAppointmentAgent.session` via `property` is repeated; consider a single context manager fixture.
- **Date helper tests rely on real `datetime.now`** - flake risk near midnight; freeze time with `freezegun` or `pytest-freezer`.
- **Many "regression guard" tests** are negative-existence checks; document the deprecation and consider deletion once a release passes.

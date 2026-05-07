# Tasks - Unit Test Mapping

> Repo: [Zocdoc/zo-agent](https://github.com/Zocdoc/zo-agent)
> Folder: `tests/tasks/`
> Generated: 2026-05-07
> Stack: Python / pytest / LiveKit Agents

## test_authenticate_patient_task.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | TestIsValidPhone::test_is_valid_phone | `is_valid_phone` accepts E.164/10/11 digit and rejects None/empty/short numbers. | Parametrized over 10 inputs; assert boolean. | Phone validity helper. | In scope: validation rule. Out of scope: phone normalization. |
| 2 | | TestPatientAuthResult::test_success_result_has_patient_data | Success dataclass carries patient fields and defaults transfer flags. | Build result; assert fields. | Dataclass shape. | In scope: schema. Out of scope: persistence. |
| 3 | | TestPatientAuthResult::test_transfer_result_has_reason | Transfer result carries reason and no patient data. | Build; assert. | Dataclass branch. | In scope: schema. Out of scope: persistence. |
| 4 | | TestPatientAuthResult::test_default_values | All optional fields default to None and transfer flags False. | Build minimal; assert. | Dataclass defaults. | In scope: defaults. Out of scope: schema. |
| 5 | | TestAuthenticatePatientTaskInit::test_init_stores_parameters | Task stores constructor params and validates phone internally. | Construct via `__new__`; set fields; assert `caller_phone_valid`. | Init contract. | In scope: init. Out of scope: run. |
| 6 | | TestDobConfirmationSkipped::test_valid_caller_phone_skips_dob_confirmation | Valid caller phone -> `dob_confirmation_skipped=True`. | Build task with valid phone; run flow; assert. | Skip branch. | In scope: branch. Out of scope: matcher. |
| 7 | | TestDobConfirmationSkipped::test_invalid_caller_phone_confirms_dob | Invalid caller phone -> DOB confirmation runs. | Build task with invalid phone; assert. | Confirm branch. | In scope: branch. Out of scope: matcher. |
| 8 | | TestZeroMatchAfterSkipConfirmation::test_zero_matches_with_unconfirmed_dob_reconfirms | Zero matches with unconfirmed DOB -> reconfirms DOB before failing. | Run flow; assert reconfirmation step occurred. | Recovery branch. | In scope: orchestration. Out of scope: matcher. |
| 9 | | TestZeroMatchAfterSkipConfirmation::test_zero_matches_with_confirmed_dob_asks_new_or_existing | Zero matches with already-confirmed DOB -> asks new-or-existing. | Run flow; assert NewOrExistingTask invoked. | Recovery branch. | In scope: orchestration. Out of scope: NewOrExisting impl. |
| 10 | | TestZeroMatchAfterSkipConfirmation::test_existing_patient_retries_with_alternate_phone | Existing-patient response -> retries with alternate phone. | Patch tasks; assert second search call with new phone. | Retry path. | In scope: orchestration. Out of scope: matcher. |
| 11 | | TestZeroMatchAfterSkipConfirmation::test_transfer_category_during_dob_reconfirmation | Caller-requested transfer mid-reconfirm sets correct transfer_category. | Patch tasks; assert PatientAuthResult fields. | Category routing. | In scope: result. Out of scope: SIP. |
| 12 | | TestAuthenticatePatientTaskHelpers::test_transfer_creates_transfer_result | `_create_transfer` builds transfer-flagged result. | Call; assert. | Helper. | In scope: helper. Out of scope: orchestration. |
| 13 | | TestAuthenticatePatientTaskHelpers::test_complete_success_creates_success_result | `_complete_success` builds success result with patient fields. | Call; assert. | Helper. | In scope: helper. Out of scope: orchestration. |
| 14 | | TestAuthenticatePatientTaskHelpers::test_complete_success_with_none_patient_transfers | `_complete_success(None)` -> transfer (defensive). | Call; assert transfer flag. | Defensive helper. | In scope: helper. Out of scope: orchestration. |
| 15 | | TestAuthenticatePatientTaskOrchestrationLogic::test_matcher_integration_single_match | Single matcher result triggers success path. | Patch matcher; run; assert. | Integration. | In scope: orchestration with matcher. Out of scope: matcher impl. |
| 16 | | TestAuthenticatePatientTaskOrchestrationLogic::test_matcher_integration_multiple_matches | Multiple matches -> filter further (first/last name). | Run; assert further filter calls. | Integration. | In scope: orchestration. Out of scope: matcher impl. |
| 17 | | TestAuthenticatePatientTaskOrchestrationLogic::test_matcher_integration_zero_matches | Zero matches -> recovery branch. | Run; assert. | Integration. | In scope: orchestration. Out of scope: matcher. |
| 18 | | TestAuthenticatePatientTaskOrchestrationLogic::test_filter_by_first_name_narrows_candidates | Filter by first name reduces candidate list. | Run; assert candidate length. | Filter step. | In scope: orchestration. Out of scope: matcher. |
| 19 | | TestAuthenticatePatientTaskOrchestrationLogic::test_filter_by_last_name_narrows_candidates | Filter by last name reduces list. | Run; assert. | Filter step. | In scope: orchestration. Out of scope: matcher. |
| 20 | | TestGoalSkipsNewOrExisting::test_existing_patient_goal_skips_new_or_existing | Existing-patient call goals (cancel, reschedule, etc.) skip NewOrExisting. | Parametrized over goals; run; assert task not invoked. | Branch. | In scope: orchestration. Out of scope: goal definitions. |
| 21 | | TestGoalSkipsNewOrExisting::test_schedule_goal_still_asks_new_or_existing | Schedule goal still asks new-or-existing. | Run; assert task invoked. | Branch. | In scope: orchestration. Out of scope: NewOrExisting. |
| 22 | | TestConfirmAndComplete::test_caller_confirms_name_completes_success | Caller confirms name -> success completion. | Patch ConfirmFullNameTask; run; assert success. | Confirmation flow. | In scope: orchestration. Out of scope: confirm task. |
| 23 | | TestConfirmAndComplete::test_caller_rejects_name_transfers | Caller rejects name -> transfer. | Patch reject; run; assert transfer. | Confirmation flow. | In scope: branch. Out of scope: SIP. |
| 24 | | TestConfirmAndComplete::test_caller_requests_transfer_during_confirmation | Transfer request during confirm -> transfer with USER_REQUESTED. | Patch; run; assert. | Mid-confirm transfer. | In scope: orchestration. Out of scope: SIP. |
| 25 | | TestConfirmAndComplete::test_disambiguation_path_skips_name_confirmation | Disambiguation path bypasses confirm-full-name (already confirmed earlier). | Run; assert task not invoked. | Optimization. | In scope: orchestration. Out of scope: confirm task. |
| 26 | | TestAuthEventPublishing::test_match1_event_published_on_single_match | Single match publishes `match=1` event. | Capture events; run; assert. | Telemetry. | In scope: event emission. Out of scope: bus. |
| 27 | | TestAuthEventPublishing::test_match0_event_published_on_zero_matches | Zero matches publish `match=0` event. | Capture; assert. | Telemetry. | In scope: emission. Out of scope: bus. |
| 28 | | TestAuthEventPublishing::test_auth_failed_emitted_on_no_match_transfer | No-match transfer emits `auth_failed` event. | Capture; assert. | Telemetry. | In scope: emission. Out of scope: bus. |
| 29 | | TestAuthEventPublishing::test_auth_failed_not_emitted_on_user_requested_transfer | User-requested transfer does NOT emit auth_failed. | Capture; assert absence. | Telemetry. | In scope: suppression. Out of scope: bus. |
| 30 | | TestAuthEventPublishing::test_auth_failed_not_emitted_on_collection_failure | Collection failures (DOB, name) do not emit auth_failed. | Capture; assert. | Telemetry. | In scope: suppression. Out of scope: bus. |
| 31 | | TestAuthEventPublishing::test_auth_failed_not_emitted_on_new_patient_transfer | New-patient transfer doesn't emit auth_failed. | Capture; assert. | Telemetry. | In scope: suppression. Out of scope: bus. |
| 32 | | TestAuthEventPublishing::test_match_event_skipped_on_search_failure | Search failure -> match event not emitted. | Capture; assert. | Telemetry. | In scope: suppression. Out of scope: bus. |
| 33 | | TestAuthEventPublishing::test_match_multi_event_published_on_multiple_matches | Multiple matches publish `match_multi` event. | Capture; assert. | Telemetry. | In scope: emission. Out of scope: bus. |

### Suggested Missing Tests
- **Collection retry limits** - DOB/phone/name collection that fails N times should escalate to transfer; current tests don't pin a retry cap.
- **Validation of collected DOB across timezones** - DOB confirmed in caller TZ vs practice TZ (off-by-one risk).
- **Concurrent task cancellation** - simulate caller hangup during NewOrExisting / ConfirmFullName task.
- **Language switching mid-task** - caller switches to Spanish; assert task continues without crashing.
- **Twin DOB+name collision** - two real patients share DOB and first name; matcher returns multiple even after filtering -> ambiguous match handling.
- **Search result paging** - very large candidate set; ensure orchestration scales.

### Improvement Suggestions / Irrelevant Tests
- **1162-line file** is the largest in this folder; split orchestration logic into a separate `..._orchestration.py` test file from event-publishing tests.
- **Heavy mocking of inner tasks** (DOBResult, PhoneResult, NameResult, ConfirmFullNameResult) reduces signal; consider higher-level integration tests using fakes.
- **`_make_task` + `_patch_session` boilerplate** - could be a single fixture.

## test_collect_dob_task.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | TestTransferTools::test_transfer_to_human_sets_user_requested | `transfer_to_human` tool sets `user_requested=True` on completion. | Build task; await tool; assert `transfer_requested` and `user_requested` True. | Tool branch. | In scope: tool flag. Out of scope: SIP. |
| 2 | | TestTransferTools::test_cannot_collect_date_of_birth_does_not_set_user_requested | `cannot_collect_date_of_birth` does NOT set user_requested. | Await tool; assert `user_requested=False`. | Tool branch. | In scope: tool flag. Out of scope: SIP. |
| 3 | | TestSubmitDateOfBirth::test_valid_dob_completes_task | Valid DOB completes task with success. | Await `submit_date_of_birth(month=3, day=15, year=1985)`; assert success and `date_of_birth==date(1985,3,15)`. | Happy. | In scope: completion. Out of scope: validation rules. |
| 4 | | TestSubmitDateOfBirth::test_invalid_dob_returns_error | Invalid DOB returns error message and does NOT complete task. | Await with month=13; assert task not completed and tool returns string. | Validation. | In scope: validation. Out of scope: error UX. |
| 5 | | TestSubmitDateOfBirth::test_two_digit_year | 2-digit year (85) -> 1985. | Await with year=85; assert. | Year handling. | In scope: parser. Out of scope: era logic. |
| 6 | | TestParseAndValidate::test_valid_dates | `_parse_and_validate` accepts valid dates including 2-digit years (2000s and 1900s). | Parametrized 3 cases; assert. | Helper happy. | In scope: parser. Out of scope: tz. |
| 7 | | TestParseAndValidate::test_invalid_dates | Rejects invalid month, invalid day, before-1900, future-date. | Parametrized 4 cases; assert None. | Helper validation. | In scope: validation. Out of scope: error UX. |
| 8 | | TestParseExistingDOB::test_parse_existing_dob | `_parse_existing_dob` accepts ISO; rejects free-form and None. | Parametrized 3 cases; assert. | Helper. | In scope: parser. Out of scope: format detection. |
| 9 | | TestDOBResult::test_success_result | Success dataclass populated correctly with default `transfer_requested=False`. | Build; assert. | Dataclass shape. | In scope: schema. Out of scope: persistence. |
| 10 | | TestDOBResult::test_transfer_result_defaults_user_requested_false | Transfer dataclass defaults `user_requested=False`. | Build; assert. | Dataclass defaults. | In scope: schema. Out of scope: persistence. |
| 11 | | TestDOBResult::test_user_requested_transfer_result | Setting `user_requested=True` reflected in dataclass. | Build; assert. | Dataclass. | In scope: schema. Out of scope: persistence. |

### Suggested Missing Tests
- **Retry limit on invalid DOB** - after N invalid attempts, escalate.
- **Year 2099 edge** - assert future cutoff is current-year-aware (today is 2026; the rule "before year 2099" may need an upper-bound revisit when it stops being true in practice).
- **Localized DOB input** - non-US date formats (DD/MM).
- **DOB transition to next task** - assert next-task handoff on success.
- **Concurrent cancellation** - hangup mid-DOB collection.

### Improvement Suggestions / Irrelevant Tests
- **`_make_task` defined twice** (top-level and inside `TestSubmitDateOfBirth`) - dedupe.
- **Validation tests test private `_parse_and_validate`** - acceptable but consider an integration test asserting validation runs end-to-end through `submit_date_of_birth`.

## test_collect_name_tasks.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | TestCollectFirstNameTask::test_valid_names | First-name task accepts whitespace-padded, hyphenated, apostrophe names (via `.strip()`). | Parametrized; assert. | Tokenization. | In scope: tokenization. Out of scope: validation. |
| 2 | | TestCollectFirstNameTask::test_empty_name_is_invalid | Whitespace-only name treated as invalid. | Assert empty after strip. | Validation. | In scope: validation. Out of scope: error UX. |
| 3 | | TestCollectLastNameTask::test_valid_names | Last-name task accepts whitespace and hyphenated. | Parametrized; assert. | Tokenization. | In scope: tokenization. Out of scope: validation. |
| 4 | | TestCollectLastNameTask::test_empty_name_is_invalid | Empty last name invalid. | Assert. | Validation. | In scope: validation. Out of scope: error UX. |
| 5 | | TestNameResult::test_success_result | Success dataclass with name and `transfer_requested=False`. | Build; assert. | Dataclass. | In scope: schema. Out of scope: persistence. |
| 6 | | TestNameResult::test_transfer_result | Transfer dataclass: `name=None`, `transfer_requested=True`. | Build; assert. | Dataclass. | In scope: schema. Out of scope: persistence. |
| 7 | | TestNameResult::test_last_name_result_same_structure | Last-name `NameResult` shares same shape as first-name. | Build with last_name import; assert. | Schema parity. | In scope: schema. Out of scope: dedup. |

### Suggested Missing Tests
- **Tasks themselves are NOT exercised** - the tests only verify Python's `str.strip()` works; no actual collected-name task tool call is invoked. Need tests for `submit_first_name` / `submit_last_name` tools (success + invalid) to mirror `test_collect_dob_task.py`.
- **Compound names** - "Mary Anne", "Van Der Berg" should be preserved; current tests don't cover spaces.
- **Unicode names** - Hong, Garcia-Lopez with accents (`Núñez`).
- **Profanity / nonsense filtering** (if relevant).
- **Retry limit on invalid name**.
- **Transition to next task** on successful collection.

### Improvement Suggestions / Irrelevant Tests
- **Most tests are tautological** - `test_valid_names` calls `name_input.strip()` and asserts equality with the expected, which doesn't exercise CollectFirstNameTask logic. Refactor to call the actual task tool.
- **Two near-duplicate classes** - `TestCollectFirstNameTask` and `TestCollectLastNameTask` are mirror images; parametrize across both via fixture.
- **Fixture is unused** (commented "_ = first_name_task # Mark as used") - either use it or remove.

## test_collect_phone_task.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | TestValidateAndFormat::test_valid_formats | `_validate_and_format` normalizes 10-digit, dashed, spaced, parens, 11-digit, plus-1 formats to E.164. | Parametrized; assert. | Normalizer. | In scope: parser. Out of scope: country support. |
| 2 | | TestValidateAndFormat::test_invalid_formats | Too short / too long -> None. | Parametrized; assert. | Validation. | In scope: parser. Out of scope: error UX. |
| 3 | | TestValidateAndFormat::test_letters_convert_to_t9_digits | Letters mapped to T9 digits before formatting. | Assert `555-abc-4567` -> `+15552224567`. | T9 mapping. | In scope: mapping. Out of scope: vanity number policy. |
| 4 | | TestConfirmPhoneNumberRejectsIncomplete::test_incomplete_phone_is_invalid | Incomplete phone (3-7 digits) rejected. | Parametrized; assert None. | Validation. | In scope: parser. Out of scope: error UX. |
| 5 | | TestFormatReadable::test_format_e164_to_readable | `_format_readable` produces "five five five, ..." spoken form. | Parametrized 2 numbers; assert string. | Spoken format. | In scope: format. Out of scope: TTS. |
| 6 | | TestFormatPhoneSpoken::test_format_phone_spoken | `format_phone_spoken` helper for raw 10-digit input. | Parametrized 3 numbers; assert. | Helper. | In scope: helper. Out of scope: TTS. |
| 7 | | TestPhoneResult::test_success_result | Success dataclass with phone_number and transfer_requested=False. | Build; assert. | Dataclass. | In scope: schema. Out of scope: persistence. |
| 8 | | TestPhoneResult::test_transfer_result | Transfer dataclass: phone_number=None, transfer_requested=True. | Build; assert. | Dataclass. | In scope: schema. Out of scope: persistence. |
| 9 | | test_task_on_user_turn_completed_sanitizes_user_message_before_reply | `on_user_turn_completed` strips spurious time-like tokens (e.g. "832 257 01:01 4" -> "832 257 1:01 4") before LLM reply. | Build task with mock activity/session; pass user message; assert content sanitized in place. | Sanitization hook. | In scope: hook. Out of scope: STT correctness. |

### Suggested Missing Tests
- **International numbers** - `+44 20 7946 0958` should either work or be rejected explicitly.
- **Letters across full keyword** - "1-800-FLOWERS" full vanity number.
- **Retry limit on invalid phone**.
- **Transition to next task** on successful collection.
- **`format_phone_spoken` for 11-digit** input including country code.
- **`on_user_turn_completed` with no time-like tokens** - assert pass-through.

### Improvement Suggestions / Irrelevant Tests
- **T9 mapping behavior** is subtle - document explicitly that letters map; consider a flag to disable for clarity.
- **`_make_task` not used** - the file uses `task` fixture only via `__new__`, so private validators run without `__init__`. Add at least one test that goes through the full task lifecycle.

## test_confirm_full_name_task.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | TestConfirmFullNameOnEnter::test_on_enter_seeds_prompt_with_name | `on_enter` forwards first/last name to super on_enter for prompt seeding. | Patch BaseTask.on_enter; await task.on_enter; assert call args. | Lifecycle wiring. | In scope: super delegation. Out of scope: prompt content. |
| 2 | | TestConfirmFullNameResult::test_confirmed_result | Confirmed dataclass: `confirmed=True`, `transfer_requested=False`. | Build; assert. | Dataclass. | In scope: schema. Out of scope: persistence. |
| 3 | | TestConfirmFullNameResult::test_rejected_result | Rejected dataclass: `confirmed=False`, `transfer_requested=False`. | Build; assert. | Dataclass. | In scope: schema. Out of scope: persistence. |
| 4 | | TestConfirmFullNameResult::test_transfer_result | Transfer dataclass: `confirmed=False`, `transfer_requested=True`. | Build; assert. | Dataclass. | In scope: schema. Out of scope: persistence. |
| 5 | | TestConfirmFullNameTask::test_task_name | `task_name == "tasks/confirm-full-name-task"`. | Build; assert. | Metadata. | In scope: name. Out of scope: prompt. |
| 6 | | TestConfirmFullNameTask::test_create_transfer_result | `_create_transfer_result` builds transfer-flagged result with confirmed=False. | Call; assert. | Helper. | In scope: helper. Out of scope: orchestration. |
| 7 | | TestConfirmFullNameTask::test_confirm_full_name_accepted | `confirm_full_name(is_correct=True)` completes with confirmed=True. | Override `complete`; await tool; assert single result. | Tool branch. | In scope: completion. Out of scope: prompt. |
| 8 | | TestConfirmFullNameTask::test_confirm_full_name_rejected | `confirm_full_name(is_correct=False)` completes with confirmed=False. | Await tool; assert. | Tool branch. | In scope: completion. Out of scope: prompt. |

### Suggested Missing Tests
- **Repeated rejection** - if caller rejects twice, escalate to transfer.
- **Transfer mid-confirmation** - separate tool to request human; test it triggers transfer result.
- **Tool registration completeness** - assert `confirm_full_name` registered as `@function_tool` and discoverable.
- **Async cancellation** during confirmation.

### Improvement Suggestions / Irrelevant Tests
- **`__wrapped__` access** bypasses LiveKit decorator - add at least one decorator-level test.
- **No DOB-paired confirmation test** - in practice, name-confirm follows DOB; integration test could pin handoff.

## test_new_or_existing_task.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | TestNewOrExistingResult::test_new_patient_result | New patient: `is_new_patient=True`, transfer flag False. | Build; assert. | Dataclass. | In scope: schema. Out of scope: persistence. |
| 2 | | TestNewOrExistingResult::test_existing_patient_result | Existing: `is_new_patient=False`, transfer flag False. | Build; assert. | Dataclass. | In scope: schema. Out of scope: persistence. |
| 3 | | TestNewOrExistingResult::test_transfer_result | Transfer: `is_new_patient=None`, transfer flag True. | Build; assert. | Dataclass. | In scope: schema. Out of scope: persistence. |
| 4 | | TestNewOrExistingResult::test_default_values | Defaults: is_new_patient None, transfer False. | Build; assert. | Dataclass defaults. | In scope: schema. Out of scope: persistence. |
| 5 | | TestNewOrExistingTaskTools::test_report_new_patient | `report_new_patient` tool completes task with is_new_patient=True. | Override complete; await tool; assert. | Tool. | In scope: completion. Out of scope: prompt. |
| 6 | | TestNewOrExistingTaskTools::test_report_existing_patient | `report_existing_patient` tool completes task with is_new_patient=False. | Await tool; assert. | Tool. | In scope: completion. Out of scope: prompt. |
| 7 | | TestNewOrExistingTaskTools::test_create_transfer_result | `_create_transfer_result` builds transfer-flagged result. | Call; assert. | Helper. | In scope: helper. Out of scope: orchestration. |

### Suggested Missing Tests
- **Tool registration completeness** - assert `report_new_patient`, `report_existing_patient`, and the transfer tool are all `@function_tool` decorated.
- **Ambiguous user reply** - LLM cannot decide; ensure task does not auto-complete and asks again (retry budget).
- **Transition to next task** on success - new -> CollectName/Phone/DOB; existing -> AuthenticatePatient.
- **Async cancellation** during this task.
- **`task_name` metadata** test (parity with confirm_full_name task).

### Improvement Suggestions / Irrelevant Tests
- **`task = NewOrExistingTask.__new__(...)`** with manual `complete` injection mirrors `test_collect_dob_task.py`; lift to shared fixture in conftest.
- **No `on_enter` coverage** - if task seeds the prompt, add a corresponding test.

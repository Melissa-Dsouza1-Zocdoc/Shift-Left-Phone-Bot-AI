# Task Tests - Unit Test Mapping

> Repo: [Zocdoc/zo-agent](https://github.com/Zocdoc/zo-agent)
> Folder: `tests/` (root)
> Generated: 2026-05-07
> Stack: Python / pytest / LiveKit Agents

## test_authenticate_patient_task.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 |  | `TestAuthenticatePatientTask.test_on_enter_already_authenticated` | Test that task completes immediately if patient already authenticated. | Calls AuthenticatePatientTask, MagicMock, make_session_state. Asserts 5 conditions on result. | On enter already authenticated [async] | In scope: TestAuthenticatePatientTask. Out of scope: real LLM/network calls (mocked). |
| 2 |  | `TestAuthenticatePatientTask.test_init_stores_parameters` | Test that constructor stores all parameters correctly. | Calls AuthenticatePatientTask, MagicMock. Asserts 9 conditions on result. | Init stores parameters | In scope: TestAuthenticatePatientTask. Out of scope: real LLM/network calls (mocked). |
| 3 |  | `TestAuthenticatePatientTask.test_init_validates_invalid_phone` | Test that constructor validates phone and sets caller_phone_valid=False for invalid. | Calls AuthenticatePatientTask, MagicMock. Asserts 2 conditions on result. | Init validates invalid phone | In scope: TestAuthenticatePatientTask. Out of scope: real LLM/network calls (mocked). |
| 4 |  | `TestAuthenticatePatientTask.test_transfer_helper_creates_correct_result` | Test that _transfer creates a proper transfer result. | Calls AuthenticatePatientTask, patch.object, MagicMock. Asserts 5 conditions on result. | Transfer helper creates correct result [async] | In scope: TestAuthenticatePatientTask. Out of scope: real LLM/network calls (mocked). |
| 5 |  | `TestAuthenticatePatientTask.test_complete_success_helper_creates_correct_result` | Test that _complete_success creates a proper success result. | Calls AuthenticatePatientTask, PatientCandidate, task._complete_success. Asserts 8 conditions on result. | Complete success helper creates correct result [async] | In scope: TestAuthenticatePatientTask. Out of scope: real LLM/network calls (mocked). |
| 6 |  | `TestPatientAuthResult.test_success_result` | Test creating a success result. | Calls PatientAuthResult. Asserts 4 conditions on result. | Success result | In scope: TestPatientAuthResult. Out of scope: integration with live services. |
| 7 |  | `TestPatientAuthResult.test_transfer_result` | Test creating a transfer result. | Calls PatientAuthResult. Asserts 4 conditions on result. | Transfer result | In scope: TestPatientAuthResult. Out of scope: integration with live services. |
| 8 |  | `TestPatientAuthResult.test_default_values` | Test default values for optional fields. | Calls PatientAuthResult. Asserts 9 conditions on result. | Default values | In scope: TestPatientAuthResult. Out of scope: integration with live services. |

### Suggested Missing Tests
- **Ambiguous match (twins)** - Two patients share name + DOB — task must escalate, not pick first.
- **Soundex / nickname match** - Caller says "Bob" but record is "Robert" — verify fuzzy-match policy.
- **Failed lookup logging redacts PHI** - On failed match, log line must not echo last name + DOB in plaintext.

### Improvement Suggestions / Irrelevant Tests
- **Limited assertions** - Tests verify happy path; add a parametrized failure matrix.

## test_collect_dob_task.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 9 |  | `TestCollectDOBInterruptibility.test_is_non_interruptible` | CollectDOBTask disables interruptions at the task level. | Calls CollectDOBTask. Asserts task.allow_interruptions is False. | Is non interruptible | In scope: TestCollectDOBInterruptibility. Out of scope: integration with live services. |

### Suggested Missing Tests
- **ASR misrecognition retries** - For dob, simulate the LLM/ASR returning gibberish and ensure the task re-prompts up to N times.
- **Silence timeout** - Caller says nothing — does the task time out and hand back to base agent?
- **Barge-in during prompt** - Caller speaks while task is asking the question — interruptibility correctness.
- **PHI in transcripts/logs** - Captured value (DOB, phone, name) must be redacted in logs/metrics.

### Improvement Suggestions / Irrelevant Tests
- **Single trivial test** - Only checks `allow_interruptions` flag; the actual collection logic (validation, retry, normalization) is untested.

## test_collect_first_name_task.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 10 |  | `TestCollectFirstNameInterruptibility.test_is_non_interruptible` | CollectFirstNameTask disables interruptions at the task level. | Calls CollectFirstNameTask. Asserts task.allow_interruptions is False. | Is non interruptible | In scope: TestCollectFirstNameInterruptibility. Out of scope: integration with live services. |

### Suggested Missing Tests
- **ASR misrecognition retries** - For first_name, simulate the LLM/ASR returning gibberish and ensure the task re-prompts up to N times.
- **Silence timeout** - Caller says nothing — does the task time out and hand back to base agent?
- **Barge-in during prompt** - Caller speaks while task is asking the question — interruptibility correctness.
- **PHI in transcripts/logs** - Captured value (DOB, phone, name) must be redacted in logs/metrics.

### Improvement Suggestions / Irrelevant Tests
- **Single trivial test** - Only checks `allow_interruptions` flag; the actual collection logic (validation, retry, normalization) is untested.

## test_collect_last_name_task.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 11 |  | `TestCollectLastNameInterruptibility.test_is_non_interruptible` | CollectLastNameTask disables interruptions at the task level. | Calls CollectLastNameTask. Asserts task.allow_interruptions is False. | Is non interruptible | In scope: TestCollectLastNameInterruptibility. Out of scope: integration with live services. |

### Suggested Missing Tests
- **ASR misrecognition retries** - For last_name, simulate the LLM/ASR returning gibberish and ensure the task re-prompts up to N times.
- **Silence timeout** - Caller says nothing — does the task time out and hand back to base agent?
- **Barge-in during prompt** - Caller speaks while task is asking the question — interruptibility correctness.
- **PHI in transcripts/logs** - Captured value (DOB, phone, name) must be redacted in logs/metrics.

### Improvement Suggestions / Irrelevant Tests
- **Single trivial test** - Only checks `allow_interruptions` flag; the actual collection logic (validation, retry, normalization) is untested.

## test_collect_patient_gender_task.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 12 |  | `TestGenderResult.test_success_result` | Success result | Calls GenderResult. Asserts 3 conditions on result. | Success result | In scope: TestGenderResult. Out of scope: integration with live services. |
| 13 |  | `TestGenderResult.test_transfer_result` | Transfer result | Calls GenderResult. Asserts 3 conditions on result. | Transfer result | In scope: TestGenderResult. Out of scope: integration with live services. |
| 14 |  | `TestSubmitGender.test_valid_gender` | Valid gender | Fixtures: input_value, expected_gender. Calls _make_task, complete.assert_called_once, task.submit_gender. Asserts 3 conditions on result. | Valid gender (parametrized) [async] | In scope: TestSubmitGender. Out of scope: integration with live services. |
| 15 |  | `TestSubmitGender.test_invalid_gender` | Invalid gender | Fixtures: input_value. Calls _make_task, complete.assert_not_called, task.submit_gender. Asserts 3 conditions on result. | Invalid gender (parametrized) [async] | In scope: TestSubmitGender. Out of scope: integration with live services. |
| 16 |  | `TestCannotDetermineGender.test_completes_with_transfer` | Completes with transfer | Calls _make_task, complete.assert_called_once, task.cannot_determine_gender. Asserts 2 conditions on result. | Completes with transfer [async] | In scope: TestCannotDetermineGender. Out of scope: integration with live services. |
| 17 |  | `TestTransferToHuman.test_completes_with_transfer` | Completes with transfer | Calls _make_task, complete.assert_called_once, task.transfer_to_human. Asserts 2 conditions on result. | Completes with transfer [async] | In scope: TestTransferToHuman. Out of scope: integration with live services. |
| 18 |  | `TestCreateTransferResult.test_returns_transfer_result` | Returns transfer result | Calls _make_task, task._create_transfer_result. Asserts 2 conditions on result. | Returns transfer result | In scope: TestCreateTransferResult. Out of scope: integration with live services. |
| 19 |  | `TestTaskProperties.test_task_name` | Task name | Calls _make_task. Asserts task.task_name == 'tasks/collect-patient-gender-task'. | Task name | In scope: TestTaskProperties. Out of scope: integration with live services. |
| 20 |  | `TestTaskProperties.test_is_non_interruptible` | Is non interruptible | Calls _make_task. Asserts task.allow_interruptions is False. | Is non interruptible | In scope: TestTaskProperties. Out of scope: integration with live services. |
| 21 |  | `TestTaskProperties.test_gender_question_phrasing` | Gender question phrasing | Fixtures: is_booking_for_self, expected_substring. Calls CollectPatientGenderTask. Asserts expected_substring in task._gender_question. | Gender question phrasing (parametrized) | In scope: TestTaskProperties. Out of scope: integration with live services. |

### Suggested Missing Tests
- **ASR misrecognition retries** - For patient_gender, simulate the LLM/ASR returning gibberish and ensure the task re-prompts up to N times.
- **Silence timeout** - Caller says nothing — does the task time out and hand back to base agent?
- **Barge-in during prompt** - Caller speaks while task is asking the question — interruptibility correctness.
- **PHI in transcripts/logs** - Captured value (DOB, phone, name) must be redacted in logs/metrics.

### Improvement Suggestions / Irrelevant Tests
- **Single trivial test** - Only checks `allow_interruptions` flag; the actual collection logic (validation, retry, normalization) is untested.

## test_collect_phone_task.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 22 |  | `TestCollectPhoneInterruptibility.test_is_non_interruptible` | CollectPhoneTask disables interruptions at the task level. | Calls CollectPhoneTask. Asserts task.allow_interruptions is False. | Is non interruptible | In scope: TestCollectPhoneInterruptibility. Out of scope: integration with live services. |

### Suggested Missing Tests
- **ASR misrecognition retries** - For phone, simulate the LLM/ASR returning gibberish and ensure the task re-prompts up to N times.
- **Silence timeout** - Caller says nothing — does the task time out and hand back to base agent?
- **Barge-in during prompt** - Caller speaks while task is asking the question — interruptibility correctness.
- **PHI in transcripts/logs** - Captured value (DOB, phone, name) must be redacted in logs/metrics.

### Improvement Suggestions / Irrelevant Tests
- **Single trivial test** - Only checks `allow_interruptions` flag; the actual collection logic (validation, retry, normalization) is untested.

## test_confirm_full_name_task.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 23 |  | `TestConfirmFullNameInterruptibility.test_is_non_interruptible` | ConfirmFullNameTask disables interruptions at the task level. | Calls ConfirmFullNameTask. Asserts task.allow_interruptions is False. | Is non interruptible | In scope: TestConfirmFullNameInterruptibility. Out of scope: integration with live services. |

### Suggested Missing Tests
- **ASR misrecognition retries** - For confirm_full_name, simulate the LLM/ASR returning gibberish and ensure the task re-prompts up to N times.
- **Silence timeout** - Caller says nothing — does the task time out and hand back to base agent?
- **Barge-in during prompt** - Caller speaks while task is asking the question — interruptibility correctness.
- **PHI in transcripts/logs** - Captured value (DOB, phone, name) must be redacted in logs/metrics.

### Improvement Suggestions / Irrelevant Tests
- **Single trivial test** - Only checks `allow_interruptions` flag; the actual collection logic (validation, retry, normalization) is untested.

## test_csat_task.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 24 |  | `TestSubmitConsent.test_consent_false_completes_with_no_data` | Consent false completes with no data | Fixtures: task, context. Calls _wire_session, _get_task_result, _make_mock_session. Asserts 4 conditions on result. | Consent false completes with no data [async] | In scope: TestSubmitConsent. Out of scope: real LLM/network calls (mocked). |
| 25 |  | `TestSubmitConsent.test_consent_true_speaks_score_prompt` | Consent true speaks score prompt | Fixtures: task, context. Calls _make_mock_session, _wire_session, say.assert_called_once. Asserts 2 conditions on result. | Consent true speaks score prompt [async] | In scope: TestSubmitConsent. Out of scope: real LLM/network calls (mocked). |
| 26 |  | `TestSubmitScore.test_valid_score_stores_and_speaks_feedback` | Valid score stores and speaks feedback | Fixtures: task, context. Calls _make_mock_session, _wire_session, say.assert_called_once. Asserts 2 conditions on result. | Valid score stores and speaks feedback [async] | In scope: TestSubmitScore. Out of scope: real LLM/network calls (mocked). |
| 27 |  | `TestSubmitScore.test_invalid_score_returns_error` | Invalid score returns error | Fixtures: task, context. Calls _wire_session, _make_mock_session, task.submit_score. Asserts 3 conditions on result. | Invalid score returns error [async] | In scope: TestSubmitScore. Out of scope: real LLM/network calls (mocked). |
| 28 |  | `TestSubmitScore.test_second_invalid_score_completes` | Second invalid score completes | Fixtures: task, context. Calls _wire_session, _get_task_result, _make_mock_session. Asserts 3 conditions on result. | Second invalid score completes [async] | In scope: TestSubmitScore. Out of scope: real LLM/network calls (mocked). |
| 29 |  | `TestSubmitScore.test_score_without_consent_returns_error` | Score without consent returns error | Fixtures: task, context. Calls _wire_session, _make_mock_session, task.submit_score. Asserts 2 conditions on result. | Score without consent returns error [async] | In scope: TestSubmitScore. Out of scope: real LLM/network calls (mocked). |
| 30 |  | `TestSubmitFeedback.test_submits_to_api_and_completes` | Submits to api and completes | Fixtures: task, context. Calls _wire_session, _get_task_result, _make_mock_session. Asserts 4 conditions on result. | Submits to api and completes [async] | In scope: TestSubmitFeedback. Out of scope: real LLM/network calls (mocked). |
| 31 |  | `TestSubmitFeedback.test_null_feedback_still_submits` | Null feedback still submits | Fixtures: task, context. Calls _wire_session, _get_task_result, _make_mock_session. Asserts 4 conditions on result. | Null feedback still submits [async] | In scope: TestSubmitFeedback. Out of scope: real LLM/network calls (mocked). |
| 32 |  | `TestSubmitFeedback.test_feedback_without_stars_returns_error` | Feedback without stars returns error | Fixtures: task, context. Calls _wire_session, _make_mock_session, task.submit_feedback. Asserts 2 conditions on result. | Feedback without stars returns error [async] | In scope: TestSubmitFeedback. Out of scope: real LLM/network calls (mocked). |
| 33 |  | `TestSubmitFeedback.test_api_failure_completes_with_submitted_false` | Api failure completes with submitted false | Fixtures: task, context. Calls _wire_session, _get_task_result, _make_mock_session. Asserts 3 conditions on result. | Api failure completes with submitted false [async] | In scope: TestSubmitFeedback. Out of scope: real LLM/network calls (mocked). |
| 34 |  | `TestCSATCollectedEvent.test_to_dict_with_feedback` | To dict with feedback | Calls CSATCollectedEvent, event.to_dict, datetime. Asserts 4 conditions on result. | To dict with feedback | In scope: TestCSATCollectedEvent. Out of scope: integration with live services. |
| 35 |  | `TestCSATCollectedEvent.test_to_dict_without_feedback` | To dict without feedback | Calls CSATCollectedEvent, event.to_dict, datetime. Asserts 2 conditions on result. | To dict without feedback | In scope: TestCSATCollectedEvent. Out of scope: integration with live services. |
| 36 |  | `TestUnifiedNewAppointmentCSATIntegration.test_end_conversation_invokes_csat_when_booking_confirmed` | End conversation invokes csat when booking confirmed | Fixtures: mocker. Calls _make_agent_with_session, mocker.patch, _make_context. Verifies behavior via mocks/return value. | End conversation invokes csat when booking confirmed [async] | In scope: TestUnifiedNewAppointmentCSATIntegration. Out of scope: real LLM/network calls (mocked). |
| 37 |  | `TestUnifiedNewAppointmentCSATIntegration.test_end_conversation_skips_csat_when_not_confirmed` | End conversation skips csat when not confirmed | Fixtures: mocker. Calls _make_agent_with_session, mocker.patch, _make_context. Verifies behavior via mocks/return value. | End conversation skips csat when not confirmed [async] | In scope: TestUnifiedNewAppointmentCSATIntegration. Out of scope: real LLM/network calls (mocked). |
| 38 |  | `TestUnifiedNewAppointmentCSATIntegration.test_end_conversation_skips_csat_when_practice_not_enabled` | End conversation skips csat when practice not enabled | Fixtures: mocker. Calls _make_agent_with_session, mocker.patch, _make_context. Verifies behavior via mocks/return value. | End conversation skips csat when practice not enabled [async] | In scope: TestUnifiedNewAppointmentCSATIntegration. Out of scope: real LLM/network calls (mocked). |
| 39 |  | `TestUnifiedManageAppointmentCSATIntegration.test_end_conversation_invokes_csat_when_goal_accomplished` | End conversation invokes csat when goal accomplished | Fixtures: mocker. Calls _make_agent_with_session, mocker.patch, _make_context. Verifies behavior via mocks/return value. | End conversation invokes csat when goal accomplished [async] | In scope: TestUnifiedManageAppointmentCSATIntegration. Out of scope: real LLM/network calls (mocked). |
| 40 |  | `TestUnifiedManageAppointmentCSATIntegration.test_end_conversation_skips_csat_when_goal_not_accomplished` | End conversation skips csat when goal not accomplished | Fixtures: mocker. Calls _make_agent_with_session, mocker.patch, _make_context. Asserts agent._goal_accomplished is False. | End conversation skips csat when goal not accomplished [async] | In scope: TestUnifiedManageAppointmentCSATIntegration. Out of scope: real LLM/network calls (mocked). |
| 41 |  | `TestUnifiedManageAppointmentCSATIntegration.test_end_conversation_skips_csat_when_practice_not_enabled` | End conversation skips csat when practice not enabled | Fixtures: mocker. Calls _make_agent_with_session, mocker.patch, _make_context. Verifies behavior via mocks/return value. | End conversation skips csat when practice not enabled [async] | In scope: TestUnifiedManageAppointmentCSATIntegration. Out of scope: real LLM/network calls (mocked). |
| 42 |  | `TestUnifiedManageAppointmentCSATIntegration.test_goal_accomplished_defaults_to_false` | Goal accomplished defaults to false | Calls UnifiedManageAppointmentAgent. Asserts agent._goal_accomplished is False. | Goal accomplished defaults to false | In scope: TestUnifiedManageAppointmentCSATIntegration. Out of scope: integration with live services. |
| 43 |  | `TestUnifiedManageAppointmentCSATIntegration.test_cancel_commit_sets_goal_accomplished` | cancel_appointment with confirm=True and speak directive sets _goal_accomplished. | Calls _make_agent_with_session, MagicMock, AsyncMock. Asserts 2 conditions on result. | Cancel commit sets goal accomplished [async] | In scope: TestUnifiedManageAppointmentCSATIntegration. Out of scope: real LLM/network calls (mocked). |
| 44 |  | `TestUnifiedManageAppointmentCSATIntegration.test_cancel_preview_does_not_set_goal_accomplished` | cancel_appointment with confirm=False does NOT set _goal_accomplished. | Calls _make_agent_with_session, MagicMock, AsyncMock. Asserts agent._goal_accomplished is False. | Cancel preview does not set goal accomplished [async] | In scope: TestUnifiedManageAppointmentCSATIntegration. Out of scope: real LLM/network calls (mocked). |
| 45 |  | `TestUnifiedManageAppointmentCSATIntegration.test_reschedule_commit_sets_goal_accomplished` | reschedule_appointment with confirm=True and speak directive sets _goal_accomplished. | Calls _make_agent_with_session, MagicMock, AsyncMock. Asserts 2 conditions on result. | Reschedule commit sets goal accomplished [async] | In scope: TestUnifiedManageAppointmentCSATIntegration. Out of scope: real LLM/network calls (mocked). |
| 46 |  | `TestUnifiedManageAppointmentCSATIntegration.test_cancel_transfer_does_not_set_goal_accomplished` | cancel_appointment with transfer directive does NOT set _goal_accomplished. | Calls _make_agent_with_session, MagicMock, AsyncMock. Asserts agent._goal_accomplished is False. | Cancel transfer does not set goal accomplished [async] | In scope: TestUnifiedManageAppointmentCSATIntegration. Out of scope: real LLM/network calls (mocked). |

### Suggested Missing Tests
- **Patient hangs up mid-CSAT** - Verify partial CSAT data is still persisted.
- **Numeric vs verbal score** - Caller says "really good" — does the task map to a numeric score?

### Improvement Suggestions / Irrelevant Tests
- **Sparse CSAT coverage** - Only 3 tests for a customer-facing satisfaction probe.

## test_resolve_booking_issue_task.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 47 |  | `TestChangeLocation.test_clears_location_state` | change_location clears state.location_id. | Fixtures: task. Calls _make_context, task.change_location. Asserts context.session.userdata.location_id is None. | Clears location state [async] | In scope: TestChangeLocation. Out of scope: integration with live services. |
| 48 |  | `TestChangeLocation.test_completes_with_change_location` | change_location completes with CHANGE_LOCATION. | Fixtures: task, context. Calls complete.assert_called_once_with, task.change_location. Verifies behavior via mocks/return value. | Completes with change location [async] | In scope: TestChangeLocation. Out of scope: real LLM/network calls (mocked). |
| 49 |  | `TestChangeProvider.test_clears_provider_state` | change_provider clears state.provider and state.provider_id. | Fixtures: task. Calls _make_context, task.change_provider. Asserts 2 conditions on result. | Clears provider state [async] | In scope: TestChangeProvider. Out of scope: integration with live services. |
| 50 |  | `TestChangeProvider.test_completes_with_change_provider` | change_provider completes with CHANGE_PROVIDER. | Fixtures: task, context. Calls complete.assert_called_once_with, task.change_provider. Verifies behavior via mocks/return value. | Completes with change provider [async] | In scope: TestChangeProvider. Out of scope: real LLM/network calls (mocked). |
| 51 |  | `TestRevertLocation.test_updates_location_state` | revert_location updates state.location_id. | Fixtures: task. Calls _make_context, patch, task.revert_location. Asserts context.session.userdata.location_id == 'loc_001'. | Updates location state [async] | In scope: TestRevertLocation. Out of scope: real LLM/network calls (mocked). |
| 52 |  | `TestRevertLocation.test_when_validation_passes_completes_revert_success` | revert_location completing validation → complete(REVERT_SUCCESS). | Fixtures: task, context. Calls complete.assert_called_once_with, patch, task.revert_location. Verifies behavior via mocks/return value. | When validation passes completes revert success [async] | In scope: TestRevertLocation. Out of scope: real LLM/network calls (mocked). |
| 53 |  | `TestRevertLocation.test_when_validation_passes_clears_previous_fields` | Successful revert clears previous_location_id. | Fixtures: task. Calls _make_context, patch, task.revert_location. Asserts context.session.userdata.previous_location_id is None. | When validation passes clears previous fields [async] | In scope: TestRevertLocation. Out of scope: real LLM/network calls (mocked). |
| 54 |  | `TestRevertLocation.test_when_validation_fails_does_not_complete` | revert_location with failing validation does not complete the task. | Fixtures: task, context. Calls complete.assert_not_called, patch, task.revert_location. Verifies behavior via mocks/return value. | When validation fails does not complete [async] | In scope: TestRevertLocation. Out of scope: real LLM/network calls (mocked). |
| 55 |  | `TestRevertLocation.test_when_validation_fails_returns_message_string` | revert_location with failing validation returns a string for the LLM. | Fixtures: task, context. Calls patch, task.revert_location. Asserts isinstance(result, str). | When validation fails returns message string [async] | In scope: TestRevertLocation. Out of scope: real LLM/network calls (mocked). |
| 56 |  | `TestRevertLocation.test_when_revert_count_exceeded_transfers` | When revert count exceeds limit, completes with TRANSFER_REQUESTED. | Fixtures: task, context. Calls complete.assert_called_once_with, patch, task.revert_location. Verifies behavior via mocks/return value. | When revert count exceeded transfers [async] | In scope: TestRevertLocation. Out of scope: real LLM/network calls (mocked). |
| 57 |  | `TestRevertProvider.test_updates_provider_state` | revert_provider updates state.provider and state.provider_id. | Fixtures: task. Calls _make_context, patch, task.revert_provider. Asserts 2 conditions on result. | Updates provider state [async] | In scope: TestRevertProvider. Out of scope: real LLM/network calls (mocked). |
| 58 |  | `TestRevertProvider.test_when_validation_passes_completes_revert_success` | revert_provider completing validation → complete(REVERT_SUCCESS). | Fixtures: task, context. Calls complete.assert_called_once_with, patch, task.revert_provider. Verifies behavior via mocks/return value. | When validation passes completes revert success [async] | In scope: TestRevertProvider. Out of scope: real LLM/network calls (mocked). |
| 59 |  | `TestRevertProvider.test_when_validation_passes_clears_previous_fields` | Successful revert clears previous_provider and previous_provider_id. | Fixtures: task. Calls _make_context, patch, task.revert_provider. Asserts 2 conditions on result. | When validation passes clears previous fields [async] | In scope: TestRevertProvider. Out of scope: real LLM/network calls (mocked). |
| 60 |  | `TestRevertProvider.test_when_validation_fails_does_not_complete` | revert_provider with failing validation does not complete the task. | Fixtures: task, context. Calls complete.assert_not_called, patch, task.revert_provider. Verifies behavior via mocks/return value. | When validation fails does not complete [async] | In scope: TestRevertProvider. Out of scope: real LLM/network calls (mocked). |
| 61 |  | `TestRevertProvider.test_when_validation_fails_returns_message_string` | revert_provider with failing validation returns a string for the LLM. | Fixtures: task, context. Calls patch, task.revert_provider. Asserts isinstance(result, str). | When validation fails returns message string [async] | In scope: TestRevertProvider. Out of scope: real LLM/network calls (mocked). |
| 62 |  | `TestRevertProvider.test_when_revert_count_exceeded_transfers` | When revert count exceeds limit, completes with TRANSFER_REQUESTED. | Fixtures: task, context. Calls complete.assert_called_once_with, patch, task.revert_provider. Verifies behavior via mocks/return value. | When revert count exceeded transfers [async] | In scope: TestRevertProvider. Out of scope: real LLM/network calls (mocked). |

### Suggested Missing Tests
- **Setup-only file** - No test functions — task is responsible for unhappy-path resolution and must be tested.

### Improvement Suggestions / Irrelevant Tests
- **Empty file** - No tests detected.

## test_language_switch_task.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 63 |  | `TestLanguageSwitchInterruptibility.test_is_non_interruptible` | LanguageSwitchTask disables interruptions at the task level. | Calls _make_task. Asserts task.allow_interruptions is False. | Is non interruptible | In scope: TestLanguageSwitchInterruptibility. Out of scope: integration with live services. |
| 64 |  | `TestLanguageSwitchResult.test_create_transfer_result` | Create transfer result | Calls _make_task, task._create_transfer_result. Asserts 2 conditions on result. | Create transfer result | In scope: TestLanguageSwitchResult. Out of scope: integration with live services. |
| 65 |  | `TestIVRDevBypass.test_dev_mode_switches_to_opposite_language` | Dev mode switches to opposite language | Fixtures: current, target, target_name. Calls _make_task, _make_mock_session, _wire_session. Asserts 2 conditions on result. | Dev mode switches to opposite language (parametrized) [async] | In scope: TestIVRDevBypass. Out of scope: real LLM/network calls (mocked). |
| 66 |  | `TestIVRFlow.test_press_1_selects_english` | Press 1 selects english | Calls _make_task, _make_mock_session, _wire_session. Asserts 2 conditions on result. | Press 1 selects english [async] | In scope: TestIVRFlow. Out of scope: real LLM/network calls (mocked). |
| 67 |  | `TestIVRFlow.test_press_2_selects_spanish` | Press 2 selects spanish | Calls _make_task, _make_mock_session, _wire_session. Asserts 2 conditions on result. | Press 2 selects spanish [async] | In scope: TestIVRFlow. Out of scope: real LLM/network calls (mocked). |
| 68 |  | `TestIVRFlow.test_press_1_when_already_english_no_switch` | Press 1 when already english no switch | Calls _make_task, _make_mock_session, _wire_session. Asserts 2 conditions on result. | Press 1 when already english no switch [async] | In scope: TestIVRFlow. Out of scope: real LLM/network calls (mocked). |
| 69 |  | `TestIVRFlow.test_invalid_digit_then_valid` | Invalid digit triggers retry, then valid digit succeeds. | Calls _make_task, _make_mock_session, _wire_session. Asserts 3 conditions on result. | Invalid digit then valid [async] | In scope: TestIVRFlow. Out of scope: real LLM/network calls (mocked). |
| 70 |  | `TestIVRFlow.test_timeout_retries_then_succeeds` | Timeout on first attempt, valid digit on second. | Calls _make_task, _make_mock_session, _wire_session. Asserts 2 conditions on result. | Timeout retries then succeeds [async] | In scope: TestIVRFlow. Out of scope: real LLM/network calls (mocked). |
| 71 |  | `TestIVRFlow.test_max_retries_exhausted_continues_current_language` | After MAX_RETRIES timeouts, stays in current language. | Calls _make_task, _make_mock_session, _wire_session. Asserts 3 conditions on result. | Max retries exhausted continues current language [async] | In scope: TestIVRFlow. Out of scope: real LLM/network calls (mocked). |
| 72 |  | `TestIVRFlow.test_max_retries_exhausted_spanish_continues_spanish` | After MAX_RETRIES timeouts when in Spanish, continues in Spanish. | Calls _make_task, _make_mock_session, _wire_session. Asserts 2 conditions on result. | Max retries exhausted spanish continues spanish [async] | In scope: TestIVRFlow. Out of scope: real LLM/network calls (mocked). |
| 73 |  | `TestDtmfHandler.test_on_dtmf_puts_digit_in_queue` | On dtmf puts digit in queue | Calls _make_task, rtc.SipDTMF, task._on_dtmf. Asserts 2 conditions on result. | On dtmf puts digit in queue | In scope: TestDtmfHandler. Out of scope: integration with live services. |
| 74 |  | `TestShouldBypassDtmfForDev.test_non_dev_env_never_bypasses` | Any non-dev environment disables the bypass even without SIP participant. | Calls patch, _should_bypass_dtmf_for_dev, self._mock_config. Asserts _should_bypass_dtmf_for_dev() is False. | Non dev env never bypasses | In scope: TestShouldBypassDtmfForDev. Out of scope: real LLM/network calls (mocked). |
| 75 |  | `TestShouldBypassDtmfForDev.test_dev_env_with_real_sip_participant_does_not_bypass` | Structural safeguard: if a real SIP participant is connected, do not bypass even when app_env is 'dev'. | Calls patch, _should_bypass_dtmf_for_dev, self._mock_config. Asserts _should_bypass_dtmf_for_dev() is False. | Dev env with real sip participant does not bypass | In scope: TestShouldBypassDtmfForDev. Out of scope: real LLM/network calls (mocked). |
| 76 |  | `TestShouldBypassDtmfForDev.test_dev_env_without_sip_participant_bypasses` | In dev mode with only STANDARD participants (playground/console), bypass. | Calls patch, _should_bypass_dtmf_for_dev, self._mock_config. Asserts _should_bypass_dtmf_for_dev() is True. | Dev env without sip participant bypasses | In scope: TestShouldBypassDtmfForDev. Out of scope: real LLM/network calls (mocked). |
| 77 |  | `TestShouldBypassDtmfForDev.test_runtime_error_from_job_context_returns_false` | When get_job_context() raises RuntimeError (no active job), return False. | Calls patch, _should_bypass_dtmf_for_dev, self._mock_config. Asserts _should_bypass_dtmf_for_dev() is False. | Runtime error from job context returns false | In scope: TestShouldBypassDtmfForDev. Out of scope: real LLM/network calls (mocked). |

### Suggested Missing Tests
- **Mid-task language switch** - Caller switches language while collecting DOB — verify task restarts in the new language.
- **Fallback on unsupported language** - Detect Mandarin when only ES/EN are supported.

### Improvement Suggestions / Irrelevant Tests
- **Locale code normalization** - Inputs assume `en`/`es`; confirm `en-US` and `es-MX` are normalized.

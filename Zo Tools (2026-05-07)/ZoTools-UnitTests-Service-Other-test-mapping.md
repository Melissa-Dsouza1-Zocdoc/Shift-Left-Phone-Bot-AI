# ZoTools.UnitTests/Service (Other) - Unit Test Mapping

> Repo: [Zocdoc/zo-tools](https://github.com/Zocdoc/zo-tools)
> Folder: `tests/ZoTools.UnitTests/Service/`
> Generated: 2026-05-07
>
> This file covers Service-layer unit tests outside the Booking and Insurance domains.
> See [ZoTools-UnitTests-Service-Booking-test-mapping.md](ZoTools-UnitTests-Service-Booking-test-mapping.md) and [ZoTools-UnitTests-Service-Insurance-test-mapping.md](ZoTools-UnitTests-Service-Insurance-test-mapping.md) for those.
>
> Note: With ~1000 tests across 34 files, this mapping uses concise rows. Test method names are descriptive enough that "What It Tests" mirrors the name; "Steps" is summarized. Refer to the source file for full assertions. Each section caps at 60 representative method rows; parameterized [TestCase] cases are collapsed into their parent method.

## AbServiceTests.cs

Source: `tests/ZoTools.UnitTests/Service/AbServiceTests.cs` &middot; 38 test attribute(s) detected.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | IsOn_WhenAssignmentIsOn_ReturnsTrue | is on when assignment is on returns true | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 2 | | IsOn_WhenAssignmentIsOff_ReturnsFalse | is on when assignment is off returns false | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 3 | | IsOn_WhenAssignmentIsOnLowercase_ReturnsTrue | is on when assignment is on lowercase returns true | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 4 | | IsOn_WhenGetAssignmentThrows_ReturnsFalse | is on when get assignment throws returns false | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 5 | | IsOn_WhenGetAssignmentThrows_DoesNotRethrow | is on when get assignment throws does not rethrow | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 6 | | IsOn_StripsPlusCharacterFromVisitorId | is on strips plus character from visitor id | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 7 | | IsOn_DoesNotStripWhitespace | is on does not strip whitespace | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 8 | | IsOn_DoesNotStripSpecialCharacters | is on does not strip special characters | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 9 | | IsOn_WhenVisitorIdIsNull_Throws | is on when visitor id is null throws | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 10 | | IsOn_WhenVisitorIdIsWhitespace_Throws | is on when visitor id is whitespace throws | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 11 | | IsOn_PassesFeatureFlagToStringAsExperimentId | is on passes feature flag to string as experiment id | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 12 | | IsOn_DefaultVisitorTypeIsPracticeId | is on default visitor type is practice id | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 13 | | IsOn_PassesNonDefaultVisitorType | is on passes non default visitor type | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 14 | | IsOn_DefaultAssignmentIsOff | is on default assignment is off | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 15 | | IsOn_RecordsTimerMetric | is on records timer metric | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 16 | | IsOn_LogsFeatureFlagAndAssignmentOnSuccess | is on logs feature flag and assignment on success | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 17 | | IsOn_WhenGetAssignmentThrows_LogsError | is on when get assignment throws logs error | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 18 | | IsOnString_WhenAssignmentIsOn_ReturnsTrue | is on string when assignment is on returns true | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 19 | | IsOnString_WhenAssignmentIsOff_ReturnsFalse | is on string when assignment is off returns false | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 20 | | IsOnString_WhenFeatureFlagNameIsNull_Throws | is on string when feature flag name is null throws | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 21 | | IsOnString_WhenFeatureFlagNameIsWhitespace_Throws | is on string when feature flag name is whitespace throws | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 22 | | IsOnString_WhenVisitorIdIsNull_Throws | is on string when visitor id is null throws | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 23 | | IsOnString_WhenVisitorIdIsWhitespace_Throws | is on string when visitor id is whitespace throws | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 24 | | IsOnString_PassesFeatureFlagNameAsExperimentId | is on string passes feature flag name as experiment id | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 25 | | IsOnString_StripsPlusCharacterFromVisitorId | is on string strips plus character from visitor id | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 26 | | IsOnString_DoesNotStripWhitespace | is on string does not strip whitespace | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 27 | | IsOnString_DoesNotStripSpecialCharacters | is on string does not strip special characters | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 28 | | GetVariant_ReturnsAssignmentFromAbClient | get variant returns assignment from ab client | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 29 | | GetVariant_WhenGetAssignmentThrows_ReturnsEmptyString | get variant when get assignment throws returns empty string | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 30 | | GetVariant_WhenVisitorIdIsNull_Throws | get variant when visitor id is null throws | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 31 | | GetVariant_WhenVisitorIdIsWhitespace_Throws | get variant when visitor id is whitespace throws | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 32 | | GetVariant_StripsPlusCharacterFromVisitorId | get variant strips plus character from visitor id | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 33 | | GetVariant_DoesNotStripWhitespace | get variant does not strip whitespace | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 34 | | GetVariant_DoesNotStripSpecialCharacters | get variant does not strip special characters | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 35 | | GetVariant_PassesExperimentNameAsExperimentId | get variant passes experiment name as experiment id | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 36 | | GetVariant_DefaultAssignmentIsEmptyString | get variant default assignment is empty string | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 37 | | GetVariant_RecordsTimerMetric | get variant records timer metric | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 38 | | GetVariant_WhenGetAssignmentThrows_LogsError | get variant when get assignment throws logs error | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |

### Suggested Missing Tests
- **Cache fallback when AB service is unreachable** - Tests assume AB lookups succeed; no test for transient downstream failure or stale-cache fallback behavior.
- **Concurrent flag flips** - No test for what happens when a flag is read while being invalidated.
- **Telemetry on flag misses** - Verify that an unknown flag emits a metric (helps detect typos/dead flags).

### Improvement Suggestions / Irrelevant Tests
- **Heavy parameterization with shallow assertions** - Many TestCases assert only the boolean result; consider snapshotting the full decision context.

## AuditLoggerServiceTests.cs

Source: `tests/ZoTools.UnitTests/Service/AuditLoggerServiceTests.cs` &middot; 34 test attribute(s) detected.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | LogExternalPatientAccess_CallsAuditLoggerWithCorrectParameters | log external patient access calls audit logger with correct parameters | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 2 | | LogExternalPatientAccess_WhenUnauthorized_CallsAuditLoggerWithFailedOutcome | log external patient access when unauthorized calls audit logger with failed outcome | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 3 | | LogExternalPatientAccess_WhenExceptionThrown_DoesNotThrow | log external patient access when exception thrown does not throw | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 4 | | LogExternalPatientAccess_RecordsSuccessMetric | log external patient access records success metric | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 5 | | LogExternalPatientAccess_WhenExceptionThrown_RecordsErrorMetric | log external patient access when exception thrown records error metric | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 6 | | LogExternalAppointmentRead_CallsAuditLoggerWithCorrectParameters | log external appointment read calls audit logger with correct parameters | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 7 | | LogExternalAppointmentRead_WhenUnauthorized_CallsAuditLoggerWithFailedOutcome | log external appointment read when unauthorized calls audit logger with failed outcome | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 8 | | LogExternalAppointmentRead_WhenExceptionThrown_DoesNotThrow | log external appointment read when exception thrown does not throw | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 9 | | LogExternalAppointmentRead_RecordsSuccessMetric | log external appointment read records success metric | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 10 | | LogExternalAppointmentRead_WhenExceptionThrown_RecordsErrorMetric | log external appointment read when exception thrown records error metric | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 11 | | LogExternalAppointmentCancellation_WithAuthorization_LogsCorrectly | log external appointment cancellation with authorization logs correctly | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 12 | | LogExternalAppointmentCancellation_WithoutAuthorization_LogsAuthorizationFailed | log external appointment cancellation without authorization logs authorization failed | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 13 | | LogExternalAppointmentCancellation_WhenCalled_DoesNotThrow | log external appointment cancellation when called does not throw | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 14 | | LogExternalAppointmentCancellation_WhenCalled_RecordsSuccessMetric | log external appointment cancellation when called records success metric | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 15 | | LogExternalAppointmentCancellation_WhenExceptionThrown_RecordsErrorMetric | log external appointment cancellation when exception thrown records error metric | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 16 | | LogExternalAppointmentReschedule_WithAuthorization_LogsCorrectly | log external appointment reschedule with authorization logs correctly | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 17 | | LogExternalAppointmentReschedule_WithoutAuthorization_LogsAuthorizationFailed | log external appointment reschedule without authorization logs authorization failed | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 18 | | LogExternalAppointmentReschedule_WhenCalled_DoesNotThrow | log external appointment reschedule when called does not throw | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 19 | | LogExternalAppointmentReschedule_WhenCalled_RecordsSuccessMetric | log external appointment reschedule when called records success metric | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 20 | | LogExternalAppointmentReschedule_WhenExceptionThrown_RecordsErrorMetric | log external appointment reschedule when exception thrown records error metric | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 21 | | LogExternalAppointmentBooked_WithAuthorization_LogsCorrectly | log external appointment booked with authorization logs correctly | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 22 | | LogExternalAppointmentBooked_WithoutAuthorization_LogsAuthorizationFailed | log external appointment booked without authorization logs authorization failed | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 23 | | LogExternalAppointmentBooked_WhenExceptionThrown_DoesNotThrow | log external appointment booked when exception thrown does not throw | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 24 | | LogExternalAppointmentBooked_WhenCalled_RecordsSuccessMetric | log external appointment booked when called records success metric | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 25 | | LogExternalAppointmentBooked_WhenExceptionThrown_RecordsErrorMetric | log external appointment booked when exception thrown records error metric | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 26 | | LogCallSessionRead_CallsAuditLoggerWithCorrectParameters | log call session read calls audit logger with correct parameters | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 27 | | LogCallSessionRead_WhenUnauthorized_LogsAuthorizationFailed | log call session read when unauthorized logs authorization failed | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 28 | | LogCallSessionRead_WhenExceptionThrown_DoesNotThrow | log call session read when exception thrown does not throw | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 29 | | LogCallSessionRead_RecordsSuccessMetric | log call session read records success metric | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 30 | | LogCallSessionRead_WhenExceptionThrown_RecordsErrorMetric | log call session read when exception thrown records error metric | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 31 | | LogCallSessionWrite_CallsAuditLoggerWithCorrectParameters | log call session write calls audit logger with correct parameters | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 32 | | LogCallSessionWrite_WhenExceptionThrown_DoesNotThrow | log call session write when exception thrown does not throw | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 33 | | LogCallSessionWrite_RecordsSuccessMetric | log call session write records success metric | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 34 | | LogCallSessionWrite_WhenExceptionThrown_RecordsErrorMetric | log call session write when exception thrown records error metric | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |

### Suggested Missing Tests
- **Failure to write audit log path** - No test for what the service does if the audit sink throws (drop? rethrow? buffer?). AUTH-005 requires this be deterministic.
- **PII/PHI redaction in payloads** - No test that PHI fields in audit payloads are scrubbed before persistence.
- **Async fire-and-forget shutdown** - If the logger is async, no test for in-flight requests during application shutdown.

### Improvement Suggestions / Irrelevant Tests
- **Mocked sink** - Per TEST-002, prefer an in-memory fake that records audit entries and exposes them for assertion.

## CsatServiceTests.cs

Source: `tests/ZoTools.UnitTests/Service/CsatServiceTests.cs` &middot; 19 test attribute(s) detected.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | SubmitCsatScore_ResolvesAndCallsPhoneBot | submit csat score resolves and calls phone bot | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 2 | | SubmitCsatScore_NullPracticeId_ThrowsCsatSubmissionException | submit csat score null practice id throws csat submission exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 3 | | SubmitCsatScore_EmptyPracticeId_ThrowsCsatSubmissionException | submit csat score empty practice id throws csat submission exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 4 | | SubmitCsatScore_NullPracticeId_DoesNotCallPhoneBot | submit csat score null practice id does not call phone bot | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 5 | | SubmitCsatScore_NullPracticeInfoResponse_ThrowsCsatSubmissionException | submit csat score null practice info response throws csat submission exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 6 | | SubmitCsatScore_InvalidScore_ThrowsCsatSubmissionException | submit csat score invalid score throws csat submission exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 7 | | SubmitCsatScore_BoundaryScore_DoesNotThrow | submit csat score boundary score does not throw | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 8 | | SubmitCsatScore_WithFeedback_PassesFeedbackToPhoneBot | submit csat score with feedback passes feedback to phone bot | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 9 | | SubmitCsatScore_WithNullFeedback_PassesNullToPhoneBot | submit csat score with null feedback passes null to phone bot | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 10 | | SubmitCsatScore_RecordsMetricWithScoreTag | submit csat score records metric with score tag | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 11 | | SubmitCsatScore_ResolvesPracticeInfoByPhoneNumber | submit csat score resolves practice info by phone number | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 12 | | SubmitCsatScore_PassesCorrectCallIdAndZdToken | submit csat score passes correct call id and zd token | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 13 | | SubmitCsatScore_InvalidScore_RecordsFailedMetric | submit csat score invalid score records failed metric | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 14 | | SubmitCsatScore_PracticeNotFound_RecordsFailedMetric | submit csat score practice not found records failed metric | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 15 | | SubmitCsatScore_RepositoryError_RecordsFailedMetric | submit csat score repository error records failed metric | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 16 | | SubmitCsatScore_RepositoryError_DoesNotRecordSuccessMetric | submit csat score repository error does not record success metric | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |

### Suggested Missing Tests
- **Out-of-range scores** - No test for scores below min or above max bounds.
- **Duplicate submissions for same call** - No test for idempotency or update-vs-insert semantics.
- **Free-text comment with PHI** - No test that comments are stored without inadvertent PHI extraction.

### Improvement Suggestions / Irrelevant Tests
- No specific concerns flagged; consider snapshotting (CS-006) where message contracts matter.

## GenerateIdServiceTests.cs

Source: `tests/ZoTools.UnitTests/Service/GenerateIdServiceTests.cs` &middot; 5 test attribute(s) detected.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | GenerateUniquePatientId_StartsWithExpectedPrefix | generate unique patient id starts with expected prefix | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 2 | | GenerateUniquePatientId_SuffixIsExactly22Characters | generate unique patient id suffix is exactly22characters | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 3 | | GenerateUniquePatientId_ContainsOnlyUrlSafeCharacters | generate unique patient id contains only url safe characters | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 4 | | GenerateUniquePatientId_ReturnsDifferentValueOnEachCall | generate unique patient id returns different value on each call | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 5 | | GenerateUniquePatientId_NeverReturnsNullOrEmpty | generate unique patient id never returns null or empty | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |

### Suggested Missing Tests
- **Collision behavior** - No test for collision retry when the random source repeats (mock the RNG).
- **ID format validation** - No test verifying the produced ID matches the documented format/length/charset.

### Improvement Suggestions / Irrelevant Tests
- **5 tests is thin** for an ID generator; add property-based tests over many random inputs.

## InteropPlatformServiceTests.cs

Source: `tests/ZoTools.UnitTests/Service/InteropPlatformServiceTests.cs` &middot; 18 test attribute(s) detected.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | SearchPatients_ReturnsResultsFromClient | search patients returns results from client | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 2 | | SearchPatients_WhenClientReturnsError_ThrowsException | search patients when client returns error throws exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 3 | | SearchPatients_WhenClientThrowsException_PropagatesException | search patients when client throws exception propagates exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 4 | | GetAppointments_ReturnsResultsFromClient | get appointments returns results from client | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 5 | | GetAppointments_WhenClientReturnsError_ThrowsException | get appointments when client returns error throws exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 6 | | GetAppointments_WhenClientThrowsException_PropagatesException | get appointments when client throws exception propagates exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 7 | | GetAppointments_WithLocationIds_PassesLocationIdsAndNoPracticeId | get appointments with location ids passes location ids and no practice id | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 8 | | GetAppointments_WithNullLocationIds_PassesPracticeIdAndNoLocationIds | get appointments with null location ids passes practice id and no location ids | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 9 | | GetAppointments_WithEmptyLocationIds_PassesPracticeIdAndNoLocationIds | get appointments with empty location ids passes practice id and no location ids | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 10 | | RescheduleAppointment_ReturnsResponseFromClient | reschedule appointment returns response from client | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 11 | | RescheduleAppointment_WhenClientReturnsError_ThrowsInteropRescheduleException | reschedule appointment when client returns error throws interop reschedule exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 12 | | RescheduleAppointment_WhenClientReturnsError_ExceptionContainsError | reschedule appointment when client returns error exception contains error | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 13 | | RescheduleAppointment_WhenClientThrowsException_PropagatesException | reschedule appointment when client throws exception propagates exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 14 | | RescheduleAppointment_TimeSlotNotAvailable_ThrowsWithAvailabilityNotFoundReason | reschedule appointment time slot not available throws with availability not found reason | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 15 | | RescheduleAppointment_PatientRuleException_ThrowsWithPatientRuleExceptionReason | reschedule appointment patient rule exception throws with patient rule exception reason | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 16 | | RescheduleAppointment_ProviderNotMappedInSync_ThrowsWithProviderNotMappedInSyncReason | reschedule appointment provider not mapped in sync throws with provider not mapped in sync reason | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 17 | | RescheduleAppointment_UnknownError_ThrowsWithUnknownReason | reschedule appointment unknown error throws with unknown reason | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 18 | | RescheduleAppointment_PassesParametersInCorrectOrderToClient | reschedule appointment passes parameters in correct order to client | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |

### Suggested Missing Tests
- **HTTP 5xx / timeout from interop platform** - No test for downstream failure modes.
- **Auth token expiry mid-request** - No test for refresh flow.
- **Malformed response payload** - No test for partial JSON / unexpected schema.

### Improvement Suggestions / Irrelevant Tests
- No specific concerns flagged; consider snapshotting (CS-006) where message contracts matter.

## LocationDataProviderTests.cs

Source: `tests/ZoTools.UnitTests/Service/LocationDataProviderTests.cs` &middot; 7 test attribute(s) detected.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | GetLocationData_ReturnsLocationsFromCache | get location data returns locations from cache | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 2 | | GetLocationData_ReturnsExcludedLocationIds | get location data returns excluded location ids | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 3 | | GetLocationData_ReturnsTransferNumbers | get location data returns transfer numbers | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 4 | | GetLocationData_PassesThroughCacheMetadata | get location data passes through cache metadata | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 5 | | GetLocationData_WhenNoLocations_ReturnsEmptyList | get location data when no locations returns empty list | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 6 | | GetLocationData_WhenNullExclusions_ReturnsEmptySet | get location data when null exclusions returns empty set | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 7 | | GetLocationData_ReturnsExcludedProviderIds | get location data returns excluded provider ids | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |

### Suggested Missing Tests
- **Empty / unknown location id** - No test for a non-existent location id.
- **Caching behavior** - If the provider caches, no test for cache invalidation or TTL.

### Improvement Suggestions / Irrelevant Tests
- **Only 7 tests** for a data provider that powers downstream search and selection - consider negative paths and bulk lookup.

## LocationSearchServiceSearchLocationsTests.cs

Source: `tests/ZoTools.UnitTests/Service/LocationSearchServiceSearchLocationsTests.cs` &middot; 52 test attribute(s) detected.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | SearchLocations_NoFilters_ReturnsAllNonExcludedLocations | search locations no filters returns all non excluded locations | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 2 | | SearchLocations_NoFilters_ExcludedLocationsOmitted | search locations no filters excluded locations omitted | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 3 | | SearchLocations_NoFilters_FormatsAddressCorrectly | search locations no filters formats address correctly | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 4 | | SearchLocations_NoFilters_FormatsAddressWithAddress2 | search locations no filters formats address with address2 | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 5 | | SearchLocations_NoFilters_FormatsAddressWithMissingParts | search locations no filters formats address with missing parts | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 6 | | SearchLocations_NoFilters_UnavailableReasonsIsEmpty | search locations no filters unavailable reasons is empty | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 7 | | SearchLocations_ProviderIds_NoUserClaim_FiltersToMatchingLocations | search locations provider ids no user claim filters to matching locations | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 8 | | SearchLocations_ProviderIds_NoUserClaim_NoMatchingProviders_ReturnsEmpty | search locations provider ids no user claim no matching providers returns empty | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 9 | | SearchLocations_ProviderIds_NoUserClaim_NullProviderLocations_ReturnsEmpty | search locations provider ids no user claim null provider locations returns empty | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 10 | | SearchLocations_ProviderIds_WithUserClaim_ProviderNotAvailable_UnavailableWithReason | search locations provider ids with user claim provider not available unavailable with reason | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 11 | | SearchLocations_ProviderIds_WithVisitReasonAndCarrier_ProviderNotAvailable_OnlyProviderNotAvailableReason | search locations provider ids with visit reason and carrier provider not available only provider not available reason | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 12 | | SearchLocations_ProviderIds_EmptyList_TreatedAsNoFilter | search locations provider ids empty list treated as no filter | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 13 | | SearchLocations_ExcludedProviderIds_ExcludedProviderDoesNotMakeLocationAvailable | search locations excluded provider ids excluded provider does not make location available | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 14 | | SearchLocations_UserClaim_PhoneticMatchReturnsResults | search locations user claim phonetic match returns results | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 15 | | SearchLocations_UserClaim_SubstringMatchOnLocationName | search locations user claim substring match on location name | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 16 | | SearchLocations_UserClaim_PhoneticMatchOnLocationName_ReturnsMatchedLocation | search locations user claim phonetic match on location name returns matched location | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 17 | | SearchLocations_UserClaim_PassesLocationNameCandidatesToPhoneticMatcher | search locations user claim passes location name candidates to phonetic matcher | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 18 | | SearchLocations_UserClaim_CombinesSubstringAndPhoneticMatches | search locations user claim combines substring and phonetic matches | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 19 | | SearchLocations_UserClaim_NoMatches_ReturnsEmpty | search locations user claim no matches returns empty | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 20 | | SearchLocations_UserClaim_MatchesStateName_ReturnsLocationsInState | search locations user claim matches state name returns locations in state | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 21 | | SearchLocations_UserClaim_MatchesStateAbbreviation_ReturnsLocationsInState | search locations user claim matches state abbreviation returns locations in state | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 22 | | SearchLocations_UserClaim_StateMatchCombinesWithPhoneticMatches | search locations user claim state match combines with phonetic matches | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 23 | | SearchLocations_ZipCode_SortsByDistance | search locations zip code sorts by distance | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 24 | | SearchLocations_ZipCode_WithRadius_FiltersOutFarLocations | search locations zip code with radius filters out far locations | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 25 | | SearchLocations_ZipCode_InvalidZip_ReturnsAllLocations | search locations zip code invalid zip returns all locations | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 26 | | SearchLocations_ZipCode_VirtualLocationsIncludedButNotFiltered | search locations zip code virtual locations included but not filtered | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 27 | | SearchLocations_ZipCode_VirtualLocationsSortedLast | search locations zip code virtual locations sorted last | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 28 | | SearchLocations_ZipCode_IncludesDistanceMiles | search locations zip code includes distance miles | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 29 | | SearchLocations_NoZipCode_DistanceMilesIsNull | search locations no zip code distance miles is null | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 30 | | SearchLocations_ZipCode_VirtualLocations_DistanceMilesIsNull | search locations zip code virtual locations distance miles is null | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 31 | | SearchLocations_UserClaim_WithZipAndRadius_ZipRadiusIgnored | search locations user claim with zip and radius zip radius ignored | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 32 | | SearchLocations_VisitReasonId_LocationHasProviderWhoPerformsIt_Available | search locations visit reason id location has provider who performs it available | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 33 | | SearchLocations_VisitReasonId_NoProviderPerformsIt_NoUserClaim_ExcludedFromResults | search locations visit reason id no provider performs it no user claim excluded from results | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 34 | | SearchLocations_VisitReasonId_NoProviderPerformsIt_WithUserClaim_UnavailableWithReason | search locations visit reason id no provider performs it with user claim unavailable with reason | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 35 | | SearchLocations_VisitReasonId_NotProvided_AllLocationsAvailable | search locations visit reason id not provided all locations available | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 36 | | SearchLocations_VisitReasonId_LocationWithMultipleProviders_OnePerforms_Available | search locations visit reason id location with multiple providers one performs available | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 37 | | SearchLocations_VisitReasonId_LocationWithNoProviderLocations_NoUserClaim_ExcludedFromResults | search locations visit reason id location with no provider locations no user claim excluded from results | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 38 | | SearchLocations_VisitReasonId_WithProviderIds_OnlyNonRequestedProviderPerformsIt_NoUserClaim_ExcludedFromResults | search locations visit reason id with provider ids only non requested provider performs it no user claim excluded from results | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 39 | | SearchLocations_CarrierId_LocationAcceptsInsurance_Available | search locations carrier id location accepts insurance available | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 40 | | SearchLocations_CarrierId_NoProviderAcceptsInsurance_NoUserClaim_ExcludedFromResults | search locations carrier id no provider accepts insurance no user claim excluded from results | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 41 | | SearchLocations_CarrierId_NoProviderAcceptsInsurance_WithUserClaim_UnavailableWithReason | search locations carrier id no provider accepts insurance with user claim unavailable with reason | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 42 | | SearchLocations_CarrierId_NotProvided_AllLocationsAvailable | search locations carrier id not provided all locations available | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 43 | | SearchLocations_PlanIdOnly_TriggersInsuranceCheck | search locations plan id only triggers insurance check | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 44 | | SearchLocations_CarrierId_WithPlanId_PassesBothToGql | search locations carrier id with plan id passes both to gql | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 45 | | SearchLocations_CarrierId_LocationWithNoProviderLocations_NoUserClaim_ExcludedFromResults | search locations carrier id location with no provider locations no user claim excluded from results | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 46 | | SearchLocations_CarrierId_WithProviderIds_OnlyNonRequestedProviderAccepts_NoUserClaim_ExcludedFromResults | search locations carrier id with provider ids only non requested provider accepts no user claim excluded from results | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 47 | | SearchLocations_BothCarrierIdAndVisitReasonId_BothFail_NoUserClaim_ExcludedFromResults | search locations both carrier id and visit reason id both fail no user claim excluded from results | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 48 | | SearchLocations_BothCarrierIdAndVisitReasonId_BothFail_WithUserClaim_BothReasonsInList | search locations both carrier id and visit reason id both fail with user claim both reasons in list | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 49 | | SearchLocations_VisitReasonLookupFails_NoUserClaim_ExcludedFromResults | search locations visit reason lookup fails no user claim excluded from results | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 50 | | SearchLocations_VisitReasonLookupFails_WithUserClaim_ReturnsLocationsAsUnavailable | search locations visit reason lookup fails with user claim returns locations as unavailable | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 51 | | SearchLocations_InsuranceLookupFails_NoUserClaim_ExcludedFromResults | search locations insurance lookup fails no user claim excluded from results | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 52 | | SearchLocations_InsuranceLookupFails_WithUserClaim_ReturnsLocationAsUnavailable | search locations insurance lookup fails with user claim returns location as unavailable | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |

### Suggested Missing Tests
- **Geographic edge cases** - No test for international zip codes, military APO/FPO addresses, or PR/territories.
- **Pagination** - No test for cursor exhaustion or page-size boundary.
- **Sort stability** - No test that ties between equally-ranked results are deterministic.

### Improvement Suggestions / Irrelevant Tests
- **52 tests in one file** - consider splitting by query type (zip / radius / fuzzy).

## LocationSearchServiceTests.cs

Source: `tests/ZoTools.UnitTests/Service/LocationSearchServiceTests.cs` &middot; 17 test attribute(s) detected.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | ValidateLocation_WhenLocationExists_ReturnsConfirmed | validate location when location exists returns confirmed | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 2 | | ValidateLocation_WhenLocationNotFound_ReturnsNotFound | validate location when location not found returns not found | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 3 | | ValidateLocation_WhenLocationExcluded_ReturnsExcludedWithLocationTransfer | validate location when location excluded returns excluded with location transfer | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 4 | | ValidateLocation_WhenExcluded_FallsBackToCatchAllTransfer | validate location when excluded falls back to catch all transfer | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 5 | | FindLocationsNearZip_ValidZip_ReturnsSortedByDistance | find locations near zip valid zip returns sorted by distance | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 6 | | FindLocationsNearZip_InvalidZip_ReturnsInvalidZip | find locations near zip invalid zip returns invalid zip | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 7 | | FindLocationsNearZip_NoLocations_ReturnsNoLocationsFound | find locations near zip no locations returns no locations found | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 8 | | FindLocationsNearZip_FiltersOutVirtualLocations | find locations near zip filters out virtual locations | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 9 | | FindLocationsInState_ValidState_ReturnsFoundLocations | find locations in state valid state returns found locations | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 10 | | FindLocationsInState_InvalidState_ReturnsInvalidState | find locations in state invalid state returns invalid state | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 11 | | FindLocationsInState_NoLocationsInState_ReturnsNoLocationsInState | find locations in state no locations in state returns no locations in state | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 12 | | FindLocationsInState_IncludesExcludedLocationsInResults | find locations in state includes excluded locations in results | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 13 | | FindLocationsByName_AddressMatch_ReturnsLocations | find locations by name address match returns locations | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 14 | | FindLocationsByName_AddressMatchShortCircuits_CityNotAttempted | find locations by name address match short circuits city not attempted | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 15 | | FindLocationsByName_NoAddressMatch_FallsThroughToCityMatch | find locations by name no address match falls through to city match | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 16 | | FindLocationsByName_WhenNoMatches_ReturnsNoMatches | find locations by name when no matches returns no matches | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 17 | | FindLocationsByName_DeduplicatesSameLocationFromNeighborhoods | find locations by name deduplicates same location from neighborhoods | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |

### Suggested Missing Tests
- **Synonym/alias handling** - No test that 'NYC' resolves to New York-area locations.
- **Case-insensitive matching across special characters** - No test with diacritics.

### Improvement Suggestions / Irrelevant Tests
- No specific concerns flagged; consider snapshotting (CS-006) where message contracts matter.

## LockedActionServiceTests.cs

Source: `tests/ZoTools.UnitTests/Service/LockedActionServiceTests.cs` &middot; 7 test attribute(s) detected.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | RunIfNotLocked_WhenNotLocked_ExecutesAction | run if not locked when not locked executes action | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 2 | | RunIfNotLocked_WhenNotLocked_StoresResultInDynamo | run if not locked when not locked stores result in dynamo | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 3 | | RunIfNotLocked_WhenNotLocked_RecordsNotLockedMetric | run if not locked when not locked records not locked metric | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 4 | | RunIfNotLocked_WhenActionThrows_DeletesLockRow | run if not locked when action throws deletes lock row | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 5 | | RunIfNotLocked_WhenLocked_WaitsForResult | run if not locked when locked waits for result | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 6 | | RunIfNotLocked_WhenLocked_AndTimeout_ThrowsTimeoutException | run if not locked when locked and timeout throws timeout exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 7 | | RunIfNotLocked_WhenLocked_AndRowDeleted_ThrowsException | run if not locked when locked and row deleted throws exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |

### Suggested Missing Tests
- **Lock not released on action throw with finalizer disabled** - Verify the lock TTL still expires.
- **Concurrent contenders queueing fairness** - No test for FIFO/LIFO ordering of waiters.
- **Lock metadata (caller id, reason)** - No test that lock metadata is preserved for audit.

### Improvement Suggestions / Irrelevant Tests
- **Mocked metric recorder** (TEST-002) - prefer fake recorder for cleaner assertions.

## MonolithApiServiceTests.cs

Source: `tests/ZoTools.UnitTests/Service/MonolithApiServiceTests.cs` &middot; 11 test attribute(s) detected.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | GetSpecialityProcedures_ReturnsProceduresFromClient | get speciality procedures returns procedures from client | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 2 | | GetSpecialityProcedures_PassesIncludeDeletedFalseToClient | get speciality procedures passes include deleted false to client | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 3 | | GetSpecialityProcedures_WhenClientReturnsError_ThrowsException | get speciality procedures when client returns error throws exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 4 | | GetSpecialityProcedures_WhenClientReturnsNullValue_ThrowsException | get speciality procedures when client returns null value throws exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 5 | | SendSmsMessage_CallsPostSendSmsMessagesWithCorrectRequest | send sms message calls post send sms messages with correct request | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 6 | | SendSmsMessage_WhenSuccess_ReturnsTrue | send sms message when success returns true | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 7 | | SendSmsMessage_WhenSuccess_IncrementsSuccessMetric | send sms message when success increments success metric | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 8 | | SendSmsMessage_WhenNotSuccess_ReturnsFalse | send sms message when not success returns false | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 9 | | SendSmsMessage_WhenNotSuccess_IncrementsHandledErrorMetric | send sms message when not success increments handled error metric | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 10 | | SendSmsMessage_WhenException_ReturnsFalse | send sms message when exception returns false | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 11 | | SendSmsMessage_WhenException_IncrementsUnhandledErrorMetric | send sms message when exception increments unhandled error metric | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |

### Suggested Missing Tests
- **Retry policy** - No test that transient 503s trigger documented retry/backoff.
- **Non-2xx response body propagation** - No test that error bodies surface to caller for diagnostics.
- **Auth header propagation** - No test that user context tokens flow correctly to monolith.

### Improvement Suggestions / Irrelevant Tests
- No specific concerns flagged; consider snapshotting (CS-006) where message contracts matter.

## NonPhiGqlServiceAvailabilityTests.cs

Source: `tests/ZoTools.UnitTests/Service/NonPhiGqlServiceAvailabilityTests.cs` &middot; 16 test attribute(s) detected.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | GetAvailability_MapsProviderLocationData | get availability maps provider location data | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 2 | | GetAvailability_WhenNoProviderLocations_ReturnsEmptyList | get availability when no provider locations returns empty list | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 3 | | GetAvailability_WhenGqlReturnsErrors_ThrowsException | get availability when gql returns errors throws exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 4 | | GetAvailability_WithResourceTimeslot_MapsIsResourceTrue | get availability with resource timeslot maps is resource true | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 5 | | GetAvailability_WhenTimesAreNull_ReturnsEmptyAvailability | get availability when times are null returns empty availability | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 6 | | GetAvailability_WhenTimesContainNullEntries_SkipsNulls | get availability when times contain null entries skips nulls | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 7 | | GetAvailability_WhenTimeslotsContainNullEntries_SkipsNulls | get availability when timeslots contain null entries skips nulls | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 8 | | GetAvailability_WithNullProcedureIdAndNullDurationOverride_ThrowsArgumentException | get availability with null procedure id and null duration override throws argument exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 9 | | GetAvailability_WithProcedureIdOnly_DoesNotThrow | get availability with procedure id only does not throw | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 10 | | GetAvailability_WithDurationOverrideOnly_DoesNotThrow | get availability with duration override only does not throw | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 11 | | GetAvailability_WithBothProcedureIdAndDurationOverride_DoesNotThrow | get availability with both procedure id and duration override does not throw | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 12 | | GetAvailability_PassesTimeFilterToGql | get availability passes time filter to gql | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 13 | | GetAvailability_WithNullTimeFilter_PassesNullToGql | get availability with null time filter passes null to gql | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 14 | | GetAvailability_LogsRequestParameters | get availability logs request parameters | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 15 | | GetAvailability_LogsResponseWithTimeslotDetails | get availability logs response with timeslot details | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 16 | | GetAvailability_WhenEmpty_DoesNotLogResponse | get availability when empty does not log response | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |

### Suggested Missing Tests
- **Empty availability response** - No test that empty slots result in an empty (not null) collection.
- **Cross-day boundary slots** - No test for slots that span midnight in patient TZ.

### Improvement Suggestions / Irrelevant Tests
- No specific concerns flagged; consider snapshotting (CS-006) where message contracts matter.

## NonPhiGqlServiceNextAvailableTests.cs

Source: `tests/ZoTools.UnitTests/Service/NonPhiGqlServiceNextAvailableTests.cs` &middot; 9 test attribute(s) detected.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | GetNextAvailable_WithFirstAvailability_MapsResult | get next available with first availability maps result | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 2 | | GetNextAvailable_WhenFirstAvailabilityIsNull_MapsWithNullStartTime | get next available when first availability is null maps with null start time | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 3 | | GetNextAvailable_WhenNoProviderLocations_ReturnsEmptyList | get next available when no provider locations returns empty list | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 4 | | GetNextAvailable_WhenGqlReturnsErrors_ThrowsException | get next available when gql returns errors throws exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 5 | | GetNextAvailable_WithNullProcedureIdAndNullDurationOverride_ThrowsArgumentException | get next available with null procedure id and null duration override throws argument exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 6 | | GetNextAvailable_WithNullAvailability_MapsWithNullStartTime | get next available with null availability maps with null start time | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 7 | | GetNextAvailable_LogsRequestParameters | get next available logs request parameters | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 8 | | GetNextAvailable_LogsResponseWithStartTimes | get next available logs response with start times | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 9 | | GetNextAvailable_WhenNoAvailability_LogsNone | get next available when no availability logs none | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |

### Suggested Missing Tests
- **No availability anywhere** - No test for the no-results-found shape.
- **DST boundary** - Per provider-calendar SQUAWK-6090, DST week is a known risk - add a test.

### Improvement Suggestions / Irrelevant Tests
- No specific concerns flagged; consider snapshotting (CS-006) where message contracts matter.

## NonPhiGqlServicePracticeProvidersTests.cs

Source: `tests/ZoTools.UnitTests/Service/NonPhiGqlServicePracticeProvidersTests.cs` &middot; 4 test attribute(s) detected.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | GetPracticeProviders_MapsProceduresAndSpecialtyIds | get practice providers maps procedures and specialty ids | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 2 | | GetPracticeProviders_WhenNoProviders_ReturnsEmptyList | get practice providers when no providers returns empty list | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 3 | | GetPracticeProviders_WhenPracticeIsNull_ThrowsException | get practice providers when practice is null throws exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 4 | | GetPracticeProviders_WhenEnsureNoErrorsThrows_PropagatesException | get practice providers when ensure no errors throws propagates exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |

### Suggested Missing Tests
- **Inactive / archived providers filter** - No test that disabled providers are excluded.
- **Practice with no providers** - No test for empty practice case.

### Improvement Suggestions / Irrelevant Tests
- **Only 4 tests** for a foundational lookup.

## NonPhiGqlServiceProcedureDurationsTests.cs

Source: `tests/ZoTools.UnitTests/Service/NonPhiGqlServiceProcedureDurationsTests.cs` &middot; 16 test attribute(s) detected.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | GetProviderProcedureDurations_SingleProvider_MapsAllFieldsCorrectly | get provider procedure durations single provider maps all fields correctly | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 2 | | GetProviderProcedureDurations_MultipleProviders_ReturnsMappedDictionaryForAll | get provider procedure durations multiple providers returns mapped dictionary for all | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 3 | | GetProviderProcedureDurations_NullProcedureDurationsFiltered | get provider procedure durations null procedure durations filtered | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 4 | | GetProviderProcedureDurations_NullProvidersFiltered | get provider procedure durations null providers filtered | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 5 | | GetProviderProcedureDurations_EmptyProvidersList_ReturnsEmptyDictionary | get provider procedure durations empty providers list returns empty dictionary | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 6 | | GetProviderProcedureDurations_NullProviders_ReturnsEmptyDictionary | get provider procedure durations null providers returns empty dictionary | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 7 | | GetProviderProcedureDurations_GqlErrors_ThrowsException | get provider procedure durations gql errors throws exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 8 | | GetProviderProcedureDurations_GqlClientThrows_PropagatesException | get provider procedure durations gql client throws propagates exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 9 | | MapProcedureDurations_EmptyInput_ReturnsEmpty | map procedure durations empty input returns empty | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 10 | | MapProcedureDurations_NullEntriesFiltered | map procedure durations null entries filtered | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 11 | | MapProcedureDurations_AllFieldsMappedCorrectly | map procedure durations all fields mapped correctly | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 12 | | MapProcedureDurations_IsActiveNull_DefaultsToTrue | map procedure durations is active null defaults to true | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 13 | | MapProcedureDurations_IsActiveFalse_MapsFalse | map procedure durations is active false maps false | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 14 | | MapProcedureDurations_IsActiveTrue_MapsTrue | map procedure durations is active true maps true | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 15 | | MapProcedureDurations_ProcedureIdNull_ThrowsException | map procedure durations procedure id null throws exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 16 | | MapProcedureDurations_ProcedureIdWhitespace_ThrowsException | map procedure durations procedure id whitespace throws exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |

### Suggested Missing Tests
- **Negative or zero duration in response** - No test asserting service rejects/normalizes invalid durations.
- **Procedure with multiple location overrides** - No test for per-location duration variants.

### Improvement Suggestions / Irrelevant Tests
- No specific concerns flagged; consider snapshotting (CS-006) where message contracts matter.

## NonPhiGqlServiceProviderProceduresTests.cs

Source: `tests/ZoTools.UnitTests/Service/NonPhiGqlServiceProviderProceduresTests.cs` &middot; 6 test attribute(s) detected.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | GetProviderProcedures_MapsProcedures | get provider procedures maps procedures | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 2 | | GetProviderProcedures_MapsProcedureIdsByProvider | get provider procedures maps procedure ids by provider | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 3 | | GetProviderProcedures_DeduplicatesProceduresAcrossProviders | get provider procedures deduplicates procedures across providers | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 4 | | GetProviderProcedures_WhenNoProviders_ReturnsEmptyList | get provider procedures when no providers returns empty list | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 5 | | GetProviderProcedures_WhenNullProviders_ReturnsEmptyList | get provider procedures when null providers returns empty list | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 6 | | GetProviderProcedures_WhenEnsureNoErrorsThrows_PropagatesException | get provider procedures when ensure no errors throws propagates exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |

### Suggested Missing Tests
- **Provider with empty procedure set** - No test for provider with no listed procedures.
- **Procedure not in catalog** - No test for filtering or fallback when GQL returns an unknown procedure id.

### Improvement Suggestions / Irrelevant Tests
- **Only 6 tests** for a critical lookup driving the visit-reason flow.

## NonPhiGqlServiceTests.cs

Source: `tests/ZoTools.UnitTests/Service/NonPhiGqlServiceTests.cs` &middot; 18 test attribute(s) detected.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | GetPracticeInfoForAppts_MapsLocationData | get practice info for appts maps location data | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 2 | | GetPracticeInfoForAppts_WithMultipleLocations_ReturnsAll | get practice info for appts with multiple locations returns all | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 3 | | GetPracticeInfoForAppts_ApprovedProvider_IsIncluded | get practice info for appts approved provider is included | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 4 | | GetPracticeInfoForAppts_ResourceProvider_IsIncluded | get practice info for appts resource provider is included | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 5 | | GetPracticeInfoForAppts_UnapprovedProvider_IsFilteredOut | get practice info for appts unapproved provider is filtered out | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 6 | | GetPracticeInfoForAppts_ResourceProvider_HasIsResourceTrue | get practice info for appts resource provider has is resource true | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 7 | | GetPracticeInfoForAppts_ApprovedProvider_HasIsResourceFalse | get practice info for appts approved provider has is resource false | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 8 | | GetPracticeInfoForAppts_ProviderInZoMappedSet_HasIsMappedToCallCenterTrue | get practice info for appts provider in zo mapped set has is mapped to call center true | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 9 | | GetPracticeInfoForAppts_ProviderNotInZoMappedSet_HasIsMappedToCallCenterFalse | get practice info for appts provider not in zo mapped set has is mapped to call center false | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 10 | | GetPracticeInfoForAppts_MapsProviderNameFields | get practice info for appts maps provider name fields | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 11 | | GetPracticeInfoForAppts_MultipleProviders_AllApprovedIncluded | get practice info for appts multiple providers all approved included | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 12 | | GetPracticeInfoForAppts_FacilityProfessionalType_HasIsFacilityTrue | get practice info for appts facility professional type has is facility true | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 13 | | GetPracticeInfoForAppts_FacilityResourceProfessionalType_HasIsFacilityTrue | get practice info for appts facility resource professional type has is facility true | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 14 | | GetPracticeInfoForAppts_NullProfessionalType_HasIsFacilityFalse | get practice info for appts null professional type has is facility false | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 15 | | GetPracticeInfoForAppts_OtherProfessionalType_HasIsFacilityFalse | get practice info for appts other professional type has is facility false | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 16 | | GetPracticeInfoForAppts_WhenPracticeIsNull_ThrowsException | get practice info for appts when practice is null throws exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 17 | | GetPracticeInfoForAppts_WhenEnsureNoErrorsThrows_PropagatesException | get practice info for appts when ensure no errors throws propagates exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 18 | | GetPracticeInfoForAppts_WhenClientThrows_PropagatesException | get practice info for appts when client throws propagates exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |

### Suggested Missing Tests
- **GraphQL partial-result responses** - No test for a query that returns both data and errors arrays.
- **Schema introspection failure** - No test for behavior when schema is missing the expected field.
- **Query timeout / cancellation** - No test for client-side cancellation of in-flight queries.

### Improvement Suggestions / Irrelevant Tests
- **Six NonPhiGql files** could share a base test fixture; significant duplication of GQL setup boilerplate visible across them.

## PatientInfoServiceTests.cs

Source: `tests/ZoTools.UnitTests/Service/PatientInfoServiceTests.cs` &middot; 75 test attribute(s) detected.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | SearchPatients_ReturnsPatientsFromInteropPlatform | search patients returns patients from interop platform | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 2 | | SearchPatients_WhenPracticeNotFound_ReturnsNull | search patients when practice not found returns null | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 3 | | SearchPatients_WhenInteropPlatformReturnsEmptyList_ReturnsEmptyList | search patients when interop platform returns empty list returns empty list | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 4 | | SearchPatients_PassesCorrectParametersToInteropPlatform | search patients passes correct parameters to interop platform | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 5 | | SearchPatients_CallsAuditLoggerForEachPatient | search patients calls audit logger for each patient | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 6 | | SearchPatients_WhenCacheFlagOn_UpsertsSearchedPatientsInCallSession | search patients when cache flag on upserts searched patients in call session | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 7 | | SearchPatients_WhenCacheFlagOff_DoesNotUpsertSearchedPatients | search patients when cache flag off does not upsert searched patients | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 8 | | SearchPatients_WhenUpsertFails_StillReturnsPatients | search patients when upsert fails still returns patients | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 9 | | SearchPatients_FiltersPatientsWithoutExternalPatientId | search patients filters patients without external patient id | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 10 | | GetAppointments_ReturnsEnrichedAppointmentDetails | get appointments returns enriched appointment details | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 11 | | GetAppointments_WhenPracticeNotFound_ReturnsNull | get appointments when practice not found returns null | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 12 | | GetAppointments_WhenInteropPlatformReturnsEmptyList_ReturnsEmptyList | get appointments when interop platform returns empty list returns empty list | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 13 | | GetAppointments_CallsAuditLoggerForEachAppointment | get appointments calls audit logger for each appointment | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 14 | | GetAppointments_PassesCorrectParametersToInteropPlatform | get appointments passes correct parameters to interop platform | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 15 | | GetAppointments_CallsAppointmentInfoServiceWithCorrectParameters | get appointments calls appointment info service with correct parameters | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 16 | | GetAppointments_CachesAppointmentsInCallSession | get appointments caches appointments in call session | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 17 | | GetAppointments_PassesCacheFlagValueToUpsert | get appointments passes cache flag value to upsert | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 18 | | GetAppointments_WhenCacheFlagOn_OverridesLocationIdsFromSearchedPatientsCache | get appointments when cache flag on overrides location ids from searched patients cache | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 19 | | GetAppointments_WhenCacheFlagOff_UsesPassedInLocationIds | get appointments when cache flag off uses passed in location ids | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 20 | | GetAppointments_WhenCacheFlagOn_AndNoSearchedPatient_UsesPassedInLocationIds | get appointments when cache flag on and no searched patient uses passed in location ids | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 21 | | GetAppointments_WhenCacheLookupFails_ProceedsWithPassedInLocationIds | get appointments when cache lookup fails proceeds with passed in location ids | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 22 | | GetAppointments_WhenCachedLocationIdsEmpty_UsesPassedInLocationIds | get appointments when cached location ids empty uses passed in location ids | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 23 | | GetAppointments_WhenCachingFails_StillReturnsAppointments | get appointments when caching fails still returns appointments | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 24 | | UpdatePatient_WhenPatientFound_CompletesWithoutThrowing | update patient when patient found completes without throwing | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 25 | | UpdatePatient_WhenPatientNotFound_ThrowsInvalidOperationException | update patient when patient not found throws invalid operation exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 26 | | UpdatePatient_PassesCallIdAndPatientIdToRepository | update patient passes call id and patient id to repository | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 27 | | UpdatePatient_WithEmail_UpdatesEmail | update patient with email updates email | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 28 | | UpdatePatient_WithPhone_UpdatesPhone | update patient with phone updates phone | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 29 | | UpdatePatient_WithAddress_UpdatesAddress | update patient with address updates address | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 30 | | UpdatePatient_WithGender_UpdatesGender | update patient with gender updates gender | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 31 | | UpdatePatient_WithInsuranceFields_UpdatesInsuranceFields | update patient with insurance fields updates insurance fields | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 32 | | UpdatePatient_WithNullEmail_DoesNotUpdateEmail | update patient with null email does not update email | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 33 | | UpdatePatient_WithNullGender_DoesNotUpdateGender | update patient with null gender does not update gender | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 34 | | UpdatePatient_WithBlankEmail_DoesNotUpdateEmail | update patient with blank email does not update email | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 35 | | UpdatePatient_WithBlankInsuranceCarrierId_DoesNotUpdateInsuranceCarrierId | update patient with blank insurance carrier id does not update insurance carrier id | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 36 | | UpdatePatient_WithBlankPhone_DoesNotUpdatePhone | update patient with blank phone does not update phone | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 37 | | UpdatePatient_WithBlankAddress_DoesNotUpdateAddress | update patient with blank address does not update address | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 38 | | UpdatePatient_WithBlankCity_DoesNotUpdateCity | update patient with blank city does not update city | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 39 | | UpdatePatient_WithBlankState_DoesNotUpdateState | update patient with blank state does not update state | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 40 | | UpdatePatient_WithBlankZip_DoesNotUpdateZip | update patient with blank zip does not update zip | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 41 | | UpdatePatient_WithBlankInsuranceMemberId_DoesNotUpdateInsuranceMemberId | update patient with blank insurance member id does not update insurance member id | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 42 | | UpdatePatient_WithBlankInsurancePlanId_DoesNotUpdateInsurancePlanId | update patient with blank insurance plan id does not update insurance plan id | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 43 | | UpdatePatient_WithInvalidEmail_ThrowsArgumentException | update patient with invalid email throws argument exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 44 | | UpdatePatient_WithValidEmail_DoesNotThrow | update patient with valid email does not throw | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 45 | | UpdatePatient_WithNullEmail_DoesNotValidateEmail | update patient with null email does not validate email | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 46 | | UpdatePatient_WithEmptyEmail_DoesNotValidateEmail | update patient with empty email does not validate email | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 47 | | UpdatePatient_WithInvalidPhone_ThrowsArgumentException | update patient with invalid phone throws argument exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 48 | | UpdatePatient_WithInvalidElevenDigitPhone_ThrowsArgumentException | update patient with invalid eleven digit phone throws argument exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 49 | | UpdatePatient_WithValidTenDigitPhone_DoesNotThrow | update patient with valid ten digit phone does not throw | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 50 | | UpdatePatient_WithValidElevenDigitPhone_DoesNotThrow | update patient with valid eleven digit phone does not throw | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 51 | | UpdatePatient_WithNullPhone_DoesNotValidatePhone | update patient with null phone does not validate phone | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 52 | | UpdatePatient_WithEmptyPhone_DoesNotValidatePhone | update patient with empty phone does not validate phone | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 53 | | InsertPatient_ReturnsGeneratedPatientId | insert patient returns generated patient id | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 54 | | InsertPatient_CallsAddPatientWithCorrectCallId | insert patient calls add patient with correct call id | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 55 | | InsertPatient_CallsAddPatientWithPatientDataFromArguments | insert patient calls add patient with patient data from arguments | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 56 | | InsertPatient_CallsAddPatientWithGeneratedPatientId | insert patient calls add patient with generated patient id | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 57 | | InsertPatient_WithFutureDateOfBirth_ThrowsArgumentException | insert patient with future date of birth throws argument exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 58 | | InsertPatient_WithTodayDateOfBirth_ThrowsArgumentException | insert patient with today date of birth throws argument exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 59 | | InsertPatient_WithPastDateOfBirth_DoesNotThrow | insert patient with past date of birth does not throw | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 60 | | DetermineIsNewPatient_WithCachedAppointments_DelegatesToIsNewPatientService | determine is new patient with cached appointments delegates to is new patient service | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |

### Suggested Missing Tests
- **Patient with multiple matching records** - No deduplication test.
- **Special characters in name fields** - No test for apostrophes, hyphens, accents.
- **Date of birth normalization** - No test for various input formats (MM/DD/YYYY vs ISO vs Unix).

### Improvement Suggestions / Irrelevant Tests
- **75 tests is large** - consider splitting by capability (lookup / create / update).

## PermissionServiceTests.cs

Source: `tests/ZoTools.UnitTests/Service/PermissionServiceTests.cs` &middot; 7 test attribute(s) detected.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | PermissionCheckRecords_AllAuthorized_ReturnsAllRecords | permission check records all authorized returns all records | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 2 | | PermissionCheckRecords_SomeUnauthorized_ThrowsUnauthorizedAccessException | permission check records some unauthorized throws unauthorized access exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 3 | | PermissionCheckRecords_EmptyCollection_ReturnsEmpty | permission check records empty collection returns empty | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 4 | | PermissionCheckRecords_CallsAuditLogForEachRecord | permission check records calls audit log for each record | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 5 | | PermissionCheckRecords_WhenUnauthorized_StillCallsAuditLogForAllRecords | permission check records when unauthorized still calls audit log for all records | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 6 | | PermissionCheckRecords_AllAuthorized_RecordsSuccessMetric | permission check records all authorized records success metric | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 7 | | PermissionCheckRecords_SomeUnauthorized_RecordsUnauthorizedMetric | permission check records some unauthorized records unauthorized metric | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |

### Suggested Missing Tests
- **Role bundle changes mid-session** - No test for what happens if a user's role changes while a request is in flight.
- **Missing/expired permission claims** - No test for expired JWT permissions.
- **AUTH-002 operation-on-domain coverage** - Are all roles tested against all guarded operations?

### Improvement Suggestions / Irrelevant Tests
- **Only 7 tests** for a security-critical service. AUTH-001/002/003 imply more coverage is required.

## PhoneBotRepositoryTests.cs

Source: `tests/ZoTools.UnitTests/Service/PhoneBotRepositoryTests.cs` &middot; 17 test attribute(s) detected.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | GetPracticeInfo_ReturnsPracticeInfoFromClient | get practice info returns practice info from client | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 2 | | GetPracticeInfo_WhenClientReturnsError_ThrowsException | get practice info when client returns error throws exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 3 | | ValidateProviderAvailability_ReturnsResponseFromClient | validate provider availability returns response from client | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 4 | | ValidateProviderAvailability_WhenClientReturnsError_ThrowsException | validate provider availability when client returns error throws exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 5 | | CancelAppointment_ReturnsResponseFromClient | cancel appointment returns response from client | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 6 | | CancelAppointment_WhenClientReturnsError_ThrowsException | cancel appointment when client returns error throws exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 7 | | CancelAppointment_SetsShouldSendSmsForCancelToFalse | cancel appointment sets should send sms for cancel to false | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 8 | | SaveRescheduleBookingRequest_CallsClientWithCorrectParameters | save reschedule booking request calls client with correct parameters | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 9 | | SaveRescheduleBookingRequest_WhenClientReturnsError_ThrowsException | save reschedule booking request when client returns error throws exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 10 | | SaveNewBookingRequest_CallsClientWithCorrectParameters | save new booking request calls client with correct parameters | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 11 | | SaveNewBookingRequest_WhenClientReturnsError_CompletesWithoutThrowing | save new booking request when client returns error completes without throwing | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 12 | | SaveNewBookingRequest_WhenClientThrowsException_CompletesWithoutThrowing | save new booking request when client throws exception completes without throwing | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 13 | | MapAvailabilityStrategy_AllValues_DoesNotThrow | map availability strategy all values does not throw | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 14 | | MapAvailabilityStrategy_InvalidValue_ThrowsArgumentException | map availability strategy invalid value throws argument exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 15 | | GetTranscriptPresignedUrls_ReturnsResponseFromClient | get transcript presigned urls returns response from client | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 16 | | GetTranscriptPresignedUrls_WhenNotFound_ReturnsNull | get transcript presigned urls when not found returns null | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 17 | | GetTranscriptPresignedUrls_WhenOtherError_ThrowsException | get transcript presigned urls when other error throws exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |

### Suggested Missing Tests
- **DDB throttling / backoff** - No test for ProvisionedThroughputExceededException.
- **TTL expiry** - No test that expired entries are excluded from reads.
- **Batch operations** - No test for batch read/write boundaries.

### Improvement Suggestions / Irrelevant Tests
- No specific concerns flagged; consider snapshotting (CS-006) where message contracts matter.

## PhoneNumberServiceTests.cs

Source: `tests/ZoTools.UnitTests/Service/PhoneNumberServiceTests.cs` &middot; 44 test attribute(s) detected.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | GetPhoneNumberInfo_AnonymousNumber_ReturnsAnonymousResult | get phone number info anonymous number returns anonymous result | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 2 | | GetPhoneNumberInfo_NullOrWhitespace_ReturnsAnonymousResult | get phone number info null or whitespace returns anonymous result | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 3 | | GetPhoneNumberInfo_ValidNumber_CallsTwilioAndReturnsCallerInfo | get phone number info valid number calls twilio and returns caller info | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 4 | | GetPhoneNumberInfo_TwilioReturnsNull_ReturnsNullCallerInfoAndCannotReceiveSms | get phone number info twilio returns null returns null caller info and cannot receive sms | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 5 | | GetPhoneNumberInfo_TwilioThrowsException_ReturnsNullCallerInfoAndCannotReceiveSms | get phone number info twilio throws exception returns null caller info and cannot receive sms | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 6 | | GetPhoneNumberInfo_EligibleUsMobileNumber_SetsCanReceiveSmsTrue | get phone number info eligible us mobile number sets can receive sms true | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 7 | | GetPhoneNumberInfo_LandlineNumber_SetsCanReceiveSmsFalse | get phone number info landline number sets can receive sms false | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 8 | | GetPhoneNumberInfo_NonFixedVoipWithFlagOn_SetsCanReceiveSmsTrue | get phone number info non fixed voip with flag on sets can receive sms true | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 9 | | GetPhoneNumberInfo_NonFixedVoipWithFlagOff_SetsCanReceiveSmsFalse | get phone number info non fixed voip with flag off sets can receive sms false | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 10 | | GetPhoneNumberInfo_MapsLineTypeIntelligenceErrorCode | get phone number info maps line type intelligence error code | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 11 | | CanReceiveSms_NullCallerInfo_ReturnsFalse | can receive sms null caller info returns false | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 12 | | CanReceiveSms_ValidUsMobile_ReturnsTrue | can receive sms valid us mobile returns true | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 13 | | CanReceiveSms_NonMobileType_ReturnsFalse | can receive sms non mobile type returns false | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 14 | | CanReceiveSms_MobileTypeCaseInsensitive_ReturnsTrue | can receive sms mobile type case insensitive returns true | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 15 | | CanReceiveSms_NonFixedVoipTypeCaseInsensitiveWithFlagOn_ReturnsTrue | can receive sms non fixed voip type case insensitive with flag on returns true | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 16 | | CanReceiveSms_NonFixedVoipTypeCaseInsensitiveWithFlagOff_ReturnsFalse | can receive sms non fixed voip type case insensitive with flag off returns false | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 17 | | CanReceiveSms_NonUsCountryCode_ReturnsFalse | can receive sms non us country code returns false | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 18 | | CanReceiveSms_UsCountryCodeCaseInsensitive_ReturnsTrue | can receive sms us country code case insensitive returns true | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 19 | | CanReceiveSms_NonUsCallingCountryCode_ReturnsFalse | can receive sms non us calling country code returns false | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 20 | | CanReceiveSms_ValidFalse_ReturnsFalse | can receive sms valid false returns false | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 21 | | CanReceiveSms_ValidNull_ReturnsFalse | can receive sms valid null returns false | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 22 | | CanReceiveSms_IsAnonymousNumber_ReturnsFalse | can receive sms is anonymous number returns false | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 23 | | CanReceiveSms_IsAnonymousNumberWithNullCallerInfo_ReturnsFalse | can receive sms is anonymous number with null caller info returns false | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |

### Suggested Missing Tests
- **International numbers (E.164 with country code)** - Verify all parsing for non-US numbers.
- **Vanity numbers (1-800-FLOWERS)** - No test for letter-to-digit conversion if supported.
- **Numbers with extensions** - No test for 'x123' or 'ext. 123' suffixes.

### Improvement Suggestions / Irrelevant Tests
- **44 tests with overlapping format variants** - some cases collapse via [TestCase] parameterization.

## PhoneticMatchingServiceTests.cs

Source: `tests/ZoTools.UnitTests/Service/PhoneticMatchingServiceTests.cs` &middot; 11 test attribute(s) detected.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | FindMatches_ExactMatch_ReturnsCandidate | find matches exact match returns candidate | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 2 | | FindMatches_ClosePhoneticMatch_ReturnsCandidate | find matches close phonetic match returns candidate | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 3 | | FindMatches_NoMatchAboveThreshold_ReturnsEmpty | find matches no match above threshold returns empty | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 4 | | FindMatches_EmptyQuery_ReturnsEmpty | find matches empty query returns empty | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 5 | | FindMatches_QueryTooLong_ReturnsEmpty | find matches query too long returns empty | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 6 | | FindMatches_EmptyCandidates_ReturnsEmpty | find matches empty candidates returns empty | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 7 | | FindMatches_MispronunciationSupport_MatchesVariant | find matches mispronunciation support matches variant | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 8 | | FindMatches_MultipleCandidates_ReturnsSortedByDistance | find matches multiple candidates returns sorted by distance | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 9 | | FindMatches_ResultsSortedByDistanceAscending | find matches results sorted by distance ascending | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 10 | | FindMatches_QueryWithLeadingStreetNumber_MatchesCandidateWithSameNumber | find matches query with leading street number matches candidate with same number | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 11 | | FindMatches_QueryWithDigits_TokenizesDigitsAlongsideLetters | find matches query with digits tokenizes digits alongside letters | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |

### Suggested Missing Tests
- **Asian/Indian name patterns** - No test for non-Latin transliterations.
- **Threshold tuning** - No test asserting the matching threshold is honored at the boundary.
- **Hyphenated/double surnames** - No test for two-part names.

### Improvement Suggestions / Irrelevant Tests
- No specific concerns flagged; consider snapshotting (CS-006) where message contracts matter.

## PracticeProceduresGqlServiceTests.cs

Source: `tests/ZoTools.UnitTests/Service/PracticeProceduresGqlServiceTests.cs` &middot; 9 test attribute(s) detected.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | GetVisitReasonsForProviders_ReturnsProceduresWithSpecialties | get visit reasons for providers returns procedures with specialties | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 2 | | GetVisitReasonsForProviders_FiltersSpecialtiesToPracticeProviders | get visit reasons for providers filters specialties to practice providers | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 3 | | GetVisitReasonsForProviders_FetchesProviderSpecialtiesFromGql | get visit reasons for providers fetches provider specialties from gql | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 4 | | GetVisitReasonsForProviders_WhenNoMatchingSpecialties_ReturnsEmptySpecialtyList | get visit reasons for providers when no matching specialties returns empty specialty list | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 5 | | GetVisitReasonsForProviders_SpecialtyFilteringIsCaseInsensitive | get visit reasons for providers specialty filtering is case insensitive | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 6 | | GetVisitReasonsForProviders_WhenNoProviderIds_ReturnsEmpty | get visit reasons for providers when no provider ids returns empty | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 7 | | GetVisitReasonsForProviders_WhenNoProviderIds_DoesNotCallGetProviderProcedures | get visit reasons for providers when no provider ids does not call get provider procedures | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 8 | | GetVisitReasonsForProviders_PassesProviderIdsToGetProviderProcedures | get visit reasons for providers passes provider ids to get provider procedures | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 9 | | GetVisitReasonsForProviders_ReturnsProcedureIdsByProvider | get visit reasons for providers returns procedure ids by provider | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |

### Suggested Missing Tests
- **GQL error array** - No test for partial-error GraphQL responses.
- **Practice with no procedures** - No test for empty practice case.

### Improvement Suggestions / Irrelevant Tests
- No specific concerns flagged; consider snapshotting (CS-006) where message contracts matter.

## ProviderHelpersTests.cs

Source: `tests/ZoTools.UnitTests/Service/ProviderHelpersTests.cs` &middot; 70 test attribute(s) detected.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | FindTransferNumber_WhenLocationSpecificExists_ReturnsLocationNumber | find transfer number when location specific exists returns location number | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 2 | | FindTransferNumber_WhenNoLocationSpecific_ReturnsCatchAll | find transfer number when no location specific returns catch all | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 3 | | FindTransferNumber_WhenNull_ReturnsNull | find transfer number when null returns null | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 4 | | CalculateAge_BirthdayNotYetPassed_ReturnsCorrectAge | calculate age birthday not yet passed returns correct age | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 5 | | CalculateAge_BirthdayAlreadyPassed_ReturnsCorrectAge | calculate age birthday already passed returns correct age | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 6 | | GetAgeCategory_LocalDate_ReturnsExpected | get age category local date returns expected | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 7 | | GetAgeCategory_String_ReturnsExpected | get age category string returns expected | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 8 | | GetAgeCategory_String_InvalidInput_ReturnsNull | get age category string invalid input returns null | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 9 | | CanSeeAge_ReturnsExpected | can see age returns expected | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 10 | | CanSeeAge_WhenFlagNull_ReturnsTrue | can see age when flag null returns true | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 11 | | CanSeeAge_UnknownCategory_ReturnsTrue | can see age unknown category returns true | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 12 | | ParseName_SingleName_ReturnsAsLastName | parse name single name returns as last name | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 13 | | ParseName_TwoNames_SplitsFirstAndLast | parse name two names splits first and last | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 14 | | ParseName_ThreeNames_FirstAndRestAsLast | parse name three names first and rest as last | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 15 | | ParseName_NullOrEmpty_ReturnsEmpty | parse name null or empty returns empty | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 16 | | MapAgeCategoryToBlockedReason_KnownCategory_ReturnsExpected | map age category to blocked reason known category returns expected | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 17 | | MapAgeCategoryToBlockedReason_UnknownCategory_ReturnsNull | map age category to blocked reason unknown category returns null | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 18 | | GetSpokenName_NoIpaFields_ReturnsFullName | get spoken name no ipa fields returns full name | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 19 | | GetSpokenName_BothIpaFields_SubstitutesBothNames | get spoken name both ipa fields substitutes both names | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 20 | | GetSpokenName_OnlyFirstNameIpa_SubstitutesFirst | get spoken name only first name ipa substitutes first | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 21 | | GetSpokenName_OnlyLastNameIpa_SubstitutesLast | get spoken name only last name ipa substitutes last | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 22 | | GetSpokenName_NoDrPrefix_WithMdTitle_AddsDoctorPrefix | get spoken name no dr prefix with md title adds doctor prefix | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 23 | | GetSpokenName_NoDrPrefix_WithDoTitle_AddsDoctorPrefix | get spoken name no dr prefix with do title adds doctor prefix | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 24 | | GetSpokenName_NoDrPrefix_WithDdsTitle_AddsDoctorPrefix | get spoken name no dr prefix with dds title adds doctor prefix | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 25 | | GetSpokenName_NoDrPrefix_WithDmdTitle_AddsDoctorPrefix | get spoken name no dr prefix with dmd title adds doctor prefix | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 26 | | GetSpokenName_AlreadyHasDrPrefix_DoesNotDuplicate | get spoken name already has dr prefix does not duplicate | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 27 | | GetSpokenName_AlreadyHasDoctorPrefix_DoesNotDuplicate | get spoken name already has doctor prefix does not duplicate | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 28 | | GetSpokenName_NonDoctorTitle_NoPrefixAdded | get spoken name non doctor title no prefix added | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 29 | | GetSpokenName_NullTitle_NoPrefixAdded | get spoken name null title no prefix added | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 30 | | GetSpokenName_NullFullName_ReturnsEmpty | get spoken name null full name returns empty | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 31 | | GetSpokenName_WithIpa_PreservesTitlePrefix | get spoken name with ipa preserves title prefix | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 32 | | GetSpokenName_WithIpa_NoDrPrefix_WithCredential_AddsDoctorAndSubstitutes | get spoken name with ipa no dr prefix with credential adds doctor and substitutes | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 33 | | GetSpokenName_WhitespaceIpa_TreatedAsNoIpa | get spoken name whitespace ipa treated as no ipa | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 34 | | ResolveSpokenName_ProviderWithIpa_ReturnsSpokenName | resolve spoken name provider with ipa returns spoken name | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 35 | | ResolveSpokenName_ProviderWithoutIpa_ReturnsFullName | resolve spoken name provider without ipa returns full name | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 36 | | ResolveSpokenName_ProviderNotFound_ReturnsNull | resolve spoken name provider not found returns null | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 37 | | ResolveSpokenName_EmptyProviderIds_ReturnsNull | resolve spoken name empty provider ids returns null | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 38 | | ResolveSpokenName_MatchesFirstProviderIdInList | resolve spoken name matches first provider id in list | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |

### Suggested Missing Tests
- **Helpers under hostile inputs** - No test for null/empty/whitespace where helpers may NPE.
- **Locale-aware capitalization** - No test for 'McDonald' vs 'mcdonald' canonicalization.

### Improvement Suggestions / Irrelevant Tests
- **70 tests in helpers file** - some helpers may belong in dedicated services.

## ProviderSearchServiceTests.cs

Source: `tests/ZoTools.UnitTests/Service/ProviderSearchServiceTests.cs` &middot; 53 test attribute(s) detected.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | SearchProviders_NameSearch_SingleMatch_ReturnsProviderDetails | search providers name search single match returns provider details | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 2 | | SearchProviders_NameSearch_MultipleMatches_ReturnsSortedByDistance | search providers name search multiple matches returns sorted by distance | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 3 | | SearchProviders_NameSearch_NoMatchesAtLocation_ReturnsEmptyProviders | search providers name search no matches at location returns empty providers | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 4 | | SearchProviders_NameSearch_NoProvidersAtLocation_ReturnsEmptyProviders | search providers name search no providers at location returns empty providers | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 5 | | SearchProviders_NameSearch_ExcludesFacilityProviders | search providers name search excludes facility providers | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 6 | | SearchProviders_NameSearch_SkipsExcludedLocationsInLocationSwitchPool | search providers name search skips excluded locations in location switch pool | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 7 | | SearchProviders_ListAll_SkipsExcludedLocations | search providers list all skips excluded locations | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 8 | | SearchProviders_NameSearch_NullLocationIds_SearchesAllLocations | search providers name search null location ids searches all locations | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 9 | | SearchProviders_Locations_IncludeIsVirtualFlag | search providers locations include is virtual flag | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 10 | | SearchProviders_Locations_IsVirtualDefaultsToFalseWhenUnset | search providers locations is virtual defaults to false when unset | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 11 | | SearchProviders_NameSearch_ProviderAtMultipleRequestedLocations_IncludesAllLocations | search providers name search provider at multiple requested locations includes all locations | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 12 | | SearchProviders_NameSearch_LocationSwitch_IncludesLocationInfo | search providers name search location switch includes location info | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 13 | | SearchProviders_NameSearch_LocationSwitch_MultipleLocations_IncludesAllLocations | search providers name search location switch multiple locations includes all locations | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 14 | | SearchProviders_NameSearch_LocationSwitch_DuplicateProviderRows_DoNotDuplicateLocations | search providers name search location switch duplicate provider rows do not duplicate locations | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 15 | | SearchProviders_NameSearch_LocationSwitch_LocationNameFallsBackToName | search providers name search location switch location name falls back to name | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 16 | | SearchProviders_NameSearch_LocationSwitch_ExcludesVirtualLocations | search providers name search location switch excludes virtual locations | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 17 | | SearchProviders_FoundAtCurrentLocation_LocationsIsNull | search providers found at current location locations is null | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 18 | | SearchProviders_NullLocationIds_LocationsIsNull | search providers null location ids locations is null | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 19 | | SearchProviders_ListAll_NullNameWithSingleLocation_ReturnsAllProviders | search providers list all null name with single location returns all providers | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 20 | | SearchProviders_ListAll_EmptyNameWithSingleLocation_ReturnsAllProviders | search providers list all empty name with single location returns all providers | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 21 | | SearchProviders_ListAll_NullNameWithMultipleLocations_ReturnsProvidersAcrossLocations | search providers list all null name with multiple locations returns providers across locations | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 22 | | SearchProviders_ListAll_ProviderAtMultipleRequestedLocations_IncludesAllLocations | search providers list all provider at multiple requested locations includes all locations | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 23 | | SearchProviders_ListAll_NullNameWithNullLocations_ReturnsErrorMessage | search providers list all null name with null locations returns error message | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 24 | | SearchProviders_ListAll_WithProviderFilters_FiltersProvidersBySpecialty | search providers list all with provider filters filters providers by specialty | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 25 | | SearchProviders_ListAll_WithProviderFilters_FiltersProvidersByGender | search providers list all with provider filters filters providers by gender | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 26 | | SearchProviders_ListAll_ExcludesBlockedProviders | search providers list all excludes blocked providers | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 27 | | SearchProviders_WithProviderFilters_AppliesAllRequestedFilters | search providers with provider filters applies all requested filters | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 28 | | SearchProviders_WithProviderFilters_FiltersOutNonMatchingSpecialties | search providers with provider filters filters out non matching specialties | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 29 | | SearchProviders_WithProviderFilters_UsesCaseInsensitiveSubstringForSpecialty | search providers with provider filters uses case insensitive substring for specialty | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 30 | | SearchProviders_WithProviderFilters_FiltersToMatchingGender | search providers with provider filters filters to matching gender | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 31 | | SearchProviders_WithProviderFilters_FiltersToMatchingLanguage | search providers with provider filters filters to matching language | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 32 | | SearchProviders_WithProviderFilters_FiltersToMdDdsDmdDo | search providers with provider filters filters to md dds dmd do | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 33 | | SearchProviders_WithNoProviderFilters_DoesNotFilterMatches | search providers with no provider filters does not filter matches | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 34 | | SearchProviders_WithGenderCaseInsensitive_Matches | search providers with gender case insensitive matches | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 35 | | SearchProviders_ValidationIssueExcluded_MapsToExcluded | search providers validation issue excluded maps to excluded | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 36 | | SearchProviders_ValidationIssueAgeRestricted_MapsToDoesNotSeeChildren | search providers validation issue age restricted maps to does not see children | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 37 | | SearchProviders_ValidationIssueDoesNotPerformProcedure_MapsToDoesNotPerformProcedure | search providers validation issue does not perform procedure maps to does not perform procedure | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 38 | | SearchProviders_ValidationIssueExistingPatientCannotSeeNewProvider_MapsToSameBlockedReason | search providers validation issue existing patient cannot see new provider maps to same blocked reason | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 39 | | SearchProviders_NoValidationIssues_ReturnsNullBlockedReason | search providers no validation issues returns null blocked reason | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 40 | | SearchProviders_WithBothIpaFields_ReturnsProviderPronunciation | search providers with both ipa fields returns provider pronunciation | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 41 | | SearchProviders_ProviderPronunciation_UsesIpaPerNamePartWhenAvailable | search providers provider pronunciation uses ipa per name part when available | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 42 | | SearchProviders_WithSingleLocation_PassesLocationIdToValidation | search providers with single location passes location id to validation | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 43 | | SearchProviders_PhoneticCandidates_IncludeExpectedVariants | search providers phonetic candidates include expected variants | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 44 | | SearchProviders_PhoneticCandidates_ExcludesHonorificsAndSuffixesEmbeddedInFullName | search providers phonetic candidates excludes honorifics and suffixes embedded in full name | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 45 | | SearchProviders_PhoneticCandidates_SplitsNicknameOnSpaces | search providers phonetic candidates splits nickname on spaces | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 46 | | SearchProviders_NameSearch_LocationSwitch_ExcludesMatchesAboveTighterThreshold | search providers name search location switch excludes matches above tighter threshold | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 47 | | SearchProviders_NameSearch_LocationSwitch_IncludesMatchesBelowTighterThreshold | search providers name search location switch includes matches below tighter threshold | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 48 | | SearchProviders_NameSearch_ReturnsRequestedAndLocationSwitchMatchesTogether | search providers name search returns requested and location switch matches together | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 49 | | SearchProviders_PhoneticCandidates_SplitsLastNameOnSpaces | search providers phonetic candidates splits last name on spaces | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |

### Suggested Missing Tests
- **Search with no matches** - No test for empty-result branding/messaging.
- **Provider matching across multiple practices** - No deduplication test.
- **Specialty filter intersection** - No test for AND vs OR semantics with multiple specialties.

### Improvement Suggestions / Irrelevant Tests
- No specific concerns flagged; consider snapshotting (CS-006) where message contracts matter.

## SecretValidationServiceTests.cs

Source: `tests/ZoTools.UnitTests/Service/SecretValidationServiceTests.cs` &middot; 9 test attribute(s) detected.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | RequestHasProductionSecret_WhenHeaderMatchesProductionSecret_ShouldReturnTrue | request has production secret when header matches production secret should return true | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 2 | | RequestHasProductionSecret_WhenHeaderDoesNotMatch_ShouldReturnFalse | request has production secret when header does not match should return false | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 3 | | RequestHasProductionSecret_WhenHeaderIsMissing_ShouldReturnFalse | request has production secret when header is missing should return false | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 4 | | RequestHasTestSecret_WhenHeaderMatchesTestSecret_ShouldReturnTrue | request has test secret when header matches test secret should return true | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 5 | | RequestHasTestSecret_WhenHeaderDoesNotMatch_ShouldReturnFalse | request has test secret when header does not match should return false | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 6 | | RequestHasTestSecret_WhenHeaderIsMissing_ShouldReturnFalse | request has test secret when header is missing should return false | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 7 | | UserHasPhoneBotPhiReadRole_WhenIdentityIsNull_ReturnsFalse | user has phone bot phi read role when identity is null returns false | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 8 | | UserHasPhoneBotPhiReadRole_WhenUserHasRole_ReturnsTrue | user has phone bot phi read role when user has role returns true | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 9 | | UserHasPhoneBotPhiReadRole_WhenUserDoesNotHaveRole_ReturnsFalse | user has phone bot phi read role when user does not have role returns false | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |

### Suggested Missing Tests
- **Time-of-check-to-time-of-use** - No test that revoked secrets are rejected even if cached.
- **Constant-time comparison** - Confirm comparison is timing-safe (review item, hard to test directly).

### Improvement Suggestions / Irrelevant Tests
- **9 tests** is thin for a security-validation service.

## SmsServiceTests.cs

Source: `tests/ZoTools.UnitTests/Service/SmsServiceTests.cs` &middot; 137 test attribute(s) detected.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | SendConfirmSms_SingleAppointment_ReturnsTrue | send confirm sms single appointment returns true | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 2 | | SendConfirmSms_SendFails_ReturnsFalse | send confirm sms send fails returns false | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 3 | | SendConfirmSms_PassesCorrectSanitizedPhone | send confirm sms passes correct sanitized phone | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 4 | | SendConfirmSms_SingleAppointment_SendsCorrectContent | send confirm sms single appointment sends correct content | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 5 | | SendConfirmSms_TwoAppointments_SendsMultiFormat | send confirm sms two appointments sends multi format | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 6 | | SendConfirmSms_ThreeAppointments_IncludesAllThree | send confirm sms three appointments includes all three | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 7 | | SendConfirmSms_ThreeAppointments_DoesNotEmitTruncationMetric | send confirm sms three appointments does not emit truncation metric | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 8 | | SendConfirmSms_FourAppointments_SendsOnlyFirstThreeSortedByStartTime | send confirm sms four appointments sends only first three sorted by start time | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 9 | | SendConfirmSms_FourAppointments_EmitsTruncationMetric | send confirm sms four appointments emits truncation metric | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 10 | | SendConfirmSms_AppointmentsSortedBeforeTruncation_TakesEarliestThree | send confirm sms appointments sorted before truncation takes earliest three | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 11 | | SendConfirmSms_SomeAppointmentsNotFound_SendsSmsForFoundOnes | send confirm sms some appointments not found sends sms for found ones | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 12 | | SendConfirmSms_NoAppointmentsFound_ThrowsSessionAppointmentNotFoundException | send confirm sms no appointments found throws session appointment not found exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 13 | | SendConfirmSms_NullProviderName_UsesYourProviderFallback | send confirm sms null provider name uses your provider fallback | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 14 | | SendConfirmSms_NullStartTimeDescription_OmitsTimeFromContent | send confirm sms null start time description omits time from content | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 15 | | SendConfirmSms_NullPracticeName_ThrowsArgumentException | send confirm sms null practice name throws argument exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 16 | | SendConfirmSms_WhitespacePracticeName_ThrowsArgumentException | send confirm sms whitespace practice name throws argument exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 17 | | SendConfirmSms_SessionNotFound_ThrowsSessionNotFoundException | send confirm sms session not found throws session not found exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 18 | | SendConfirmSms_CallerPhoneNumberInfoIsNull_ThrowsSessionCallerInfoNotFoundException | send confirm sms caller phone number info is null throws session caller info not found exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 19 | | SendConfirmSms_CallerInfoIsNull_ThrowsSessionCallerInfoNotFoundException | send confirm sms caller info is null throws session caller info not found exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 20 | | SendConfirmSms_CanReceiveSmsIsFalse_ThrowsInvalidSmsPhoneNumberException | send confirm sms can receive sms is false throws invalid sms phone number exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 21 | | SendConfirmSms_IsAnonymousNumber_ThrowsInvalidSmsPhoneNumberException | send confirm sms is anonymous number throws invalid sms phone number exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 22 | | SendConfirmSms_InvalidUsPhoneNumber_ThrowsInvalidSmsPhoneNumberException | send confirm sms invalid us phone number throws invalid sms phone number exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 23 | | SendConfirmSms_PhoneNumberWithPlusPrefix_SanitizesCorrectly | send confirm sms phone number with plus prefix sanitizes correctly | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 24 | | SendConfirmSms_CanReceiveSmsIsFalse_DoesNotCallSendSmsMessage | send confirm sms can receive sms is false does not call send sms message | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 25 | | SendConfirmSms_MultiAppointment_NullProviderNameAndNullStartTimeDescription_OmitsBoth | send confirm sms multi appointment null provider name and null start time description omits both | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 26 | | SendConfirmSms_CustomPracticeName_UsedInContent | send confirm sms custom practice name used in content | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 27 | | SendConfirmSms_ConsentRefused_ThrowsSmsConsentRefusedException | send confirm sms consent refused throws sms consent refused exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 28 | | SendConfirmSms_ConsentRefused_DoesNotCallSendSmsMessage | send confirm sms consent refused does not call send sms message | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 29 | | SendConfirmSms_ConsentNull_SmsSucceeds_SavesSessionWithConsentTrue | send confirm sms consent null sms succeeds saves session with consent true | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 30 | | SendConfirmSms_ConsentNull_SmsFails_DoesNotSaveSession | send confirm sms consent null sms fails does not save session | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 31 | | SendConfirmSms_ConsentAlreadyGranted_SendsSms_DoesNotSaveSession | send confirm sms consent already granted sends sms does not save session | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 32 | | SendCancellationSms_Success_ReturnsTrue | send cancellation sms success returns true | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 33 | | SendCancellationSms_SendFails_ReturnsFalse | send cancellation sms send fails returns false | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 34 | | SendCancellationSms_SendsCorrectContent | send cancellation sms sends correct content | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 35 | | SendCancellationSms_NullProviderName_UsesYourProviderFallback | send cancellation sms null provider name uses your provider fallback | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 36 | | SendCancellationSms_NullStartTimeDescription_OmitsTimeFromContent | send cancellation sms null start time description omits time from content | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 37 | | SendCancellationSms_NullPracticeName_ThrowsArgumentException | send cancellation sms null practice name throws argument exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 38 | | SendCancellationSms_WhitespacePracticeName_ThrowsArgumentException | send cancellation sms whitespace practice name throws argument exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 39 | | SendCancellationSms_SessionNotFound_ThrowsSessionNotFoundException | send cancellation sms session not found throws session not found exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 40 | | SendCancellationSms_AppointmentNotFound_ThrowsSessionAppointmentNotFoundException | send cancellation sms appointment not found throws session appointment not found exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 41 | | SendCancellationSms_CallerPhoneNumberInfoIsNull_ThrowsSessionCallerInfoNotFoundException | send cancellation sms caller phone number info is null throws session caller info not found exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 42 | | SendCancellationSms_CallerInfoIsNull_ThrowsSessionCallerInfoNotFoundException | send cancellation sms caller info is null throws session caller info not found exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 43 | | SendCancellationSms_CanReceiveSmsIsFalse_ThrowsInvalidSmsPhoneNumberException | send cancellation sms can receive sms is false throws invalid sms phone number exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 44 | | SendCancellationSms_IsAnonymousNumber_ThrowsInvalidSmsPhoneNumberException | send cancellation sms is anonymous number throws invalid sms phone number exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 45 | | SendCancellationSms_InvalidUsPhoneNumber_ThrowsInvalidSmsPhoneNumberException | send cancellation sms invalid us phone number throws invalid sms phone number exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 46 | | SendCancellationSms_PassesCorrectSanitizedPhone | send cancellation sms passes correct sanitized phone | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 47 | | SendCancellationSms_AppointmentStatusIsCancelled_DoesNotThrow | send cancellation sms appointment status is cancelled does not throw | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 48 | | SendCancellationSms_AppointmentStatusIsNotCancelled_ThrowsAppointmentNotCancelledException | send cancellation sms appointment status is not cancelled throws appointment not cancelled exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 49 | | SendCancellationSms_AppointmentStatusIsNull_ThrowsAppointmentNotCancelledException | send cancellation sms appointment status is null throws appointment not cancelled exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 50 | | SendCancellationSms_ConsentRefused_ThrowsSmsConsentRefusedException | send cancellation sms consent refused throws sms consent refused exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 51 | | SendCancellationSms_ConsentRefused_DoesNotCallSendSmsMessage | send cancellation sms consent refused does not call send sms message | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 52 | | SendCancellationSms_ConsentNull_SmsSucceeds_SavesSessionWithConsentTrue | send cancellation sms consent null sms succeeds saves session with consent true | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 53 | | SendCancellationSms_ConsentNull_SmsFails_DoesNotSaveSession | send cancellation sms consent null sms fails does not save session | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 54 | | SendCancellationSms_ConsentAlreadyGranted_SendsSms_DoesNotSaveSession | send cancellation sms consent already granted sends sms does not save session | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 55 | | SendBookingConfirmedSms_Success_ReturnsTrue | send booking confirmed sms success returns true | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 56 | | SendBookingConfirmedSms_SendFails_ReturnsFalse | send booking confirmed sms send fails returns false | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 57 | | SendBookingConfirmedSms_SendsCorrectContent | send booking confirmed sms sends correct content | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 58 | | SendBookingConfirmedSms_NullProviderName_UsesYourProviderFallback | send booking confirmed sms null provider name uses your provider fallback | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 59 | | SendBookingConfirmedSms_TimezoneResolutionReturnsNullDescription_OmitsTimeFromContent | send booking confirmed sms timezone resolution returns null description omits time from content | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 60 | | SendBookingConfirmedSms_PassesCorrectSanitizedPhone | send booking confirmed sms passes correct sanitized phone | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |

### Suggested Missing Tests
- **Carrier error responses** - No test for Twilio carrier-level rejection codes (delivery failed, blocked).
- **Opt-out (STOP) flow** - No test that subsequent sends after opt-out are blocked.
- **Throttling/rate-limit** - No test for back-pressure when over limit.

### Improvement Suggestions / Irrelevant Tests
- **137 tests is the largest file in the service folder** - heavy parameterization without snapshots; consider Verify snapshots (CS-006).

## SpecialtyGroupingServiceTests.cs

Source: `tests/ZoTools.UnitTests/Service/SpecialtyGroupingServiceTests.cs` &middot; 10 test attribute(s) detected.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | GetRootSpecialties_WhenSpecialtyHasGrouping_ReturnsRootSpecialty | get root specialties when specialty has grouping returns root specialty | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 2 | | GetRootSpecialties_WhenSpecialtyHasNoGrouping_ReturnsSpecialtyAsIs | get root specialties when specialty has no grouping returns specialty as is | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 3 | | GetRootSpecialties_WhenSpecialtyHasNoGrouping_LogsWarning | get root specialties when specialty has no grouping logs warning | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 4 | | GetRootSpecialties_WhenMultipleMissingSpecialties_LogsOnceWithAllNames | get root specialties when multiple missing specialties logs once with all names | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 5 | | GetRootSpecialties_WhenSameMissingSpecialtyDifferentCasing_LogsOnlyOnce | get root specialties when same missing specialty different casing logs only once | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 6 | | GetRootSpecialties_WhenSameMissingSpecialtyCalledTwice_LogsOnlyOnce | get root specialties when same missing specialty called twice logs only once | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 7 | | GetRootSpecialties_WhenNoMissingSpecialties_DoesNotLog | get root specialties when no missing specialties does not log | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 8 | | GetRootSpecialties_WhenEmpty_ReturnsEmpty | get root specialties when empty returns empty | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 9 | | GetRootSpecialties_WhenMixOfGroupedAndUngrouped_ReturnsBoth | get root specialties when mix of grouped and ungrouped returns both | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 10 | | GetRootSpecialties_IsCaseInsensitive | get root specialties is case insensitive | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |

### Suggested Missing Tests
- **Specialty with no group** - No test for orphan specialties.
- **Cross-group ambiguity** - No test for a specialty that matches multiple groups.

### Improvement Suggestions / Irrelevant Tests
- No specific concerns flagged; consider snapshotting (CS-006) where message contracts matter.

## StateResolverTests.cs

Source: `tests/ZoTools.UnitTests/Service/StateResolverTests.cs` &middot; 11 test attribute(s) detected.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | GetAbbreviation_FullName_ReturnsAbbreviation | get abbreviation full name returns abbreviation | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 2 | | GetAbbreviation_Abbreviation_ReturnsSameAbbreviation | get abbreviation abbreviation returns same abbreviation | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 3 | | GetAbbreviation_CaseInsensitive_ReturnsAbbreviation | get abbreviation case insensitive returns abbreviation | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 4 | | GetAbbreviation_WithWhitespace_ReturnsAbbreviation | get abbreviation with whitespace returns abbreviation | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 5 | | GetAbbreviation_InvalidState_ReturnsNull | get abbreviation invalid state returns null | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 6 | | GetAbbreviation_Null_ReturnsNull | get abbreviation null returns null | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 7 | | GetAbbreviation_Empty_ReturnsNull | get abbreviation empty returns null | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 8 | | GetAbbreviation_DC_ReturnsAbbreviation | get abbreviation dc returns abbreviation | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 9 | | GetAbbreviation_AllStates_ReturnValidAbbreviations | get abbreviation all states return valid abbreviations | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 10 | | IsValidState_ValidState_ReturnsTrue | is valid state valid state returns true | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 11 | | IsValidState_InvalidState_ReturnsFalse | is valid state invalid state returns false | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |

### Suggested Missing Tests
- **Territories/non-state codes** - No test for PR, GU, VI, AS, DC.
- **Misspelled state name** - No test for fuzzy fallback or rejection.

### Improvement Suggestions / Irrelevant Tests
- No specific concerns flagged; consider snapshotting (CS-006) where message contracts matter.

## TimezoneServiceTests.cs

Source: `tests/ZoTools.UnitTests/Service/TimezoneServiceTests.cs` &middot; 80 test attribute(s) detected.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | ResolveAppointmentTimes_WithSingleTimezone_ReturnsLocalTimeWithoutTimezoneInDescription | resolve appointment times with single timezone returns local time without timezone in description | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 2 | | ResolveAppointmentTimes_WithMultipleLocationsSameTimezone_ReturnsLocalTimeWithoutTimezoneInDescription | resolve appointment times with multiple locations same timezone returns local time without timezone in description | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 3 | | ResolveAppointmentTimes_WithInvalidTimezoneString_FallsThroughToNextResolution | resolve appointment times with invalid timezone string falls through to next resolution | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 4 | | ResolveAppointmentTimes_WithUsOrUtcOffset_ReturnsNullLocalAndDualDescription | resolve appointment times with us or utc offset returns null local and dual description | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 5 | | ResolveAppointmentTimes_NonUsNonUtcOffset_ReturnsNullLocalTime | resolve appointment times non us non utc offset returns null local time | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 6 | | ResolveAppointmentTimes_PositiveOffset_ReturnsNullLocalTime | resolve appointment times positive offset returns null local time | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 7 | | ResolveAppointmentTimes_AlwaysPopulatesStartTimeWithOffset | resolve appointment times always populates start time with offset | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 8 | | ResolveAppointmentTimes_FormatStartTimeWithOffset_Fractional | resolve appointment times format start time with offset fractional | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 9 | | FindMatchingUsZone_ReturnsExpectedZone | find matching us zone returns expected zone | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 10 | | FindMatchingUsZone_WithNonUsOffset_ReturnsNull | find matching us zone with non us offset returns null | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 11 | | FindMatchingUsZone_WithFractionalOffset_ReturnsNull | find matching us zone with fractional offset returns null | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 12 | | FindMatchingUsZone_OnDstTransitionDay_UsesActualInstantNotMonth | find matching us zone on dst transition day uses actual instant not month | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 13 | | GetOrdinalSuffix_ReturnsExpected | get ordinal suffix returns expected | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 14 | | FormatTime_ReturnsExpected | format time returns expected | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 15 | | FormatDayAndDate_AllMonths_ReturnsExpected | format day and date all months returns expected | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 16 | | FormatDescriptionWithoutTimezone_CombinesDayDateAndTime | format description without timezone combines day date and time | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 17 | | FormatDualTimezoneDescription_WhenSameDate_ReturnsSingleDateWithBothTimes | format dual timezone description when same date returns single date with both times | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 18 | | FormatDualTimezoneDescription_WhenDateMismatch_IncludesBothDatesAndRecordsMetric | format dual timezone description when date mismatch includes both dates and records metric | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 19 | | TryGetLocationTimezone_WithValidMatchingTimezone_ReturnsTimezone | try get location timezone with valid matching timezone returns timezone | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 20 | | TryGetLocationTimezone_WithNoMatchingLocations_ReturnsNull | try get location timezone with no matching locations returns null | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 21 | | TryGetLocationTimezone_WithNullTimezone_ReturnsNull | try get location timezone with null timezone returns null | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 22 | | TryGetLocationTimezone_WithWhitespaceTimezone_ReturnsNull | try get location timezone with whitespace timezone returns null | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 23 | | TryGetLocationTimezone_WithMultipleLocationsSameTimezone_ReturnsTimezone | try get location timezone with multiple locations same timezone returns timezone | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 24 | | TryGetLocationTimezone_WithMultipleLocationsDifferentTimezones_ReturnsNull | try get location timezone with multiple locations different timezones returns null | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 25 | | TryGetLocationTimezone_WithInvalidTimezoneString_ReturnsNull | try get location timezone with invalid timezone string returns null | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |

### Suggested Missing Tests
- **DST spring-forward / fall-back boundaries** - Critical given the provider-calendar SQUAWK-6090. Verify all conversions over the DST hour gap.
- **IANA tz updates** - No test simulating a tz database update (e.g., Brazil DST removal).
- **Locations with no tz mapping** - No test for fallback behavior.

### Improvement Suggestions / Irrelevant Tests
- **80 tests but DST scenarios appear under-represented** relative to risk (per provider-calendar findings).

## TwilioServiceTests.cs

Source: `tests/ZoTools.UnitTests/Service/TwilioServiceTests.cs` &middot; 12 test attribute(s) detected.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | LookupPhoneNumber_WithEmptyAccountSid_ReturnsNull | lookup phone number with empty account sid returns null | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 2 | | LookupPhoneNumber_WithNullAccountSid_ReturnsNull | lookup phone number with null account sid returns null | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 3 | | LookupPhoneNumber_WithEmptyAuthToken_ReturnsNull | lookup phone number with empty auth token returns null | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 4 | | LookupPhoneNumber_WithNullAuthToken_ReturnsNull | lookup phone number with null auth token returns null | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 5 | | LookupPhoneNumber_WithBothSettingsEmpty_ReturnsNull | lookup phone number with both settings empty returns null | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 6 | | LookupPhoneNumber_WithEmptyAccountSid_LogsError | lookup phone number with empty account sid logs error | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 7 | | LookupPhoneNumber_WithEmptyAuthToken_LogsError | lookup phone number with empty auth token logs error | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 8 | | LookupPhoneNumber_WithEmptyAccountSid_IncrementsConfigurationErrorMetric | lookup phone number with empty account sid increments configuration error metric | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 9 | | LookupPhoneNumber_WithEmptyAuthToken_IncrementsConfigurationErrorMetric | lookup phone number with empty auth token increments configuration error metric | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 10 | | LookupPhoneNumber_CalledMultipleTimesWithMissingSettings_OnlyLogsOnce | lookup phone number called multiple times with missing settings only logs once | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 11 | | LookupPhoneNumber_CalledMultipleTimesWithMissingSettings_OnlyIncrementsMetricOnce | lookup phone number called multiple times with missing settings only increments metric once | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 12 | | LookupPhoneNumber_CalledMultipleTimesWithMissingSettings_AlwaysReturnsNull | lookup phone number called multiple times with missing settings always returns null | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |

### Suggested Missing Tests
- **Webhook signature validation** - No test that incoming Twilio webhooks are signature-verified.
- **Idempotency keys on outbound** - No test for duplicate-send protection.

### Improvement Suggestions / Irrelevant Tests
- **Only 12 tests** for a service that touches a paid external API with significant failure surface.

## ValidationServiceTests.cs

Source: `tests/ZoTools.UnitTests/Service/ValidationServiceTests.cs` &middot; 107 test attribute(s) detected.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | ValidateAvailability_NoDiagnostics_ReturnsIsValidTrue | validate availability no diagnostics returns is valid true | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 2 | | ValidateAvailability_ProviderLocationInvalid_ReturnsCorrectIssue | validate availability provider location invalid returns correct issue | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 3 | | ValidateAvailability_ProcedureUnmapped_ReturnsCorrectIssue | validate availability procedure unmapped returns correct issue | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 4 | | ValidateAvailability_LongDuration_WithoutIsNewPatient_IsIgnored | validate availability long duration without is new patient is ignored | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 5 | | ValidateAvailability_LongDuration_New_WithNewPatient_ReturnsProcedureUnmapped | validate availability long duration new with new patient returns procedure unmapped | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 6 | | ValidateAvailability_LongDuration_New_WithExistingPatient_IsIgnored | validate availability long duration new with existing patient is ignored | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 7 | | ValidateAvailability_LongDuration_Existing_WithExistingPatient_ReturnsProcedureUnmapped | validate availability long duration existing with existing patient returns procedure unmapped | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 8 | | ValidateAvailability_LongDuration_Existing_WithNewPatient_IsIgnored | validate availability long duration existing with new patient is ignored | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 9 | | ValidateAvailability_LongDuration_Both_WithNewPatient_ReturnsProcedureUnmapped | validate availability long duration both with new patient returns procedure unmapped | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 10 | | ValidateAvailability_LongDuration_Both_WithExistingPatient_ReturnsProcedureUnmapped | validate availability long duration both with existing patient returns procedure unmapped | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 11 | | ValidateAvailability_MultipleIssues_ReturnsAll | validate availability multiple issues returns all | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 12 | | ValidateAvailability_UnknownErrorLabel_IsIgnored | validate availability unknown error label is ignored | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 13 | | ValidateAvailability_WhenPracticeNotFound_ReturnsNull | validate availability when practice not found returns null | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 14 | | ValidateBooking_ExcludedProvider_ReturnsExcludedIssue | validate booking excluded provider returns excluded issue | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 15 | | ValidateBooking_AgeRestrictedChild_ReturnsAgeRestrictedWithChildren | validate booking age restricted child returns age restricted with children | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 16 | | ValidateBooking_AgeRestrictedTeenager_ReturnsAgeRestrictedWithTeenagers | validate booking age restricted teenager returns age restricted with teenagers | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 17 | | ValidateBooking_AgeRestrictedYoungAdult_ReturnsAgeRestrictedWithYoungAdults | validate booking age restricted young adult returns age restricted with young adults | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 18 | | ValidateBooking_AgeRestrictedAdult_ReturnsAgeRestrictedWithAdults | validate booking age restricted adult returns age restricted with adults | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 19 | | ValidateBooking_AgeRestrictedSenior_ReturnsAgeRestrictedWithSeniors | validate booking age restricted senior returns age restricted with seniors | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 20 | | ValidateBooking_NoDob_SkipsAgeCheck | validate booking no dob skips age check | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 21 | | ValidateBooking_ProviderHasNoAgeRangesInPracticeInfo_SkipsAgeCheck | validate booking provider has no age ranges in practice info skips age check | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 22 | | ValidateBooking_LocationId_UsesAgeRangesForThatLocation | validate booking location id uses age ranges for that location | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 23 | | ValidateBooking_LocationId_SkipsAgeCheckWhenProviderNotAtLocation | validate booking location id skips age check when provider not at location | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 24 | | ValidateBooking_NoLocationId_UsesAnyProviderAgeRanges | validate booking no location id uses any provider age ranges | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 25 | | ValidateBooking_ProcedureNotSupported_ReturnsDoesNotPerformProcedure | validate booking procedure not supported returns does not perform procedure | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 26 | | ValidateBooking_ProviderNotInProcedureMap_ReturnsDoesNotPerformProcedure | validate booking provider not in procedure map returns does not perform procedure | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 27 | | ValidateBooking_NoVisitReasonId_SkipsProcedureCheck | validate booking no visit reason id skips procedure check | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 28 | | ValidateBooking_GqlFailure_SkipsProcedureCheck | validate booking gql failure skips procedure check | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 29 | | ValidateBooking_AllChecksPass_ReturnsEmptyList | validate booking all checks pass returns empty list | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 30 | | ValidateBooking_AllIssuesPresent_ReturnsAllInPriorityOrder | validate booking all issues present returns all in priority order | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 31 | | ValidateBooking_NullOrEmptyProviderId_ReturnsEmptyList | validate booking null or empty provider id returns empty list | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 32 | | ValidateBooking_InactiveProcedure_ReturnsDoesNotPerformProcedure | validate booking inactive procedure returns does not perform procedure | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 33 | | ValidateBooking_ContinuityPolicyNull_SkipsCheck | validate booking continuity policy null skips check | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 34 | | ValidateBooking_ContinuityPolicyOfferOtherProviders_SkipsCheck | validate booking continuity policy offer other providers skips check | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 35 | | ValidateBooking_TransferToStaff_PastRealizedWithDifferentProvider_ReturnsIssue | validate booking transfer to staff past realized with different provider returns issue | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 36 | | ValidateBooking_TransferToStaff_PastRealizedWithSameProvider_NoIssue | validate booking transfer to staff past realized with same provider no issue | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 37 | | ValidateBooking_TransferToStaff_FutureAppointment_NoIssue | validate booking transfer to staff future appointment no issue | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 38 | | ValidateBooking_TransferToStaff_NonRealizedStatus_NoIssue | validate booking transfer to staff non realized status no issue | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 39 | | ValidateBooking_TransferToStaff_NoAppointments_NoIssue | validate booking transfer to staff no appointments no issue | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 40 | | ValidateBooking_TransferToStaff_EmptyAppointments_NoIssue | validate booking transfer to staff empty appointments no issue | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 41 | | ValidateBooking_TransferToStaff_NullCallId_SkipsCheck | validate booking transfer to staff null call id skips check | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 42 | | ValidateBooking_TransferToStaff_NullPatientId_SkipsCheck | validate booking transfer to staff null patient id skips check | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 43 | | ValidateBooking_TransferToStaff_PracticeLookupThrows_SkipsCheck | validate booking transfer to staff practice lookup throws skips check | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 44 | | ValidateBooking_TransferToStaff_PastRealizedAppointmentWithBlankProviderIds_NoIssue | validate booking transfer to staff past realized appointment with blank provider ids no issue | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 45 | | ValidateProviders_MultipleProviders_ReturnsIssuesPerProvider | validate providers multiple providers returns issues per provider | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 46 | | ValidateProviders_EmptyProvidersList_ReturnsEmptyDictionary | validate providers empty providers list returns empty dictionary | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 47 | | ValidateProviders_SkipsNullAndEmptyProviderIds | validate providers skips null and empty provider ids | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 48 | | ValidateProviders_FetchesProcedureMapOnceForAllProviders | validate providers fetches procedure map once for all providers | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 49 | | ValidateProviders_AllChecksPass_ReturnsEmptyIssuesPerProvider | validate providers all checks pass returns empty issues per provider | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 50 | | ValidateProviders_GqlFailure_SkipsProcedureCheckForAllProviders | validate providers gql failure skips procedure check for all providers | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 51 | | ValidateProviders_TransferToStaff_AppliesContinuityCheckPerProvider | validate providers transfer to staff applies continuity check per provider | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 52 | | ValidateProviders_FallbackDob_UsedWhenPatientIdIsNull | validate providers fallback dob used when patient id is null | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 53 | | ValidateProviders_SessionDob_TakesPrecedenceOverFallback | validate providers session dob takes precedence over fallback | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 54 | | ValidateProviders_FallbackDob_IgnoredWhenSessionHasDob | validate providers fallback dob ignored when session has dob | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 55 | | ValidateProviders_NoAgeCheck_WhenBothPatientIdAndFallbackDobNull | validate providers no age check when both patient id and fallback dob null | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 56 | | ValidateNewEhrPatient_WhenPatientNotFound_ThrowsInvalidOperationException | validate new ehr patient when patient not found throws invalid operation exception | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 57 | | ValidateNewEhrPatient_WithAllRequiredFields_ReturnsEmptyList | validate new ehr patient with all required fields returns empty list | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 58 | | ValidateNewEhrPatient_MissingFirstName_IncludesFieldInResult | validate new ehr patient missing first name includes field in result | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 59 | | ValidateNewEhrPatient_MissingLastName_IncludesFieldInResult | validate new ehr patient missing last name includes field in result | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 60 | | ValidateNewEhrPatient_MissingEmail_IncludesFieldInResult | validate new ehr patient missing email includes field in result | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |

### Suggested Missing Tests
- **Cross-field validation** - No test for rules that span multiple fields (e.g., DOB + age consistency).
- **Localized validation messages** - No test that messages match the i18n contract.

### Improvement Suggestions / Irrelevant Tests
- **107 tests with heavy [TestCase]** - consider Verify snapshots over message strings to detect copy drift (CS-006).

## VisitReasonLlmMatchServiceTests.cs

Source: `tests/ZoTools.UnitTests/Service/VisitReasonLlmMatchServiceTests.cs` &middot; 6 test attribute(s) detected.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | FindMatches_WhenLlmIdentifiesMatches_ReturnsMatchingIds | find matches when llm identifies matches returns matching ids | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 2 | | FindMatches_WhenLlmReturnsNoMatches_ReturnsEmpty | find matches when llm returns no matches returns empty | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 3 | | FindMatches_WhenCandidatesEmpty_ReturnsEmptyWithoutCallingLlm | find matches when candidates empty returns empty without calling llm | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 4 | | FindMatches_WhenLlmReturnsNull_ReturnsEmpty | find matches when llm returns null returns empty | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 5 | | FindMatches_WhenLlmReturnsUnknownIds_FiltersThemOut | find matches when llm returns unknown ids filters them out | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 6 | | FindMatches_WhenLlmReturnsManyMatches_LimitsTo5 | find matches when llm returns many matches limits to5 | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |

### Suggested Missing Tests
- **LLM 5xx / timeout** - No test for what the service does when the LLM call fails.
- **Confidence threshold** - No test asserting low-confidence matches are rejected.
- **Prompt injection defenses** - No test that user-supplied free-text cannot exfiltrate prompt content.

### Improvement Suggestions / Irrelevant Tests
- **6 tests** for an LLM-backed service with high failure-mode surface area.

## VisitReasonSearchServiceTests.cs

Source: `tests/ZoTools.UnitTests/Service/VisitReasonSearchServiceTests.cs` &middot; 26 test attribute(s) detected.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | SearchVisitReasons_WhenLlmMatchFound_ReturnsFound | search visit reasons when llm match found returns found | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 2 | | SearchVisitReasons_WhenLlmNoMatchFound_ReturnsEmpty | search visit reasons when llm no match found returns empty | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 3 | | SearchVisitReasons_WhenNoProcedures_ReturnsEmpty | search visit reasons when no procedures returns empty | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 4 | | SearchVisitReasons_WhenNoProcedures_LlmIsNotCalled | search visit reasons when no procedures llm is not called | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 5 | | SearchVisitReasons_PassesScopedProviderIds_ToGqlService | search visit reasons passes scoped provider ids to gql service | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 6 | | SearchVisitReasons_WhenNewPatientExcluded_MarkedAsExcluded | search visit reasons when new patient excluded marked as excluded | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 7 | | SearchVisitReasons_WhenExistingPatientExcluded_MarkedAsExcluded | search visit reasons when existing patient excluded marked as excluded | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 8 | | SearchVisitReasons_WhenNoAppointments_ExclusionsNotApplied | search visit reasons when no appointments exclusions not applied | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 9 | | SearchVisitReasons_WhenDifferentStatusPerResult_AppliesCorrectExclusions | search visit reasons when different status per result applies correct exclusions | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 10 | | SearchVisitReasons_WithAppointments_CallsBatchMethod | search visit reasons with appointments calls batch method | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 11 | | SearchVisitReasons_WithoutPatientId_DoesNotFetchAppointments | search visit reasons without patient id does not fetch appointments | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 12 | | SearchVisitReasons_WhenNoExclusionLists_SkipsNewPatientDetermination | search visit reasons when no exclusion lists skips new patient determination | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 13 | | SearchVisitReasons_WithEmptyPatientId_DoesNotFetchAppointments | search visit reasons with empty patient id does not fetch appointments | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 14 | | SearchVisitReasons_WhenSessionNotFound_ExclusionsNotApplied | search visit reasons when session not found exclusions not applied | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 15 | | SearchVisitReasons_WhenGetAppointmentsThrows_ExclusionsNotApplied | search visit reasons when get appointments throws exclusions not applied | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 16 | | SearchVisitReasons_WhenLlmFindsMatch_EmitsLlmFoundIncrement | search visit reasons when llm finds match emits llm found increment | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 17 | | SearchVisitReasons_WhenLlmReturnsNothing_EmitsLlmNotFoundIncrement | search visit reasons when llm returns nothing emits llm not found increment | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 18 | | SearchVisitReasons_WhenProviderIdSet_MarksUnperformedAsBlocked | search visit reasons when provider id set marks unperformed as blocked | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 19 | | SearchVisitReasons_WhenExcludedAndProviderDoesNotPerform_ExcludedTakesPriority | search visit reasons when excluded and provider does not perform excluded takes priority | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 20 | | SearchVisitReasons_WhenProviderIdNull_NoBlockedReason | search visit reasons when provider id null no blocked reason | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 21 | | SearchVisitReasons_WhenLocationIdSet_SetsOtherProvidersPerform | search visit reasons when location id set sets other providers perform | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 22 | | SearchVisitReasons_WhenLocationIdNull_OtherProvidersPerformIsFalse | search visit reasons when location id null other providers perform is false | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 23 | | SearchVisitReasons_WhenSpecialtySet_FiltersResults | search visit reasons when specialty set filters results | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 24 | | SearchVisitReasons_WhenSpecialtyNull_ReturnsAll | search visit reasons when specialty null returns all | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 25 | | SearchVisitReasons_WhenProviderAndLocationSet_BothFieldsPopulated | search visit reasons when provider and location set both fields populated | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 26 | | SearchVisitReasons_WhenLocationIdSet_ExcludesTargetProviderFromOtherProviders | search visit reasons when location id set excludes target provider from other providers | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |

### Suggested Missing Tests
- **Spell-check / typo tolerance** - No test that 'sor throat' resolves to 'sore throat'.
- **Homonyms** - No test for visit reasons that share names across specialties.

### Improvement Suggestions / Irrelevant Tests
- No specific concerns flagged; consider snapshotting (CS-006) where message contracts matter.

## ZipCodeServiceTests.cs

Source: `tests/ZoTools.UnitTests/Service/ZipCodeServiceTests.cs` &middot; 9 test attribute(s) detected.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | ResolveZip_ValidZip_ReturnsZipCodeInfo | resolve zip valid zip returns zip code info | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 2 | | ResolveZip_InvalidZip_ReturnsNull | resolve zip invalid zip returns null | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 3 | | ResolveZip_NonNumeric_ReturnsNull | resolve zip non numeric returns null | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 4 | | ResolveZip_FourDigitZip_PrependsZero | resolve zip four digit zip prepends zero | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 5 | | ResolveZip_Null_ReturnsNull | resolve zip null returns null | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 6 | | ResolveZip_Empty_ReturnsNull | resolve zip empty returns null | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |
| 7 | | ResolveZip_KnownZips_ReturnsResults | resolve zip known zips returns results | Arrange service + mocks &rarr; invoke method &rarr; assert result/exception/log/metric. | Behavior implied by name. | Unit |

### Suggested Missing Tests
- **5/9 vs 9-digit zips** - No test for ZIP+4 normalization.
- **Canadian postal codes** - No test for behavior with non-US format inputs.

### Improvement Suggestions / Irrelevant Tests
- No specific concerns flagged; consider snapshotting (CS-006) where message contracts matter.


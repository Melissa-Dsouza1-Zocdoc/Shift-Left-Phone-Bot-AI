# ZoTools.Web.UnitTests / Other (root) - Unit Test Mapping

> Repo: [Zocdoc/zo-tools](https://github.com/Zocdoc/zo-tools)
> Folder: `tests/ZoTools.Web.UnitTests/` (subset: non-`*AgentToolsImpl*` root files)
> Generated: 2026-05-07

This file maps the remaining root-level unit test specs that are not per-agent tool implementations. The agent-specific tools (Greeting/ManageAppointment/SelectLocation/SelectProvider/SelectTimeslot/SelectVisitReason `*ImplTests`) are mapped in `ZoTools-Web-UnitTests-AgentTools-test-mapping.md`.

Files mapped here:
- AbImplTests.cs
- AppointmentImplTests.cs
- AudioImplTests.cs
- CallCompletedImplTests.cs
- CallIdLogEnrichmentMiddlewareTests.cs
- CsatToolImplTests.cs
- DebugToolsImplTests.cs
- ExampleTests.cs (placeholder - skipped)
- HttpLoggingMiddlewareTests.cs
- InitializeCallImplTests.cs
- PatientInfoImplTests.cs
- PracticeFaqToolsImplTests.cs
- PracticeInfoImplTests.cs
- PracticeInfoResponseExtensionsTests.cs
- SmsImplTests.cs
- ToolSchemaResponseSerializationTests.cs
- ToolsSchemaImplTests.cs
- ValidationToolsImplTests.cs

---

## AbImplTests.cs

Tests `AbImpl` (the AB/feature-flag adapter) which wraps `IAbService` and routes the visitor id based on `AbVisitorType` (PracticeId / CallCenter / CallerPhoneNumber / CallId). Both `IsFlagOn` and `GetVariant` are tested across visitor types, success/failure paths, and exception swallowing (returns false / empty string + logs error).

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | Yes | IsFlagOn_WithPracticeIdVisitorType_PassesPracticeIdAsVisitorId | PracticeId visitor type forwards practiceId | Mock IAbService; call w/ PracticeId | Verify visitorId == practiceId | Adapter |
| 2 | Yes | IsFlagOn_WithCallCenterVisitorType_PassesPracticePhoneNumberAsVisitorId | CallCenter -> practicePhoneNumber | Mock; call | Args match | Adapter |
| 3 | Yes | IsFlagOn_WithCallerPhoneNumberVisitorType_PassesCallerPhoneNumberAsVisitorId | CallerPhoneNumber -> callerPhoneNumber | Mock; call | Args match | Adapter |
| 4 | Yes | IsFlagOn_WithCallIdVisitorType_PassesCallIdAsVisitorId | CallId -> callId | Mock; call | Args match | Adapter |
| 5 | Yes | IsFlagOn_WhenServiceReturnsTrue_ReturnsTrue | Flag-on result propagates | Mock true | Returns true | Adapter |
| 6 | Yes | IsFlagOn_WhenServiceReturnsFalse_ReturnsFalse | Flag-off propagates | Mock false | Returns false | Adapter |
| 7 | Yes | IsFlagOn_WhenServiceThrows_ReturnsFalse | Service throws -> default to false (do not crash caller) | Mock throws | Returns false | Adapter (resilience) |
| 8 | Yes | IsFlagOn_WhenServiceThrows_LogsError | And the error is logged | Mock throws + log capture | Error logged once | Adapter (observability) |
| 9 | Yes | IsFlagOn_WithUnknownVisitorType_ReturnsFalse | Unknown enum value -> false | Cast invalid; call | Returns false | Adapter |
| 10 | Yes | IsFlagOn_AllVisitorTypes_DoNotThrow ([Values]) | Exhaustive enum coverage | All values | None throws | Adapter |
| 11 | Yes | GetVariant_WithPracticeIdVisitorType_PassesPracticeIdAsVisitorId | Variant lookup uses practiceId | Mock; call | Args match | Adapter |
| 12 | Yes | GetVariant_WithCallCenterVisitorType_PassesPracticePhoneNumberAsVisitorId | Variant uses practicePhoneNumber | Mock; call | Args match | Adapter |
| 13 | Yes | GetVariant_WithCallerPhoneNumberVisitorType_PassesCallerPhoneNumberAsVisitorId | Variant uses callerPhoneNumber | Mock; call | Args match | Adapter |
| 14 | Yes | GetVariant_WithCallIdVisitorType_PassesCallIdAsVisitorId | Variant uses callId | Mock; call | Args match | Adapter |
| 15 | Yes | GetVariant_WhenServiceReturnsValue_ReturnsVariantValue | Variant value propagates | Mock returns "treatment" | Returns "treatment" | Adapter |
| 16 | Yes | GetVariant_WhenServiceThrows_ReturnsEmptyString | Service throws -> "" (do not crash) | Mock throws | Returns "" | Adapter (resilience) |
| 17 | Yes | GetVariant_WhenServiceThrows_LogsError | And the error is logged | Mock throws + capture | Error logged once | Adapter |
| 18 | Yes | GetVariant_WithUnknownVisitorType_ReturnsEmptyString | Unknown enum -> "" | Cast invalid; call | Returns "" | Adapter |
| 19 | Yes | GetVariant_AllVisitorTypes_DoNotThrow ([Values]) | Exhaustive enum coverage | All values | None throws | Adapter |

### Suggested Missing Tests

- Cancellation token forwarding to `IAbService` (these methods are async).
- Empty / null `flagName` argument validation.
- Empty / null visitorId components (e.g., empty practiceId for `PracticeId` visitor type) - lock down whether the call still goes through or short-circuits.
- Logged error includes structured properties (flagName, visitorType) - shape useful for Sentry filtering.
- The "service throws" tests use a generic Exception. Worth one test with a `TaskCanceledException` to confirm cancellation is re-thrown rather than swallowed (cancellation should bubble up, not return false).

### Improvement Suggestions / Irrelevant Tests

- `IsFlagOn` and `GetVariant` mirror each other almost perfectly. Consider extracting a `[TestCaseSource]` matrix that drives both methods through the same scenarios.
- The visitor-type forwarding tests (#1-#4 / #11-#14) are mocks-only verify-args (per CS-021 borderline). They are justified because the mapping IS the behavior under test (it's the only code path), but they could be replaced by a single matrix test driving all visitor types into a captured-args check.
- No test is irrelevant.

---

## AppointmentImplTests.cs

The largest spec file in the suite (2121 lines, ~95 tests). Tests `AppointmentImpl` - the controller that wraps `RescheduleAppointment`, `CancelAppointment`, `UpdateRescheduleLocation`, and `SubmitBooking`. Heavy use of `OffsetDateTime` for tz-aware times, `IZoCallSessionRepository` for session/cache, `IAppointmentManagementService` for the upstream booking system, and `IPhoneBotRepositoryCache` for practice info.

Setup at line 37 wires the system-under-test with mocked services. Shared helpers (lines ~1213-1485) build representative request bodies and configure the mocks for preview/confirm flows, location lookups, and reschedule param regeneration.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | Yes | RescheduleAppointment_WhenRescheduleBlocked_ReturnsTransferDirective | Blocked appt -> transfer directive (no booking call) | Cache w/ blocked reason; call | Transfer directive returned | Controller |
| 2 | Yes | RescheduleAppointment_WhenProviderExcluded_EmitsExcludedProviderCallEvent | Excluded-provider blocked reason emits structured event | Cache w/ ExcludedProvider; capture events | Event emitted once | Controller (observability) |
| 3 | Yes | RescheduleAppointment_WhenBlockedReasonIsNotExcludedProvider_DoesNotEmitExcludedProviderCallEvent | Other blocked reasons do NOT emit excluded-provider event | Cache w/ different reason | Event NOT emitted | Controller |
| 4 | Yes | RescheduleAppointment_WhenRescheduleBlocked_DoesNotCallPreviewService | Blocked path skips preview | Verify mock | Times.Never | Controller |
| 5 | Yes | RescheduleAppointment_WhenRescheduleBlocked_DoesNotCallRescheduleService | Blocked path skips reschedule | Verify mock | Times.Never | Controller |
| 6 | Yes | RescheduleAppointment_WhenNoBlockedReason_ProceedsNormally | Happy path proceeds | Cache w/o reason | Service called | Controller |
| 7 | Yes | RescheduleAppointment_WhenNoSession_ProceedsNormally | No session -> still proceeds (does not block on missing session) | No session | Service called | Controller (resilience) |
| 8 | Yes | RescheduleAppointment_AcceptLanguage_PassesExpectedLanguageToService ([TestCase 4x]) | "es-US"/"en-US"/""/null map to "es-US"/"en-US"/"en-US"/"en-US" | Iterate cases | Default fallback en-US | Controller (i18n) |
| 9 | Yes | RescheduleAppointment_PreviewWithResult_ReturnsOkWithSpeakSideEffect | Preview success -> 200 with speak directive | Mock preview | OK + speak | Controller |
| 10 | Yes | RescheduleAppointment_PreviewWithNull_ReturnsNotFound | Preview null -> 404 | Mock null | NotFound | Controller |
| 11 | Yes | RescheduleAppointment_ConfirmWithResult_ReturnsOkWithSpeakSideEffect | Confirm success -> 200 + speak | Mock confirm | OK + speak | Controller |
| 12 | Yes | RescheduleAppointment_ConfirmWithNull_ReturnsNotFound | Confirm null -> 404 | Mock null | NotFound | Controller |
| 13 | Yes | RescheduleAppointment_ConfirmWithValidationError_Returns200WithTransferSideEffect | Validation error -> 200 + transfer | Mock validation error | OK + transfer | Controller (UX) |
| 14 | Yes | RescheduleAppointment_InteropTimeSlotNotAvailable_Returns200WithErrorSideEffect | Interop "slot not available" -> 200 + error directive | Mock InteropException w/ TimeSlotNotAvailable | OK + error | Controller |
| 15 | Yes | RescheduleAppointment_InteropPatientRuleException_Returns200WithTransferSideEffect | PatientRule -> 200 + transfer | Mock | OK + transfer | Controller |
| 16 | Yes | RescheduleAppointment_InteropProviderNotMappedInSync_Returns200WithTransferSideEffect | ProviderNotMapped -> 200 + transfer | Mock | OK + transfer | Controller |
| 17 | Yes | RescheduleAppointment_InteropUnknownError_Returns200WithTransferSideEffect | Unknown interop error -> 200 + transfer (do not crash) | Mock | OK + transfer | Controller (resilience) |
| 18 | Yes | RescheduleAppointment_ConfirmSuccess_UpdatesCachedAppointmentInSession | Successful confirm updates the cached appt | Mock; verify cache write | Cache write happens | Controller |
| 19 | Yes | RescheduleAppointment_ConfirmSuccess_ClearsBlockedReasons | Successful confirm clears any blocked reasons | Cache w/ reason -> confirm -> verify cleared | Cleared | Controller |
| 20 | Yes | RescheduleAppointment_ConfirmSuccess_UpdatesStartTime | Cached startTime updated to new time | Mock; verify field | New start time | Controller |
| 21 | Yes | RescheduleAppointment_ConfirmSuccess_StillReturnsSpeakDirectiveWhenCacheSaveFails | Cache save failure does NOT break the response | Mock cache throws | Speak directive still returned | Controller (resilience) |
| 22 | Yes | RescheduleAppointment_ValidationError_DoesNotUpdateCache | Validation error path skips cache update | Mock; verify Times.Never | No cache write | Controller |
| 23 | Yes | RescheduleAppointment_WhenNoSession_ConfirmSuccess_DoesNotAttemptCacheUpdate | No session + success -> no cache write attempt | Mock; verify | Times.Never | Controller |
| 24 | Yes | RescheduleAppointment_ConfirmSuccess_PreservesStartTimeDescriptionAsPrevious | startTimeDescription becomes previousStartTimeDescription | Mock; verify | Field moved | Controller (UX) |
| 25 | Yes | RescheduleAppointment_ConfirmSuccess_ClearsStartTimeDescription | startTimeDescription is cleared after preserving | Verify | Cleared | Controller |
| 26 | Yes | RescheduleAppointment_ConfirmSuccess_WhenStartTimeDescriptionIsNull_PreviousStartTimeDescriptionIsNull | Null preserves as null (not "") | Verify | Null | Controller |
| 27 | Yes | CancelAppointment_WhenCancelBlocked_ReturnsTransferDirective | Cancel-blocked appt -> transfer directive | Cache w/ blocked reason | Transfer | Controller |
| 28 | Yes | CancelAppointment_WhenCancelBlocked_DoesNotCallCancelService | And cancel service is not called | Verify | Times.Never | Controller |
| 29 | Yes | CancelAppointment_WhenNoBlockedReason_ProceedsNormally | Happy path | Cache w/o reason | Service called | Controller |
| 30 | Yes | CancelAppointment_PreviewWithCachedAppointment_ReturnsOkWithSpeakDirective | Preview returns speak directive built from cached appt | Cache; call preview | Speak directive | Controller |
| 31 | Yes | CancelAppointment_PreviewUsesLocalTimeForTts | TTS uses LOCAL time (not UTC) for natural speech | Cache w/ tz; call | Speak text contains local-time string | Controller (UX) |
| 32 | Yes | CancelAppointment_PreviewWithNullStartTimeLocal_FallsBackToScheduledTime | Null local time falls back to scheduled time | Cache w/ null local | Falls back gracefully | Controller |
| 33 | Yes | CancelAppointment_PreviewWithNoSession_ReturnsNotFound | No session -> 404 | No session | NotFound | Controller |
| 34 | Yes | CancelAppointment_ConfirmWithCachedAppointment_ReturnsOkWithSpeakDirective | Confirm w/ cache returns speak directive | Cache; call confirm | OK + speak | Controller |
| 35 | Yes | CancelAppointment_ConfirmUsesLocalTimeForTts | Confirm path also uses local time | Cache; verify | Local time in TTS | Controller (UX) |
| 36 | Yes | CancelAppointment_ConfirmWithNullStartTimeLocal_FallsBackToScheduledTime | Null local -> fallback | Cache | Fallback | Controller |
| 37 | Yes | CancelAppointment_ConfirmWithNoSession_ReturnsNotFound | No session on confirm -> 404 | Verify | NotFound | Controller |
| 38 | Yes | CancelAppointment_ConfirmCallsCancelAppointment | Confirm invokes cancel service | Verify mock called | Times.Once | Controller |
| 39 | Yes | CancelAppointment_ConfirmWithGeneratedPatientId_ReturnsTransferDirective | Generated (synthetic) patientId on confirm -> transfer (do not actually cancel) | Cache w/ generated id | Transfer | Controller (security) |
| 40 | Yes | CancelAppointment_ConfirmThrowsException_ReturnsTransferDirective | Service throws -> transfer (graceful degradation) | Mock throws | Transfer | Controller (resilience) |
| 41 | Yes | CancelAppointment_SpanishLanguage_ReturnsSpeakInSpanish | Spanish accept-language -> Spanish speak directive | Mock; call w/ es-US | Spanish text | Controller (i18n) |
| 42 | Yes | CancelAppointment_EnglishLanguage_ReturnsSpeakInEnglish | English accept-language -> English speak directive | Mock; call w/ en-US | English text | Controller (i18n) |
| 43 | Yes | CancelAppointment_ConfirmSuccess_UpdatesCachedAppointmentStatusToCancelled | Success updates status field in cache | Verify field | Status=Cancelled | Controller |
| 44 | Yes | CancelAppointment_ConfirmSuccess_StillReturnsSpeakDirectiveWhenCacheSaveFails | Cache save failure does not break response | Mock cache throws | Speak still returned | Controller (resilience) |
| 45 | Yes | CancelAppointment_CancelServiceThrows_DoesNotUpdateCache | Service throw skips cache update | Verify Times.Never | No write | Controller |
| 46 | Yes | UpdateRescheduleLocation_PhysicalOnly_UpdatesLocationIdToPhysicalLocation | Physical-only path picks physical location | Setup; call | Cache updated | Controller |
| 47 | Yes | UpdateRescheduleLocation_PhysicalOnly_CallsRegenerateRescheduleParamsWithCorrectArgs | And service is called with correct args | Verify args | Match | Controller |
| 48 | Yes | UpdateRescheduleLocation_PhysicalOnly_SavesSessionWithUpdatedLocationId | Session is saved with new locationId | Verify | New id saved | Controller |
| 49 | Yes | UpdateRescheduleLocation_VirtualOnly_UpdatesLocationIdToVirtualLocation | Virtual-only path picks virtual | Setup; call | Cache updated | Controller |
| 50 | Yes | UpdateRescheduleLocation_VirtualOnly_CallsRegenerateRescheduleParamsWithCorrectArgs | Service called for virtual | Verify args | Match | Controller |
| 51 | Yes | UpdateRescheduleLocation_VirtualOnly_SavesSessionWithVirtualLocationId | Session saved w/ virtual id | Verify | Match | Controller |
| 52 | Yes | UpdateRescheduleLocation_PhysicalOnly_SelectsLocationMatchingAppointmentLocationIds | When multiple physical locations exist, pick the one matching the appt | Mock 2 physical | Correct one chosen | Controller |
| 53 | Yes | UpdateRescheduleLocation_HybridLocationType_Returns400 | Hybrid input is invalid | Call w/ Hybrid | BadRequest | Controller (validation) |
| 54 | Yes | UpdateRescheduleLocation_UnknownLocationType_Returns400 | Unknown input is invalid | Call w/ Unknown | BadRequest | Controller |
| 55 | Yes | UpdateRescheduleLocation_NoMatchingPhysicalLocation_ReturnsTransferDirective | No matching physical location -> transfer (graceful) | Setup w/ no physical match | Transfer | Controller |
| 56 | Yes | UpdateRescheduleLocation_NoMatchingVirtualLocation_ReturnsTransferDirective | No matching virtual -> transfer | Setup w/ no virtual | Transfer | Controller |
| 57 | Yes | UpdateRescheduleLocation_SessionNotFound_ReturnsTransferDirective | Missing session -> transfer | No session | Transfer | Controller (resilience) |
| 58 | Yes | UpdateRescheduleLocation_AppointmentNotInSession_ReturnsTransferDirective | Appt absent -> transfer | Session w/o appt | Transfer | Controller |
| 59 | Yes | UpdateRescheduleLocation_RescheduleParamsNull_ReturnsTransferDirective | Null params -> transfer | Cache w/ null params | Transfer | Controller |
| 60 | Yes | UpdateRescheduleLocation_AppointmentLocationIdsDoNotMatchNearbyLocations_ReturnsTransferDirective | Mismatched location ids -> transfer | Setup mismatch | Transfer | Controller |
| 61 | Yes | UpdateRescheduleLocation_SaveThrowsException_ReturnsTransferDirective | Save throws -> transfer (resilient) | Mock throws | Transfer | Controller (resilience) |
| 62 | Yes | UpdateRescheduleLocation_EmptyNearbyLocations_ReturnsTransferDirective | No nearby locations cached -> transfer | Setup empty | Transfer | Controller |
| 63 | Yes | UpdateRescheduleLocation_GetPracticeInfoThrows_ReturnsTransferDirective | Practice info lookup throws -> transfer | Mock throws | Transfer | Controller (resilience) |
| 64 | Yes | UpdateRescheduleLocation_FindAppointmentThrows_ReturnsTransferDirective | Repo throws -> transfer | Mock throws | Transfer | Controller (resilience) |
| 65 | Yes | SubmitBooking_ConfirmFalse_ReturnsSpeakDirective | Preview path returns speak | Mock preview | OK + speak | Controller |
| 66 | Yes | SubmitBooking_EmptyAcceptLanguage_DefaultsToEnUs | Empty header defaults to en-US | Verify args | en-US | Controller (i18n) |
| 67 | Yes | SubmitBooking_ConfirmFalse_PassesCorrectParamsToService | Preview args | Verify | Match | Controller |
| 68 | Yes | SubmitBooking_WhenBuildPreviewSpeakThrows_ReturnsTransferDirective | Preview build throws -> transfer | Mock throws | Transfer | Controller |
| 69 | Yes | SubmitBooking_ConfirmTrue_CallsExecuteBooking | Confirm path invokes ExecuteBooking | Verify mock | Called | Controller |
| 70 | Yes | SubmitBooking_ConfirmTrue_WithAvailabilityNotFound_ReturnsErrorDirective | AvailabilityNotFound -> error directive | Mock | Error directive | Controller |
| 71 | Yes | SubmitBooking_ConfirmTrue_WhenExecuteBookingReturnsExcludedProvider_EmitsExcludedProviderCallEvent | Excluded provider on booking emits event | Mock + event capture | Emitted once | Controller (observability) |
| 72 | Yes | SubmitBooking_ConfirmTrue_WhenExecuteBookingReturnsNonExcludedProviderError_DoesNotEmitExcludedProviderCallEvent | Other booking errors do NOT emit excluded-provider event | Mock other error | Not emitted | Controller |
| 73 | Yes | SubmitBooking_ConfirmTrue_WithNullResult_ReturnsTransferDirective | Null booking result -> transfer | Mock null | Transfer | Controller |
| 74 | Yes | SubmitBooking_ConfirmTrue_PassesCorrectBookingParams | Booking args | Verify | Match | Controller |
| 75 | Yes | SubmitBooking_ConfirmTrue_UsesAvailabilityStrategyNone | Default strategy is None | Verify args | Strategy=None | Controller |
| 76 | Yes | SubmitBooking_ConfirmTrue_OmitsAvailabilityStrategyWithoutThrowing | Omitted strategy does not throw | Call w/o strategy | No throw | Controller (resilience) |
| 77 | Yes | SubmitBooking_ConfirmTrue_MapsAllFieldsToNewBookingParams | Full field mapping | Verify all args | All mapped | Controller |
| 78 | Yes | SubmitBooking_WhenPatientNotInSession_ReturnsBadRequest | No patient in session -> 400 | No session patient | BadRequest | Controller (auth) |
| 79 | Yes | SubmitBooking_Unauthenticated_ValidatesWithNewPatientRules | Unauthenticated path uses new-patient rules | Setup | Validates correctly | Controller |
| 80 | Yes | SubmitBooking_Authenticated_ValidatesWithExistingPatientRules | Authenticated path uses existing-patient rules | Setup | Validates correctly | Controller |
| 81 | Yes | SubmitBooking_WhenValidationFails_ReturnsMissingFieldsError | Missing required field -> error response | Setup invalid | Returns missing-fields error | Controller (validation) |
| 82 | Yes | SubmitBooking_WhenValidationFails_DoesNotCallExecuteBooking | And does not invoke booking | Verify | Times.Never | Controller |
| 83 | Yes | SubmitBooking_WhenValidationPasses_PassesPatientToBookingParams | Patient object forwarded to booking | Verify | Patient passed | Controller |
| 84 | Yes | SubmitBooking_ConfirmSuccess_ExistingPatient_CachesWithIsNewPatientFalse | Cache reflects authenticated booking | Verify field | isNew=false | Controller |
| 85 | Yes | SubmitBooking_ConfirmSuccess_NewPatientWithGeneratedId_CachesWithIsNewPatientTrue | Cache reflects synthetic-id new patient | Verify | isNew=true | Controller |
| 86 | Yes | SubmitBooking_ConfirmSuccess_NewPatient_PassesNullPatientIdToInterop | Interop receives null patientId for new patients (interop creates id) | Verify args | Null | Controller (security) |
| 87 | Yes | SubmitBooking_ConfirmSuccess_ExistingPatient_PassesPatientIdToInterop | Existing patient -> patientId forwarded | Verify | Match | Controller |
| 88 | Yes | SubmitBooking_ConfirmSuccess_CacheFailure_StillReturnsSpeakDirective | Cache failure does not break response | Mock cache throws | Speak returned | Controller (resilience) |
| 89 | Yes | SubmitBooking_BookingFails_DoesNotCacheAppointment | Failed booking -> no cache write | Verify Times.Never | No write | Controller |
| 90 | Yes | SubmitBooking_ConfirmSuccess_CachesCorrectStartTimeFields | Cached startTime fields are correct | Verify fields | Match | Controller |
| 91 | Yes | SubmitBooking_ConfirmSuccess_CachesProviderAndLocationNames | Cached provider/location names are populated | Verify | Match | Controller |

### Suggested Missing Tests

- Cancellation token forwarding to all upstream services (the impl is async but cancellation behavior is not asserted).
- DST / time-zone edge cases: a reschedule across DST boundary - is the resulting `OffsetDateTime` correct?
- Concurrent reschedule requests on the same call (race on cache write).
- Idempotency on duplicate confirm (caller resubmits after timeout) - is the second call idempotent or does it double-cancel/double-book?
- The "transfer directive" tests (#55-#64) all assert `TransferNumberType=Appointment`. Worth one test that documents fallback to a different TransferNumberType when configured.
- Acceptance-language: only "es-US"/"en-US"/""/null tested. Add a "fr-CA" or other unsupported language to confirm en-US fallback is the rule (not en-CA, etc.).
- The "ExcludedProvider event" assertions (#2 and #71) test event emission count but not the event payload shape. The payload is what an analytics dashboard consumes.
- `SubmitBooking` validation tests do not cover all field combinations (e.g., missing dob + missing phone).

### Improvement Suggestions / Irrelevant Tests

- Several test pairs assert "X happens" + "Y does not happen" as separate tests (e.g., #4/#5, #28, #82, #89). Per CS-021 these could be combined into single tests that assert both outcomes - shorter and clearer.
- The interop-error tests (#14-#17) and update-reschedule-location resilience tests (#55-#64) follow a great pattern (every failure mode -> graceful transfer). Keep this pattern; it is the right level of coverage for an LLM-driven controller.
- A handful of mocks-only verify tests (#67, #74, #77) verify args without confirming a side effect. These are borderline per CS-021; they are justified because the args define the LLM->service contract, but consider pairing them with integration tests that confirm the resulting behavior end-to-end.
- Spanish/English tests (#41, #42) only check the language of the speak directive. Worth a snapshot of the full text via Verify (per CS-006) so wording iterations are reviewable.
- No test is irrelevant.

---

## AudioImplTests.cs

Tests `AudioImpl.GetAudioUploadUrls` - returns presigned S3 URLs for uploading call audio. Three tests covering happy path, 404 (not found), and exception propagation.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | Yes | GetAudioUploadUrls_ReturnsPresignedUrl | Happy path returns 200 with presigned URL | Mock service; call | URL returned | Controller |
| 2 | Yes | GetAudioUploadUrls_WhenNotFound_Returns404 | Service NotFound -> 404 | Mock not found | NotFound | Controller |
| 3 | Yes | GetAudioUploadUrls_WhenServiceThrows_PropagatesException | Service exception is rethrown (NOT swallowed) | Mock throws | Re-throws | Controller |

### Suggested Missing Tests

- The presigned URL has a TTL - is it reflected in the response headers / body?
- Multiple URLs (call has multiple audio segments) - is each correctly mapped?
- Argument validation: empty/null callId.
- Cancellation token forwarding.

### Improvement Suggestions / Irrelevant Tests

- Three tests is light for a critical upload-flow endpoint. Add the missing tests above.
- All three tests are valuable; none are irrelevant.

---

## CallCompletedImplTests.cs

Tests `PostCallCompleted` - the endpoint that posts the call transcript, costs, tool calls, and identifiers (TwilioCallSid, LivekitRoomSid, BookingId, CancellationId) to the transcript service after the call ends. ~20 tests cover request mapping fidelity, null-safety, error propagation, and the ZO2 framework tag.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | Yes | PostCallCompleted_CallsTranscriptServiceWithMappedRequest | Service called with mapped request | Verify args | Match | Controller |
| 2 | Yes | PostCallCompleted_ReturnsOk | Happy path returns 200 | Mock; call | OK | Controller |
| 3 | Yes | PostCallCompleted_MapsTurnsToTranscript | Turn-array -> transcript field | Mock; verify field | Maps | Controller |
| 4 | Yes | PostCallCompleted_WhenTurnTextIsNull_MapsToEmptyString | Null turn text -> "" (do not crash) | Mock w/ null text | "" | Controller (defensive) |
| 5 | Yes | PostCallCompleted_MapsSessionFields | Session info forwarded | Verify | Match | Controller |
| 6 | Yes | PostCallCompleted_WhenNoSession_SetsSessionToNull | No session -> null in upstream request | Verify | Null | Controller |
| 7 | Yes | PostCallCompleted_SetsAgentFrameworkToZo2 | agentFramework hardcoded to "Zo2" | Verify | Equals "Zo2" | Controller |
| 8 | Yes | PostCallCompleted_WhenTranscriptServiceThrows_PropagatesException | Service throws -> rethrows (NOT swallowed) | Mock throws | Throws | Controller |
| 9 | Yes | PostCallCompleted_WhenTranscriptServiceReturnsError_ReturnsBadRequest | Service returns error result -> 400 | Mock error result | BadRequest | Controller |
| 10 | Yes | PostCallCompleted_MapsCostsToLineItems | Costs array -> lineItems array | Verify | Maps | Controller |
| 11 | Yes | PostCallCompleted_WhenNoCosts_SetsLineItemsToEmpty | No costs -> [] (not null) | Verify | Empty list | Controller |
| 12 | Yes | PostCallCompleted_MapsBookingAndCancellationIds | Both ids mapped | Verify | Match | Controller |
| 13 | Yes | PostCallCompleted_MapsAuditableFields | Auditable fields (call duration, etc.) mapped | Verify | Match | Controller |
| 14 | Yes | PostCallCompleted_MapsTwilioCallSid | TwilioCallSid mapped | Verify | Match | Controller |
| 15 | Yes | PostCallCompleted_WhenNoTwilioCallSid_SetsToNull | Missing -> null | Verify | Null | Controller |
| 16 | Yes | PostCallCompleted_MapsLivekitRoomSid | LivekitRoomSid mapped | Verify | Match | Controller |
| 17 | Yes | PostCallCompleted_WhenNoLivekitRoomSid_SetsToNull | Missing -> null | Verify | Null | Controller |
| 18 | Yes | PostCallCompleted_WhenNoBookingOrCancellationIds_SetsToEmpty | Missing -> empty (not null) | Verify | Empty | Controller |
| 19 | Yes | PostCallCompleted_MapsToolCallRoleToFunctionResult | Tool-call role gets the FunctionResult role label | Verify | Mapping | Controller |
| 20 | Yes | PostCallCompleted_MapsToolCallsOnTurn | Per-turn tool calls mapped | Verify | Match | Controller |
| 21 | Yes | PostCallCompleted_MapsToolResultOnToolCall | Tool result attached to tool call | Verify | Match | Controller |
| 22 | Yes | PostCallCompleted_MapsNullToolResult | Null tool result handled gracefully | Verify | Maps to null | Controller (defensive) |
| 23 | Yes | PostCallCompleted_WhenNoToolCalls_SetsToolCallsToNull | No tool calls -> null (not []) | Verify | Null | Controller |

### Suggested Missing Tests

- Empty-transcript case (call with zero turns).
- Very large transcript (50k+ chars) - performance / payload-size limit.
- Cost line items with negative/zero values.
- Cancellation token forwarding.
- Argument validation: callId required, but empty/null behavior is untested.
- ToolCalls with malformed JSON-arg payloads (parser robustness).

### Improvement Suggestions / Irrelevant Tests

- Coverage is strong on field-by-field mapping fidelity. Worth a Verify snapshot of the full upstream request payload for one representative scenario - reduces 23 tests to 1 + a few negative cases.
- The pattern of "MapsX" + "WhenNoX_SetsToNull/Empty" is consistent and useful. Keep.
- Tests #22 and #23 are slightly contradictory in convention (null tool result vs empty list for no tool calls) - the inconsistency is a real product decision and should be documented in code, but the tests pinning it are correct.

---

## CallIdLogEnrichmentMiddlewareTests.cs

Tests middleware that pushes the call_id header into the Serilog `LogContext` (so every log line during a request includes call_id) and into the Sentry scope (tag + context). Two test classes: log-context enrichment (tests 1-6) and Sentry enrichment (tests 7-12). The Serilog test class hosts a custom in-memory `ILogEventSink` that captures log events during middleware execution.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | Yes | InvokeAsync_WithCallIdHeader_PushesCallIdToLogContext | call_id header -> LogContext property on every log line | Set header; invoke; check captured logs | Property present | Middleware |
| 2 | Yes | InvokeAsync_WithoutCallIdHeader_DoesNotPushCallIdToLogContext | No header -> no enrichment | No header; invoke | No property | Middleware |
| 3 | Yes | InvokeAsync_WithEmptyCallIdHeader_DoesNotPushCallIdToLogContext | Empty header -> no enrichment | Header=""; invoke | No property | Middleware (defensive) |
| 4 | Yes | InvokeAsync_WithWhitespaceCallIdHeader_DoesNotPushCallIdToLogContext | Whitespace -> no enrichment | Header="   "; invoke | No property | Middleware (defensive) |
| 5 | Yes | InvokeAsync_WithCallIdHeader_CallsNext | Pipeline still proceeds | Verify next() invoked | Times.Once | Middleware |
| 6 | Yes | InvokeAsync_WithoutCallIdHeader_CallsNext | Missing header does not block pipeline | Verify | Times.Once | Middleware |
| 7 | Yes | InvokeAsync_WithCallIdHeader_SetsSentryTag | Sentry scope tag is set | Mock Sentry; invoke | Tag set | Middleware (Sentry) |
| 8 | Yes | InvokeAsync_WithoutCallIdHeader_DoesNotSetSentryTag | No header -> no tag | Verify | Not set | Middleware |
| 9 | Yes | InvokeAsync_WithEmptyCallIdHeader_DoesNotSetSentryTag | Empty -> no tag | Verify | Not set | Middleware (defensive) |
| 10 | Yes | InvokeAsync_WithWhitespaceCallIdHeader_DoesNotSetSentryTag | Whitespace -> no tag | Verify | Not set | Middleware (defensive) |
| 11 | Yes | InvokeAsync_WithCallIdHeader_SetsCallViewerContext | call viewer link is added as Sentry context | Verify | Context set with viewer URL | Middleware (Sentry/observability) |
| 12 | Yes | InvokeAsync_WithoutCallIdHeader_DoesNotSetCallViewerContext | No header -> no context | Verify | Not set | Middleware |

### Suggested Missing Tests

- Multiple call_id headers (HTTP header collection allows duplicates) - which one wins?
- Very long call_id (e.g., > 4kb) - is it truncated for the Sentry tag (which has a 200-char limit)?
- Exception in `next()` - does the middleware still set tag/context BEFORE the exception, so Sentry captures the call_id alongside the error? This is the whole point of the Sentry enrichment and is currently NOT tested.
- The scope is properly disposed/popped after the request (no LogContext bleeding into the next request).
- Concurrent requests on the same scope - LogContext should be request-scoped (push/pop discipline).

### Improvement Suggestions / Irrelevant Tests

- Tests 5 and 6 (CallsNext) assert pipeline continuation but not response status - low value as standalone tests (the Push tests cover this implicitly). Could be removed or merged.
- The "did not set tag/context" tests (8/9/10/12) are valuable defensive guards.
- Strongly recommend adding the "exception in next()" test - that is the load-bearing scenario for the Sentry enrichment.

---

## CsatToolImplTests.cs

Tests `SubmitCsat` - the post-call CSAT submission endpoint. The contract: never return an error to the caller (LLM agent), even if the upstream throws. Returns 200 with `success=true` on the happy path and 200 with `success=false` on any exception. Five tests.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | Yes | SubmitCsat_ReturnsOkWithSuccessTrue | Happy path returns 200 + success=true | Mock service; call | OK + true | Tool impl |
| 2 | Yes | SubmitCsat_CallsCsatServiceWithMappedParams | Service receives mapped params | Verify args | Match | Tool impl |
| 3 | Yes | SubmitCsat_WhenCsatSubmissionException_ReturnsOkWithSuccessFalse | Specific CsatSubmissionException -> 200 + success=false (do not 4xx/5xx) | Mock throws | OK + false | Tool impl (resilience) |
| 4 | Yes | SubmitCsat_WhenUnexpectedException_ReturnsOkWithSuccessFalse | Generic exception -> same | Mock generic | OK + false | Tool impl (resilience) |
| 5 | Yes | SubmitCsat_WithNullFeedback_CallsCsatServiceWithNullFeedback | Null feedback forwarded as null (not "") | Verify args | Null | Tool impl |

### Suggested Missing Tests

- The exception path should LOG the exception for observability (Sentry / Serilog) - not asserted.
- Argument validation: empty/null callId, score out of range (e.g., 6 on a 1-5 scale).
- Cancellation token forwarding.
- Score boundaries (1, 5, 0, -1, 100) - lock down what the impl considers valid.

### Improvement Suggestions / Irrelevant Tests

- The "always 200" contract is the load-bearing behavior here. All 5 tests pull weight. Add the missing logging assertion - it is what makes the swallowed exceptions observable.

---

## DebugToolsImplTests.cs

Setup at line 24 includes the SUT with mocked repository. Tests two endpoints (`GetCachedAppointments`, `GetSession`) plus the heavy private mappers `MapToResponseItem`, `MapAvailabilityStrategy`, `MapStatus`, `MapBlockedReason`, `MapLocationType`, `MapToPatientResponse`, `MapCallerPhoneNumberInfo`, `MapGender`. Most mappers are tested with `[TestCase]` rows + an exhaustive `[Values]` test + an invalid-value test. ~50 tests total.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | Yes | GetCachedAppointments_WhenRepositoryReturnsNull_Returns404 | Null repo result -> 404 | Mock null | NotFound | Endpoint |
| 2 | Yes | GetCachedAppointments_WhenRepositoryReturnsEmptyList_Returns200WithEmptyAppointments | Empty list -> 200 + [] | Mock empty | OK + [] | Endpoint |
| 3 | Yes | GetCachedAppointments_PassesCallIdAndPatientIdToRepository | Repo args | Verify | Match | Endpoint |
| 4 | Yes | GetCachedAppointments_WhenAppointmentsExist_ReturnsMappedAppointments | Appts mapped to response | Mock; verify | Mapped | Endpoint |
| 5 | Yes | GetCachedAppointments_IncludesResolvedPatientIdInResponse | Resolved patientId attached to response | Verify | Match | Endpoint |
| 6 | Yes | MapToResponseItem_MapsAllFields | Full field mapping | Build cached appt; map | All fields populated | Mapper |
| 7 | Yes | MapToResponseItem_WhenNullableFieldsAreNull_MapsNulls | Null in -> null out (no crash) | Build w/ nulls | Nulls preserved | Mapper |
| 8 | Yes | MapAvailabilityStrategy_ReturnsExpected ([TestCase x3]) | None/UseZdProcedure/UseDuration map 1:1 | Iterate | Match | Mapper |
| 9 | Yes | MapAvailabilityStrategy_AllValues_DoesNotThrow ([Values]) | Exhaustive | All values | None throws | Mapper |
| 10 | Yes | MapAvailabilityStrategy_InvalidValue_Throws | Out-of-range cast throws | Cast invalid | Throws | Mapper |
| 11 | Yes | MapStatus_ReturnsExpected ([TestCase x3]) | Confirmed/Cancelled/Rescheduled map 1:1 | Iterate | Match | Mapper |
| 12 | Yes | MapStatus_NullInput_ReturnsNull | Null -> null | Pass null | Null | Mapper |
| 13 | Yes | MapStatus_AllValues_DoesNotThrow ([Values]) | Exhaustive | All values | None throws | Mapper |
| 14 | Yes | MapStatus_InvalidValue_ReturnsNull | Invalid -> null (lenient) | Cast invalid | Null | Mapper |
| 15 | Yes | MapBlockedReason_ReturnsExpected ([TestCase x2]) | Two enum values map 1:1 | Iterate | Match | Mapper |
| 16 | Yes | MapBlockedReason_NullInput_ReturnsNull | Null -> null | Pass null | Null | Mapper |
| 17 | Yes | MapBlockedReason_AllValues_DoesNotThrow ([Values]) | Exhaustive | All values | None throws | Mapper |
| 18 | Yes | MapBlockedReason_InvalidValue_Throws | Out-of-range cast throws | Cast invalid | Throws | Mapper |
| 19 | Yes | MapLocationType_ReturnsExpected ([TestCase x4]) | All four enum values map 1:1 | Iterate | Match | Mapper |
| 20 | Yes | MapLocationType_AllValues_DoesNotThrow ([Values]) | Exhaustive | All values | None throws | Mapper |
| 21 | Yes | MapLocationType_InvalidValue_Throws | Out-of-range cast throws | Cast invalid | Throws | Mapper |
| 22 | Yes | GetSession_WhenSessionNotFound_Returns404 | No session -> 404 | Mock null | NotFound | Endpoint |
| 23 | Yes | GetSession_WhenPatientsIsNull_NoPatientIdFilter_Returns200WithEmptyPatients | Null Patients dict -> 200 + empty | Mock null | OK + [] | Endpoint |
| 24 | Yes | GetSession_WithPatients_NoPatientIdFilter_ReturnsAllPatients | All patients returned when no filter | Mock; call | All returned | Endpoint |
| 25 | Yes | GetSession_WithPatientIdFilter_PatientFound_ReturnsSinglePatient | Filter returns single patient | Mock; call w/ filter | Single returned | Endpoint |
| 26 | Yes | GetSession_WithPatientIdFilter_PatientNotFound_Returns404 | Filter no match -> 404 | Mock; call | NotFound | Endpoint |
| 27 | Yes | GetSession_PassesCallIdToRepository | Repo arg | Verify | Match | Endpoint |
| 28 | Yes | GetSession_PatientWithAppointments_MapsAppointmentsCorrectly | Patient appts mapped via MapToResponseItem | Verify | Match | Endpoint |
| 29 | Yes | GetSession_PatientWithAllFields_MapsAllFieldsCorrectly | Full patient mapping | Verify all fields | Match | Endpoint |
| 30 | Yes | GetSession_WithCallerPhoneNumberInfo_ReturnsMappedInfo | Caller phone info mapped | Verify | Match | Endpoint |
| 31 | Yes | GetSession_WithNullCallerPhoneNumberInfo_ReturnsNullCallerPhoneNumberInfo | Null caller info -> null on response | Verify | Null | Endpoint |
| 32 | Yes | MapToPatientResponse_MapsAllFieldsCorrectly | Full patient mapping | Build patient; map | All fields | Mapper |
| 33 | Yes | MapToPatientResponse_NullDateOfBirth_MapsAsNull | Null DOB | Pass null | Null | Mapper |
| 34 | Yes | MapToPatientResponse_WithAppointments_MapsUsingMapToResponseItem | Patient appts use MapToResponseItem | Verify mapping shared | Reuse | Mapper |
| 35 | Yes | MapToPatientResponse_NullGender_MapsAsNull | Null gender | Pass null | Null | Mapper |
| 36 | Yes | MapCallerPhoneNumberInfo_WhenNull_ReturnsNull | Null in -> null out | Pass null | Null | Mapper |
| 37 | Yes | MapCallerPhoneNumberInfo_MapsAllFields | Full mapping | Build; map | All fields | Mapper |
| 38 | Yes | MapCallerPhoneNumberInfo_WhenCallerInfoIsNull_ReturnsInfoWithNullCallerInfo | Null inner caller info -> null inner | Verify | Null preserved | Mapper |
| 39 | Yes | MapCallerPhoneNumberInfo_NullableFields_MappedCorrectly | Specific nullable fields | Build w/ nulls | Nulls preserved | Mapper |
| 40 | Yes | MapGender_ReturnsExpected ([TestCase x2]) | Male/Female map 1:1 | Iterate | Match | Mapper |
| 41 | Yes | MapGender_NullInput_ReturnsNull | Null -> null | Pass null | Null | Mapper |
| 42 | Yes | MapGender_AllValues_DoesNotThrow ([Values]) | Exhaustive | All values | None throws | Mapper |
| 43 | Yes | MapGender_InvalidValue_ThrowsArgumentOutOfRangeException | Out-of-range cast throws | Cast invalid | Throws | Mapper |

### Suggested Missing Tests

- Debug tools should be access-controlled (or at least audit-logged) since GetSession exposes session contents - is there an auth check? If so, test the unauthenticated path.
- The `GetSession` endpoint MAY return PHI-adjacent fields (DOB, gender, phone). For DEID/non-prod environments only - test that prod-config shape is acceptable.
- Argument validation: callId required, but empty/null behavior is untested.
- Cancellation token forwarding to repo.
- The MapStatus inconsistency (lenient null fallback) vs the MapBlockedReason / MapLocationType / MapAvailabilityStrategy / MapGender (strict throw) - already covered, but worth a code comment.
- Boundary on MapToPatientResponse: a patient with 1000+ appointments - performance.

### Improvement Suggestions / Irrelevant Tests

- This file mirrors the same pattern as the AgentTools mappers (per-value + exhaustive + invalid). The duplication between these mappers and the ones in `ManageAppointmentAgentToolsImplTests.cs` is a code-organization issue, not a test issue - mappers could potentially share an implementation.
- Consider Verify snapshots for `MapToPatientResponse` and `MapToResponseItem` for representative inputs - one snapshot replaces several field-by-field assertions.
- All 43 tests are valuable. None are irrelevant.

---

## ExampleTests.cs

Placeholder file. Single test (`TokenEmptyTest`) that asserts `(1 + 1).Should().Be(2)`. Recommend deletion (or repurposing) once any real test exists.

### Suggested Missing Tests

- Not applicable - file is a placeholder.

### Improvement Suggestions / Irrelevant Tests

- Delete `ExampleTests.cs`. The test asserts that math works. This is irrelevant noise that increases the suite's run count without adding value.
- If the file is meant to be a template for new contributors, replace with a comment-only file or a README under `tests/` describing the convention.

---

## HttpLoggingMiddlewareTests.cs

Setup at line 20 wires the middleware. Only ONE test today.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | Yes | InvokeAsync_WhenCalled_LogsRequest | Middleware logs the incoming request | Build request; invoke; capture logs | Log entry includes request data | Middleware |

### Suggested Missing Tests

This is the largest single coverage gap in the file set. The HTTP logging middleware is on every request and should be tested across the major risk paths:

- Logs the response status code (currently only the request side is tested).
- Does NOT log request bodies that are PHI-bearing (privacy guard - critical).
- Logs request duration / latency.
- Logs are at the correct level (Info for 2xx, Warning for 4xx, Error for 5xx).
- Path-based filtering (health-check endpoints skip logging).
- An exception thrown by `next()` is logged AND re-thrown.
- Sensitive headers (Authorization, Cookie) are redacted from logs.
- Very large bodies are truncated (do not blow up the log shipper).

### Improvement Suggestions / Irrelevant Tests

- Severe under-coverage. Recommend at least 6-8 additional tests covering the bullets above. The privacy/PHI redaction test is the most important.

---

## InitializeCallImplTests.cs

Setup at line 27. Tests `InitializeCall` - the entry point of every voice call. Returns the practice info, AB-flag-driven feature toggles (`shouldAutoTransfer`, `useMultilingualStt`, `fillersEnabled`, `voiceQualityV2`), cache-hit headers (`X-Cache-Hit`, `X-Cache-TTL`), creates the call session, and looks up the caller's phone number info (if the AB flag is on). ~25 tests.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | Yes | InitializeCall_ReturnsPracticeInfo | Returns the practice info from cache | Mock cache; call | Practice info returned | Endpoint |
| 2 | Yes | InitializeCall_WhenFlagIsOn_ReturnsShouldAutoTransferTrue | shouldAutoTransfer flag on -> true | Mock flag on | true | Endpoint |
| 3 | Yes | InitializeCall_WhenFlagIsOff_ReturnsShouldAutoTransferFalse | Flag off -> false | Mock flag off | false | Endpoint |
| 4 | Yes | InitializeCall_WhenPracticeInfoShouldAutoTransferIsTrue_ReturnsShouldAutoTransferTrue | Practice info field overrides flag (true wins) | Mock practice info | true | Endpoint |
| 5 | Yes | InitializeCall_WhenBothFlagAndPracticeInfoAreTrue_ReturnsShouldAutoTransferTrue | Both true -> true | Mock both | true | Endpoint |
| 6 | Yes | InitializeCall_WhenMultilingualSttFlagIsOn_ReturnsTrue | useMultilingualStt flag on -> true | Mock | true | Endpoint |
| 7 | Yes | InitializeCall_WhenMultilingualSttFlagIsOff_ReturnsFalse | Off -> false | Mock | false | Endpoint |
| 8 | Yes | InitializeCall_WhenMultilingualSttFlagFails_ReturnsFalse | Flag service throws -> default to false | Mock throws | false | Endpoint (resilience) |
| 9 | Yes | InitializeCall_WhenFillersEnabledFlagIsOn_ReturnsTrue | fillersEnabled flag on | Mock | true | Endpoint |
| 10 | Yes | InitializeCall_WhenFillersEnabledFlagIsOff_ReturnsFalse | Off -> false | Mock | false | Endpoint |
| 11 | Yes | InitializeCall_WhenVoiceQualityV2FlagIsOn_ReturnsTrue | voiceQualityV2 flag on | Mock | true | Endpoint |
| 12 | Yes | InitializeCall_WhenVoiceQualityV2FlagIsOff_ReturnsFalse | Off -> false | Mock | false | Endpoint |
| 13 | Yes | InitializeCall_WhenCacheHit_SetsCacheHitHeaderToTrue | Cache hit -> X-Cache-Hit=true header | Mock IsHit; verify response | Header set | Endpoint (observability) |
| 14 | Yes | InitializeCall_WhenCacheMiss_SetsCacheHitHeaderToFalse | Cache miss -> X-Cache-Hit=false | Mock; verify | Header set | Endpoint |
| 15 | Yes | InitializeCall_WhenCacheHitWithTtl_SetsTtlHeader | Cache hit w/ TTL -> X-Cache-TTL header | Mock; verify | Header set | Endpoint |
| 16 | Yes | InitializeCall_WhenCacheMiss_DoesNotSetTtlHeader | Miss -> no TTL header | Mock; verify | No header | Endpoint |
| 17 | Yes | InitializeCall_WhenHttpContextIsNull_DoesNotThrow | Null HttpContext (test path) -> no NRE | Null context; call | No throw | Endpoint (defensive) |
| 18 | Yes | InitializeCall_UsesPracticeIdAsAbVisitorId | AB flag visitor id is practiceId (when known) | Verify args | Match | Endpoint |
| 19 | Yes | InitializeCall_WhenPracticeIdIsEmpty_DoesNotCallAbServiceForPracticeFlags | Empty practiceId skips AB calls (would be a no-op) | Verify Times.Never | No AB calls | Endpoint |
| 20 | Yes | InitializeCall_CreatesCallSession | Session created in repo | Verify | Times.Once | Endpoint |
| 21 | Yes | InitializeCall_WhenSessionCreationFails_ReturnsShouldAutoTransferTrue | Session create throw -> auto-transfer (graceful degradation) | Mock throws | shouldAutoTransfer=true | Endpoint (resilience) |
| 22 | Yes | InitializeCall_WhenSessionCreationFails_StillReturnsUseMultilingualStt | Even on failure, return useMultilingualStt | Mock throws | Returned | Endpoint |
| 23 | Yes | InitializeCall_WhenSessionCreationFails_StillReturnsFillersEnabled | Even on failure, return fillersEnabled | Mock throws | Returned | Endpoint |
| 24 | Yes | InitializeCall_WhenPracticeIdIsEmpty_DoesNotCreateCallSession | Empty practiceId skips session creation | Verify Times.Never | No session created | Endpoint |
| 25 | Yes | InitializeCall_WhenPhoneNumberLookupFlagIsOff_DoesNotCallPhoneNumberService | Phone-lookup flag off -> phone service not called | Verify Times.Never | Not called | Endpoint |
| 26 | Yes | InitializeCall_WhenPhoneNumberLookupFlagIsOn_CallsPhoneNumberServiceWithCallerPhoneNumber | On -> service called with caller phone | Verify | Match | Endpoint |
| 27 | Yes | InitializeCall_WhenPhoneNumberLookupFlagIsOn_UsesCallerPhoneNumberAsVisitorId | And uses caller phone as AB visitor id for the lookup flag | Verify | Match | Endpoint |
| 28 | Yes | InitializeCall_WhenPhoneNumberLookupEnabled_SavesPhoneNumberInfoToSession | Looked-up info saved to session | Verify | Saved | Endpoint |
| 29 | Yes | InitializeCall_WhenPhoneNumberLookupDisabled_SavesSessionWithNullPhoneNumberInfo | Disabled -> null on session | Verify | Null | Endpoint |
| 30 | Yes | InitializeCall_WhenPhoneNumberLookupEnabled_ReturnsPhoneNumberInfoFields | Returns the looked-up fields | Verify response | Match | Endpoint |
| 31 | Yes | InitializeCall_WhenPhoneNumberLookupDisabled_ReturnsDefaultPhoneNumberFields | Disabled -> default empty fields | Verify response | Defaults | Endpoint |
| 32 | Yes | InitializeCall_WhenPhoneNumberLookupEnabled_WithAnonymousNumber_ReturnsAnonymousFields | Anonymous caller -> anonymous fields | Mock anonymous; verify | Anonymous fields | Endpoint |
| 33 | Yes | InitializeCall_WhenPracticeIdIsEmpty_ReturnsDefaultPhoneNumberFields | Empty practiceId -> default fields (no lookup) | Verify | Defaults | Endpoint |

### Suggested Missing Tests

- AB flag service throws for `shouldAutoTransfer` / `fillersEnabled` / `voiceQualityV2` (only `useMultilingualStt` failure path is tested).
- Practice info lookup throws (PhoneBot service down) - is the response a transfer or 5xx?
- Concurrent calls for the same practice - cache thrash.
- The cache TTL header value is asserted as set, but the actual seconds value is not asserted.
- Phone lookup throws an exception - what does the response look like?
- Acceptance-language header propagation if applicable.
- Cancellation token forwarding throughout.

### Improvement Suggestions / Irrelevant Tests

- Coverage is broad and structurally sound. The "session creation fails -> still returns flags" tests (#21-#23) are the right kind of resilience tests.
- Tests #6-#7, #9-#10, #11-#12 are paired flag-on/flag-off tests. Could be combined via a `[TestCase(true), TestCase(false)]` pattern.
- No test is irrelevant.

---

## PatientInfoImplTests.cs

Tests `InsertPatient`, `UpdatePatient`, `SearchPatients`, `ValidatePatient`. Strong coverage on phone-number sanitization (dashes, parentheses, plus signs, country codes), gender mapping, body-vs-caller-phone fallback, and validation responses. ~50 tests.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | Yes | InsertPatient_PassesCallIdToService | callId forwarded | Verify | Match | Endpoint |
| 2 | Yes | InsertPatient_PassesDateOfBirthToService | DOB forwarded | Verify | Match | Endpoint |
| 3 | Yes | InsertPatient_PassesFirstNameToService | First name forwarded | Verify | Match | Endpoint |
| 4 | Yes | InsertPatient_PassesLastNameToService | Last name forwarded | Verify | Match | Endpoint |
| 5 | Yes | InsertPatient_ReturnsPatientIdFromService | Returns patientId from upstream | Verify response | Match | Endpoint |
| 6 | Yes | InsertPatient_WhenServiceThrowsArgumentException_ReturnsBadRequest | ArgumentException -> 400 | Mock throws | BadRequest | Endpoint |
| 7 | Yes | InsertPatient_WhenServiceThrowsArgumentException_ReturnsErrorMessage | And error message included in response | Mock; verify | Message present | Endpoint |
| 8 | Yes | UpdatePatient_WhenServiceCompletes_Returns200 | Happy path | Mock; call | OK | Endpoint |
| 9 | Yes | UpdatePatient_WhenServiceThrowsInvalidOperationException_ReturnsBadRequest | InvalidOp -> 400 | Mock throws | BadRequest | Endpoint |
| 10 | Yes | UpdatePatient_PassesCallIdToService | callId | Verify | Match | Endpoint |
| 11 | Yes | UpdatePatient_PassesPatientIdToService | patientId | Verify | Match | Endpoint |
| 12 | Yes | UpdatePatient_PassesAllFieldsToService | All fields | Verify | Match | Endpoint |
| 13 | Yes | UpdatePatient_MapsGenderCorrectly ([TestCase x2]) | Male/Female mapping | Iterate | Match | Endpoint (mapper) |
| 14 | Yes | UpdatePatient_WhenPhoneProvidedInBody_PassesBodyPhoneToService | Body phone wins over caller phone | Verify | Body used | Endpoint |
| 15 | Yes | UpdatePatient_WhenPhoneNullInBody_PassesCallerPhoneToService | Body null -> fallback to caller phone | Verify | Caller used | Endpoint |
| 16 | Yes | UpdatePatient_WithNullGender_PassesNullToService | Null gender forwarded | Verify | Null | Endpoint |
| 17 | Yes | UpdatePatient_WithInvalidGender_ThrowsArgumentOutOfRangeException | Invalid enum -> throws | Cast invalid | Throws | Endpoint |
| 18 | Yes | UpdatePatient_AllGenderValues_DoNotThrow ([Values]) | Exhaustive enum | All values | None throws | Endpoint |
| 19 | Yes | UpdatePatient_WhenServiceThrowsArgumentException_ReturnsBadRequest | Argument -> 400 | Mock throws | BadRequest | Endpoint |
| 20 | Yes | UpdatePatient_WhenServiceThrowsArgumentException_ReturnsErrorMessage | Error message included | Verify | Present | Endpoint |
| 21 | Yes | UpdatePatient_WithPhoneContainingDashes_SanitizesBeforePassingToService | "555-555-5555" -> "5555555555" | Verify | Sanitized | Endpoint (sanitization) |
| 22 | Yes | UpdatePatient_WithPhoneContainingParensAndSpaces_SanitizesBeforePassingToService | "(555) 555-5555" -> "5555555555" | Verify | Sanitized | Endpoint |
| 23 | Yes | UpdatePatient_WithPhoneContainingPlusAndCountryCode_KeepsPlusSign | "+15555555555" preserves the + | Verify | + kept | Endpoint |
| 24 | Yes | UpdatePatient_WhenPhoneNullInBody_SanitizesCallerPhone | Caller-phone fallback also sanitized | Verify | Sanitized | Endpoint |
| 25 | Yes | SearchPatients_WithPhoneNumberInBody_UsesBodyPhoneNumber | Body phone wins | Verify | Body used | Endpoint |
| 26 | Yes | SearchPatients_WithoutPhoneNumberInBody_UsesCallerPhoneNumber | Body null -> caller | Verify | Caller used | Endpoint |
| 27 | Yes | SearchPatients_WithDashesInPhoneNumber_StripsNonDigits | Sanitization | Verify | Stripped | Endpoint |
| 28 | Yes | SearchPatients_WithCountryCodeInPhoneNumber_StripsNonDigits | "+1 555..." -> digits only | Verify | Stripped | Endpoint |
| 29 | Yes | SearchPatients_WithParenthesesInPhoneNumber_StripsNonDigits | Parens stripped | Verify | Stripped | Endpoint |
| 30 | Yes | SearchPatients_WithCallerPhoneContainingNonDigits_StripsNonDigits | Caller-phone path also sanitized | Verify | Stripped | Endpoint |
| 31 | Yes | SearchPatients_MapsResponseFields | Service result mapped to response | Verify | Match | Endpoint |
| 32 | Yes | SearchPatients_WhenServiceReturnsNull_ReturnsNotFound | Null result -> 404 | Mock null | NotFound | Endpoint |
| 33 | Yes | ValidatePatient_WhenServiceThrowsInvalidOperationException_ReturnsBadRequest | InvalidOp -> 400 | Mock throws | BadRequest | Endpoint |
| 34 | Yes | ValidatePatient_WhenNoMissingFields_ReturnsValidStatus | All fields present -> Valid | Mock; call | Status=Valid | Endpoint |
| 35 | Yes | ValidatePatient_WhenNoMissingFields_DoesNotSetMessage | And no message | Verify | Null | Endpoint |
| 36 | Yes | ValidatePatient_WhenMissingFields_ReturnsMissingFieldsStatus | Missing -> MissingFields | Mock | Status | Endpoint |
| 37 | Yes | ValidatePatient_WhenMissingFields_SetsHumanizedMessage | And humanized message included | Verify | Message present | Endpoint |
| 38 | Yes | ValidatePatient_PassesPracticePhoneNumberToService | Args | Verify | Match | Endpoint |
| 39 | Yes | ValidatePatient_PassesCallIdToService | Args | Verify | Match | Endpoint |
| 40 | Yes | ValidatePatient_PassesPatientIdToService | Args | Verify | Match | Endpoint |

### Suggested Missing Tests

- Phone sanitization edge: international formats with extension (`+1 555 555-5555 ext 123`).
- DOB validation: future date, year < 1900, malformed.
- Name length boundaries (empty, max length).
- Cancellation token forwarding.
- Concurrent updates on the same patientId.
- ValidatePatient with multiple missing fields - is the message a list or comma-joined?
- Auth-style: an LLM should not be able to update a patient that does not belong to the call's session - test the auth boundary.

### Improvement Suggestions / Irrelevant Tests

- Tests #1-#4 individually verify different fields are forwarded. Per CS-021 these are mocks-only verify-args. Could collapse into one test that captures the args object once and asserts all fields. The granularity is fine but slightly verbose.
- Phone sanitization tests (#21-#24, #27-#30) are valuable - phone number normalization is exactly the kind of pure logic worth direct tests.
- No test is irrelevant.

---

## PracticeFaqToolsImplTests.cs

Setup at line 23. Tests `GetPracticeFaq` with topic enum (General/Pricing/Insurance) and optional locationId. ~8 tests.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | Yes | GetPracticeFaq_General_ReturnsGeneralContent | General topic returns general FAQ content | Mock; call w/ General | Content returned | Tool impl |
| 2 | Yes | GetPracticeFaq_Pricing_ReturnsPricingContent | Pricing topic | Mock; call | Content | Tool impl |
| 3 | Yes | GetPracticeFaq_Insurance_ReturnsInsuranceContent | Insurance topic | Mock; call | Content | Tool impl |
| 4 | Yes | GetPracticeFaq_AllValues_DoesNotThrow ([Values]) | Exhaustive enum | All values | None throws | Tool impl |
| 5 | Yes | GetPracticeFaq_NullField_ReturnsEmptyString | Topic field null on practice info -> "" (graceful) | Mock null | "" | Tool impl (defensive) |
| 6 | Yes | GetPracticeFaq_InvalidTopic_Throws | Invalid enum value throws | Cast invalid | Throws | Tool impl |
| 7 | Yes | GetPracticeFaq_LocationIdProvided_ForwardsLocationToService | locationId arg forwarded | Verify | Match | Tool impl |
| 8 | Yes | GetPracticeFaq_LocationIdOmitted_ForwardsNullLocation | Null locationId forwarded | Verify | Null | Tool impl |

### Suggested Missing Tests

- locationId provided but does not match any of the practice's locations - validation/404 vs silent fallback.
- Empty content returned by service (e.g., practice has no Insurance FAQ configured).
- Cancellation token forwarding.
- Localization (FAQ content language matches accept-language).

### Improvement Suggestions / Irrelevant Tests

- Tests #1-#3 could collapse into a `[TestCase]` matrix driven by topic + expected content lookup, but the explicit form reads better.
- All tests are valuable.

---

## PracticeInfoImplTests.cs

Tests `GetPracticeInfo` with strong focus on cache-result headers. ~6 tests.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | Yes | GetPracticeInfo_ReturnsPracticeInfoFromCache | Returns the cached payload | Mock; call | Match | Endpoint |
| 2 | Yes | GetPracticeInfo_WhenCacheHit_SetsCacheHitHeaderToTrue | Hit -> X-Cache-Hit=true | Mock IsHit; verify | Header set | Endpoint (observability) |
| 3 | Yes | GetPracticeInfo_WhenCacheMiss_SetsCacheHitHeaderToFalse | Miss -> X-Cache-Hit=false | Mock; verify | Header set | Endpoint |
| 4 | Yes | GetPracticeInfo_WhenCacheHitWithTtl_SetsTtlHeader | Hit w/ TTL -> X-Cache-TTL header | Mock; verify | Header set | Endpoint |
| 5 | Yes | GetPracticeInfo_WhenCacheMiss_DoesNotSetTtlHeader | Miss -> no TTL header | Verify | No header | Endpoint |
| 6 | Yes | GetPracticeInfo_WhenHttpContextIsNull_DoesNotThrow | Null HttpContext -> no NRE | Null context; call | No throw | Endpoint (defensive) |

### Suggested Missing Tests

- Cache returns null payload (service never populated it) - is the response 404 or 200-with-empty?
- TTL header value is asserted as set, but the actual seconds value is not.
- Repository throws (downstream PhoneBot 5xx) - response shape.
- Cancellation token forwarding.
- Practice phone number normalization (e.g., +1 prefix).

### Improvement Suggestions / Irrelevant Tests

- This file mirrors the cache-header test pattern in `InitializeCallImplTests.cs`. The shared logic is in `CacheHeaderHelper` (which has its own coverage in the Caching project). Worth a comment linking to that.
- All 6 tests are valuable.

---

## PracticeInfoResponseExtensionsTests.cs

Tests the long chain of mapping extensions that convert `PhoneBot.PracticeInfoV2Response` (upstream contract) to ZoTools' `PracticeInfoResponse` (downstream contract). ~70 tests covering: top-level fields, providers (with IPA cross-population), insurance network types, locations, provider-locations, age ranges, transfer numbers (16 enum values), preflight messages, patient information settings, patient info requirements, synchronizer details, screen-pop settings, V2 exclusions, voice config, opted-out SMS messages, RX refill config, new patient definition, provider continuity policy, call-center status. Heavily TestCase-driven.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | Yes | ToPracticeInfoResponse_MapsTopLevelFields | Top-level field mapping | Build; map | All fields | Mapper |
| 2 | Yes | ToProviderResponse_WhenNull_ReturnsNull | Null in -> null out | Pass null | Null | Mapper |
| 3 | Yes | ToProviderResponse_MapsAllFields | Full provider mapping | Build; map | All fields | Mapper |
| 4 | Yes | ToProviderResponse_CrossPopulatesIpaFromProviderLocations | IPA pronunciation comes from ProviderLocations and is hoisted to Provider | Build w/ IPA on PL; map | IPA on response | Mapper (cross-mapping) |
| 5 | Yes | ToInsuranceNetworkTypeResponse_WhenNull_ReturnsNull | Null safety | Pass null | Null | Mapper |
| 6 | Yes | ToInsuranceNetworkTypeResponse_MapsValue ([TestCase x4]) | Epo/Hmo/Ppo/Pos enum mapping | Iterate | Match | Mapper |
| 7 | Yes | ToLocationResponse_WhenNull_ReturnsNull | Null safety | Pass null | Null | Mapper |
| 8 | Yes | ToLocationResponse_MapsAllFields | Full location mapping | Build; map | All fields | Mapper |
| 9 | Yes | ToProviderLocationResponse_WhenNull_ReturnsNull | Null safety | Pass null | Null | Mapper |
| 10 | Yes | ToProviderLocationResponse_MapsRequiredFields | Required fields | Build; map | Match | Mapper |
| 11 | Yes | ToProviderLocationResponse_MapsOptionalFields | Optional fields | Build; map | Match | Mapper |
| 12 | Yes | ToAgeRangesInfoResponse_WhenNull_ReturnsNull | Null safety | Pass null | Null | Mapper |
| 13 | Yes | ToAgeRangesInfoResponse_MapsAllFields | Full mapping | Build; map | Match | Mapper |
| 14 | Yes | ToTransferNumberResponse_WhenNull_ReturnsNull | Null safety | Pass null | Null | Mapper |
| 15 | Yes | ToTransferNumberResponse_MapsAllFields | Full mapping | Build; map | Match | Mapper |
| 16 | Yes | ToTransferNumberResponse_MapsTransferNumberType ([TestCase x16]) | All 16 transfer number types map 1:1 | Iterate | Each maps | Mapper |
| 17 | Yes | ToPreflightMessageResponse_WhenNull_ReturnsNull | Null safety | Pass null | Null | Mapper |
| 18 | Yes | ToPreflightMessageResponse_MapsValue ([TestCase x8]) | All 8 preflight message values map | Iterate | Each maps | Mapper |
| 19 | Yes | ToPatientInformationSettingsResponse_WhenNull_ReturnsNull | Null safety | Pass null | Null | Mapper |
| 20 | Yes | ToPatientInformationSettingsResponse_MapsAllFields | Full mapping | Build; map | Match | Mapper |
| 21 | Yes | ToPatientInformationSettingsResponse_GenderIsRequiredForNewAndNotAskedForExisting | Gender requirement levels distinguish patient types | Build; map | Differentiation preserved | Mapper |
| 22 | Yes | ToPatientInfoRequirementResponse_WhenNull_ReturnsNull | Null safety | Pass null | Null | Mapper |
| 23 | Yes | ToPatientInfoRequirementResponse_MapsNewAndExistingPatient | Per-patient-type requirement mapping | Build; map | Both branches mapped | Mapper |
| 24 | Yes | ToPatientInfoRequirementLevelResponse_WhenNull_ReturnsNull | Null safety | Pass null | Null | Mapper |
| 25 | Yes | ToPatientInfoRequirementLevelResponse_MapsValue ([TestCase x3]) | NotAsked/Asked/Required map | Iterate | Match | Mapper |
| 26 | Yes | ToPatientInfoRequirementLevelResponse_AllValues_DoNotThrow ([Values]) | Exhaustive | All values | None throws | Mapper |
| 27 | Yes | ToSynchronizerDetailsResponse_WhenNull_ReturnsNull | Null safety | Pass null | Null | Mapper |
| 28 | Yes | ToSynchronizerDetailsResponse_MapsAllFields | Full mapping | Build; map | Match | Mapper |
| 29 | Yes | ToScreenPopSettingsResponse_WhenNull_ReturnsNull | Null safety | Pass null | Null | Mapper |
| 30 | Yes | ToScreenPopSettingsResponse_MapsAllFields | Full mapping | Build; map | Match | Mapper |
| 31 | Yes | ToV2ExclusionsResponse_WhenNull_ReturnsNull | Null safety | Pass null | Null | Mapper |
| 32 | Yes | ToV2ExclusionsResponse_MapsAllFields | Full mapping | Build; map | Match | Mapper |
| 33 | Yes | ToVoiceConfigResponse_WhenNull_ReturnsNull | Null safety | Pass null | Null | Mapper |
| 34 | Yes | ToVoiceConfigResponse_MapsVendor ([TestCase x2]) | Elevenlabs/Cartesia map | Iterate | Match | Mapper |
| 35 | Yes | ToVoiceConfigResponse_MapsVoiceId | VoiceId pass-through | Build; map | Match | Mapper |
| 36 | Yes | ToOptedOutSmsMessageResponse_WhenNull_ReturnsNull | Null safety | Pass null | Null | Mapper |
| 37 | Yes | ToOptedOutSmsMessageResponse_MapsValue ([TestCase x4]) | All 4 opted-out values map | Iterate | Match | Mapper |
| 38 | Yes | ToRxRefillConfigResponse_WhenNull_ReturnsNull | Null safety | Pass null | Null | Mapper |
| 39 | Yes | ToRxRefillConfigResponse_MapsEnabledStatus ([TestCase x3]) | Disabled/Enabled/EnabledForTestNumbers map | Iterate | Match | Mapper |
| 40 | Yes | ToRxRefillConfigResponse_MapsMaxMonthsSinceLastVisit | Pass-through | Build; map | Match | Mapper |
| 41 | Yes | ToNewPatientDefinitionResponse_MapsValue ([TestCase x2]) | ForPractice/ForSpecialty | Iterate | Match | Mapper |
| 42 | Yes | ToNewPatientDefinitionResponse_AllValues_DoNotThrow ([Values]) | Exhaustive | All values | None throws | Mapper |
| 43 | Yes | ToProviderContinuityPolicyResponse_WhenNull_ReturnsNull | Null safety | Pass null | Null | Mapper |
| 44 | Yes | ToProviderContinuityPolicyResponse_MapsValue ([TestCase x2]) | OfferOtherProviders/TransferToStaff | Iterate | Match | Mapper |
| 45 | Yes | ToProviderContinuityPolicyResponse_AllValues_DoNotThrow ([Values]) | Exhaustive | All values | None throws | Mapper |
| 46 | Yes | ToCallCenterStatusResponse_WhenNull_ReturnsNull | Null safety | Pass null | Null | Mapper |
| 47 | Yes | ToCallCenterStatusResponse_MapsValue ([TestCase x5]) | All 5 statuses map | Iterate | Match | Mapper |
| 48 | Yes | ToCallCenterStatusResponse_AllValues_DoNotThrow ([Values]) | Exhaustive | All values | None throws | Mapper |

### Suggested Missing Tests

- The top-level `ToPracticeInfoResponse_MapsTopLevelFields` test could miss new fields added later. Consider a Verify snapshot of a fully populated representative input -> the mapped output.
- ToProviderResponse_CrossPopulatesIpaFromProviderLocations is good; also worth testing what happens when MULTIPLE provider-locations have different IPAs (which one wins?).
- Many of the per-mapper "AllFields" tests (#3, #8, #11, #13, #15, #20, #28, #30, #32) build representative inputs - a snapshot per scenario would lock down the contract more strongly.
- Field-level edge cases: empty strings, very long strings, unicode in names.
- Null inside lists (e.g., a List<Provider> where one element is null) - graceful or throw?

### Improvement Suggestions / Irrelevant Tests

- This is a textbook mapping-test file: clean, consistent structure (`WhenNull_ReturnsNull` + `MapsValue` + `AllValues_DoNotThrow`). Use it as a template for other mapper specs.
- The ~16 enum-mapping tests for transfer number types and the 8 preflight tests are exhaustive-coverage gold. Keep.
- No test is irrelevant.

---

## SmsImplTests.cs

Setup at line 32. Tests two methods: `SendSms` (with four SmsType variants: ConfirmDetails, Cancel, Reschedule, BookingConfirmed) and `SetSmsConsent`. Heavy coverage on validation (null/empty/whitespace appointment ids), single-id-vs-multi-id rules per SmsType, opted-out messages, session/caller-info presence, consent refusal, feature-flag gating. ~30 tests.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | Yes | SendSms_NullAppointmentIds_ReturnsBadRequest | Null ids -> 400 | Pass null | BadRequest | Tool impl (validation) |
| 2 | Yes | SendSms_EmptyAppointmentIds_ReturnsBadRequest | Empty ids -> 400 | Pass [] | BadRequest | Tool impl |
| 3 | Yes | SendSms_WhitespaceInAppointmentIds_ReturnsBadRequest | "   " -> 400 | Pass whitespace | BadRequest | Tool impl |
| 4 | Yes | SendSms_InvalidAppointmentIds_DoesNotCallSmsService ([Values]) | Invalid ids -> service NOT called for any SmsType | Verify Times.Never | Not called | Tool impl |
| 5 | Yes | SendSms_SingleIdTypeWithMultipleIds_ReturnsBadRequest ([TestCase x3]) | Cancel/Reschedule/BookingConfirmed accept ONE id only | Pass 2 ids | BadRequest | Tool impl |
| 6 | Yes | SendSms_SingleIdTypeWithMultipleIds_DoesNotCallSmsService ([TestCase x3]) | And service is not called | Verify | Times.Never | Tool impl |
| 7 | Yes | SendSms_ConfirmDetailsWithMultipleIds_Succeeds | ConfirmDetails accepts multiple ids | Pass 3 ids | Success | Tool impl |
| 8 | Yes | SendSms_PracticeOptedOut_ReturnsBadRequest ([TestCase x4]) | Each SmsType blocked when practice opts out of that message | Mock opted-out | BadRequest per type | Tool impl |
| 9 | Yes | SendSms_PracticeOptedOut_DoesNotCallSmsService ([Values]) | Service not called | Verify | Times.Never | Tool impl |
| 10 | Yes | SendSms_NullOptedOutMessages_Succeeds ([Values]) | Null opted-out list (no opt-outs) -> succeeds for all types | Mock null | Success | Tool impl |
| 11 | Yes | SendSms_ConfirmDetails_SmsSucceeds_ReturnsOkWithNoDirectives | Happy path returns 200 + no directives | Mock success; call | OK + no directives | Tool impl |
| 12 | Yes | SendSms_SingleIdType_SmsSucceeds_ReturnsOkWithNoDirectives ([TestCase x3]) | Happy path for single-id types | Per type | OK | Tool impl |
| 13 | Yes | SendSms_ConfirmDetails_PassesAllIdsToSmsService | All ids forwarded for ConfirmDetails | Verify | Match | Tool impl |
| 14 | Yes | SendSms_SingleIdType_PassesSingleIdToSmsService ([TestCase x3]) | Single id forwarded for single-id types | Per type | Match | Tool impl |
| 15 | Yes | SendSms_SmsFails_ReturnsOkWithTransferAndSmsSendFailedError ([Values]) | SMS send failure -> 200 + transfer + SmsSendFailed error directive | Mock failure | OK + transfer + error | Tool impl (resilience) |
| 16 | Yes | SendSms_SessionNotFound_ReturnsBadRequest ([Values]) | No session -> 400 | No session | BadRequest | Tool impl |
| 17 | Yes | SendSms_ConfirmDetails_SessionAppointmentNotFound_ReturnsBadRequest | ConfirmDetails: appt not in session -> 400 | Session w/o appt | BadRequest | Tool impl |
| 18 | Yes | SendSms_SingleIdType_SessionAppointmentNotFound_ReturnsBadRequest ([TestCase x3]) | Same for single-id types | Per type | BadRequest | Tool impl |
| 19 | Yes | SendSms_InvalidSmsPhoneNumber_ReturnsOkWithTransferAndMissingCallerInfoError ([Values]) | Invalid SMS phone -> 200 + transfer + MissingCallerInfo error | Setup invalid phone | OK + directives | Tool impl |
| 20 | Yes | SendSms_SessionCallerInfoNotFound_ReturnsBadRequest ([Values]) | No caller info in session -> 400 | No caller info | BadRequest | Tool impl |
| 21 | Yes | SendSms_ConsentRefused_ReturnsBadRequest ([Values]) | Caller refused SMS consent -> 400 | Mock refused | BadRequest | Tool impl |
| 22 | Yes | SendSms_Cancel_AppointmentNotCancelled_ReturnsOkWithTransferAndAppointmentNotCancelledError | Cancel SMS but appt was not actually cancelled -> 200 + transfer + AppointmentNotCancelled error | Setup mismatch | OK + directives | Tool impl |
| 23 | Yes | SetSmsConsent_SessionNotFound_ReturnsBadRequest | No session -> 400 | No session | BadRequest | Tool impl |
| 24 | Yes | SetSmsConsent_SessionNotFound_ReturnsBadRequestWithMessage | And message included | Verify | Present | Tool impl |
| 25 | Yes | SetSmsConsent_CallerInfoNotFound_ReturnsBadRequest | No caller info -> 400 | No info | BadRequest | Tool impl |
| 26 | Yes | SetSmsConsent_CallerInfoNotFound_ReturnsBadRequestWithMessage | And message included | Verify | Present | Tool impl |
| 27 | Yes | SetSmsConsent_ConsentTrue_ReturnsOk | Consent true -> 200 | Setup; call | OK | Tool impl |
| 28 | Yes | SetSmsConsent_ConsentFalse_ReturnsOk | Consent false -> 200 | Setup; call | OK | Tool impl |
| 29 | Yes | SetSmsConsent_ConsentTrue_ReturnsOkWithNoDirectives | And no directives | Verify | Empty | Tool impl |
| 30 | Yes | SetSmsConsent_ConsentTrue_CallsSmsService | And service called | Verify | Times.Once | Tool impl |
| 31 | Yes | SetSmsConsent_ConsentFalse_CallsSmsService | Both true/false call the service | Verify | Times.Once | Tool impl |
| 32 | Yes | SendSms_FlagOff_ReturnsOkWithNoDirectives ([Values]) | Feature-flag off -> 200 + no-op (no SMS sent) | Flag off | OK + no directives | Tool impl (feature flag) |
| 33 | Yes | SendSms_FlagOff_DoesNotCallSmsService ([Values]) | And service not called | Verify | Times.Never | Tool impl |
| 34 | Yes | SendSms_FlagOff_DoesNotCallPracticeInfoCache ([Values]) | And cache not consulted (cost optimization) | Verify | Times.Never | Tool impl |
| 35 | Yes | SendSms_FlagOn_ProceedsWithNormalLogic ([Values]) | Flag on -> normal logic | Flag on | Service called | Tool impl |
| 36 | Yes | SetSmsConsent_FlagOff_ReturnsOk | SetSmsConsent also flag-gated -> 200 no-op | Flag off | OK | Tool impl |
| 37 | Yes | SetSmsConsent_FlagOff_DoesNotCallSmsService | And not called | Verify | Times.Never | Tool impl |
| 38 | Yes | SetSmsConsent_FlagOn_ProceedsWithNormalLogic | On -> normal | Flag on | Called | Tool impl |

### Suggested Missing Tests

- ConfirmDetails with mixed valid + invalid ids (some null/whitespace in the list) - is it all-or-nothing or partial?
- BookingConfirmed but the appointment was cancelled AFTER booking - do we still send SMS or no-op?
- International phone number formats for the SMS recipient.
- Consent expiry (consent set N days ago - is it still valid?).
- Multiple `SendSms` calls in quick succession - rate-limiting / dedupe.
- Cancellation token forwarding.
- The "SmsSendFailed" error directive shape (transfer reason, error code) is not asserted - only its presence.

### Improvement Suggestions / Irrelevant Tests

- Strong use of `[Values]` for SmsType-driven tests. Keep this pattern.
- Tests are well-paired (e.g., #5/#6, #20/#21, #23/#24) - the second test in each pair adds the message-content assertion. Per CS-021 they could collapse, but readability is fine as-is.
- The feature-flag tests (#32-#38) are critical for protecting prod from unintended SMS during rollout. Keep.
- No test is irrelevant.

---

## ToolSchemaResponseSerializationTests.cs

Setup at line 15. ONE test that snapshots the full JSON serialization of `ToolSchemaResponse` (the response shape for the agent-tools-schema endpoint). Tests JSON property names, casing, and the round-trip through `ToolParametersJsonConverter` (covered in Converters/).

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | Yes | Serialize_FullToolSchemaResponse_IncludesAllFields | Full serialization includes all expected fields and casing | Build full response; serialize; assert keys/values | All fields present, JSON shape matches OpenAI tool-definition contract | Serialization |

### Suggested Missing Tests

- Serialization with `ToolDefinition.Returns = null` (the null branch from ToolDefinitionBuilderTests #6).
- Empty parameters (RequiredParams empty + Properties empty) - JSON shape.
- Snapshot/Verify the entire JSON output rather than per-field assertions for stronger contract lock-down (per CS-006).
- Round-trip: serialize -> deserialize -> serialize and assert equality (idempotency).
- Backward-compat: lock down that legacy fields still serialize (otherwise an LLM with a cached schema breaks).

### Improvement Suggestions / Irrelevant Tests

- One test is light. The OpenAI tool-definition contract is the source of truth for every LLM call; a snapshot test plus the negative cases above would significantly strengthen this.
- Strongly recommend converting to a Verify snapshot approach (per CS-006) to lock down the JSON output shape.

---

## ToolsSchemaImplTests.cs

Setup at line 23. Tests `GetToolsSchema` - the endpoint that returns the tool schema for a given AgentName. Uses `IAgentToolsRegistry` to look up the right `IAgentToolsProvider` and emits the schema response. ~5 tests.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | Yes | GetToolsSchema_UnknownAgent_ReturnsNotFound | Unknown agent name -> 404 | Mock registry returns null | NotFound | Endpoint |
| 2 | Yes | GetToolsSchema_KnownAgent_ReturnsOKWithCorrectResponse | Known agent -> 200 + correct response | Mock registry; call | OK + body matches | Endpoint |
| 3 | Yes | GetToolsSchema_KnownAgent_ReturnsToolsFromProvider | Tools come from the matched provider | Mock provider; verify | Match | Endpoint |
| 4 | Yes | GetToolsSchema_KnownAgent_ReturnsVersionFromProvider | Version comes from the matched provider | Mock provider; verify | Match | Endpoint |
| 5 | Yes | GetToolsSchema_MultipleAgents_ReturnsCorrectAgentTools | Different agents return their own tools (no cross-contamination) | Register 2 providers; call each | Each returns own tools | Endpoint |

### Suggested Missing Tests

- Empty agent name parameter (or null) - argument validation.
- Provider's GetTools throws - response shape.
- Cancellation token forwarding.
- The endpoint's HTTP caching headers (this schema is stable per agent and should be cacheable).
- The response includes the provider's Version - is the format verified (semver)?

### Improvement Suggestions / Irrelevant Tests

- 5 tests is reasonable for this surface. Coverage is adequate.
- Test #5 (multi-agent isolation) is the most valuable and protects against registry bugs.

---

## ValidationToolsImplTests.cs

Tests `ValidateAvailability` only. Two tests.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | Yes | ValidateAvailability_WhenValid_ReturnsOkWithIsValidTrue | Valid inputs -> 200 + isValid=true | Mock; call | OK + true | Tool impl |
| 2 | Yes | ValidateAvailability_WhenNotFound_ReturnsNotFound | Service NotFound -> 404 | Mock not found | NotFound | Tool impl |

### Suggested Missing Tests

- IsValid=false return path (validation failure with explanation).
- Service throws an unexpected exception.
- Argument validation: required ids missing.
- Cancellation token forwarding.
- Time-zone handling for the validated availability time.
- The response includes a structured reason / suggestion when invalid - shape not asserted.

### Improvement Suggestions / Irrelevant Tests

- Severe under-coverage for an availability-validation endpoint. Recommend at least 4-5 additional tests covering the bullets above.
- Both existing tests are valuable; none are irrelevant.

# ZoTools.Web.ApiTests - Telephony Test Mapping

**Repo:** `Zocdoc/zo-tools` (branch `main`)
**Folder:** `tests/ZoTools.Web.ApiTests/`
**Theme:** Telephony / call lifecycle (Audio, Sms, InitializeCall, CallCompleted, CsatTool)
**Author:** QA audit (Claude)
**Date:** 2026-05-07

This file maps every test in the telephony slice of `ZoTools.Web.ApiTests`. Tests are integration-style HTTP tests that exercise the live `zo-tools` web service against a fake or real backend. Auth is via the `zd-token` header constants (`TestSecret = "ZoToolsTest"`, `ProductionSecret = "ZoToolsNick"`). Call-context headers (`call-id`, `caller-phone-number`) are required on most call-time endpoints.

Quality lens applied: HTTP status code coverage (API-001), header-secret + call-context auth (AUTH-001), input validation, response-shape assertions, idempotency, audit logging (AUTH-005), PHI handling, feature-flag gating, and cache-header semantics.

Helper / setup-only files excluded: `ApiTestHelperExtensions.cs`, `TestConstants.cs`, `AssemblyInfo.cs`.

---

## AudioApiTests.cs

Endpoint: `GET /zo-tools/v1/audio/{callId}/upload-urls` - returns presigned S3 URLs for raw call audio.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | Yes | `GetAudioUploadUrls_WithoutAuth_Returns401` | Missing `zd-token` header rejected | GET endpoint with no headers; assert 401. | Negative auth: anonymous request blocked. | Auth |
| 2 | Yes | `GetAudioUploadUrls_WithInvalidSecret_Returns401` | Wrong secret rejected | GET with `zd-token: invalid-secret`; assert 401. | Negative auth: bad secret blocked. | Auth |
| 3 | Yes | `GetAudioUploadUrls_WithValidSecret_ReturnsPresignedUrls` | Happy path returns the call id and a fake presigned URL | GET with `TestSecret`; assert response `CallId` and `AudioPresignedUrl` match expected fake-S3 URL pattern. | Happy path response shape verified against fake S3. | Functional - FakeOnly |
| 4 | Yes | `GetAudioUploadUrls_UnknownCallId_Returns404` | Unknown call id returns 404 | GET `/audio/unknown-call/upload-urls`; assert 404. | Negative path: missing call id surfaces 404 correctly. | Functional - FakeOnly |

### Suggested Missing Tests - Audio

1. URL expiry / TTL assertion (presigned URLs typically include `X-Amz-Expires`; current test only checks the literal fake string).
2. Empty / malformed `callId` path segment (e.g. spaces, very long ids) should return 400 rather than 404.
3. Production secret (`ProductionSecret`) accepted variant - currently only `TestSecret` is exercised.
4. Audit-log verification (AUTH-005) - generating a presigned URL is sensitive and should be logged.
5. Idempotency: two calls with the same `callId` should return the same URL or new URLs that both work; document expected behavior.
6. Error path: simulate downstream S3 / signing failure, expect 500 with structured error envelope.

### Improvement Suggestions / Irrelevant Tests - Audio

- The happy-path test uses `BeEquivalentTo(... ExcludingMissingMembers)`, which silently ignores any new fields the contract grows. Consider a Verify-style snapshot (CS-006) to lock the full response shape.
- Only `FakeOnly` coverage exists for the happy path. There is no `RealOnly` smoke counterpart that confirms the prod path also signs URLs.
- Consider parameterising the auth-negative tests across all telephony endpoints rather than duplicating per file.

---

## CallCompletedApiTests.cs

Endpoint: `POST /zo-tools/v1/call/complete` - terminal callback that records full call transcript + metadata after a call ends.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | Yes | `PostCallCompleted_WithoutAuth_Returns401` | No `zd-token` header rejected | POST a populated `CallCompletedRequest`; assert 401. | Negative auth: anonymous post blocked. | Auth |
| 2 | Yes | `PostCallCompleted_WithWrongSecret_Returns401` | Wrong secret rejected | POST with `zd-token: wrong-secret`; assert 401. | Negative auth: bad secret blocked. | Auth |

### Suggested Missing Tests - CallCompleted

1. Happy-path `Returns200` with valid `TestSecret` - the suite has zero positive coverage for this endpoint; the public contract is essentially untested.
2. Persistence verification: after a successful POST, a follow-up admin/seed read or transcript-fetch endpoint should return the recorded turns.
3. Validation: missing `CallId`, `PracticeId`, `StartTime > EndTime`, empty `Turns`, future-dated `StartTime`, or unknown `TurnRole` enum should all return 400.
4. Idempotency: replaying the same payload (same `CallId`) should not double-record; assert the second call returns 200/204/409 per design.
5. PHI handling: turn `Text` may contain patient utterances - confirm no PHI leaks into error messages or request logs (HttpLoggingMiddleware).
6. Audit log (AUTH-005): write a record indicating the call was finalized.
7. `IsTestCall=false` end-to-end smoke against the real downstream sink.
8. Large payloads: many turns / very long `Text` fields should be accepted up to a documented limit, then rejected with 413.
9. Caller context headers: should `call-id`/`caller-phone-number` be required here? Document and test.

### Improvement Suggestions / Irrelevant Tests - CallCompleted

- This file has only auth-negative coverage. Strongly recommend treating it as a P0 gap - this is the endpoint that captures the source of truth for every completed call.
- `[Category("FakeOnly")]` happy-path tests are missing; recommend at least one fake-backed positive test plus one Verify snapshot of the persisted record.
- The two existing tests duplicate what every other endpoint tests; the auth gate is presumably middleware-shared, so there is little marginal value beyond one negative test.

---

## CsatToolApiTests.cs

Endpoint: `POST /zo-tools/v1/tools/submit_csat` - end-of-call CSAT capture (1-5 stars + optional feedback).

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | Yes | `SubmitCsat_WithoutAuth_Returns401` | No header rejected | POST CSAT body without auth; assert 401. | Negative auth: anonymous blocked. | Auth |
| 2 | Yes | `SubmitCsat_WithInvalidSecret_Returns401` | Wrong header rejected | POST with `zd-token: invalid-secret`; assert 401. | Negative auth: bad secret blocked. | Auth |
| 3 | Yes | `SubmitCsat_MissingScore_Returns400` | Missing `stars` field rejected | POST `{call_id, practice_phone_number}` without `stars`; assert 400. | Validation: required field missing. | Validation |
| 4 | Yes | `SubmitCsat_ScoreBelowRange_Returns400` | `stars=0` rejected | POST with `stars=0`; assert 400. | Validation: lower-out-of-range. | Validation |
| 5 | Yes | `SubmitCsat_ScoreAboveRange_Returns400` | `stars=6` rejected | POST with `stars=6`; assert 400. | Validation: upper-out-of-range. | Validation |
| 6 | Yes | `SubmitCsat_ScoreLowerBoundary_ReturnsSuccess` | `stars=1` accepted | POST with `stars=1`; assert `Success=true`. | Boundary-inclusive lower edge. | Validation - FakeOnly |
| 7 | Yes | `SubmitCsat_ScoreUpperBoundary_ReturnsSuccess` | `stars=5` accepted | POST with `stars=5`; assert `Success=true`. | Boundary-inclusive upper edge. | Validation - FakeOnly |
| 8 | Yes | `SubmitCsat_ValidRequest_ReturnsSuccess` | Default valid payload accepted | POST default body (`stars=4`); assert `Success=true`. | Happy path. | Success - FakeOnly |
| 9 | Yes | `SubmitCsat_WithFeedback_ReturnsSuccess` | Optional `feedback` field accepted | POST with `feedback="Great experience..."`; assert success. | Optional free-text accepted. | Success - FakeOnly |
| 10 | Could be merged into #8 | `SubmitCsat_WithoutFeedback_ReturnsSuccess` | Null `feedback` accepted | POST with `feedback=null`; assert success. | Confirms `feedback` is optional. | Success - FakeOnly |

### Suggested Missing Tests - CsatTool

1. Missing `call_id` and missing `practice_phone_number` should each return 400 - currently only missing `stars` is tested.
2. Non-integer `stars` (e.g. `4.5`, `"four"`, negative) - already partially covered, but float / string variants are untested.
3. Idempotency: re-submitting CSAT for the same `call_id` should either upsert or return 409; behavior is undocumented and untested.
4. Excessive `feedback` length (e.g. >5KB) - should reject with 400/413.
5. PHI scrubbing: `feedback` is free-text user input that may contain PHI; confirm it is not logged in plain text and is stored encrypted.
6. Audit log (AUTH-005) entry for each CSAT submission tying score back to call.
7. Persistence verification: after success, a downstream metric / record should reflect the submitted score.
8. Feature-flag-off path (if CSAT is gated by a flag) returns a no-op success.

### Improvement Suggestions / Irrelevant Tests - CsatTool

- Tests #6/#7 (boundary success) and #8 (typical success) are strong, but assertion is `Success=true` only - response shape is otherwise unverified. A Verify snapshot would catch contract drift.
- Test #10 is weakly differentiated from #8 (default body already implicitly covers null feedback in some helpers); consolidating would reduce duplication.
- Validation tests #3/#4/#5 use raw anonymous types - extracting a `BadCsatPayload` helper would make intent clearer.
- No test exercises the `ProductionSecret` constant against this endpoint.

---

## InitializeCallApiTests.cs

Endpoint: `GET /zo-tools/v1/tools/practice/{practicePhoneNumber}/init` - returns the practice context, transfer config, voice config, and SMS-eligibility for an inbound call. Marked `[NonParallelizable]` because tests touch shared seed state. Cache headers `X-Cache-Hit` / `X-Cache-TTL` are observed.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | Yes | `InitializeCall_WithNoAuth_Returns401` | No header rejected | GET endpoint without auth; assert 401. | Negative auth: anonymous blocked. | Auth |
| 2 | Yes | `InitializeCall_WithInvalidSecret_Returns401` | Bad secret rejected | GET with `zd-token: invalid-secret`; assert 401. | Negative auth: bad secret blocked. | Auth |
| 3 | Yes | `InitializeCall_MissingCallIdHeader_Returns400` | Missing `call-id` rejected | GET with `TestSecret` + `caller-phone-number` only; assert 400. | Validation: required call-context header missing. | Auth |
| 4 | Yes | `InitializeCall_MissingCallerPhoneNumberHeader_Returns400` | Missing `caller-phone-number` rejected | GET with `TestSecret` + `call-id` only; assert 400. | Validation: required call-context header missing. | Auth |
| 5 | Yes | `InitializeCall_WithTestSecret_ReturnsAllFields` | Default response shape | GET with full headers; assert `PracticeId`/`PracticeName` non-empty, `ShouldAutoTransfer=false`, `UseMultilingualStt=false`. | Happy path field check. | Success - FakeOnly |
| 6 | Yes | `InitializeCall_FirstRequest_ReturnsCacheMissHeader` | First call yields cache-miss | GET with unique phone; assert `X-Cache-Hit=false` and no `X-Cache-TTL`. | Cache-miss header semantics. | Success - FakeOnly |
| 7 | Yes | `InitializeCall_SecondRequest_ReturnsCacheHitHeaderWithTtl` | Second call yields cache-hit + TTL | GET twice with same phone; assert `X-Cache-Hit=true`, TTL present and 1-300. | Cache-hit + TTL bounds. | Success - FakeOnly |
| 8 | Yes | `InitializeCall_DifferentPhoneNumbers_IndependentCacheState` | Cache is keyed per phone | GET phone A then phone B; assert phone B is `X-Cache-Hit=false`. | Cache-key isolation per practice phone. | Success - FakeOnly |
| 9 | Yes | `InitializeCall_WithValidHeaders_ReturnsPhoneNumberFields` | Default phone-lookup-disabled defaults | GET with auth headers; assert `IsAnonymousNumber=true`, `CanReceiveSms=false`, `IsConsentToReceiveSmsGranted=null`. | Defaults when phone-number-lookup feature is off. | Success - FakeOnly |
| 10 | Yes | `InitializeCall_WithSeededData_ReturnsSeededResponse` | Admin-seeded values surfaced | Seed `/admin/seed/initialize-call/{phone}` with custom values; GET; assert response matches seeded shape. | Seed roundtrip. | Seed - FakeOnly |
| 11 | Yes | `InitializeCall_AfterClearingSeededData_ReturnsDefaultResponse` | Seed clear restores defaults | Seed, then DELETE seed, then GET; assert PracticeId is no longer the seeded value. | Seed lifecycle - clear path. | Seed - FakeOnly |
| 12 | Yes | `InitializeCall_WhenPhoneNumberLookupEnabled_WithUsMobileNumber_ReturnsCanReceiveSmsTrue` | Mobile -> SMS-eligible when flag on | Seed flag on; GET with `caller-phone-number=+15551234567`; assert `IsAnonymousNumber=false`, `CanReceiveSms=true`. | Phone-type lookup happy mobile path. | PhoneNumberLookup - FakeOnly |
| 13 | Yes | `InitializeCall_WhenPhoneNumberLookupEnabled_WithLandlineNumber_SetsCanReceiveSmsFalse` | Landline -> SMS not eligible | Seed flag on; GET with landline number; assert `CanReceiveSms=false`. | Landline rejection from SMS. | PhoneNumberLookup - FakeOnly |
| 14 | Yes | `InitializeCall_WhenPhoneNumberLookupEnabled_WithAnonymousNumber_SetsIsAnonymousTrue` | "Anonymous" caller flagged | Seed flag on; GET with `caller-phone-number=Anonymous`; assert `IsAnonymousNumber=true`, `CanReceiveSms=false`. | Anonymous-caller branch. | PhoneNumberLookup - FakeOnly |
| 15 | Yes | `InitializeCall_WhenPhoneNumberLookupDisabled_ReturnsDefaultPhoneNumberFields` | Flag off -> default safe values | GET without seeding flag; assert `IsAnonymousNumber=true`, `CanReceiveSms=false`. | Default branch when feature flag off. | PhoneNumberLookup - FakeOnly |

### Suggested Missing Tests - InitializeCall

1. Cache-TTL countdown: a second hit a few seconds later should show a smaller `X-Cache-TTL` value than the first hit.
2. Cache eviction / expiry: after waiting past TTL or after a `DELETE` invalidation, `X-Cache-Hit` should flip back to false.
3. Unknown / unmapped practice phone number returns 404 (not 200 with empty defaults).
4. Practice-phone-number URL encoding edge cases (international formats, leading `+`, percent-encoded `%2B`).
5. International / non-US mobile numbers - should they be `CanReceiveSms=true`? Document and test.
6. Audit log (AUTH-005) entry per call init carrying `call-id` and `caller-phone-number`.
7. Concurrency: many parallel inits for same phone should serialize cache-warm correctly without producing two distinct responses.
8. Production-secret variant.
9. Verify-snapshot of the full `InitializeCallData` payload to lock the contract (current tests probe scattered fields with `BeEquivalentTo + ExcludingMissingMembers`).
10. PHI: `caller-phone-number` is logged in middleware; confirm scrubbing rules.

### Improvement Suggestions / Irrelevant Tests - InitializeCall

- Tests #6 and #8 generate phone numbers from `DateTime.UtcNow.Ticks`, which is flaky under fast-running CI (collisions when ticks differ by 1). Recommend `Guid.NewGuid()` for uniqueness.
- The custom `GetWithHeadersAsync` helper duplicates much of `ApiTestHelpers.GetAsync`; consider extending the shared helper to expose response headers so tests need no inline `HttpClient`.
- Seed lifecycle tests (#10, #11) couple to `/admin/seed/initialize-call` - consider extracting a `using SeededInitializeCall(...)` IDisposable to eliminate try/finally clutter.
- Tests #12-#15 are very similar - consider `[TestCase]` parameterization to reduce duplication and improve readability.
- `BuildMinimalPracticeInfo` is static helper inside the test class; if `PracticeInfoApiTests` builds similar shapes, dedupe into a shared builder.

---

## SmsApiTests.cs

Endpoints under `/zo-tools/v1/tools/{practicePhoneNumber}/...`:
- `POST .../send-sms` - dispatch a confirm/cancel/reschedule/booking_confirmed SMS for a session's appointments.
- `GET .../set-sms-consent?is_consent_to_receive_sms_granted={bool}` - record patient SMS consent.

Most tests gate on the `phonebot_turn_on_sms` experiment via `WithExperimentOverride(...)`.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | Yes | `SendSms_WithoutAuth_Returns401` | Anonymous POST rejected | POST send-sms without `zd-token`; assert 401. | Negative auth. | SendSms - Auth |
| 2 | Yes | `SendSms_WithWrongSecret_Returns401` | Bad secret rejected | POST with `zd-token: wrong-secret`; assert 401. | Negative auth. | SendSms - Auth |
| 3 | Yes | `SendSms_MissingCallIdHeader_Returns400` | Missing `call-id` header rejected | POST with `TestSecret` only; assert 400. | Validation: required call-context header. | SendSms - Auth |
| 4 | Yes | `SendSms_ConfirmDetails_EmptyAppointmentIds_Returns400` | confirm_details with empty list | POST `sms_type="confirm_details"` + empty `appointment_ids`; assert 400. | Validation: empty list rejected. | SendSms - Validation - FakeOnly |
| 5 | Yes | `SendSms_CancelWithMultipleIds_Returns400` | cancel disallows multiple ids | POST `sms_type="cancel"` with two ids; assert 400. | Validation: cancel is single-appointment only. | SendSms - Validation - FakeOnly |
| 6 | Yes | `SendSms_RescheduleWithMultipleIds_Returns400` | reschedule disallows multiple ids | POST `sms_type="reschedule"` with two ids; assert 400. | Validation: reschedule single-appointment only. | SendSms - Validation - FakeOnly |
| 7 | Yes | `SendSms_BookingConfirmedWithMultipleIds_Returns400` | booking_confirmed disallows multiple ids | POST `sms_type="booking_confirmed"` with two ids; assert 400. | Validation: booking-confirmed single-appointment only. | SendSms - Validation - FakeOnly |
| 8 | Yes | `SendSms_ConfirmDetails_SingleAppointment_Returns200` | confirm_details happy path - one appt | Seed call session; POST confirm-details with one appt; assert 200 and `Directives` is null. | Single-appointment confirm path. | SendSms - Success - FakeOnly |
| 9 | Yes | `SendSms_ConfirmDetails_MultipleAppointments_Returns200` | confirm_details happy path - many appts | Seed session with two appts; POST confirm-details for both; assert 200 and `Directives` null. | Multi-appointment confirm path (only confirm allows multi). | SendSms - Success - FakeOnly |
| 10 | Could be merged | `SendSms_Cancel_EmptyAppointmentIds_Returns400` | cancel with empty list | POST `sms_type="cancel"` empty list; assert 400. | Duplicates #4 in spirit; specific to cancel. | SendSms - Validation - FakeOnly |
| 11 | Yes | `SendSms_Cancel_CancelledAppointment_Returns200WithNoDirectives` | Cancelling already-cancelled appt | Seed cancelled appt; POST cancel; assert 200 and `Directives` null (no transfer). | Idempotent cancel branch. | SendSms - Success - FakeOnly |
| 12 | Yes | `SendSms_Cancel_NonCancelledAppointment_Returns200WithTransferDirective` | Cancel on active appt produces transfer | Seed active appt; POST cancel; assert 200 and `Directives.Transfer != null`. | Cancel-required-transfer branch. | SendSms - Success - FakeOnly |
| 13 | Could be merged | `SendSms_BookingConfirmed_EmptyAppointmentIds_Returns400` | booking_confirmed with empty list | POST `sms_type="booking_confirmed"` empty list; assert 400. | Duplicates #4/#10 for booking_confirmed. | SendSms - Validation - FakeOnly |
| 14 | Yes | `SendSms_BookingConfirmed_ValidAppointment_Returns200WithNoDirectives` | booking_confirmed happy path | Seed session; POST booking_confirmed with one appt; assert 200 and `Directives` null. | Booking-confirmed happy path. | SendSms - Success - FakeOnly |
| 15 | Could be merged | `SendSms_Reschedule_EmptyAppointmentIds_Returns400` | reschedule with empty list | POST `sms_type="reschedule"` empty list; assert 400. | Duplicates #4/#10/#13 for reschedule. | SendSms - Validation - FakeOnly |
| 16 | Yes | `SendSms_Reschedule_ValidRequest_Returns200WithNoDirectives` | reschedule happy path | Seed session; POST reschedule one appt; assert 200 and `Directives` null. | Reschedule happy path. | SendSms - Success - FakeOnly |
| 17 | Yes | `SendSms_FlagOff_Returns200WithEmptyResult` | Feature flag off short-circuits | POST with `phonebot_turn_on_sms=off`; assert 200 and `Directives` null (no SMS sent). | Feature-flag gating - off branch. | SendSms - FakeOnly |
| 18 | Yes | `SendSms_FlagOn_ProceedsNormally` | Feature flag on processes normally | Seed session; POST with flag on; assert 200 and null directives. | Feature-flag gating - on branch. | SendSms - FakeOnly |
| 19 | Yes | `SetSmsConsent_WithoutAuth_Returns401` | Anonymous GET rejected | GET with `?is_consent_to_receive_sms_granted=true` no auth; assert 401. | Negative auth. | SetSmsConsent - Auth |
| 20 | Yes | `SetSmsConsent_WithWrongSecret_Returns401` | Bad secret rejected | GET with `zd-token: wrong-secret`; assert 401. | Negative auth. | SetSmsConsent - Auth |
| 21 | Yes | `SetSmsConsent_MissingCallIdHeader_Returns400` | Missing `call-id` rejected | GET with `TestSecret` only; assert 400. | Validation: missing call-context. | SetSmsConsent - Auth |
| 22 | Yes | `SetSmsConsent_ConsentTrue_Returns200` | Consent=true happy path | Seed session; GET `?is_consent_to_receive_sms_granted=true`; assert 200 and `Directives` null. | Consent grant path. | SetSmsConsent - Success - FakeOnly |
| 23 | Yes | `SetSmsConsent_ConsentFalse_Returns200` | Consent=false happy path | Seed session; GET `?is_consent_to_receive_sms_granted=false`; assert 200 and `Directives` null. | Consent revoke path. | SetSmsConsent - Success - FakeOnly |
| 24 | Yes | `SetSmsConsent_NoSession_Returns400` | Missing session for `call-id` rejected | GET with `call-id` that has no seeded session; assert 400. | Validation: session must exist. | SetSmsConsent - FakeOnly |
| 25 | Yes | `SetSmsConsent_FlagOff_Returns200WithEmptyResult` | Flag off returns no-op | GET with flag off; assert 200 and `Directives` null. | Feature-flag gating - off branch. | SetSmsConsent - FakeOnly |
| 26 | Yes | `SetSmsConsent_FlagOn_ProceedsNormally` | Flag on processes normally | Seed session; GET with flag on; assert 200 and `Directives` null. | Feature-flag gating - on branch. | SetSmsConsent - FakeOnly |

### Suggested Missing Tests - Sms

1. `send-sms` with invalid / unknown `sms_type` enum value should return 400 with a clear error.
2. `appointment_ids` containing duplicates (e.g. `[A, A]` for confirm_details) - dedupe vs reject behavior should be specified and tested.
3. Patient with `canReceiveSms=false` (landline / anonymous) should not dispatch SMS for any `sms_type` - missing.
4. Session whose patient has no consent recorded (`IsConsentToReceiveSmsGranted=null`) - dispatch behavior is unclear and untested.
5. After a successful `set-sms-consent`, a follow-up `init` should reflect the new consent value (cross-endpoint state verification).
6. `set-sms-consent` query parameter validation: missing param, non-boolean, etc.
7. Idempotent re-send: posting the same `confirm_details` twice in one session - should it spam SMS or short-circuit?
8. Audit log (AUTH-005) for both endpoints - SMS dispatch and consent change are sensitive events.
9. PHI handling: appointment metadata in `Directives.Transfer` may contain provider/patient name; verify it does not leak to logs.
10. Response-shape Verify snapshot for the `Directives.Transfer` populated branch (test #12 only checks `Directives.Transfer != null`).
11. International phone numbers in `practicePhoneNumber` URL segment - encoding + carrier eligibility.
12. Production-secret happy path.
13. Cancel-on-non-existent appointment id should return 404, not 200/400.
14. Concurrency: two SMS dispatches for the same `call-id` racing - ensure no double-send.

### Improvement Suggestions / Irrelevant Tests - Sms

- The four "empty-list" validation tests (#4, #10, #13, #15) are essentially the same scenario across `sms_type`s - parameterise via `[TestCase]` to halve the surface area.
- All "happy path" success tests assert only `result.Directives.Should().BeNull()` (or `.NotBeNull()`). The actual SMS payload that would be dispatched is never asserted - a fake SMS sink that captures the dispatched message would dramatically increase value, otherwise these tests cannot detect a regression where the SMS is silently dropped or sent with wrong content.
- The custom `JsonSerializerOptions` (snake_case + enum converter) and inline `SeedZoCallSession` helper are shared with other suites - move to a `TelephonySessionBuilder`/shared fixture.
- Test #11 (cancelling an already-cancelled appt) is the most informative test in the file - call this out as a model for similar idempotency tests in other suites.
- `SeedZoCallSession` builds a deeply nested anonymous object - extract a strongly-typed builder so contract drift in `/admin/seed/zo-call-session` is detected at compile time.
- `ClearZoCallSession` and `ClearFeatureFlag` are duplicated across `InitializeCallApiTests` and `SmsApiTests` - dedupe into a shared `TelephonyAdminClient`.

---

## Cross-Cutting Coverage Gaps (Telephony)

Patterns that recur across all five files and represent the highest-leverage gaps:

1. **Audit logging (AUTH-005)**: None of the telephony endpoints have tests verifying audit-log emission. Every call-completion, CSAT submission, SMS dispatch, and consent change is a sensitive event and should have a corresponding log assertion.
2. **PHI scrubbing**: Endpoints handle phone numbers, free-text feedback, and full call transcripts. There are no tests confirming that `HttpLoggingMiddleware` redacts these fields. Recommend a shared "PHI redaction" test pattern.
3. **Idempotency / replay**: `POST /call/complete`, `POST /submit_csat`, and `POST /send-sms` are all replayable mutation endpoints with no idempotency tests. Document expected behavior (upsert? 409? no-op?) and lock it in tests.
4. **Response-shape contracts**: Most positive tests use `BeEquivalentTo + ExcludingMissingMembers` or just probe a couple of fields. Adopt Verify snapshot tests (CS-006) for at least one happy-path per endpoint to lock the full contract.
5. **Production-secret coverage**: Only `TestSecret` is exercised. `ProductionSecret` is defined in `TestConstants` but never used positively - either delete the constant or add at least one production-flow smoke test.
6. **Concurrency / `[NonParallelizable]` sprawl**: `InitializeCallApiTests` is `[NonParallelizable]`, but other files mutate shared seed state (`/admin/seed/zo-call-session/{callId}`) without the same attribute. Audit and either parallelize safely or mark consistently.
7. **Downstream-failure paths (5xx)**: No test simulates an S3 sign failure (Audio), SMS-provider outage (Sms), or persistence sink failure (CallCompleted). Each should produce a structured 500/502 response and a retryable error envelope.

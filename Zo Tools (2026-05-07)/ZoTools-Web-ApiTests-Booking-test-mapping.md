# ZoTools.Web.ApiTests - API Test Mapping (Booking)

> Repo: [Zocdoc/zo-tools](https://github.com/Zocdoc/zo-tools)  
> Folder: `tests/ZoTools.Web.ApiTests/`  
> Generated: 2026-05-07
>
> These are HTTP-level integration-style API tests, exercising the controller surface end-to-end with stubbed downstream services. This file covers the booking flow: appointment reschedule/cancel/update-location, booking submit, and patient CRUD/validation.

## AppointmentApiTests.cs

`POST /zo-tools/v1/tools/{phone}/appointments/{apptId}/{reschedule|cancel|update-reschedule-location}`.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|-------------------|-----------|---------------|-------|---------|-------|
| 1 | | RescheduleAppointment_WithoutAuth_Returns401 | Auth missing rejected | POST without zd-token; assert 401. | Reject anonymous. | In: auth. Out: validation. |
| 2 | | RescheduleAppointment_WithWrongSecret_Returns401 | Invalid secret rejected | POST with bad token; assert 401. | Reject bad token. | In: secret. Out: validation. |
| 3 | | RescheduleAppointment_MissingCallIdHeader_Returns400 | Missing call-id rejected | Omit call-id; POST; assert 400. | Required header. | In: header. Out: body. |
| 4 | | RescheduleAppointment_MissingCallerPhoneNumberHeader_Returns400 | Missing caller-phone-number rejected | Omit; POST; assert 400. | Required header. | In: header. Out: body. |
| 5 | | RescheduleAppointment_PreviewWithNoSession_Returns404 (RealOnly) | Real-mode preview without session => 404 | Real cluster; nonexistent call id; POST confirm=false; assert 404. | Real-mode session lookup miss. | In: real cluster path. Out: fake fallback. |
| 6 | | RescheduleAppointment_PreviewWithSession_Returns200WithSpeakSideEffect | Preview returns speak directive | Seed session; POST confirm=false; assert appointment_id + directives.speak populated; cleanup. | Preview emits speak. | In: preview directives. Out: actual reschedule. |
| 7 | | RescheduleAppointment_PreviewWithAcceptLanguageHeader_Returns200 | Spanish preview path | Seed; POST with accept-language=es-US; assert appointment_id + speak. | Localization passthrough. | In: language. Out: text content checks. |
| 8 | | RescheduleAppointment_ConfirmWithNoSession_Returns404 (RealOnly) | Real-mode confirm without session => 404 | nonexistent call id; POST confirm=true; assert 404. | Real-mode missing session. | In: real cluster path. Out: fake. |
| 9 | | RescheduleAppointment_ConfirmWithSession_Returns200WithSpeakSideEffect | Confirm reschedules and returns speak | Seed; POST confirm=true; assert appointment_id + speak; cleanup. | Confirm path. | In: confirm directive. Out: write through. |
| 10 | | RescheduleAppointment_WhenRescheduleBlocked_Returns200WithTransferDirective | Blocked reason yields transfer directive | Seed with rescheduleBlockedReason=WithinCutoffHours; POST confirm=false; assert transfer directive type=appointment; cleanup. | Blocked => transfer. | In: blocked reschedule. Out: cancel blocked. |
| 11 | | CancelAppointment_WithoutAuth_Returns401 | Auth missing rejected | POST cancel without token; assert 401. | Reject anonymous. | In: auth. Out: validation. |
| 12 | | CancelAppointment_WithWrongSecret_Returns401 | Invalid secret rejected | POST cancel with bad token; assert 401. | Reject bad token. | In: secret. Out: validation. |
| 13 | | CancelAppointment_MissingCallIdHeader_Returns400 | Missing call-id rejected | Omit call-id; POST cancel; assert 400. | Required header. | In: header. Out: body. |
| 14 | | CancelAppointment_MissingCallerPhoneNumberHeader_Returns400 | Missing caller-phone-number rejected | Omit; POST cancel; assert 400. | Required header. | In: header. Out: body. |
| 15 | | CancelAppointment_PreviewWithSession_Returns200WithSpeakDirective | Preview cancel returns speak | Seed; POST confirm=false; assert speak contains "cancel"; cleanup. | Preview cancel. | In: preview directives. Out: confirm. |
| 16 | | CancelAppointment_PreviewWithNoSession_Returns404 | Preview without session => 404 | Nonexistent call; POST confirm=false; assert 404. | Missing session. | In: 404. Out: success. |
| 17 | | CancelAppointment_ConfirmWithSession_Returns200WithSpeakDirective | Confirm cancels and returns speak | Seed; POST confirm=true; assert speak contains "cancelled"; cleanup. | Confirm cancel. | In: write-through. Out: blocked path. |
| 18 | | CancelAppointment_ConfirmWithNoSession_Returns404 | Confirm without session => 404 | Nonexistent call; POST confirm=true; assert 404. | Missing session. | In: 404. Out: success. |
| 19 | | CancelAppointment_WhenCancelBlocked_Returns200WithTransferDirective | Blocked reason => transfer directive | Seed with cancelBlockedReason=WithinCutoffHours; POST confirm=false; assert transfer directive; cleanup. | Blocked => transfer. | In: blocked cancel. Out: reschedule. |
| 20 | | UpdateRescheduleLocation_WithoutAuth_Returns401 | Auth missing rejected | POST without token; assert 401. | Reject anonymous. | In: auth. Out: validation. |
| 21 | | UpdateRescheduleLocation_WithWrongSecret_Returns401 | Invalid secret rejected | POST with bad token; assert 401. | Reject bad token. | In: secret. Out: validation. |
| 22 | | UpdateRescheduleLocation_MissingCallIdHeader_Returns400 | Missing call-id rejected | Omit; POST; assert 400. | Required header. | In: header. Out: body. |
| 23 | | UpdateRescheduleLocation_MissingCallerPhoneNumberHeader_Returns400 | Missing caller-phone-number rejected | Omit; POST; assert 400. | Required header. | In: header. Out: body. |
| 24 | | UpdateRescheduleLocation_HybridLocationType_Returns400 | Hybrid location rejected | location_type=Hybrid; POST; assert 400. | Enum constrained. | In: enum subset. Out: success enums. |
| 25 | | UpdateRescheduleLocation_UnknownLocationType_Returns400 | Unknown location rejected | location_type=Unknown; POST; assert 400. | Enum constrained. | In: enum subset. Out: success enums. |
| 26 | | UpdateRescheduleLocation_PhysicalOnly_Returns200WithAppointmentId | PhysicalOnly accepted | Seed; POST PhysicalOnly; assert appointment_id; cleanup. | Physical-only accepted. | In: PhysicalOnly. Out: VirtualOnly. |
| 27 | | UpdateRescheduleLocation_VirtualOnly_Returns200WithAppointmentId | VirtualOnly accepted | Seed; POST VirtualOnly; assert appointment_id; cleanup. | Virtual-only accepted. | In: VirtualOnly. Out: PhysicalOnly. |
| 28 | | UpdateRescheduleLocation_NoSessionForCall_Returns200WithTransferDirective | Missing session => 200 with transfer | Nonexistent call; POST PhysicalOnly; assert appointment_id and transfer directive (no 404). | Session-less fallback to transfer. | In: graceful degradation. Out: 404. |

### Suggested Missing Tests
- **RescheduleAppointment_NewStartTime_InThePast_Returns400** — date-validation gap.
- **RescheduleAppointment_PerformingProviderId_Mismatch_Returns400** — body mismatch case.
- **CancelAppointment idempotency** — calling confirm twice on same appointment behavior unclear.
- **Audit logging** — reschedule and cancel are sensitive writes; per AUTH-005 audit-log assertion missing.
- **RescheduleAppointment_WithBothBlockedReasons_Returns200WithTransfer** — only single-blocked scenarios tested.
- **UpdateRescheduleLocation_NoSession_Returns200WithTransfer is suspicious** — test 28 returns 200 when no session, but most other endpoints return 404. Flag inconsistency.
- **Concurrency: reschedule + cancel in parallel** — race coverage absent.
- **PHI** — appointment data uses synthetic providers/locations; ok.

### Improvement Suggestions / Irrelevant Tests
- **Test 5/8 (RealOnly)** — these are environmental-only tests. Document why they cannot run in fake mode (real cluster session lookup), and make sure CI doesn't accidentally run them in a fake-only profile.
- **SeedZoCallSession / SeedZoCallSessionWithBlocked are duplicated across files** — extract to a shared fixture.
- **`json.GetProperty(...).GetString()` accumulates** — consider a typed POJO (`RescheduleResult`) and `BeEquivalentTo` for a stricter response shape assertion.
- **Three near-identical auth+header sections** for reschedule/cancel/update-location — parametrize with [TestCase] over endpoint URL.

---

## BookingApiTests.cs

`POST /zo-tools/v1/tools/{phone}/booking/submit`.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|-------------------|-----------|---------------|-------|---------|-------|
| 1 | | SubmitBooking_NoCredentials_Returns401 | Auth missing rejected | POST with empty headers; assert 401. | Reject anonymous. | In: auth. Out: validation. |
| 2 | | SubmitBooking_InvalidSecret_Returns401 | Invalid secret rejected | POST with bad token; assert 401. | Reject bad token. | In: secret. Out: validation. |
| 3 | | SubmitBooking_MissingCallId_Returns400 | Missing call-id rejected | Omit call-id; POST; assert 400. | Required header. | In: header. Out: body. |
| 4 | | SubmitBooking_MissingCallerPhoneNumber_Returns400 | Missing caller-phone-number rejected | Omit; POST; assert 400. | Required header. | In: header. Out: body. |
| 5 | | SubmitBooking_ConfirmFalse_ReturnsSpeakDirective | Preview returns English speak | POST confirm=false; assert directives.speak contains "I'd like to book an appointment" and AppointmentId is null. | Preview emits language-specific speak. | In: preview text. Out: localization. |
| 6 | | SubmitBooking_ConfirmFalse_SpanishLanguage_ReturnsSpeakInSpanish | Spanish preview | accept-language=es-US; POST confirm=false; assert speak contains "Quisiera reservar". | Spanish localization. | In: locale. Out: confirm. |
| 7 | | SubmitBooking_ConfirmTrue_ExistingPatient_ReturnsAppointmentId | Confirm with existing patient | Seed session with patient-123; POST confirm=true with patient_id=patient-123; assert AppointmentId non-empty and speak contains "has been booked"; cleanup. | Confirm path for existing patient. | In: existing patient. Out: new patient. |
| 8 | | SubmitBooking_ConfirmTrue_NewPatient_ReturnsAppointmentId | Confirm with new patient | Seed session with zo_pat_test_new_patient; POST confirm=true; assert AppointmentId non-empty + speak; cleanup. | Confirm path for new patient. | In: new patient. Out: existing patient. |
| 9 | | SubmitBooking_ConfirmTrue_SpanishLanguage_ReturnsSpanishSpeak | Spanish confirmation speak | Seed; POST confirm=true with accept-language=es-US; assert speak contains "reservada"; cleanup. | Spanish confirm message. | In: locale. Out: text content. |

### Suggested Missing Tests
- **SubmitBooking_ConfirmFalse_MissingRequiredFields_Returns400** — no body-validation test (e.g. missing start_time, provider_id).
- **SubmitBooking_PastStartTime_Returns400** — no temporal validation test.
- **SubmitBooking_DurationZeroOrNegative_Returns400** — body range validation gap.
- **SubmitBooking_ConfirmTrue_NoSession_Returns404** — only happy paths exercise session; missing-session path absent.
- **SubmitBooking_ConfirmTrue_DownstreamFailure_Returns500_OrSpecificError** — downstream-failure not exercised.
- **SubmitBooking_Idempotency** — calling confirm twice with same body — should produce one appointment, not two.
- **Audit logging** — booking creates a write; AUTH-005 requires audit. Not asserted.
- **SubmitBooking_PHI** — Jane Doe seed is synthetic; ok; no test asserts logging redaction of patient details.
- **SubmitBooking_ResponseShape (full)** — only asserts AppointmentId+Directives.Speak; full schema not snapshotted.

### Improvement Suggestions / Irrelevant Tests
- **Spanish + English duplicated** — tests 5+6 and 7+9 differ only by accept-language. Use [TestCase] over locale.
- **Hardcoded message strings ("I'd like to book an appointment", "Quisiera reservar", "has been booked", "reservada")** — fragile to copy edits. Either snapshot via Verify or use locale resource constants.
- **`patient-123` magic id repeated 5+ times** — extract to a const.

---

## InsertPatientApiTests.cs

`POST /zo-tools/v1/tools/practice/{phone}/insert-patient`.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|-------------------|-----------|---------------|-------|---------|-------|
| 1 | | InsertPatient_WithoutAuth_Returns401 | Auth missing rejected | POST without token; assert 401. | Reject anonymous. | In: auth. Out: validation. |
| 2 | | InsertPatient_WithWrongSecret_Returns401 | Invalid secret rejected | POST with bad token; assert 401. | Reject bad token. | In: secret. Out: validation. |
| 3 | | InsertPatient_MissingCallIdHeader_Returns400 | Missing call-id rejected | Omit; POST; assert 400. | Required header. | In: header. Out: body. |
| 4 | | InsertPatient_MissingCallerPhoneNumberHeader_Returns400 | Missing caller-phone-number rejected | Omit; POST; assert 400. | Required header. | In: header. Out: body. |
| 5 | | InsertPatient_MissingDateOfBirth_Returns400 | DoB required | Body missing date_of_birth; POST; assert 400. | Body validation. | In: required field. Out: success. |
| 6 | | InsertPatient_MissingFirstName_Returns400 | first_name required | Body missing first_name; POST; assert 400. | Body validation. | In: required field. Out: success. |
| 7 | | InsertPatient_MissingLastName_Returns400 | last_name required | Body missing last_name; POST; assert 400. | Body validation. | In: required field. Out: success. |
| 8 | | InsertPatient_WithProductionSecret_Returns200WithPatientId | Happy path returns patient id | POST with prod secret; assert PatientId="zo_pat_someId". | Successful insertion. | In: stub-driven id. Out: duplicate detection. |

### Suggested Missing Tests
- **InsertPatient_DuplicatePatient_Returns409** — what happens if patient already exists? Untested.
- **InsertPatient_FutureDateOfBirth_Returns400** — temporal validation absent.
- **InsertPatient_FirstNameTooLong_Returns400** — oversized payload rejection absent.
- **InsertPatient_FirstNameContainsControlChars_Returns400** — sanitization absent.
- **InsertPatient_AuditLog** — patient creation creates PHI; per AUTH-005 audit log required; no assertion.
- **InsertPatient_500_FromDownstream** — downstream-failure case absent.
- **InsertPatient_Idempotency** — same body twice — what's the contract?
- **InsertPatient_UnicodeName_AcceptedAndPersisted** — i18n names not tested.
- **Response shape (full)** — only PatientId is asserted; no snapshot.

### Improvement Suggestions / Irrelevant Tests
- **Asserting `PatientId.Should().Be("zo_pat_someId")`** — relies on a hardcoded fake id, which couples the test to the stub. Acceptable for fake mode but flag this clearly so it's not confused with a real-id assertion.
- **`zo_pat_someId` magic string** — extract to a TestConstants entry to avoid copy-paste drift.

---

## UpdatePatientApiTests.cs

`POST /zo-tools/v1/tools/practice/{phone}/update-patient`.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|-------------------|-----------|---------------|-------|---------|-------|
| 1 | | UpdatePatient_WithoutAuth_Returns401 | Auth missing rejected | POST without token; assert 401. | Reject anonymous. | In: auth. Out: validation. |
| 2 | | UpdatePatient_WithWrongSecret_Returns401 | Invalid secret rejected | POST with bad token; assert 401. | Reject bad token. | In: secret. Out: validation. |
| 3 | | UpdatePatient_MissingCallIdHeader_Returns400 | Missing call-id rejected | Omit; POST; assert 400. | Required header. | In: header. Out: body. |
| 4 | | UpdatePatient_MissingCallerPhoneNumberHeader_Returns400 | Missing caller-phone-number rejected | Omit; POST; assert 400. | Required header. | In: header. Out: body. |
| 5 | | UpdatePatient_MissingPatientId_Returns400 | patient_id required | Body without patient_id; POST; assert 400. | Body validation. | In: required field. Out: success. |
| 6 | | UpdatePatient_WhenPatientNotInSession_Returns400 (RealOnly) | Real-mode unknown patient => 400 | Real cluster, nonexistent call+patient; POST; assert 400. | Real-mode session check. | In: real cluster. Out: fake fallback. |
| 7 | | UpdatePatient_WithSeededPatient_Returns200 | Happy path with seeded patient | Seed session with patient; POST with patient_id; assert 200; cleanup. | Update success. | In: seeded patient. Out: full field. |
| 8 | | UpdatePatient_WithAllFields_Returns200 | All optional fields accepted | Seed; POST with all fields (email/phone/address/city/state/zip/gender/insurance*); assert 200; cleanup. | All fields wired. | In: full field shape. Out: response correctness. |
| 9 | | UpdatePatient_WhenPatientNotInSessionFake_Returns400 | Fake-mode unknown patient => 400 | Seed only one patient; POST with different patient_id; assert 400; cleanup. | Patient-in-session validation. | In: session lookup. Out: success. |

### Suggested Missing Tests
- **UpdatePatient_InvalidEmail_Returns400** — email format validation absent.
- **UpdatePatient_InvalidPhone_Returns400** — phone format absent.
- **UpdatePatient_InvalidStateCode_Returns400** — state validation absent.
- **UpdatePatient_InvalidZip_Returns400** — zip format absent.
- **UpdatePatient_NoFieldsProvided_NoOpOr400** — body with only patient_id behavior unclear.
- **UpdatePatient_Idempotency** — same payload twice should be safe.
- **UpdatePatient_AuditLog** — patient update is sensitive; per AUTH-005 audit log required; no assertion.
- **UpdatePatient_500_FromDownstream** — downstream failure not exercised.
- **UpdatePatient_ResponseShape** — only status code asserted (test 7,8); no body content check. After all, what does this endpoint return?

### Improvement Suggestions / Irrelevant Tests
- **Tests 7 and 8 only assert HTTP 200** — no body check at all. These are functionally smoke tests; flag for adding response-body assertions (or a Verify snapshot).
- **Test 6 is RealOnly and test 9 is FakeOnly** — they assert effectively the same thing (unknown patient => 400). Document why both are needed or consolidate behind a parametrized environment switch.

---

## ValidatePatientApiTests.cs

`POST /zo-tools/v1/tools/practice/{phone}/validate-patient`.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|-------------------|-----------|---------------|-------|---------|-------|
| 1 | | ValidatePatient_WithoutAuth_Returns401 | Auth missing rejected | POST without token; assert 401. | Reject anonymous. | In: auth. Out: validation. |
| 2 | | ValidatePatient_WithWrongSecret_Returns401 | Invalid secret rejected | POST with bad token; assert 401. | Reject bad token. | In: secret. Out: validation. |
| 3 | | ValidatePatient_MissingCallIdHeader_Returns400 | Missing call-id rejected | Omit; POST; assert 400. | Required header. | In: header. Out: body. |
| 4 | | ValidatePatient_MissingCallerPhoneNumberHeader_Returns400 | Missing caller-phone-number rejected | Omit; POST; assert 400. | Required header. | In: header. Out: body. |
| 5 | | ValidatePatient_MissingPatientId_Returns400 | patient_id required | Empty body; POST; assert 400. | Body validation. | In: required field. Out: success. |
| 6 | | ValidatePatient_WhenPatientNotInSession_Returns400 (RealOnly) | Real-mode unknown patient => 400 | Real cluster; nonexistent call+patient; POST; assert 400. | Real-mode session check. | In: real cluster. Out: fake. |
| 7 | | ValidatePatient_WithFullPatient_ReturnsValidStatus | Full patient => Valid | Seed full patient; POST; assert PatientValidationStatus=Valid and Message=null. | All fields present yields Valid. | In: full validation pass. Out: missing fields. |
| 8 | | ValidatePatient_WithPartialPatient_ReturnsMissingFieldsStatusWithMessage | Partial patient => MissingFields with msg | Seed partial patient (only patientId); POST; assert MissingFields status with non-empty message. | Missing-fields path. | In: missing-fields. Out: success. |
| 9 | | ValidatePatient_WhenPatientNotInSessionFake_Returns400 | Fake-mode unknown patient => 400 | Seed one patient; POST with other id; assert 400; cleanup. | Patient-in-session validation. | In: session lookup. Out: validation. |

### Suggested Missing Tests
- **ValidatePatient_MessageContent_DescribesMissingFields** — test 8 asserts message non-empty but doesn't verify it lists which fields are missing. The agent uses this message to prompt the patient — content matters.
- **ValidatePatient_PartialPatient_VariousFieldCombinations** — only one partial scenario (just patientId) is tested. Each missing-field combination could yield different messages.
- **ValidatePatient_500_FromDownstream** — downstream failure absent.
- **ValidatePatient_AuditLog** — reading PHI; per AUTH-005 audit-log assertion missing.
- **ValidatePatient_InvalidEmailInSession_ReturnsMissingFields** — patient with bad email present? Untested.
- **ValidatePatient_ResponseShape (full)** — Verify snapshot would catch silent shape changes.

### Improvement Suggestions / Irrelevant Tests
- **Test 6 (RealOnly) and test 9 (FakeOnly) overlap** — same outcome. Document or consolidate.
- **Test 8 asserts `Message.Should().NotBeNullOrEmpty()`** — too loose. The message guides agent behavior; pin to expected text or snapshot.
- **SeedZoCallSessionWithFullPatient / SeedZoCallSessionWithPartialPatient** are duplicated across UpdatePatient and ValidatePatient. Extract.

---

## PatientSearchApiTests.cs

`POST /zo-tools/v1/tools/practice/{phone}/search-patients`.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|-------------------|-----------|---------------|-------|---------|-------|
| 1 | | SearchPatients_WithoutAuth_Returns401 | Auth missing rejected | POST without token; assert 401. | Reject anonymous. | In: auth. Out: validation. |
| 2 | | SearchPatients_WithWrongHeaderValue_Returns401 | Invalid secret rejected | POST with bogus token; assert 401. | Reject bad token. | In: secret. Out: validation. |
| 3 | | SearchPatients_MissingCallIdHeader_Returns400 | Missing call-id rejected | Omit; POST; assert 400. | Required header. | In: header. Out: body. |
| 4 | | SearchPatients_MissingCallerPhoneNumberHeader_Returns400 | Missing caller-phone-number rejected | Omit; POST; assert 400. | Required header. | In: header. Out: body. |
| 5 | | SearchPatients_WithProductionSecret_Returns200 | Prod secret happy path | POST with prod secret; assert response.Patients not null. | Successful search. | In: smoke. Out: shape. |
| 6 | | SearchPatients_ReturnsPatientData | Result contents asserted | POST with prod secret; assert two known patients (John Doe, Jane Smith) returned in order. | Stable ordering and content. | In: ordered content. Out: filter behavior. |
| 7 | | SearchPatients_WithoutPhoneNumberInBody_UsesCallerPhoneNumber | Falls back to caller-phone-number header | Body without phone_number; POST; assert non-empty patients. | Header fallback. | In: implicit phone. Out: body explicit. |
| 8 | | SearchPatients_WithTestSecret_Returns500 | Test secret triggers 500 | POST with test secret; assert 500. | Test secret intentionally errors. | In: tier behavior. Out: success. |

### Suggested Missing Tests
- **SearchPatients_ZeroMatches_ReturnsEmptyList** — empty-result branch missing.
- **SearchPatients_DateOfBirthOnly_NoPhone** — at-least-one-criterion validation untested.
- **SearchPatients_InvalidDateOfBirth_Returns400** — body validation gap.
- **SearchPatients_500_FromDownstream** — only the "test secret triggers 500" path is tested; an actual downstream failure path is not.
- **SearchPatients_AuditLog** — searching patient PII is sensitive; AUTH-005 audit; not asserted.
- **SearchPatients_PartialMatch_FuzzyName** — fuzzy/substring behavior untested.
- **SearchPatients_PHI** — uses synthetic names (Doe/Smith); ok; no log-redaction assertion.

### Improvement Suggestions / Irrelevant Tests
- **Test 8 (`SearchPatients_WithTestSecret_Returns500`) is asserting a deliberate stub-throws-500 behavior** — unusual; flag this clearly. Per API-001 a 500 is a server error — exposing it as a documented contract is questionable. Consider adding a comment in the test or replacing with a 503 / specific status.
- **`Patients[0].PatientId.Should().NotBeNullOrEmpty()`** in test 7 — only checks first element exists. Use `BeEquivalentTo` for the full set.

# ZoTools.Web.ApiTests - API Test Mapping (Admin)

> Repo: [Zocdoc/zo-tools](https://github.com/Zocdoc/zo-tools)  
> Folder: `tests/ZoTools.Web.ApiTests/`  
> Generated: 2026-05-07
>
> These are HTTP-level integration-style API tests, exercising the controller surface end-to-end with stubbed downstream services. This file covers admin/ops-style endpoints: feature flag (Ab), admin seeding, debug tools, and tools schema discovery.

## AbApiTests.cs

`POST /zo-tools/v1/tools/ab/is-flag-on` and `POST /zo-tools/v1/tools/ab/get-variant`.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|-------------------|-----------|---------------|-------|---------|-------|
| 1 | | IsFlagOn_WithoutAuth_Returns401 | Auth missing rejected | Send headers without zd-token; POST is-flag-on; assert 401. | Reject unauthenticated flag check. | In: header-secret auth. Out: variant flow. |
| 2 | | IsFlagOn_WithInvalidSecret_Returns401 | Invalid secret rejected | Send wrong secret; POST is-flag-on; assert 401. | Reject bad token. | In: secret validation. Out: missing-header validation. |
| 3 | | IsFlagOn_MissingCallIdHeader_Returns400 | Missing call-id rejected | Omit call-id header; POST; assert 400. | Required header guard. | In: header validation. Out: body validation. |
| 4 | | IsFlagOn_MissingPracticePhoneNumberHeader_Returns400 | Missing practice-phone-number rejected | Omit practice-phone-number; POST; assert 400. | Required header guard. | In: header validation. Out: body validation. |
| 5 | | IsFlagOn_MissingCallerPhoneNumberHeader_Returns400 | Missing caller-phone-number rejected | Omit caller-phone-number; POST; assert 400. | Required header guard. | In: header validation. Out: body validation. |
| 6 | | IsFlagOn_WithEmptyFlagName_Returns400 | Empty flag name rejected | Send body with flag_name=""; POST; assert 400. | Body validation. | In: flag name required. Out: variant flow. |
| 7 | | IsFlagOn_WithWhitespaceFlagName_Returns400 | Whitespace flag name rejected | flag_name="   "; POST; assert 400. | Body validation. | In: flag name trimming. Out: visitor type. |
| 8 | | IsFlagOn_WithEmptyPracticeId_Returns400 | Empty practice id rejected | practice_id=""; POST; assert 400. | Body validation. | In: practice_id required. Out: caller phone visitor type. |
| 9 | | IsFlagOn_WithValidRequest_Returns200WithIsFlagOnField | Happy path returns IsFlagOn | Valid headers+body; POST; deserialize; assert IsFlagOn=false. | Successful flag check. | In: PracticeId visitor type. Out: variant. |
| 10 | | IsFlagOn_WithCallerPhoneNumberVisitorType_Returns200 | CallerPhoneNumber visitor type | Set visitor_type=CallerPhoneNumber; POST; assert 200. | Alt visitor type. | In: caller-phone visitor. Out: practice-id case. |
| 11 | | GetVariant_WithoutAuth_Returns401 | Auth missing rejected | No zd-token; POST get-variant; assert 401. | Reject unauthenticated variant. | In: auth. Out: body validation. |
| 12 | | GetVariant_WithInvalidSecret_Returns401 | Invalid secret rejected | Wrong secret; POST; assert 401. | Reject bad token. | In: secret validation. Out: header validation. |
| 13 | | GetVariant_MissingCallIdHeader_Returns400 | Missing call-id rejected | Omit call-id; POST; assert 400. | Required header guard. | In: header validation. Out: body validation. |
| 14 | | GetVariant_MissingPracticePhoneNumberHeader_Returns400 | Missing practice-phone-number rejected | Omit header; POST; assert 400. | Required header guard. | In: header validation. Out: body validation. |
| 15 | | GetVariant_MissingCallerPhoneNumberHeader_Returns400 | Missing caller-phone-number rejected | Omit header; POST; assert 400. | Required header guard. | In: header validation. Out: body validation. |
| 16 | | GetVariant_WithEmptyVariantName_Returns400 | Empty variant name rejected | variant_name=""; POST; assert 400. | Body validation. | In: variant name required. Out: practice id. |
| 17 | | GetVariant_WithWhitespaceVariantName_Returns400 | Whitespace variant name rejected | variant_name="   "; POST; assert 400. | Body validation. | In: variant name trimming. Out: visitor type. |
| 18 | | GetVariant_WithWhitespacePracticeId_Returns400 | Whitespace practice id rejected | practice_id="   "; POST; assert 400. | Body validation. | In: practice_id trimming. Out: variant logic. |
| 19 | | GetVariant_WithValidRequest_Returns200WithVariantValueField | Happy path returns variant | Valid; POST; deserialize; assert VariantValue not null. | Successful variant lookup. | In: variant lookup. Out: visitor type. |
| 20 | | GetVariant_WithCallerPhoneNumberVisitorType_Returns200 | CallerPhoneNumber visitor type | visitor_type=CallerPhoneNumber; POST; assert 200. | Alt visitor type. | In: caller-phone visitor. Out: practice-id case. |

### Suggested Missing Tests
- **IsFlagOn_FlagOverrideViaExperimentOverridesHeader_AppliesOverride** — only auth/validation are exercised; no test confirms the `ZD-Experiment-Overrides` header actually flips the flag.
- **GetVariant_WithVariantOverride_ReturnsOverriddenValue** — no positive assertion that overrides change the variant value.
- **IsFlagOn_FlagOnReturnsTrue** — every success case returns `IsFlagOn=false`. There is no fixture or override that exercises the true branch.
- **GetVariant_UnknownExperiment_ReturnsDefault** — no test for unknown variant name returning a documented default vs 400.
- **IsFlagOn_VisitorIdMismatch_ConsistencyAcrossCalls** — A/B should be sticky per visitor; not tested.
- **Response shape assertion for IsFlagOnResult / GetVariantResult** — only single fields are spot-checked; full schema is not snapshotted.

### Improvement Suggestions / Irrelevant Tests
- **Five-way duplication of header tests** — identical 401/400 header tests for IsFlagOn and GetVariant could share a [TestCase] matrix to halve count and stop drifting.
- **No FakeOnly/RealOnly category** — these tests run in any environment despite calling Plinth. Consider marking and asserting fake state.
- **No assertion on response body schema** — relies on Newtonsoft deserialize succeeding; a full snapshot would catch silent shape changes.

---

## AdminSeedApiTests.cs

`/admin/seed/*` POST/DELETE/GET — used to inject fake state for FakeOnly tests. Note: class-level `[Category("FakeOnly")]` and `[NonParallelizable]`.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|-------------------|-----------|---------------|-------|---------|-------|
| 1 | | SeedPracticeInfo_ThenGetPracticeInfo_ReturnsSeededData | Round-trip practice info seeding | POST seed/practice-info/{phone}; GET tools/practice/{phone}/info; assert seeded id+name. | Seeded practice info read back through public API. | In: seed read-through. Out: cache invalidation. |
| 2 | | ClearPracticeInfo_ThenGetPracticeInfo_ReturnsDefaultData | DELETE clears seeded record | Seed; DELETE; GET; assert seeded id is gone. | Seed deletion restores defaults. | In: clear-by-key. Out: clear-all. |
| 3 | | SeedPatientSearch_ThenSearchPatients_ReturnsSeededData | Round-trip patient search seeding | POST seed/patient-search/{phone}; POST tools search-patients; assert seeded patient. | Patient search seeding works. | In: seed patient list. Out: appointments seeding. |
| 4 | | SeedAppointments_ThenGetAppointments_ReturnsSeededData | Round-trip appointment seeding | POST seed/appointments/{phone}/{patientId}; POST appointments tool; assert seeded appointment. | Appointment seeding works. | In: seed appointments. Out: cache. |
| 5 | | ListSeededKeys_AfterSeeding_ShowsSeededKeys | List endpoint reflects seeded keys | Seed; GET /admin/seed; assert Count > 0. | Listing returns the seeded keys. | In: GET listing. Out: filter by entity. |
| 6 | | ClearAll_RemovesAllSeededData | DELETE all clears storage | Seed; DELETE /admin/seed; GET listing; assert Count=0. | Clear-all empties everything. | In: clear-all. Out: per-entity clear. |
| 7 | | ClearByEntityType_ClearsOnlyThatType | DELETE by entity type | Seed practice-info + patient-search; DELETE practice-info; assert only practice-info removed. | Selective clear by entity type. | In: per-entity clear. Out: per-key clear. |

### Suggested Missing Tests
- **SeedPracticeInfo_WithoutAuth_Returns401_OrConfirmedOpen** — admin/seed has no auth tests at all; whether endpoints are intentionally open is not asserted (security risk if deployed to non-local).
- **SeedPracticeInfo_InvalidEntityShape_Returns400** — no test for malformed seed payload.
- **ClearByEntityType_UnknownEntityType_Returns400Or404** — `ClearEntityType("does-not-exist")` is not exercised.
- **SeedAppointments_OverwriteExistingKey_LastWriteWins** — no idempotency/overwrite test.
- **ListSeededKeys_PaginationOrLargeResult** — no test of the dictionary response when many keys exist.
- **Concurrency** — `[NonParallelizable]` is used because shared state leaks; no tests assert behavior under parallel writers (would surface race conditions).

### Improvement Suggestions / Irrelevant Tests
- **Auth strategy for /admin/seed not documented in tests** — file should call out (in a comment or test) that these endpoints are FakeOnly + dev-only; otherwise security review can't confirm.
- **SeedAppointments helper uses raw HttpClient with cert validation disabled** — typical of fake-mode testing but should ideally route through ApiTestHelpers for consistency.
- **Test cleanup is best-effort** — some tests `await ClearAll()` only on success path, leaving leftover state on failure. Use `[TearDown]` or `try/finally` consistently.

---

## DebugToolsApiTests.cs

`GET /zo-tools/v1/debug-tools/{callId}/call-session` and `/call-session/appointments`. Class is `[NonParallelizable]`. Endpoints use JWT auth (not header secret) — file only covers negative-auth so far.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|-------------------|-----------|---------------|-------|---------|-------|
| 1 | | GetCachedAppointments_WithNoCredentials_Returns401 | Anonymous request rejected | GET cached-appointments with no headers; assert 401. | JWT-protected endpoint requires creds. | In: auth presence. Out: success path. |
| 2 | | GetCachedAppointments_WithHeaderSecret_Returns401 | Header-secret rejected (JWT only) | GET with zd-token; assert 401 since endpoint is JWT-auth. | Header secret is not accepted. | In: scheme rejection. Out: valid JWT path. |
| 3 | | GetCachedAppointments_WithInvalidSecret_Returns401 | Invalid secret rejected | GET with bogus zd-token; assert 401. | Bad credentials denied. | In: invalid creds. Out: roles. |
| 4 | | GetSession_WithNoCredentials_Returns401 | Anonymous rejected | GET call-session with no headers; assert 401. | Same as above for session endpoint. | In: auth presence. Out: success path. |
| 5 | | GetSession_WithHeaderSecret_Returns401 | Header-secret rejected (JWT only) | GET with zd-token; assert 401. | JWT-only enforcement. | In: scheme rejection. Out: valid JWT path. |
| 6 | | GetSession_WithInvalidSecret_Returns401 | Invalid secret rejected | GET with bogus zd-token; assert 401. | Bad creds denied. | In: invalid creds. Out: success path. |

### Suggested Missing Tests
- **WhenCallSessionNotFound_Returns404** (both endpoints) — already TODO'd in source, blocked on test JWT. High priority — these are the only success tests for the debug surface.
- **WhenCallSessionExists_Returns200WithPatients / Appointments** — both endpoints lack any happy-path coverage.
- **WithPatientId_FiltersToSpecificPatient / WithoutPatientId_UsesFirstPatient** — query-param semantics not tested.
- **Wrong-role JWT (PhoneBotReadPhi missing) returns 403** — TODOs only mention 401 path; need a 403 path for AUTH-001 conformance.
- **Audit logging assertion** — debug tools view PHI; per AUTH-005 the audit event should be asserted (verify a row written to audit store).
- **Rate limit / abuse guard** — debug endpoint serving PHI should rate-limit; not tested.

### Improvement Suggestions / Irrelevant Tests
- **JWT test infrastructure is the blocker** — TODO comments are explicit. This file is essentially "401-only smoke" until a fake JWT mint exists. Track this as a coverage debt item — the `// TODO` should be a Jira ticket, not a permanent comment.
- **PHI risk** — debug tools surface call session contents (patient identifiers, appointments). Tests that DO get added must use synthetic data only and audit-log assertions should be required.

---

## ToolsApiTests.cs

Mixed legacy tools surface: `/zo-tools/v1/tools/{phone}/{patientId}/appointments`, `/cancel_appointment`, `/get_transfer_number/{callCenterNumber}`. Some tests overlap with `AppointmentApiTests.cs`.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|-------------------|-----------|---------------|-------|---------|-------|
| 1 | | GetAppointments_WithoutAuth_Returns401 | Auth missing rejected | POST appointments without zd-token; assert 401. | Auth required. | In: auth. Out: validation. |
| 2 | | GetAppointments_WithInvalidSecret_Returns401 | Invalid secret rejected | Wrong secret; POST; assert 401. | Reject bad token. | In: secret validation. Out: validation. |
| 3 | | GetAppointments_MissingCallIdHeader_Returns400 | Missing call-id rejected | Omit call-id; POST; assert 400. | Required header. | In: header validation. Out: body validation. |
| 4 | | GetAppointments_MissingCallerPhoneNumberHeader_Returns400 | Missing caller-phone-number rejected | Omit; POST; assert 400. | Required header. | In: header validation. Out: body validation. |
| 5 | | GetAppointments_WithNegativePreviousAppointmentCount_Returns400 | Negative count rejected | get_previous_appointment_count=-1; POST; assert 400. | Body range validation. | In: range check. Out: success path. |
| 6 | | GetAppointments_WithPreviousAppointmentCountExceedingMax_Returns400 | Count above 10 rejected | get_previous_appointment_count=11; POST; assert 400. | Upper bound enforced. | In: range check. Out: success path. |
| 7 | | GetAppointments_WithProductionSecret_ReturnsAppointmentList | Happy path returns expected appointments | POST with prod secret; assert two known appointments returned with full shape (provider/location/status). | Documented response shape. | In: shape assertion. Out: cache headers. |
| 8 | | GetAppointments_PopulatesStartTimeFields | Start time fields populated | POST; assert StartTime, StartTimeWithOffset, StartTimeDescription all present. | Time fields formatted correctly. | In: time field shape. Out: timezone math. |
| 9 | | GetAppointments_ForExistingPatient_IsNewPatientIsFalse | Existing patient flag | POST against existing-patient endpoint; assert all IsNewPatient=false. | New-patient flag flips for known patient. | In: IsNewPatient logic. Out: scheduling rules. |
| 10 | | GetAppointments_WithEmptyLocationIds_ReturnsAppointmentList | Empty filter returns all | location_ids=[]; POST; assert non-empty list. | No-filter returns everything. | In: empty filter. Out: specific location filter. |
| 11 | | GetAppointments_RescheduleBlockedReasonMatchesCancelBlockedReason | Both blocked reasons surfaced | POST; assert both blocked-reason fields populated and match. | Modification-blocked reasons exposed. | In: blocked reasons. Out: actual modification. |
| 12 | | GetAppointments_WhenVisitReasonMappingIssueSeeded_ReturnsCancelBlockedReason | Seeded VR mapping appears in response | Seed appointment with cancelBlocked=true; POST; assert reason returned; clean up via DELETE. | Mapping issue surfaces. | In: seed-driven shape. Out: real EHR errors. |
| 13 | | GetAppointments_WithPhysicalLocations_ReturnsLocationTypePhysicalOnly | Physical location type detection | POST; assert all LocationType=PhysicalOnly for fake locs. | Type derived from location. | In: location classification. Out: hybrid case. |
| 14 | | GetAppointments_WithUnknownLocation_ReturnsLocationTypeUnknown | Unknown location yields Unknown | Seed appointment with unknown loc id; POST; assert LocationType=Unknown. | Default-out classification. | In: unknown loc handling. Out: physical/virtual. |
| 15 | | GetAppointments_WithPreviousAppointmentCount_ReturnsAppointmentList | Past-appt query param works | get_previous_appointment_count=5; POST; assert non-empty. | Past-history pagination. | In: query param wired. Out: shape of past appts. |
| 16 | | CancelAppointment_WithoutAuth_Returns401 | Auth missing rejected | POST cancel without token; assert 401. | Auth required. | In: auth. Out: validation. |
| 17 | | CancelAppointment_WithInvalidSecret_Returns401 | Invalid secret rejected | POST with bad token; assert 401. | Bad creds. | In: secret. Out: validation. |
| 18 | | CancelAppointment_MissingCallIdHeader_Returns400 | Missing call-id rejected | Omit; POST; assert 400. | Required header. | In: header. Out: body. |
| 19 | | CancelAppointment_MissingCallerPhoneNumberHeader_Returns400 | Missing caller-phone-number rejected | Omit; POST; assert 400. | Required header. | In: header. Out: body. |
| 20 | | CancelAppointment_MissingPracticePhoneNumberHeader_Returns400 | Missing practice-phone-number rejected | Omit; POST; assert 400. | Required header. | In: header. Out: body. |
| 21 | | CancelAppointment_WithProductionSecret_ReturnsCancellationId | Happy path returns cancellation id | POST with valid body; assert CancellationId not empty. | Cancellation success path. | In: cancel side effect. Out: blocked path. |
| 22 | | CancelAppointment_WithOnlyAppointmentId_ReturnsNotFoundWhenCacheDisabled | No cached session => 404 | POST with only appointment_id (no ehr id/sync); assert 404. | Cache miss returns 404. | In: cache fallback. Out: success path. |
| 23 | | CancelAppointment_WithAllFields_BackwardCompatible | Legacy field shape still works | POST with all fields; assert CancellationId returned. | Backward compatibility. | In: legacy shape. Out: forward shape. |
| 24 | | CancelAppointment_WhenCancelBlocked_ReturnsOKWithMessage | Blocked => 200 with reason | Seed cancelBlockedReason=WithinCutoffHours; POST; assert Success=false, message contains reason; cleanup. | 200 OK with explanation rather than 4xx. | In: blocked reason surfacing. Out: actual cancellation. |
| 25 | | GetTransferNumber_WithoutAuth_Returns401 | Auth missing rejected | GET transfer-number without token; assert 401. | Auth required. | In: auth. Out: success. |
| 26 | | GetTransferNumber_WithInvalidSecret_Returns401 | Invalid secret rejected | GET with bad token; assert 401. | Bad creds. | In: secret. Out: success. |
| 27 | | GetTransferNumber_WithTestSecret_ReturnsTransferNumber | Test-secret happy path | GET; assert transfer number contains "+1-555-123-4567". | Successful lookup. | In: lookup. Out: filtering by type. |
| 28 | | GetTransferNumber_WithProductionSecret_ReturnsTransferNumber | Prod-secret happy path | GET; assert transfer number returned. | Successful lookup with prod secret. | In: lookup. Out: filtering by type. |

### Suggested Missing Tests
- **CancelAppointment idempotency** — calling cancel twice with the same appointment id should be a no-op, not double-cancel; not tested.
- **CancelAppointment audit log** — cancellation is a sensitive write; AUTH-005 says we audit-log; no assertion present.
- **CancelAppointment_500_FromDownstream** — no test for downstream failure path / partial failure.
- **GetTransferNumber unknown call center => 404** — only happy path tested.
- **GetTransferNumber filtering by transfer number type** — endpoint likely supports type-filter but no test exercises it.
- **GetAppointments large result pagination** — no test of paging or capping if practice has many appointments.
- **GetAppointments PHI handling** — uses synthetic names; ok; but no test asserts logging redaction (per AUTH-005).
- **Concurrent CancelAppointment from two callers** — race / conflict (409) coverage absent.
- **GetAppointments cache header behavior** — `PracticeInfo` tests cache headers; this endpoint likely also caches but is not asserted.
- **Response schema snapshot** — uses `BeEquivalentTo` with `ExcludingMissingMembers` so unknown fields don't fail; consider strict mode or Verify snapshot test.

### Improvement Suggestions / Irrelevant Tests
- **Heavy overlap with AppointmentApiTests.cs cancel/reschedule** — `cancel_appointment` (this file) vs `appointments/{id}/cancel` (AppointmentApiTests). Tests should be consolidated or clearly labeled "v1 legacy" vs "v2".
- **Inline DELETE helpers replicated in every file** — `DeleteAsync` duplicates across ~6 files; move to `ApiTestHelpers`.
- **`SeedFeatureFlag`/`ClearFeatureFlag` defined but unused** in this file — dead code.

---

## ToolsSchemaApiTests.cs

`GET /zo-tools/v1/tools/schemas/{agentName}` — returns per-agent tool registry.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|-------------------|-----------|---------------|-------|---------|-------|
| 1 | | GetToolsSchema_AuthenticatePatientAgent_WithTestSecret_ReturnsExpectedResponse | Authenticate-patient agent has empty toolset | GET schemas/authenticate-patient-agent with test secret; assert agent name, version="1.0.0", Tools empty. | Agent registered with no tools. | In: agent existence. Out: tool contents. |
| 2 | | GetToolsSchema_AuthenticatePatientAgent_WithProductionSecret_ReturnsExpectedResponse | Same with prod secret | GET with prod secret; assert same shape. | Auth tier independence. | In: secret variants. Out: tool contents. |
| 3 | | GetToolsSchema_WithoutAuth_Returns401 | Auth missing rejected | GET schemas/greeting-agent without zd-token; assert 401. | Schema endpoint is auth-protected. | In: auth. Out: invalid agent. |
| 4 | | GetToolsSchema_WithInvalidSecret_Returns401 | Invalid secret rejected | GET with bad token; assert 401. | Bad creds. | In: secret. Out: agent name validation. |
| 5 | | GetToolsSchema_InvalidAgent_Returns400 | Unknown agent name rejected | GET schemas/unknown-agent; assert 400. | Agent name enum-checked. | In: agent name validation. Out: 404 vs 400 choice. |
| 6 | | GetToolsSchema_GreetingAgent_WithTestSecret_ReturnsExpectedResponse | Greeting agent toolset shape | GET; iterate Tools; for each assert Name/Endpoint/Method/Description/HasSideEffect/Parameters present; verify get_transfer_number tool fields/parameter "call_center_number". | Greeting agent registers tools with valid metadata and known transfer tool exists. | In: tool metadata schema. Out: actual tool execution. |
| 7 | | GetToolsSchema_GreetingAgent_WithProductionSecret_ReturnsExpectedResponse | Greeting agent with prod secret | GET; assert non-empty tools. | Tier independence. | In: secret variants. Out: tool execution. |
| 8 | | GetToolsSchema_ManageAppointmentAgent_WithTestSecret_ReturnsExpectedResponse | Manage-appointment agent shape | GET; iterate Tools; verify cancel_appointment exists with HasSideEffect=true, parameters={appointment_id}, no ehr_patient_id/sync_id. | Side-effecting tool exposed correctly. | In: side-effect flag and params. Out: tool execution. |
| 9 | | GetToolsSchema_ManageAppointmentAgent_IncludesCancelAppointmentTool | Cancel-appointment tool registered | GET; assert cancel_appointment in list with full shape (POST, /cancel_appointment, side-effect=true). | Specific tool registration. | In: cancel tool registration. Out: actual cancel behavior. |
| 10 | | GetToolsSchema_ManageAppointmentAgent_WithProductionSecret_ReturnsExpectedResponse | Same with prod secret | GET; assert non-empty Tools. | Tier independence. | In: secret variants. Out: tool execution. |
| 11 | | GetToolsSchema_SelectLocationAgent_WithTestSecret_ReturnsExpectedResponse | Select-location agent has 4 read-only tools | GET; assert 4 tools, all HasSideEffect=false, named validate_location/find_locations_near_zip/find_locations_in_state/find_locations_by_name. | Read-only tool registry shape. | In: tool count + names. Out: actual lookup. |
| 12 | | GetToolsSchema_SelectLocationAgent_WithProductionSecret_ReturnsExpectedResponse | Same with prod secret | GET; assert same. | Tier independence. | In: secret variants. Out: tool execution. |
| 13 | | GetToolsSchema_SelectTimeslotAgent_ReturnsSearchAvailabilityTool | Timeslot agent has search_availability | GET schemas/select-timeslot-agent; assert search_availability tool present. | Timeslot agent surface. | In: tool existence. Out: tool params. |

### Suggested Missing Tests
- **Schema-vs-impl contract** — for every tool listed by schema, the test should verify the corresponding endpoint exists and accepts the documented parameters. Today the schema is checked in isolation. This is the highest-value missing coverage per the AI agent contract.
- **HasSideEffect flag matches HTTP method semantics** — POST tools should have HasSideEffect=true unless explicitly safe; GET tools should have HasSideEffect=false. Not asserted globally.
- **Coverage of remaining agents** — no tests for select-provider-agent, csat-agent, sms-agent, etc. that exist elsewhere in this suite. Catalog drift.
- **Schema versioning** — Version is asserted as "1.0.0" string-equal; no test for backwards compatibility (e.g. "schema is forward-compatible: clients can ignore unknown fields").
- **Snapshot test (Verify) of full schema** — would catch any unintended tool addition/removal/rename in CI.
- **Auth: role-gated vs open** — schema endpoint is currently header-secret only; should it also be JWT? Not tested.

### Improvement Suggestions / Irrelevant Tests
- **GetToolsSchema_*_WithProductionSecret duplicates _WithTestSecret tests** — three pairs of tests are essentially copy-paste with secret swapped. Use [TestCase] with both secrets to halve count.
- **No catalog completeness test** — the existing `GetToolsSchema_ManageAppointmentAgent_IncludesCancelAppointmentTool` is a single-tool spot-check. A snapshot test of all tool names per agent would be much more durable.
- **Iteration assertion `foreach (tool in Tools)`** — repeated 3 times across tests; extract a helper that asserts "all tools have valid metadata".

# Repository - Unit Test Mapping

> Repo: [Zocdoc/zo-tools](https://github.com/Zocdoc/zo-tools)  
> Folder: `tests/ZoTools.UnitTests/Repository/`  
> Generated: 2026-05-07

## ZoCallSessionRepositoryTests.cs

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|-------------------|-----------|---------------|-------|---------|-------|
| 1 | | Create_SetsCallId | Factory method assigns CallId | Call `ZoCallSession.Create(CallId)`; assert. | CallId persistence. | In scope: callId. Out of scope: keys. |
| 2 | | Create_SetsPrimaryKey | Factory builds composite PrimaryKey "#ZoCallSession#{CallId}" | Create; assert PrimaryKey. | Composite key. | In scope: PK. Out of scope: SK. |
| 3 | | Create_SetsSortKey | SortKey is constant "SESSION" | Create; assert SortKey. | Sort key. | In scope: SK. Out of scope: PK. |
| 4 | | Create_InitializesEmptyGoals | Goals collection is initialized empty | Create; assert empty. | Defaults. | In scope: Goals. Out of scope: other. |
| 5 | | Create_InitializesEmptyPatients | Patients collection is initialized empty | Create; assert empty. | Defaults. | In scope: Patients. Out of scope: other. |
| 6 | | Create_WithNullCallId_ThrowsArgumentException | Null CallId rejected | Create(null!); assert throw. | Null guard. | In scope: null. Out of scope: empty. |
| 7 | | Create_WithEmptyCallId_ThrowsArgumentException | Empty CallId rejected | Create(""); assert throw. | Empty guard. | In scope: empty. Out of scope: null. |
| 8 | | Create_WithPhoneNumberInfo_SetsCallerPhoneNumberInfo | Optional phone info passed through to session | Build PhoneNumberInfo; Create; assert reference identity. | Phone info wiring. | In scope: phone. Out of scope: validation. |
| 9 | | Create_WithoutPhoneNumberInfo_SetsCallerPhoneNumberInfoToNull | Default no-phone form leaves the property null | Create(callId only); assert null. | Default null. | In scope: default. Out of scope: typed phone. |
| 10 | | GetByCallIdAsync_ReturnsSessionFromDynamo | Loads from Dynamo and returns session | Setup LoadAsync; call repo; assert returned session has expected keys. | Happy-path read. | In scope: load + return. Out of scope: cache. |
| 11 | | GetByCallIdAsync_WhenNotFound_ReturnsNull | Returns null when Dynamo misses | LoadAsync returns null; assert null. | Miss returns null. | In scope: null pass-through. Out of scope: log. |
| 12 | | GetByCallIdAsync_WhenNotFound_DoesNotLogRead | Miss does not emit audit log | Setup null; verify audit.LogCallSessionRead never called. | Audit only on hit. | In scope: audit gating. Out of scope: metric. |
| 13 | | GetByCallIdAsync_WhenAuthorized_LogsRead | Hit + production secret -> audit log emitted | Standard setup; verify audit called once with isAllowed=true. | Audit on hit. | In scope: audit. Out of scope: secret evaluation. |
| 14 | | GetByCallIdAsync_WhenUserHasPhoneBotPhiRole_IsAuthorizedAndLogs | Production secret false but phi role -> still authorized | Setup secret false / role true; verify audit and result. | Role-based auth fallback. | In scope: role auth. Out of scope: secret. |
| 15 | | GetByCallIdAsync_WhenNeitherSecretNorPhiRoleGranted_LogsAndThrows | No secret, no role -> UnauthorizedAccessException + audit denied | Setup both false; verify throw + audit isAllowed=false. | Auth denied path. | In scope: deny path. Out of scope: caller. |
| 16 | | GetByCallIdAsync_UsesConsistentRead | Always uses ConsistentRead=true on Dynamo | Verify config.ConsistentRead = true. | Consistency setting. | In scope: read consistency. Out of scope: index. |
| 17 | | SaveAsync_SavesSessionToDynamo | Save delegates to Dynamo SaveAsync | Call SaveCallSession; verify SaveAsync once. | Save delegation. | In scope: save call. Out of scope: TTL. |
| 18 | | SaveAsync_LogsAuditWrite | Save emits LogCallSessionWrite audit | Verify audit.LogCallSessionWrite called once with CallId. | Audit on write. | In scope: audit. Out of scope: error. |
| 19 | | UpsertSearchedPatientsAsync_WhenNoPatientsProvided_DoesNotLoadOrSave | Empty input -> short-circuits no I/O | Pass []; verify Dynamo Load and Save never. | Short-circuit on empty input. | In scope: no-op. Out of scope: error. |
| 20 | | UpsertSearchedPatientsAsync_WhenSessionNotFound_Throws | Missing session -> SessionNotFoundException; no save | LoadAsync null; pass [patient]; assert throw + no save. | Missing session throws. | In scope: throw. Out of scope: audit. |
| 21 | | UpsertSearchedPatientsAsync_WhenNewPatient_AddsToSearchedPatients | New ext id -> added with provided fields | LoadAsync returns session; pass new ExternalPatient; assert SearchedPatients map contains entry with fields. | New patient add. | In scope: add. Out of scope: dedupe. |
| 22 | | UpsertSearchedPatientsAsync_DoesNotTouchPatientsMap | Searched patients are separate from confirmed Patients | After upsert; assert Patients still empty. | Map separation. | In scope: separation. Out of scope: TTL. |
| 23 | | UpsertSearchedPatientsAsync_WhenExistingSearchedPatient_OverwritesEntry | Same ext id is overwritten with new fields | Pre-populate SearchedPatients; upsert; assert new fields win. | Overwrite semantics. | In scope: overwrite. Out of scope: merge. |
| 24 | | UpsertSearchedPatientsAsync_SkipsPatientsWithoutExternalPatientId | Null/empty/whitespace ext ids skipped | Pass mix of invalid + valid; assert single entry kept. | Validation filter. | In scope: filter. Out of scope: throw. |
| 25 | | UpsertSearchedPatientsAsync_SavesSessionOnce | Session saved exactly once after batch | Pass two patients; verify SaveAsync Times.Once. | Single save. | In scope: count. Out of scope: ordering. |
| 26 | | UpsertAppointments_ConvertsAndDelegates_AppointmentsAppearInSession | AppointmentDetails overload populates Patients map with mapped appointment | Pre-seed SearchedPatients; pass AppointmentDetails; assert Patients map has converted entry. | Conversion + delegation. | In scope: conversion. Out of scope: validation. |
| 27 | | UpsertAppointments_WhenSessionNotFound_DoesNotThrow | (CachedAppointmentDetails overload) Missing session does not throw | LoadAsync null; assert NotThrowAsync. | Soft-fail behavior. | In scope: no throw. Out of scope: log. |
| 28 | | UpsertAppointments_WhenSessionNotFound_DoesNotSave | Missing session also skips save | LoadAsync null; assert SaveAsync never. | No-op save. | In scope: save count. Out of scope: log. |
| 29 | | UpsertAppointments_WhenPatientExists_AppendsAppointment | Existing patient gets new appointment appended | Pre-seed Patients; upsert; assert two appointments. | Append. | In scope: append. Out of scope: dedupe. |
| 30 | | UpsertAppointments_WhenPatientNotInSession_CreatesPatientWithAppointment | Creates patient from SearchedPatients on first appointment | Pre-seed SearchedPatients; upsert; assert Patient created with appointment. | Hydrate from search. | In scope: hydrate. Out of scope: typeof. |
| 31 | | UpsertAppointments_WhenPatientNotInSession_MergesSearchedPatientData | Hydrated patient inherits search data fields | Pre-seed SearchedPatients with rich data; upsert; assert fields copied. | Field merge. | In scope: merge. Out of scope: types. |
| 32 | | UpsertAppointments_WhenPatientNotInSession_AndNoSearchedData_Throws | No SearchedPatients entry and useSearchPatientCache=true -> InvalidOperationException | Empty Searched; useSearchPatientCache=true; assert throw with message. | Strict guard. | In scope: throw. Out of scope: bypass. |
| 33 | | UpsertAppointments_WhenUseSearchPatientCacheFalse_CreatesBarePatientWithoutThrowing | Bypass flag creates a bare patient | useSearchPatientCache=false; assert patient created with null fields + appointment. | Bypass flag. | In scope: bypass. Out of scope: hydrate. |
| 34 | | UpsertAppointments_WhenPatientsMapIsNull_InitializesAndAddsPatient | Null Patients map is initialized | Set Patients=null; upsert; assert map exists with new patient. | Null-map init. | In scope: init. Out of scope: thread safety. |
| 35 | | UpsertAppointments_SavesSessionAfterUpdate | Save called once after upsert | Verify SaveAsync once. | Save. | In scope: count. Out of scope: order. |
| 36 | | UpsertAppointments_WhenDuplicateAppointmentId_DoesNotAddDuplicate | Duplicate appt id -> no growth | Pre-seed appt; upsert same id; assert single. | Dedupe. | In scope: dedupe. Out of scope: which wins. |
| 37 | | UpsertAppointments_WithMultipleAppointments_MergesBothIntoSession | Multiple appts in one call merged | Pass 2 new; assert both present. | Bulk merge. | In scope: merge. Out of scope: order. |
| 38 | | UpsertAppointments_WhenPatientExists_PreservesExistingPatientData | Existing fields not erased on upsert | Pre-seed patient with DOB, insurance ids; upsert; assert preserved. | Field preservation. | In scope: preserve. Out of scope: appt. |
| 39 | | ToCachedAppointmentDetails_MapsAllFields | Mapper copies every AppointmentDetails field including RescheduleParams | Build full details; map; assert all fields. | DTO mapper. | In scope: mapping. Out of scope: validation. |
| 40 | | ToCachedAppointmentDetails_WhenNullableFieldsAreNull_MapsNulls | Mapper handles all-null nullable inputs | Null inputs; map; assert null fields. | Null mapping. | In scope: null. Out of scope: defaults. |
| 41 | | FindAppointmentAsync_WhenSessionNotFound_ReturnsBothNull | Missing session -> tuple of nulls | LoadAsync null; assert both null. | Miss path. | In scope: miss. Out of scope: log. |
| 42 | | FindAppointmentAsync_WhenAppointmentFound_ReturnsBoth | Found appt -> tuple session+appt by reference | Setup; assert reference identity. | Hit path. | In scope: hit. Out of scope: filter. |
| 43 | | FindAppointmentAsync_WhenAppointmentNotFound_ReturnsSessionAndNullAppointment | Session present but appt id missing | Wrong appt id; assert session+null. | Partial hit. | In scope: partial. Out of scope: throw. |
| 44 | | FindAppointmentAsync_WhenSessionHasNullPatients_ReturnsSessionAndNullAppointment | Null Patients map handled | Patients=null; assert session+null. | Null-map safety. | In scope: null map. Out of scope: throw. |
| 45 | | FindAppointmentAsync_WhenMultiplePatients_FindsCorrectAppointment | Searches across all patients | Two patients; assert correct appt found. | Cross-patient search. | In scope: search. Out of scope: ordering. |
| 46 | | FindAppointmentAsync_WithPatientId_WhenPatientNotFound_ReturnsSessionAndNullAppointment | PatientId restriction + missing patient -> null | Wrong patient id; assert null. | Patient filter miss. | In scope: filter. Out of scope: cross-patient. |
| 47 | | FindAppointmentAsync_WithPatientId_WhenAppointmentFoundUnderCorrectPatient_ReturnsBoth | PatientId disambiguates same appt id under different patients | Two patients with same appt id; assert correct one returned. | Filter-by-patient. | In scope: filter. Out of scope: dupe. |
| 48 | | FindAppointmentAsync_WithPatientId_WhenAppointmentExistsUnderDifferentPatient_ReturnsSessionAndNullAppointment | PatientId scopes search; cross-patient appt invisible | Wrong patient owns the appt; assert null. | Filter scoping. | In scope: scoping. Out of scope: log. |
| 49 | | FindAppointmentAsync_WhenPatientHasNullAppointments_ReturnsSessionAndNullAppointment | Null Appointments list handled | Appointments=null!; assert session+null. | Null-list safety. | In scope: null list. Out of scope: throw. |
| 50 | | FindAppointmentsAsync_WhenSessionNotFound_ReturnsNull | Missing session -> null result | LoadAsync null; assert null. | Miss. | In scope: miss. Out of scope: log. |
| 51 | | FindAppointmentsAsync_WhenPatientsMapIsNull_ReturnsNull | Null Patients -> null | Patients=null; assert null. | Null map. | In scope: null. Out of scope: empty. |
| 52 | | FindAppointmentsAsync_WhenPatientsMapIsEmpty_ReturnsNull | Empty Patients map -> null | Patients.Clear(); assert null. | Empty map. | In scope: empty. Out of scope: null. |
| 53 | | FindAppointmentsAsync_WithPatientId_WhenPatientFound_ReturnsPatientIdAndAppointments | Returns patient id and their appointments | Pre-seed; assert. | Patient lookup. | In scope: lookup. Out of scope: order. |
| 54 | | FindAppointmentsAsync_WithPatientId_WhenPatientNotFound_ReturnsNull | Missing patient -> null | Wrong patient id; assert null. | Patient miss. | In scope: miss. Out of scope: log. |
| 55 | | FindAppointmentsAsync_WithoutPatientId_UsesFirstPatientAndReturnsAppointments | No patient id -> uses the first patient | Two patients; assert first returned. | Default patient = first. | In scope: default. Out of scope: ordering guarantee. |
| 56 | | FindAppointmentsAsync_WhenPatientHasNoAppointments_ReturnsEmptyList | Patient with empty Appointments list | Empty list; assert empty result. | Empty list. | In scope: empty. Out of scope: null. |
| 57 | | FindAppointmentsAsync_WhenPatientAppointmentsAreNull_ReturnsEmptyList | Patient with Appointments=null | Null list; assert empty result. | Null list. | In scope: null. Out of scope: empty. |
| 58 | | GetAppointmentsByIds_WhenSessionNotFound_ReturnsNullSessionAndEmptyList | Missing session -> (null, []) | LoadAsync null; assert null+empty. | Miss. | In scope: miss. Out of scope: log. |
| 59 | | GetAppointmentsByIds_WhenSessionHasNullPatients_ReturnsSessionAndEmptyList | Session w/ null Patients -> session + empty | Patients=null; assert session+empty. | Null patient. | In scope: null. Out of scope: empty. |
| 60 | | GetAppointmentsByIds_WhenAllIdsFound_ReturnsAllAppointments | All ids matched | Pre-seed; assert all returned. | All-match. | In scope: search. Out of scope: order. |
| 61 | | GetAppointmentsByIds_WhenSomeIdsNotFound_ReturnsOnlyFoundAppointments | Partial match returns only found | One id missing; assert subset. | Partial match. | In scope: subset. Out of scope: error. |
| 62 | | GetAppointmentsByIds_WhenNoIdsFound_ReturnsSessionAndEmptyList | No ids match -> session+empty | Wrong ids; assert empty. | No match. | In scope: empty. Out of scope: log. |
| 63 | | GetAppointmentsByIds_AcrossMultiplePatients_FindsFromAll | Search spans all patients | Two patients with one each; assert both returned. | Cross-patient. | In scope: cross. Out of scope: order. |
| 64 | | GetAppointmentsByIds_WhenAppointmentIdsIsEmpty_ReturnsSessionAndEmptyList | Empty input ids -> session + empty | Pass []; assert empty. | Empty input. | In scope: empty input. Out of scope: null. |
| 65 | | GetAppointmentsByIds_WhenSessionHasEmptyPatients_ReturnsSessionAndEmptyList | Empty Patients map -> session + empty | Patients=[]; assert empty. | Empty map. | In scope: empty. Out of scope: null. |
| 66 | | MergeCachedAppointments_WhenExistingAppointmentsAreNull_ReturnsNewAppointments | Null existing -> returns new list | Null existing; pass new; assert equal new. | Null existing. | In scope: null. Out of scope: empty. |
| 67 | | MergeCachedAppointments_WhenNewAppointmentsIsEmpty_ReturnsExistingAppointments | Empty new -> returns existing list | Empty new; assert equal existing. | Empty new. | In scope: empty. Out of scope: null. |
| 68 | | MergeCachedAppointments_WhenBothListsAreNonOverlapping_ReturnsCombined | Disjoint lists merged with new-first ordering | Non-overlapping; assert combined order. | Combined merge. | In scope: order. Out of scope: dedupe. |
| 69 | | MergeCachedAppointments_WhenAppointmentIdsOverlap_DedupesAndPrefersNewAppointments | Overlapping ids -> new wins | Overlap on appt_001; assert new wins. | Dedupe + prefer-new. | In scope: dedupe. Out of scope: order strictness. |
| 70 | | GetPatient_ReturnsMatchingPatient | Returns patient with expected DOB | Pre-seed; assert DOB. | Patient lookup. | In scope: lookup. Out of scope: filter. |
| 71 | | GetPatient_ReturnsSessionAlongWithPatient | Returns session + patient | Pre-seed; assert both non-null. | Tuple. | In scope: tuple. Out of scope: keys. |
| 72 | | GetPatient_NullCallId_ReturnsNull | Null callId -> null patient | Pass null; assert null. | Null guard. | In scope: null. Out of scope: throw. |
| 73 | | GetPatient_EmptyCallId_ReturnsNull | Empty callId -> null patient | Pass ""; assert null. | Empty guard. | In scope: empty. Out of scope: null. |
| 74 | | GetPatient_NullPatientId_ReturnsNull | Null patientId -> null | Pass null patientId; assert null. | Null guard. | In scope: null. Out of scope: throw. |
| 75 | | GetPatient_PatientIdNotFound_ReturnsNull | Unknown patient id -> null | Wrong id; assert null. | Miss. | In scope: miss. Out of scope: log. |
| 76 | | GetPatient_SessionNotFound_ReturnsNull | Missing session -> both null | LoadAsync null; assert both null. | Miss. | In scope: miss. Out of scope: log. |
| 77 | | GetPatient_SessionHasNoPatients_ReturnsNull | Patients=null -> null patient | Set null; assert null. | Null map. | In scope: null. Out of scope: empty. |
| 78 | | GetPatient_PatientHasNullDob_ReturnsPatientWithNullDob | DOB null is preserved | Pre-seed null DOB; assert null DOB. | Null DOB. | In scope: null DOB. Out of scope: validation. |
| 79 | | GetPatient_ExceptionThrown_ReturnsNull | DynamoDB exception -> null patient (swallowed) | LoadAsync throws; assert null. | Exception swallow. | In scope: exception swallow. Out of scope: log assertion. |
| 80 | | AddPatient_WhenSessionNotFound_ThrowsException | Missing session -> Exception with formatted message; no save | LoadAsync null; assert throw + SaveAsync never. | Missing session error. | In scope: throw. Out of scope: type specificity. |
| 81 | | AddPatient_WhenPatientAlreadyExists_ThrowsInvalidOperationException | Duplicate patient -> InvalidOperationException | Pre-seed patient; assert throw. | Duplicate guard. | In scope: dup. Out of scope: save. |
| 82 | | AddPatient_WhenPatientDoesNotExist_AddsPatientToSession | New patient added by reference identity | Empty session; assert ContainsKey + BeSameAs. | Add. | In scope: add. Out of scope: save. |
| 83 | | AddPatient_WhenPatientsMapIsNull_InitializesAndAddsPatient | Null Patients map initialized then added | Patients=null; assert map non-null + contains. | Null-map init. | In scope: init. Out of scope: thread safety. |
| 84 | | AddPatient_WhenPatientDoesNotExist_SavesSession | Save invoked once on success | Verify SaveAsync once. | Save. | In scope: save count. Out of scope: order. |

### Suggested Missing Tests
- **Concurrent_GetByCallIdAsync_DoesNotCorruptCache** — DynamoDB ops can interleave; verify thread safety.
- **Save_FailsAtDynamo_PropagatesException** — Currently no explicit test that a Dynamo exception bubbles up from SaveCallSession.
- **Save_FailsAtDynamo_DoesNotEmitAuditWrite** — Audit must not log a successful write if Dynamo throws (or test the documented behavior).
- **GetByCallIdAsync_WhenAuditFails_DoesNotMaskOriginalException** — If audit logging itself fails, ensure the call result still flows through.
- **UpsertAppointments_RescheduleParams_PreservedOnAppendAndUpdate** — Mapping test exists, but no end-to-end roundtrip via Upsert + Find.
- **MergeCachedAppointments_WhenBothEmpty_ReturnsEmpty** — Null + empty are tested but not both-empty.
- **UpsertSearchedPatientsAsync_WhenSomeFieldsAreNull_PreservesNonNullFields** — Partial-null upserts.
- **AddPatient_NullPatient_Behavior** — What happens if `null` patient is passed?
- **PermissionService is constructed inline** — Real production behavior is exercised; consider also testing an injected mock to isolate this from PermissionService changes.
- **CancellationToken_PropagatesToDynamoCalls** — No test verifies CT is forwarded to LoadAsync/SaveAsync.

### Improvement Suggestions / Irrelevant Tests
- **PermissionService is real, not mocked** — This couples ZoCallSessionRepository tests to PermissionService internals; per CS-021 prefer mocking collaborators.
- **Audit verification asserts `LogCallSessionRead(CallId, true/false)` literal** — This tests implementation, not behavior; consider asserting via an in-memory audit collector instead.
- **`Create_*` tests are factory tests on `ZoCallSession`, not the repository** — Consider moving to `ZoCallSessionTests.cs` if it exists; mixing model and repository tests in one class makes file-of-truth ambiguous.
- **Reference identity asserts (`BeSameAs`)** — Several tests depend on exact reference equality from in-memory mocks; if the repo is later changed to clone results, every test breaks unnecessarily. Consider value equality.
- **Repeated `SetupDynamoLoad(...)` boilerplate** — Could be a fluent builder helper.
- **`UpsertAppointments_WhenPatientNotInSession_CreatesPatientWithAppointment` and `_MergesSearchedPatientData`** — Could be merged into one parameterized test.

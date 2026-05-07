# ZoTools.Web.IntegrationTests - Integration Test Mapping

> Repo: [Zocdoc/zo-tools](https://github.com/Zocdoc/zo-tools)  
> Folder: `tests/ZoTools.Web.IntegrationTests/`  
> Generated: 2026-05-07

## ExampleTests.cs

(placeholder file - contains a single trivial assertion `(1+1).Should().Be(2)` named `TokenEmptyTest` in class `TokenTest`. No real integration coverage.)

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|-------------------|-----------|---------------|-------|---------|-------|
| 1 | No - delete or replace | TokenEmptyTest | Nothing meaningful; asserts `1 + 1 == 2` | No setup -> trivial arithmetic -> assert | Smoke placeholder, not an integration test | In scope: nothing real. Out of scope: any actual ZoTools.Web integration behavior |

## LockedActionServiceIntegrationTests.cs

Exercises `LockedActionService` + `LockedActionRepository` against a real DynamoDB-compatible store via LocalStack. `IMetricRecorder` is mocked so the test asserts on emitted metric tags. The lock key is randomized per test and the table is scanned + cleared in `TearDown`.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|-------------------|-----------|---------------|-------|---------|-------|
| 1 | | RunIfNotLocked_WhenNotLocked_ShouldExecuteAction | Happy path: no existing lock -> action runs and result is returned | Setup fresh key -> call `RunIfNotLocked` with delay+result action -> assert result and `not_locked` metric | Action executes when no lock exists | In scope: lock acquisition + action execution + metric. Out of scope: persisted DTO shape (covered by next test) |
| 2 | | RunIfNotLocked_WhenNotLocked_UpdatesLockWithTheResult | Lock row is written with `IsCompleted=true`, `Version=v1`, serialized result | Run action -> load `LockedActionDto` directly from DDB -> assert flags + `Result` JSON | Confirms persistence contract after success | In scope: DDB write contract. Out of scope: TTL, GSIs |
| 3 | | RunIfNotLocked_WhenActionThrows_ShouldDeleteLockAndRethrow | Failure cleanup: action exception causes lock row to be deleted and exception rethrown | Run throwing action -> assert `Exception` thrown -> assert DDB row is null | Lock is released on failure so retries are not blocked | In scope: failure rollback. Out of scope: partial failure / network error mid-write |
| 4 | | RunIfNotLocked_WhenLockExists_CallsWaitAndReturnResult | Waiter path: existing in-progress lock -> caller waits for completion and returns the other writer's result | Pre-seed in-progress lock -> background task marks complete after 500ms -> assert returned result + `locked_found_success` metric + local action did not run | Second caller polls and adopts the first caller's result | In scope: cross-process completion handoff. Out of scope: clock skew, polling interval tuning |
| 5 | | RunIfNotLocked_WhenLocked_AndTimeout_ShouldThrowTimeoutException | Timeout path: existing lock never completes within `TestTimeout` (1s) -> `TimeoutException` thrown | Pre-seed in-progress lock -> call with timeout=1 -> assert `TimeoutException` and `locked_timed_out` metric | Caller bails out with a clear error when waiting too long | In scope: timeout signaling. Out of scope: cancellation token propagation, deadlock |
| 6 | | RunIfNotLocked_MultipleConcurrentRequests_OnlyOneExecutesAction | Contention: 5 parallel `Task.Run` callers on same lock key -> exactly one runs the action, all 5 receive the result, zero exceptions | Setup -> 5 concurrent calls -> assert `executedCount==1`, `successCount==5`, `exceptionCount==0` | Locking is correct under real concurrency | In scope: single-process parallel contention. Out of scope: multi-process / multi-host contention; conditional-write race details |
| 7 | | RunIfNotLocked_WithRescheduleLockedResult_ShouldSerializeAndDeserializeCorrectly | Polymorphic result type round-trips through DDB JSON | Run action returning `RescheduleLockedResult` -> assert returned object equals expected -> load row, deserialize stored `Result`, assert equality | Confirms `RescheduleLockedResult` serialization contract | In scope: serialization of one concrete result type. Out of scope: other lock action types, error branches of the result |
| 8 | | RunIfNotLocked_WhenLocked_WithV1JsonResult_ShouldDeserializeCorrectly | Backward-compat: pre-existing completed `v1` row is read and returned without re-running the action | Pre-insert completed lock with serialized `RescheduleLockedResult` -> call service -> assert equality + `locked_found_success` metric | Existing completed locks are honored, action is short-circuited | In scope: read-side compat for `v1`. Out of scope: future version migration / unknown version handling |

### Suggested Missing Tests

- **TTL / expiry behavior** - The `LockedActionDto` likely carries a TTL. Verify expired rows are treated as "no lock" (or rejected) so a stuck lock self-heals.
- **Stale (incomplete + old) lock recovery** - What happens if a writer crashes after `GivenExistingLockDto` but before completion and the timeout window elapses? Ensure the next caller can take over (today the test only covers the timeout-throws case).
- **Different `LockedActionType` values** - All tests use `LockedActionType.Reschedule`. Add at least one other type to confirm primary key construction does not collide across types for the same lock key.
- **Multi-key isolation** - Two different keys executing in parallel must not block each other.
- **Result deserialization failure** - Corrupt / unknown JSON in the stored `Result` should surface a clear error rather than silent null.
- **Unknown / future `Version` value** - Today only `v1` is exercised. Decide and test the behavior for an unrecognized version.
- **Metric increment count** - `VerifyMetricRecorded` defaults to `times = 1` but is never called with a higher count. The waiter path probably emits multiple metrics over polling iterations - assert the expected total.
- **Cancellation** - `CancellationToken` is not threaded through any of these tests; add a cancellation-during-wait test.

### Improvement Suggestions / Irrelevant Tests

- **Mocking `IMetricRecorder` while integrating against real DDB is a hybrid** - This is fine, but per TEST-002 prefer a fake (e.g., recording fake) over Moq verification for richer assertions and cleaner diagnostics.
- **TearDown does a full table scan** - With parallel test execution this risks cross-test interference. Prefer deleting only rows for the per-test `_testLockKey`, or run tests sequentially with `[NonParallelizable]`.
- **Magic timing (`Task.Delay(500)`, timeout=1s)** - Flake risk on slow CI. Inject a fake clock / configurable poll interval so timing is deterministic.
- **`#region` blocks substitute for splitting the class** - Consider splitting into `RunIfNotLocked_NotLockedTests`, `RunIfNotLocked_AlreadyLockedTests`, `ConcurrencyTests`, `SerializationTests` per CS-021 readability guidance.
- **`LockedActionType` and `Version` are stringly-typed in assertions** - Use the enum / a typed constant for `"v1"` to catch future changes at compile time.

## ZoCallSessionRepositoryTests.cs

Exercises `ZoCallSessionRepository` against LocalStack DynamoDB. Audit logger and metric recorder are mocked, `ISecretValidationService` is mocked to always return "production secret". No teardown - relies on unique randomized call IDs per test.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|-------------------|-----------|---------------|-------|---------|-------|
| 1 | | GetByCallIdAsync_WhenNoSessionExists_ReturnsNull | Read-miss returns null | Setup -> call `GetCallSession` for unknown id -> assert null | Repository returns null on miss | In scope: miss path. Out of scope: error / partial-row cases |
| 2 | | GetByCallIdAsync_AfterSavingCleanSlate_ReturnsSessionWithCallId | Default session round-trips with expected keys, model type, empty collections | Save default `ZoCallSession` -> read back -> assert primary/sort keys, `ModelType`, empty `Patients` and `Goals` | Confirms baseline persistence contract | In scope: keys + model type + default collections. Out of scope: optional fields |
| 3 | | SaveAsync_WithPatientAndGoal_CanResolvePatientViaGoalPatientId | Goal `PatientId` resolves to a patient on the same session, with full appointment payload | Build session with one patient (2 appointments) and one goal -> save -> read back -> assert goal->patient resolution and appointment fields | Cross-field linkage between Goals and Patients persists correctly | In scope: linkage + appointment subset of fields. Out of scope: multiple patients / multiple goals |
| 4 | | SaveAsync_UpdatePatientOnExistingSession_OverwritesPrevious | Re-save replaces the prior `Patients` map (not merged) | Save session with `patient-old` -> save fresh session for same callId with `patient-new` -> read back -> assert only `patient-new` is present | Documents replace-not-merge semantics for `Patients` | In scope: overwrite semantics for one map. Out of scope: merge semantics for any field |
| 5 | | SaveAsync_WithRescheduleParams_CanBeReadBack | `RescheduleParams` nested in `CachedAppointmentDetails` round-trips | Save patient with appointment containing `RescheduleParams` -> read back -> assert all 5 fields equal | Confirms nested object serialization | In scope: positive serialization. Out of scope: schema evolution |
| 6 | | SaveAsync_WithNullRescheduleParams_CanBeReadBack | Null `RescheduleParams` round-trips as null | Save appointment with `RescheduleParams = null` -> read back -> assert null | Null-handling for optional nested object | In scope: null branch. Out of scope: missing-attribute (vs explicit null) distinction |
| 7 | | SaveAsync_WithAllFields_CanBeReadBack | "Wide" session with patient + goal + timeslot + nodatime `LocalDate` round-trips | Build session with most fields populated -> save -> read back -> assert deep equality | Smoke test for full DTO surface | In scope: positive round-trip of populated fields. Out of scope: each field independently, validation / required fields |
| 8 | | SaveAsync_WithSearchedPatient_AllFieldsRoundTrip | `SearchedPatients` map round-trips with all fields | Save session with one `SearchedPatient` -> read back -> assert equivalence | Confirms the second patient-style map persists | In scope: positive round-trip. Out of scope: empty map vs absent map |
| 9 | | SaveAsync_WithSearchedPatientNullDateOfBirth_RoundTripsAsNull | Null DOB and empty `LocationIds` round-trip safely | Save searched patient with null DOB and empty list -> read back -> assert null + empty | Null/empty edges for optional fields on `SearchedPatient` | In scope: null/empty edges for one DTO. Out of scope: validation rules |

### Suggested Missing Tests

- **Authorization branch** - `ISecretValidationService.RequestHasProductionSecret()` is forced to `true`. Add tests for the `false` path and assert the repository denies / no-ops as expected. Today the auth gate is effectively untested.
- **Audit logging** - `IAuditLoggerService` is a Moq object that is never verified. Per AUTH-005 / AUTH-006, every save / read of session data should be auditable - assert the audit calls happen with the right operation + subject.
- **PermissionService outcomes** - The repo takes a real `PermissionService`. Add tests for both allow and deny outcomes.
- **Concurrent saves on the same callId** - Two writers updating the same session - last-write-wins or conditional update? Today only sequential overwrite is covered (test 4).
- **Multiple goals / multiple patients** - All multi-entity tests use exactly one. Confirm collections work past N=1 and that goal-to-patient resolution still works when several patients exist.
- **Delete / expire** - No coverage for deleting a session or for TTL-driven expiry, if applicable.
- **Cancellation** - All tests pass `CancellationToken.None`. Add at least one cancellation test to verify the repository honors the token.
- **Cross-callId isolation** - Writing session A must not affect session B (today implicit through random ids but never asserted).
- **Schema evolution** - Save with current code, load with a forward/backward variant of the DTO to confirm tolerant deserialization.

### Improvement Suggestions / Irrelevant Tests

- **No teardown** - Unlike `LockedActionServiceIntegrationTests`, this class never cleans up rows. Random ids prevent collisions but data accumulates across runs. Add a `[TearDown]` that deletes by call id, or run against an isolated table per test.
- **Heavy use of Moq for collaborators that should be fakes** - `IAuditLoggerService` and `IMetricRecorder` would be more useful as recording fakes (TEST-002), enabling the "audit logging" assertions called out above.
- **Assertions skip fields** - Many assertions use `options.ExcludingMissingMembers()`, which silently passes if a field is dropped. Tighten to full equivalence for the round-trip tests so missing-field regressions fail loudly.
- **`ZocdocAppointmentStatusType.Confirmed` etc. mixed with raw strings** - Mix of typed enums and string literals (`"call-abc"`, `"v1"`). Centralize test data builders for clearer intent.
- **`SaveAsync_WithAllFields_CanBeReadBack` is doing too much** - Fine as a smoke test, but its breadth makes failure diagnosis hard. Pair with narrower per-field tests for fast triage.
- **No negative path** - Every test is a happy-path round-trip. At least one malformed-input or repository-error test would round out coverage.

### Notes on What This Project Should Cover (per TEST-001 / TEST-002 / TEST-003)

`ZoTools.Web.IntegrationTests` is the right place for: full HTTP request pipeline (controller -> service -> repository -> fake/LocalStack), middleware chain (auth, logging, call-id enrichment), DI wire-up, persistence against a realistic store, and end-to-end agent tool endpoints invoked over HTTP. Today the project exists but only contains repository / service tests that do not exercise the web pipeline at all - consider either renaming to `ZoTools.Web.RepositoryIntegrationTests` or adding actual `WebApplicationFactory<>`-based tests that hit the controllers.

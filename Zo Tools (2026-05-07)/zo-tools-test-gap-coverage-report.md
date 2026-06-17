# Zo Tools — Test Gap Coverage Report

**Date:** 2026-06-17
**Scope:** All test layers (unit, web unit, web API, integration) for the Zo voice-agent tools server — 2,401 test cases across 118 spec files.
**Source:** Test mapping for [Zocdoc/zo-tools](https://github.com/Zocdoc/zo-tools), generated 2026-05-07. Mapping files: [Shift-Left-Phone-Bot-AI / Zo Tools (2026-05-07)](https://github.com/Melissa-Dsouza1-Zocdoc/Shift-Left-Phone-Bot-AI/tree/main/Zo%20Tools%20(2026-05-07))

> **Note on structure.** Unlike the single-project Interop Availability report, this suite spans four test projects and ~90 spec files. Sections are grouped by project/layer (Unit → Web Unit → Web API → Integration) rather than a flat 1–N list. Per-file blocks keep the same `Irrelevant Tests` / `Missing Tests` shape as the reference report. Priorities were assigned from the mapping's findings using the heuristic: **High** = auth/RBAC, PHI scrubbing, audit logging, idempotency/replay of mutations, data-integrity, missing core-mutation happy path, or 5xx/downstream-failure on a critical path; **Medium** = validation, boundary, edge, timezone, empty-result, non-critical error handling; **Low** = cosmetic, redundancy cleanup, naming, nice-to-haves.

---

## Executive Summary

| Metric | Zo Tools |
|--------|----------|
| Total tests analyzed | 2,401 |
| Relevant | **2,350 (97.9%)** |
| Irrelevant / Stale | 51 |
| High-priority missing gaps | 122 |
| Medium-priority missing gaps | 370 |
| Low-priority missing gaps | 86 |

### Coverage by layer

| Layer | Spec files | Tests |
|-------|-----------:|------:|
| Unit tests (`tests/ZoTools.UnitTests/`) | 57 | 1,446 |
| Web unit tests (`tests/ZoTools.Web.UnitTests/`) | 33 | 656 |
| Web API tests (`tests/ZoTools.Web.ApiTests/`) | 24 | 282 |
| Integration tests (`*.IntegrationTests/`) | 4 | 17 |

---

# 1. Unit Tests — `tests/ZoTools.UnitTests/`

## 1.1 Caching

### tests/ZoTools.UnitTests/Caching/CacheEntryTests.cs (1 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | CacheEntry_WithNullValue_StoresNull | Generic null reference values accepted or rejected deterministically | Medium |
| 2 | CacheEntry_WithDefaultDateTime_HandlesGracefully | `default(DateTime)` (MinValue) constructor contract | Medium |
| 3 | CacheEntry_WithNonUtcDateTime_BehaviorIsExplicit | Behavior when Local/Unspecified DateTime supplied to `ExpiresAtUtc` | Medium |
| 4 | CacheEntry immutability / record equality semantics | Assert immutability if the type is a record | Low |

### tests/ZoTools.UnitTests/Caching/CacheHeaderHelperTests.cs (5 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | SetCacheHeaders_WhenHitWithFractionalTtl_RoundsCorrectly | Deterministic rounding/truncation/flooring of fractional TTL | Medium |
| 2 | SetCacheHeaders_WhenHitWithZeroTtl_WritesZeroOrSkips | Edge case for immediately-expiring entry | Medium |
| 3 | SetCacheHeaders_WhenHitWithNegativeTtl_BehaviorIsExplicit | Negative TTL not silently leaked as header value | Medium |
| 4 | SetCacheHeaders_OverwritesPreviouslySetHeaders | Idempotency/overwrite semantics when headers already set | Medium |

### tests/ZoTools.UnitTests/Caching/CacheResultTests.cs (2 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | CacheResult_WithHitButNullTtl_AllowedOrRejected | Whether IsHit=true with null TTL is a valid combination | Medium |
| 2 | CacheResult_WithMissAndNonNullTtl_BehaviorIsExplicit | Whether IsHit=false with a TTL is legal | Medium |
| 3 | CacheResult_GenericTypeWorks_ForReferenceAndValueTypes | Generic contract with int/struct payloads | Low |
| 4 | CacheResult record equality semantics | Assert equality semantics if the record defines them | Low |

### tests/ZoTools.UnitTests/Caching/PhoneBotRepositoryCacheTests.cs (17 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Repository_ThrowsException_NotCachedAndPropagated | Repo failure is not cached and the exception bubbles up — data integrity | **High** |
| 2 | Cache key uniqueness on PracticeId / CallCenterId / IsNewPatient | All key components keyed independently (only locationId/phone exercised) | Medium |
| 3 | Concurrent_GetPracticeInfo_OnlyCallsRepositoryOnce | Thundering-herd safety on concurrent miss | Medium |
| 4 | TTL_Expiration_TriggersRefetch | TTL expiry triggers re-fetch (via injected clock) | Medium |
| 5 | Cancellation_TokenIsRespected | Cancelled token propagates OperationCanceledException without poisoning cache | Medium |
| 6 | NullPracticeId / NullPhoneNumber input validation | Defensive checks for invalid arguments | Medium |
| 7 | Repository_ReturnsNull_BehaviorIsExplicit | Whether nulls are cached or skipped | Medium |

### tests/ZoTools.UnitTests/Caching/SpecialityProceduresCacheTests.cs (20 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | MonolithService_ThrowsException_NotCached | Downstream failure does not poison the cache — data integrity | **High** |
| 2 | Concurrency_OnlyOneMonolithCall | Concurrent calls coalesce to a single monolith fetch (thundering herd) | Medium |
| 3 | MonolithService_ReturnsEmptyResponse_HandledGracefully | Defensive handling of empty response | Medium |
| 4 | TTL_Expiration_TriggersRefetch | TTL expiry triggers re-fetch (covered for PhoneBotRepositoryCache, missing here) | Medium |
| 5 | CancellationToken_IsHonored | OperationCanceledException propagates without corrupting cache | Medium |
| 6 | Cache-key uniqueness / invariance coverage | Cache key invariant per call and negative collision test | Low |

## 1.2 Helpers

### tests/ZoTools.UnitTests/Helpers/AppointmentModificationHelperTests.cs (77 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | DetermineCancelModificationBlockedReason_WhenMultipleHybridLocations_BlocksOrAllowsExplicitly | Positive assertion of when the `MultipleMappedHybridLocations` enum value is returned (only the "DoesNotBlock" path is tested) | Medium |
| 2 | TimezoneSensitivity | Cutoff math when appointment offset differs from clock offset (all tests use `Offset.Zero`) | Medium |
| 3 | DST_BoundaryAroundCutoff | Spring-forward / fall-back appointment for off-by-one-hour cutoff bugs | Medium |
| 4 | IsWithinCutoff_WithFractionalCutoff | Whether sub-hour cutoff rules are intentional given `cutoffInHours` is `int` | Low |
| 5 | VeryLargeCutoffOrFarFutureAppointment | No overflow when cutoff = `int.MaxValue` | Medium |
| 6 | EmptyProviderList vs NullProviderList parity | Empty providers list may behave differently than null (only null is tested) | Medium |

### tests/ZoTools.UnitTests/Helpers/LocationNameHelperTests.cs (14 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | ExpandAddressAbbreviations_AbbreviationInsideWord_DoesNotExpand | Word-boundary correctness (e.g. "Stephanie" not becoming "Streetphanie") | Medium |
| 2 | ExpandAddressAbbreviations_TrailingPunctuation | "123 Main St." (with period) still expands | Medium |
| 3 | ExpandStateAbbreviation_WithLeadingTrailingSpaces | Whitespace handling around state code | Medium |
| 4 | ExpandStateAbbreviation_NullInput | Null code behavior (throw vs return null) | Medium |
| 5 | ResolveLocationName_AllEmptyStrings | Returns empty, symmetric with all-null case | Medium |
| 6 | ResolveLocationName_AddressWithoutAbbreviations | No double-expansion or whitespace artifacts on already-expanded input | Low |

### tests/ZoTools.UnitTests/Helpers/TtsTimeFormatterTests.cs (80 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Midnight (00:00) handling in English/Spanish | Midnight formatting (only noon is currently tested) | Medium |
| 2 | 00:30, 12:30 grammar correctness | "twelve thirty AM/PM" specifically | Medium |
| 3 | Spanish minute words for 1-9 minutes | "y cinco" / "y nueve" (only "y treinta" is exercised) | Medium |
| 4 | Unsupported language code (e.g. "fr-FR") behavior | Whether it defaults to English or throws | Medium |
| 5 | Year boundary / leap-day formatting | Feb 29 leap year, Dec 31 -> Jan 1 | Medium |
| 6 | V2 cancel/reschedule preview tests | V2 preview coverage (V2 only has confirm tests for cancel/reschedule) | Medium |
| 7 | BuildBookingPreviewSpeak V2 | V2 booking preview (V2 confirm exists but no V2 preview) | Medium |
| 8 | EmptyTime string handling | "" handling for methods that handle null but not empty | Medium |
| 9 | HighSurrogate/EmojiInProviderName | Exotic characters in provider names not breaking exact-match assertions | Low |

### tests/ZoTools.UnitTests/Helpers/ValidationHelpersTests.cs (13 tests)

**Irrelevant Tests:**

| Test # | Test Name | Issue |
|--------|-----------|-------|
| 11 | SanitizePhoneNumber_UnicodeEscapedPlus_ReplacedWithPlus | Input and expected are both literal "+15551234567"; the test does not exercise a unicode-escaped sequence as its name claims |

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | IsValidEmail_VeryLongEmail | RFC 5321 254-char limit | Medium |
| 2 | IsValidEmail_PlusAddressing | `user+tag@example.com` common case | Medium |
| 3 | IsValidEmail_UnicodeDomain | IDN/punycode domains | Low |
| 4 | IsDateOfBirthValid_VeryOldDate | Upper bound (e.g. 150 years ago) | Medium |
| 5 | IsValidUsPhoneNumber_VanityNumber | "1-800-FLOWERS" explicitly invalid per matrix | Low |
| 6 | SanitizePhoneNumber_LeadingPlusBeforeSpaces | " +15551234567 " trimming | Medium |
| 7 | IsAnonymousNumber_MixedAnonymousString | Substring-match behavior ("Anonymous Caller" / "Caller Anonymous") with positive/negative cases | Medium |
| 8 | SanitizePhoneNumber_UnicodeEscapedPlus (rewrite) | Rewrite the existing test to actually feed a unicode-escaped plus sequence | Medium |

## 1.3 Repository

### tests/ZoTools.UnitTests/Repository/ZoCallSessionRepositoryTests.cs (84 tests)

**Irrelevant Tests:**

| Test # | Test Name | Issue |
|--------|-----------|-------|
| 13 | GetByCallIdAsync_WhenAuthorized_LogsRead | Asserts literal `LogCallSessionRead(CallId, true)`; tests implementation, not behavior — prefer in-memory audit collector |
| 1-9 | Create_* (factory tests) | These test `ZoCallSession.Create` factory, not the repository; belong in `ZoCallSessionTests.cs` |

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Save_FailsAtDynamo_DoesNotEmitAuditWrite | Audit must not log a successful write when Dynamo throws — audit integrity | **High** |
| 2 | GetByCallIdAsync_WhenAuditFails_DoesNotMaskOriginalException | Audit-logging failure does not swallow/mask the call result | **High** |
| 3 | Save_FailsAtDynamo_PropagatesException | Dynamo exception bubbles up from SaveCallSession on a mutation path | **High** |
| 4 | Concurrent_GetByCallIdAsync_DoesNotCorruptCache | Thread safety when DynamoDB ops interleave — data integrity | **High** |
| 5 | CancellationToken_PropagatesToDynamoCalls | CT forwarded to LoadAsync/SaveAsync | Medium |
| 6 | UpsertAppointments_RescheduleParams_PreservedOnAppendAndUpdate | End-to-end roundtrip of RescheduleParams via Upsert + Find | Medium |
| 7 | UpsertSearchedPatientsAsync_WhenSomeFieldsAreNull_PreservesNonNullFields | Partial-null upsert field preservation | Medium |
| 8 | MergeCachedAppointments_WhenBothEmpty_ReturnsEmpty | Both-empty merge case (only null+empty covered) | Low |
| 9 | AddPatient_NullPatient_Behavior | Behavior when null patient is passed | Medium |
| 10 | Inject mocked PermissionService | Isolate repo from real PermissionService internals (CS-021) | Medium |

## 1.4 Service — Booking

### tests/ZoTools.UnitTests/Service/AppointmentInfoServiceTests.cs (85 tests)

**Irrelevant Tests:**

| Test # | Test Name | Issue |
|--------|-----------|-------|
| 40 | ResolveLocationInfo_WithPartialResolution_OneResolves_NotHybrid | Tests implementation rather than user-visible behavior; should be renamed to capture the expected outcome. |
| 68 | BuildAppointmentDetails_DoesNotCallGetProviderProcedureDurations | Coupling-assertion; breaking it is acceptable if the strategy changes. |

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Concurrency: parallel BuildAppointmentDetails calls | Many parallel calls share GQL/cache safely | Medium |
| 2 | DST boundary appointment local time | Boundary appointment produces correct local time (TZ integration check) | Medium |
| 3 | BuildAppointmentDetails with malformed start time string | Robust handling of unparseable start time | Medium |
| 4 | RegenerateRescheduleParams with null vs empty ZoMapped procedure ids | Distinguishes null vs empty handling | Medium |

### tests/ZoTools.UnitTests/Service/AppointmentManagementServiceTests.cs (16 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | CancelAppointment with test (non-production) secret path | Cancel behavior under non-prod secret/auth path | **High** |
| 2 | CancelAppointment when ConsentRefused | Parity with SmsService consent treatment | Medium |
| 3 | CancelAppointment when audit logger throws | Whether cancel still proceeds when audit fails | **High** |
| 4 | CancellationToken propagation through CancelAppointment | Token honored on cancel mutation | Medium |
| 5 | ValidateReschedule with multiple violations | Priority/ordering when several rules fail | Medium |

### tests/ZoTools.UnitTests/Service/AvailabilityServiceTests.cs (13 tests)

**Irrelevant Tests:**

| Test # | Test Name | Issue |
|--------|-----------|-------|
| 7 | SearchAvailability_ConstructsCorrectProviderLocationIds | Implementation-coupled; should assert behavior rather than internal construction. |

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Cancellation handling | Cancellation token honored during search | Medium |
| 2 | Very large date range | Boundary behavior on wide date window | Medium |
| 3 | Timezone-aware sorting across DST transition | Correct ordering when slots cross DST | Medium |
| 4 | Interaction with feature flags (e.g., NewAvailabilityStrategy) | Behavior under feature-flag variations | Medium |

### tests/ZoTools.UnitTests/Service/AvailabilityServiceGetNextAvailableTests.cs (8 tests)

**Irrelevant Tests:**

| Test # | Test Name | Issue |
|--------|-----------|-------|
| 5 | GetNextAvailable_ConstructsCorrectProviderLocationIds | Implementation-coupled (same concern as AvailabilityServiceTests). |

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Cancellation | Cancellation token honored | Medium |
| 2 | Behavior when GQL throws | Downstream failure handling on search path | Medium |
| 3 | Logging assertions parity with SearchAvailability | Consistent log emission across search methods | Low |

### tests/ZoTools.UnitTests/Service/BookingServiceTests.cs (69 tests)

**Irrelevant Tests:**

| Test # | Test Name | Issue |
|--------|-----------|-------|
| 67 | ExecuteBooking_WhenValidationThrows_ProceedsWithBooking | Silently tolerating validation exceptions is a questionable decision; flag for review rather than lock in. |
| 33 | ExecuteBooking_UnauthenticatedPatient_NullGender_DefaultsToFemale | Surprising default (gender bias); flag for product review. |

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Concurrency: two simultaneous bookings for the same slot | Whether one booking correctly fails (data-integrity) | **High** |
| 2 | LockedActionService coverage parity with reschedule | Booking does not appear to lock; verify lock on mutation | **High** |
| 3 | Audit logging parity with cancel/reschedule | Booking emits audit log for the mutation | **High** |
| 4 | CancellationToken propagation | Token honored through ExecuteBooking | Medium |
| 5 | Booking succeeds but PhoneBot save throws repeatedly | Retry/metric behavior on persistent save failure | Medium |

### tests/ZoTools.UnitTests/Service/InteropPlatformBookingTests.cs (9 tests)

**Irrelevant Tests:**

| Test # | Test Name | Issue |
|--------|-----------|-------|
| 9 | BookAppointment_PassesParametersInCorrectOrderToClient | Implementation-coupled; should use named-argument assertions instead of positional order. |

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Cancellation token honored | Token propagated to client call | Medium |
| 2 | Retry policy (if any) | Retry behavior on transient failures | Medium |
| 3 | Logging on each error reason | Log emission per mapped error reason | Low |

### tests/ZoTools.UnitTests/Service/IsNewPatientServiceTests.cs (37 tests)

**Irrelevant Tests:**

| Test # | Test Name | Issue |
|--------|-----------|-------|
| 6 | DetermineIsNewPatientForReschedules_ForPractice_AllStatusValues_DoNotThrow | Weak assertion; should assert explicit expected outcome per status value. |
| 8 | DetermineIsNewPatientForReschedules_AllDefinitionValues_DoNotThrow | Weak assertion; should assert explicit expected outcome per definition value. |
| 30 | DetermineIsNewPatientForVisitReason_AllDefinitionValues_DoNotThrow | Weak assertion; should assert explicit expected outcome per definition value. |

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Caching: cache miss vs hit on procedure-specialty lookup | Correct behavior on cache miss/hit | Medium |
| 2 | DST/timezone-sensitive midnight-boundary appointment | Correct past/future classification at local-day boundary | Medium |
| 3 | Self-exclusion when same id appears twice in input | Duplicate-id handling for self-exclusion | Medium |
| 4 | Concurrency safety | Safe concurrent determination calls | Medium |
| 5 | Metric tag content for forSpecialty path | Asserts tag values, not just emission count | Low |

### tests/ZoTools.UnitTests/Service/RescheduleAppointmentServiceTests.cs (70 tests)

**Irrelevant Tests:**

| Test # | Test Name | Issue |
|--------|-----------|-------|
| 53 | Confirm_DifferentStartTimes_UseDifferentLockKeys | Implementation-coupled; should assert key shape rather than inequality of two calls. |

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Audit log payload contents | Asserts payload shape, not just that audit is called | **High** |
| 2 | Permission check disabled flag path | Authz behavior when permission check is disabled | **High** |
| 3 | Concurrent reschedule attempts for same appt | Data-integrity under concurrent reschedule | **High** |
| 4 | DST transition during reschedule (cutoff math) | Correct cutoff calculation across DST | Medium |
| 5 | Interop returns success but external id missing | Handling of success response lacking external id | Medium |
| 6 | Lock contention metric | Metric emitted on lock contention | Low |

### tests/ZoTools.UnitTests/Service/RescheduleParamServiceTests.cs (31 tests)

**Irrelevant Tests:**

| Test # | Test Name | Issue |
|--------|-----------|-------|
| 22 | ResolveZdProcedure_AlwaysReturnsNullDurationInMinutes | "Always" assertion is brittle to intentional extension; rename to state why duration is null. |

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Multi-procedure-match logging assertion | Silent first-match is dangerous; verify it logs | Medium |
| 2 | Hybrid + UseDuration combination | Behavior of Hybrid location with duration strategy | Medium |
| 3 | Non-Hybrid location types with duration override | Duration override across non-Hybrid types | Medium |
| 4 | Zero/negative duration value in UseDuration | Boundary handling of invalid duration | Medium |
| 5 | Inactive procedure in catalog vs caching staleness | Stale-cache vs inactive-procedure distinction | Medium |

## 1.5 Service — Insurance

### tests/ZoTools.UnitTests/Service/InsuranceAcceptanceServiceTests.cs (17 tests)

**Irrelevant Tests:**

| Test # | Test Name | Issue |
|--------|-----------|-------|
| 7 | ComputeAcceptance_NoProviderLocations_DefaultsToNoWithoutCallingGql | Couples to implementation detail (GQL call count); acceptable as a perf guarantee but should be tagged as such. |

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Behavior with extremely large plan lists | Boundary/pagination handling | Medium |
| 2 | Concurrency: same plan asked simultaneously by two callers | Safe concurrent acceptance computation | Medium |
| 3 | Cancellation token honored on long GQL call | Token propagation on long-running call | Medium |
| 4 | Metric/log emission per acceptance status outcome | Observability for each Yes/No/UsuallyAccepted outcome | Low |
| 5 | Boundary: single accept among many | Explicit boundary for Yes/No/UsuallyAccepted bucketing | Medium |

### tests/ZoTools.UnitTests/Service/InsuranceCarrierGqlTests.cs (1 tests)

**Irrelevant Tests:**

| Test # | Test Name | Issue |
|--------|-----------|-------|
| 1 | InsuranceCarrierInfo_WhenConstructed_HasExpectedProperties | Pure constructor smoke test; candidate for deletion unless the GQL contract requires a guaranteed shape. |

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | None — DTO smoke is sufficient | No high-value gaps identified | Low |

### tests/ZoTools.UnitTests/Service/InsuranceCatalogServiceTests.cs (24 tests)

**Irrelevant Tests:**

| Test # | Test Name | Issue |
|--------|-----------|-------|
| 3 | AllCarriers_ContainsAtLeastOneHealthAndOneDentalCarrier | Brittle to data; should assert on the category set rather than a count. |
| 1 | AllPlans_LoadsFromEmbeddedResource | Only checks non-empty; should pin to a known plan id or count for a stronger contract. |

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Catalog loading failure (invalid JSON / missing resource) | Negative path for embedded resource loading | Medium |
| 2 | Idempotency: AllCarriers twice returns same instance | Singleton/caching guarantee | Medium |
| 3 | Orphan plan (carrier in plans but not in carriers) | Whether orphan plans are filtered or surfaced | Medium |
| 4 | BCBS lookup returns multiple carriers per state | Multi-carrier BCBS state results | Medium |
| 5 | ProcessName with very long / unicode / accented names | Robust name canonicalization | Medium |

### tests/ZoTools.UnitTests/Service/InsuranceSearchServiceTests.cs (39 tests)

**Irrelevant Tests:**

| Test # | Test Name | Issue |
|--------|-----------|-------|
| 20 | Search_WhenCarrierIdProvided_AndStateMatches_ReturnsCarrier | Carrier-id precedence is asserted across multiple tests; consider consolidating overlapping cases. |
| 24 | Search_WhenInvalidStateCode_IgnoresFilterAndLogsError | Should additionally assert the metric/tag, not only the log. |

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Cancellation/timeout for the acceptance call portion | Token/timeout handling on acceptance enrichment | Medium |
| 2 | Phonetic threshold boundary (just over / just under) | Boundary behavior of phonetic match cutoff | Medium |
| 3 | Combined filter: plan + state + dental practice | 3-way intersection of filters | Medium |
| 4 | Carrier-id with plan claim not belonging to that carrier | Mismatched plan/carrier handling | Medium |
| 5 | Logging of metric tags for disambiguation outcomes | Observability for state/plan disambiguation | Low |

## 1.6 Service — Other

### tests/ZoTools.UnitTests/Service/AbServiceTests.cs (38 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Cache fallback when AB service is unreachable | Behavior on transient downstream failure or stale-cache fallback when AB lookups fail. | **High** |
| 2 | Concurrent flag flips | What happens when a flag is read while being invalidated. | Medium |
| 3 | Telemetry on flag misses | An unknown flag emits a metric (helps detect typos/dead flags). | Low |

### tests/ZoTools.UnitTests/Service/AuditLoggerServiceTests.cs (34 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Failure to write audit log path | Deterministic behavior (drop/rethrow/buffer) when the audit sink throws, per AUTH-005. | **High** |
| 2 | PII/PHI redaction in payloads | PHI fields in audit payloads are scrubbed before persistence. | **High** |
| 3 | Async fire-and-forget shutdown | In-flight audit requests are handled correctly during application shutdown. | Medium |

### tests/ZoTools.UnitTests/Service/CsatServiceTests.cs (16 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Duplicate submissions for same call | Idempotency or update-vs-insert semantics for repeated submissions. | **High** |
| 2 | Free-text comment with PHI | Comments are stored without inadvertent PHI extraction. | **High** |
| 3 | Out-of-range scores | Handling of scores below min or above max bounds. | Medium |

### tests/ZoTools.UnitTests/Service/GenerateIdServiceTests.cs (5 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Collision behavior | Collision retry when the random source repeats (mock the RNG). | **High** |
| 2 | ID format validation | Produced ID matches the documented format/length/charset. | Medium |

### tests/ZoTools.UnitTests/Service/InteropPlatformServiceTests.cs (18 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | HTTP 5xx / timeout from interop platform | Behavior under downstream failure modes on a critical path. | **High** |
| 2 | Auth token expiry mid-request | Token refresh flow when auth expires during a request. | **High** |
| 3 | Malformed response payload | Handling of partial JSON / unexpected schema. | Medium |

### tests/ZoTools.UnitTests/Service/LocationDataProviderTests.cs (7 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Empty / unknown location id | Behavior for a non-existent location id. | Medium |
| 2 | Caching behavior | Cache invalidation or TTL handling if the provider caches. | Medium |

### tests/ZoTools.UnitTests/Service/LocationSearchServiceSearchLocationsTests.cs (52 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Geographic edge cases | International zip codes, military APO/FPO addresses, or PR/territories. | Medium |
| 2 | Pagination | Cursor exhaustion or page-size boundary handling. | Medium |
| 3 | Sort stability | Ties between equally-ranked results resolve deterministically. | Low |

### tests/ZoTools.UnitTests/Service/LocationSearchServiceTests.cs (17 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Synonym/alias handling | 'NYC' resolves to New York-area locations. | Medium |
| 2 | Case-insensitive matching across special characters | Matching with diacritics. | Medium |

### tests/ZoTools.UnitTests/Service/LockedActionServiceTests.cs (7 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Lock not released on action throw with finalizer disabled | Lock TTL still expires when the action throws. | **High** |
| 2 | Concurrent contenders queueing fairness | FIFO/LIFO ordering of waiters. | Medium |
| 3 | Lock metadata (caller id, reason) | Lock metadata is preserved for audit. | Medium |

### tests/ZoTools.UnitTests/Service/MonolithApiServiceTests.cs (11 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Auth header propagation | User context tokens flow correctly to the monolith. | **High** |
| 2 | Retry policy | Transient 503s trigger documented retry/backoff. | Medium |
| 3 | Non-2xx response body propagation | Error bodies surface to caller for diagnostics. | Medium |

### tests/ZoTools.UnitTests/Service/NonPhiGqlServiceAvailabilityTests.cs (16 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Empty availability response | Empty slots result in an empty (not null) collection. | Medium |
| 2 | Cross-day boundary slots | Slots that span midnight in patient TZ. | Medium |

### tests/ZoTools.UnitTests/Service/NonPhiGqlServiceNextAvailableTests.cs (9 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | No availability anywhere | The no-results-found response shape. | Medium |
| 2 | DST boundary | Next-available behavior during the DST week (known risk per SQUAWK-6090). | Medium |

### tests/ZoTools.UnitTests/Service/NonPhiGqlServicePracticeProvidersTests.cs (4 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Inactive / archived providers filter | Disabled providers are excluded. | Medium |
| 2 | Practice with no providers | Empty-practice case. | Medium |

### tests/ZoTools.UnitTests/Service/NonPhiGqlServiceProcedureDurationsTests.cs (16 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Negative or zero duration in response | Service rejects/normalizes invalid durations. | Medium |
| 2 | Procedure with multiple location overrides | Per-location duration variants. | Medium |

### tests/ZoTools.UnitTests/Service/NonPhiGqlServiceProviderProceduresTests.cs (6 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Provider with empty procedure set | Provider with no listed procedures. | Medium |
| 2 | Procedure not in catalog | Filtering or fallback when GQL returns an unknown procedure id. | Medium |

### tests/ZoTools.UnitTests/Service/NonPhiGqlServiceTests.cs (18 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | GraphQL partial-result responses | A query that returns both data and errors arrays. | Medium |
| 2 | Schema introspection failure | Behavior when schema is missing the expected field. | Medium |
| 3 | Query timeout / cancellation | Client-side cancellation of in-flight queries. | Medium |

### tests/ZoTools.UnitTests/Service/PatientInfoServiceTests.cs (60 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Patient with multiple matching records | Deduplication of multiple matching patient records. | **High** |
| 2 | Special characters in name fields | Handling of apostrophes, hyphens, accents. | Medium |
| 3 | Date of birth normalization | Various input formats (MM/DD/YYYY vs ISO vs Unix). | Medium |

### tests/ZoTools.UnitTests/Service/PermissionServiceTests.cs (7 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | AUTH-002 operation-on-domain coverage | All roles tested against all guarded operations. | **High** |
| 2 | Missing/expired permission claims | Rejection of expired JWT permissions. | **High** |
| 3 | Role bundle changes mid-session | Behavior when a user's role changes while a request is in flight. | **High** |

### tests/ZoTools.UnitTests/Service/PhoneBotRepositoryTests.cs (17 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | DDB throttling / backoff | Handling of ProvisionedThroughputExceededException. | Medium |
| 2 | TTL expiry | Expired entries are excluded from reads. | Medium |
| 3 | Batch operations | Batch read/write boundary behavior. | Medium |

### tests/ZoTools.UnitTests/Service/PhoneNumberServiceTests.cs (23 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | International numbers (E.164 with country code) | Parsing of non-US numbers. | Medium |
| 2 | Vanity numbers (1-800-FLOWERS) | Letter-to-digit conversion if supported. | Low |
| 3 | Numbers with extensions | Parsing of 'x123' or 'ext. 123' suffixes. | Medium |

### tests/ZoTools.UnitTests/Service/PhoneticMatchingServiceTests.cs (11 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Asian/Indian name patterns | Matching of non-Latin transliterations. | Medium |
| 2 | Threshold tuning | The matching threshold is honored at the boundary. | Medium |
| 3 | Hyphenated/double surnames | Matching of two-part names. | Medium |

### tests/ZoTools.UnitTests/Service/PracticeProceduresGqlServiceTests.cs (9 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | GQL error array | Partial-error GraphQL responses. | Medium |
| 2 | Practice with no procedures | Empty-practice case. | Medium |

### tests/ZoTools.UnitTests/Service/ProviderHelpersTests.cs (38 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Helpers under hostile inputs | Null/empty/whitespace inputs where helpers may NPE. | Medium |
| 2 | Locale-aware capitalization | 'McDonald' vs 'mcdonald' canonicalization. | Low |

### tests/ZoTools.UnitTests/Service/ProviderSearchServiceTests.cs (49 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Provider matching across multiple practices | Deduplication of a provider appearing in multiple practices. | Medium |
| 2 | Specialty filter intersection | AND vs OR semantics with multiple specialties. | Medium |
| 3 | Search with no matches | Empty-result branding/messaging. | Medium |

### tests/ZoTools.UnitTests/Service/SecretValidationServiceTests.cs (9 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Time-of-check-to-time-of-use | Revoked secrets are rejected even if cached. | **High** |
| 2 | Constant-time comparison | Comparison is timing-safe (review item, hard to test directly). | **High** |

### tests/ZoTools.UnitTests/Service/SmsServiceTests.cs (60 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Opt-out (STOP) flow | Subsequent sends after opt-out are blocked. | **High** |
| 2 | Carrier error responses | Handling of Twilio carrier-level rejection codes (delivery failed, blocked). | **High** |
| 3 | Throttling/rate-limit | Back-pressure behavior when over limit. | Medium |

### tests/ZoTools.UnitTests/Service/SpecialtyGroupingServiceTests.cs (10 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Specialty with no group | Handling of orphan specialties. | Medium |
| 2 | Cross-group ambiguity | A specialty that matches multiple groups. | Medium |

### tests/ZoTools.UnitTests/Service/StateResolverTests.cs (11 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Territories/non-state codes | Handling of PR, GU, VI, AS, DC. | Medium |
| 2 | Misspelled state name | Fuzzy fallback or rejection. | Medium |

### tests/ZoTools.UnitTests/Service/TimezoneServiceTests.cs (25 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | DST spring-forward / fall-back boundaries | Conversions over the DST hour gap (critical per SQUAWK-6090). | **High** |
| 2 | IANA tz updates | Behavior simulating a tz database update (e.g., Brazil DST removal). | Medium |
| 3 | Locations with no tz mapping | Fallback behavior when no tz mapping exists. | Medium |

### tests/ZoTools.UnitTests/Service/TwilioServiceTests.cs (12 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Webhook signature validation | Incoming Twilio webhooks are signature-verified. | **High** |
| 2 | Idempotency keys on outbound | Duplicate-send protection on outbound messages. | **High** |

### tests/ZoTools.UnitTests/Service/ValidationServiceTests.cs (60 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Cross-field validation | Rules that span multiple fields (e.g., DOB + age consistency). | Medium |
| 2 | Localized validation messages | Messages match the i18n contract. | Low |

### tests/ZoTools.UnitTests/Service/VisitReasonLlmMatchServiceTests.cs (6 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | LLM 5xx / timeout | Behavior when the LLM call fails on a core matching path. | **High** |
| 2 | Prompt injection defenses | User-supplied free-text cannot exfiltrate prompt content. | **High** |
| 3 | Confidence threshold | Low-confidence matches are rejected. | Medium |

### tests/ZoTools.UnitTests/Service/VisitReasonSearchServiceTests.cs (26 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Spell-check / typo tolerance | 'sor throat' resolves to 'sore throat'. | Medium |
| 2 | Homonyms | Visit reasons that share names across specialties. | Medium |

### tests/ZoTools.UnitTests/Service/ZipCodeServiceTests.cs (7 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | 5/9 vs 9-digit zips | ZIP+4 normalization. | Medium |
| 2 | Canadian postal codes | Behavior with non-US format inputs. | Medium |

---

# 2. Web Unit Tests — `tests/ZoTools.Web.UnitTests/`

## 2.1 AgentTools impls

### tests/ZoTools.Web.UnitTests/GreetingAgentToolsImplTests.cs (3 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Unknown call-center number not in mapping | Whether an unmapped number returns 404 or 200-with-empty (currently undefined); entry point misroutes caller | Medium |
| 2 | Null input argument validation | Behavior when null call-center number is passed | Medium |
| 3 | Non-E.164 input (`123`, `abc`) | Validation behavior for malformed numbers | Medium |
| 4 | Missing-number is logged for observability | Whether a missing lookup emits a log line for debugging | Low |

### tests/ZoTools.Web.UnitTests/ManageAppointmentAgentToolsImplTests.cs (60 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | GetAppointments PHI redaction of unneeded fields | Agent does not receive PHI (e.g., insurance member id) it doesn't need; schema-level protection | **High** |
| 2 | GetAppointments returns appointments sorted (start time ascending) | Sort order of returned appointments (not currently asserted) | Medium |
| 3 | CancelAppointment emits structured log/observability event on blocked reason | Observability event when a cancel is blocked, to aid QA debugging | Medium |
| 4 | CancelAppointment Spanish-language / i18n path | This tool impl's localization behavior (only legacy controller is covered) | Medium |
| 5 | MapStatus lenient invalid-value behavior pinned with rationale | Reconcile/pin the inconsistency where MapStatus returns None but other mappers throw | Low |
| 6 | FilterUpcomingAndRecentPrevious boundary at start time == "now" | Whether an appointment exactly at now is included or excluded | Medium |
| 7 | FilterUpcomingAndRecentPrevious very large previousCount (int.MaxValue) | Performance / exception behavior at extreme count | Low |

### tests/ZoTools.Web.UnitTests/SelectLocationAgentToolsImplTests.cs (18 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Service throws (transient HTTP / 500) | Whether downstream failure is swallowed or propagated (untested) | **High** |
| 2 | Empty input arrays (e.g., empty zip code) | Validation path for empty inputs | Medium |
| 3 | FindLocationsByName forwards search term verbatim | Parameter forwarded with no client-side trimming so service owns fuzzy/case-insensitive matching | Medium |
| 4 | SearchLocations with both filters (zip + name) | Precedence behavior when both filters supplied | Medium |
| 5 | Cancellation token forwarding to service | Latency/timeout handling via CT forwarding | Medium |
| 6 | Logging on enum-mapping fallthrough | Log line emitted on unknown enum values to aid debugging | Low |

### tests/ZoTools.Web.UnitTests/SelectProviderAgentToolsImplTests.cs (11 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Service throws | Exception propagation or wrapping on downstream failure | **High** |
| 2 | Pagination / large-result truncation | Whether impl caps N results for LLM context budget | Medium |
| 3 | Provider with empty-string pronunciation | Whether empty pronunciation is null-coalesced or returned verbatim | Low |
| 4 | Locations sorted (e.g., by distance) | Sort order of locations (not asserted) | Medium |
| 5 | Description / boilerplate fields localized | i18n behavior of response fields | Medium |
| 6 | Error directive includes transferReason or similar | Shape of the error directive is not pinned | Medium |

### tests/ZoTools.Web.UnitTests/SelectTimeslotAgentToolsImplTests.cs (51 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Boundary at 49 timeslots | Truncation boundary just below the cap (only 50 and >50 tested) | Medium |
| 2 | Truncation directive exact text snapshot | LLM prompt instruction wording locked to prevent silent UX degradation | Medium |
| 3 | Cancellation token forwarding to availability service | CT passed through on async search | Medium |
| 4 | Time zone correctness across DST / Accept-Language mismatch | Search window correctness around DST and locale | Medium |
| 5 | AvailabilityNotFound event includes attempted search window | Event payload shape (not asserted) | Medium |
| 6 | Concurrent reschedule path: session nulled mid-call (race) | Data-integrity when appt cached but session nulled during call | **High** |
| 7 | SearchAvailability direct params + patientId for new patient | Mirror of existing-patient case for new patients (missing) | Medium |
| 8 | Direct path with duration_override | Override only tested in reschedule branch, not direct path | Medium |

### tests/ZoTools.Web.UnitTests/SelectVisitReasonAgentToolsImplTests.cs (18 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | 400 responses include structured error code/reason | Shape of validation/auth failure responses (#9, #12, #16) not asserted | Medium |
| 2 | Auth-style validations emit structured log/observability event | Failed auth-style calls are observable for QA debugging | **High** |
| 3 | Service throws transient exception | Propagation behavior on downstream failure | Medium |
| 4 | Missing callId behavior | Required call ID enforcement (not tested) | Medium |
| 5 | Generated-patientId format check in isolation | The GUID-prefix/format check that gates new-patient auth | **High** |

## 2.2 Providers

### tests/ZoTools.Web.UnitTests/Providers/AgentToolsRegistryTests.cs (5 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | GetProvider_AdjacentEnumValue_ReturnsNull | Strict/case-sensitive lookup on AgentName enum (negative lookup with different enum value) | Medium |
| 2 | Constructor_NullProviderEntry_Behavior | Behavior when providers list contains a null entry (likely NREs today) | Medium |
| 3 | GetProvider_ConcurrentReads_AreSafe | Thread-safe concurrent reads (built-once, consumed from many ASP.NET threads) | Medium |
| 4 | AllDiRegisteredProviders_ReachableViaRegistry | Integration-style test asserting every DI-registered `IAgentToolsProvider` is reachable (catches missing DI registration) | Medium |

### tests/ZoTools.Web.UnitTests/Providers/ToolDefinitionBuilderTests.cs (7 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Build_WithoutName_ValidatesNameRequired | Name-required validation if a parameterless ctor exists | Medium |
| 2 | WithParameter_DuplicateName_LastWriteOrThrows | Behavior when same parameter name added twice (undefined today) | Medium |
| 3 | WithMethod_Lowercase_NormalizationBehavior | Whether "post" is normalized to "POST" or preserved (routing depends on it) | Medium |
| 4 | WithMethod_InvalidVerb_ValidatedOrAccepted | Whether an invalid HTTP verb ("BREW") is validated | Medium |
| 5 | WithEndpoint_ValidatesPlaceholderSegments | `:placeholder` segments are syntactically valid / non-empty | Medium |
| 6 | WithReturns_CalledTwice_LastWriteWins | Last-write-wins on repeated `WithReturns` | Low |
| 7 | Build_IsIdempotent | Calling Build() twice yields equivalent/shared objects (matters for cached schema responses) | Medium |

### tests/ZoTools.Web.UnitTests/Providers/AuthenticatePatientAgentToolsProviderTests.cs (3 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Future tool coverage (TODO) | When tools are added, port the required-params/returns-schema/description tests from peer providers | Low |
| 2 | Provider_IsDiscoverableViaAgentToolsRegistry | Provider reachable via registry, for symmetry with peers | Low |

### tests/ZoTools.Web.UnitTests/Providers/GreetingAgentToolsProviderTests.cs (11 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Version_ReturnsInitialVersion | Version = "1.0.0" (inconsistent with peers that test it) | Low |
| 2 | GetPracticeFaq_TopicEnumValues | Description documents valid `topic` enum values | Medium |
| 3 | GetTransferNumber_FullEndpointUrl | Pin the full endpoint URL instead of `Contain("get_transfer_number")` | Medium |
| 4 | Provider_IsDiscoverableViaAgentToolsRegistry | Provider reachable via registry, for symmetry | Low |
| 5 | GetTools_AllToolsHaveNoSideEffect | Greeting agent must not expose side-effecting tools (`All(t => !t.HasSideEffect)`) | **High** |

### tests/ZoTools.Web.UnitTests/Providers/ManageAppointmentAgentToolsProviderTests.cs (8 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | CancelAppointment_FullEndpointUrl | Lock down the full endpoint path (only `Contain` checked today) | Medium |
| 2 | CancelAppointment_DescriptionSnapshot | Snapshot the cancellation prompt description (wording change alters bot behavior) | Medium |
| 3 | CancelAppointment_DescriptionMentionsConfirmation | Description mentions previewing/confirming (biphasic side-effecting tool) | **High** |
| 4 | Provider_IsDiscoverableViaAgentToolsRegistry | Provider reachable via registry, for symmetry | Low |
| 5 | CancelAppointment_AppointmentIdIsStringType | `appointment_id` schema type is `string` (not integer), matching downstream impl | Medium |

### tests/ZoTools.Web.UnitTests/Providers/SelectLocationAgentToolsProviderTests.cs (12 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | PerTool_DescriptionSnapshot | Verify/snapshot each tool's description (the prompt copy the LLM sees) | Medium |
| 2 | FindLocationsNearZip_RadiusMilesOptionalParam | `radius_miles` (or equivalent) optional parameter is present (properties not asserted) | Medium |
| 3 | FindLocationsInState_StateFormatEnum | `state` is two-letter USA code, documented and tested | Medium |
| 4 | FindLocationsByName_LocationQueryPartialMatch | `location_query` description mentions partial-match behavior | Low |
| 5 | GetTools_AllToolsHaveNoSideEffect | No tool has `HasSideEffect=true` (`All(t => !t.HasSideEffect)`) | **High** |
| 6 | Provider_IsDiscoverableViaAgentToolsRegistry | Provider reachable via registry, for symmetry | Low |

### tests/ZoTools.Web.UnitTests/Providers/UnifiedManageAppointmentAgentToolsProviderTests.cs (16 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Version_ReturnsInitialVersion | Version tested (peers test it; this provider does not) | Low |
| 2 | UpdateRescheduleLocation_LocationTypeEnumValues | Schema documents supported `location_type` values (PhysicalOnly/VirtualOnly/Hybrid; impl rejects Hybrid/Unknown) | Medium |
| 3 | SearchAvailability_TimeFilterEnumValues | `time_filter` enum values (Morning/Afternoon/Evening/Anytime) present and tested | Medium |
| 4 | GetTools_ExactlyOneSideEffectingTool | Only `update-reschedule-location` is side-effecting (`Count(t => t.HasSideEffect) == 1`) | **High** |
| 5 | Provider_IsDiscoverableViaAgentToolsRegistry | Provider reachable via registry, for symmetry | Low |
| 6 | GetTools_DoesNotExposeCancelAppointment | Unified provider must not expose `cancel_appointment` (lives on legacy provider) | **High** |

### tests/ZoTools.Web.UnitTests/Providers/UnifiedNewAppointmentAgentToolsProviderTests.cs (48 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Version_ReturnsInitialVersion | Version tested (peers test it; this provider does not) | Low |
| 2 | SearchInsurances_MemberIdClaim_PerParamDescriptionPermutations | `user_member_id_claim` per-param description permutations for parity with carrier/plan | Medium |
| 3 | SearchAvailability_TimeFilterEnumValues | `time_filter` schema matches `SearchAvailabilityTimeFilter` enum | Medium |
| 4 | SearchVisitReasons_IsNewPatientIsBoolean | `is_new_patient` schema is type=boolean | Medium |
| 5 | SearchLocations_RadiusMilesDefaultAndRange | `radius_miles` sensible default and bounded range in schema/description | Medium |
| 6 | SearchInsurances_UsaStateCodeFormat | `usa_state_code` description calls out two-letter code format | Low |
| 7 | GetTools_AllToolsHaveNoSideEffect | Zero side-effecting tools (`All(t => !t.HasSideEffect)`) | **High** |
| 8 | SearchInsurances_DescriptionSnapshot | Verify/snapshot full `search_insurances` description across practice-config matrix (currently brittle piecewise `Contain`) | Medium |

## 2.3 Converters

### tests/ZoTools.Web.UnitTests/Converters/ToolParametersJsonConverterTests.cs (6 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | CanRead / ReadJson behavior | Deserialization side is unsupported (or tested if supported) | Medium |
| 2 | CanConvert returns true for ToolParameters, false otherwise | Type-matching of the converter | Low |
| 3 | Stable property ordering (type → properties → required) | OpenAI contract stability not regressed | Medium |
| 4 | camelCase/lowercase property names regardless of serializer naming strategy | Matches OpenAI schema spec keys | Medium |
| 5 | Nested JObject value written by reference | No double-encoding to a string | Medium |
| 6 | RequiredParams key not present in Properties | Currently silently allowed — decide and lock down | Medium |
| 7 | Duplicate keys in RequiredParams | Behavior with duplicate required keys | Low |

## 2.4 Other (impls, middleware, schema/serialization)

### tests/ZoTools.Web.UnitTests/AbImplTests.cs (19 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Cancellation token forwarding to IAbService | Async methods propagate the caller's CancellationToken to the flag service | Medium |
| 2 | Empty/null flagName validation | Behavior when flagName is null or empty | Medium |
| 3 | Empty/null visitorId component | Whether call short-circuits or proceeds when e.g. practiceId is empty for PracticeId visitor type | Medium |
| 4 | Logged error includes structured properties | Error log carries flagName + visitorType for Sentry filtering | Low |
| 5 | TaskCanceledException bubbles up | Cancellation is re-thrown rather than swallowed as false/"" | Medium |

### tests/ZoTools.Web.UnitTests/AppointmentImplTests.cs (91 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Cancellation token forwarding to upstream services | Async impl propagates cancellation on all paths | Medium |
| 2 | Reschedule across DST boundary | Resulting OffsetDateTime is correct across a DST transition | Medium |
| 3 | Concurrent reschedule on same call | Race on cache write between simultaneous requests | **High** |
| 4 | Idempotency on duplicate confirm | Re-submitted confirm after timeout does not double-cancel/double-book | **High** |
| 5 | Transfer directive fallback TransferNumberType | Documents non-Appointment TransferNumberType when configured | Low |
| 6 | Unsupported accept-language (e.g. fr-CA) | Confirms en-US is the universal fallback, not en-CA etc. | Low |
| 7 | ExcludedProvider event payload shape | Asserts the structured event payload, not just emission count | Medium |
| 8 | SubmitBooking validation field combinations | Missing dob + missing phone together (and other combos) | Medium |

### tests/ZoTools.Web.UnitTests/AudioImplTests.cs (3 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Presigned URL TTL reflected in response | TTL surfaced in response headers/body | Medium |
| 2 | Multiple audio segments | Each segment correctly mapped to its presigned URL | Medium |
| 3 | Argument validation: empty/null callId | Behavior on missing callId | Medium |
| 4 | Cancellation token forwarding | Cancellation propagated to the service | Medium |

### tests/ZoTools.Web.UnitTests/CallCompletedImplTests.cs (23 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Empty-transcript case | Call with zero turns maps cleanly | Medium |
| 2 | Very large transcript (50k+ chars) | Performance / payload-size limit handling | Medium |
| 3 | Cost line items with negative/zero values | Boundary handling of cost values | Medium |
| 4 | Cancellation token forwarding | Cancellation propagated to transcript service | Medium |
| 5 | Argument validation: empty/null callId | Behavior when required callId is missing | Medium |
| 6 | ToolCalls with malformed JSON-arg payloads | Parser robustness on bad tool-call args | Medium |

### tests/ZoTools.Web.UnitTests/CallIdLogEnrichmentMiddlewareTests.cs (12 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Multiple call_id headers | Which value wins when the header is duplicated | Medium |
| 2 | Very long call_id (>4kb) | Truncation for Sentry tag's 200-char limit | Low |
| 3 | Exception in next() still sets tag/context | Sentry captures call_id alongside the error (the load-bearing scenario) | **High** |
| 4 | Scope disposed/popped after request | No LogContext bleed into the next request | Medium |
| 5 | Concurrent requests on the scope | LogContext is request-scoped via push/pop discipline | Medium |

### tests/ZoTools.Web.UnitTests/CsatToolImplTests.cs (5 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Exception path logs the exception | Swallowed exceptions are observable in Sentry/Serilog | Medium |
| 2 | Argument validation: empty/null callId, score out of range | Rejects/handles invalid callId and out-of-scale score | Medium |
| 3 | Cancellation token forwarding | Cancellation propagated to CSAT service | Medium |
| 4 | Score boundaries (1, 5, 0, -1, 100) | Locks down what the impl treats as valid | Medium |

### tests/ZoTools.Web.UnitTests/DebugToolsImplTests.cs (43 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Unauthenticated access to GetSession | Auth check on endpoint that exposes session contents | **High** |
| 2 | Prod-config shape of PHI-adjacent fields | GetSession DOB/gender/phone exposure acceptable per environment | **High** |
| 3 | Argument validation: empty/null callId | Behavior when required callId is missing | Medium |
| 4 | Cancellation token forwarding to repo | Cancellation propagated | Medium |
| 5 | MapToPatientResponse with 1000+ appointments | Mapper performance boundary | Low |

### tests/ZoTools.Web.UnitTests/ExampleTests.cs (1 tests)

**Irrelevant Tests:**

| Test # | Test Name | Issue |
|--------|-----------|-------|
| 1 | TokenEmptyTest | Placeholder asserting (1+1)==2; tests nothing real — recommend deletion |

**Missing Tests:** None — all tests are relevant.

### tests/ZoTools.Web.UnitTests/HttpLoggingMiddlewareTests.cs (1 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Does NOT log PHI-bearing request bodies | Privacy guard against logging PHI (critical) | **High** |
| 2 | Sensitive headers redacted | Authorization/Cookie headers scrubbed from logs | **High** |
| 3 | Exception in next() logged and re-thrown | Errors are logged then propagated | **High** |
| 4 | Logs the response status code | Response-side logging (only request side tested today) | Medium |
| 5 | Logs request duration / latency | Latency captured | Medium |
| 6 | Correct log level per status | Info for 2xx, Warning for 4xx, Error for 5xx | Medium |
| 7 | Path-based filtering | Health-check endpoints skip logging | Low |
| 8 | Very large bodies truncated | Avoid overwhelming the log shipper | Low |

### tests/ZoTools.Web.UnitTests/InitializeCallImplTests.cs (33 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | AB flag service throws for shouldAutoTransfer/fillersEnabled/voiceQualityV2 | Resilience on flag failure (only useMultilingualStt failure tested) | Medium |
| 2 | Practice info lookup throws (PhoneBot down) | Whether response is a transfer or 5xx on critical entry point | **High** |
| 3 | Concurrent calls for same practice | Cache thrash under concurrency | Medium |
| 4 | Cache TTL header value asserted | Actual seconds value (not just presence) | Low |
| 5 | Phone lookup throws | Response shape when phone lookup fails | Medium |
| 6 | Accept-language header propagation | Language header forwarded if applicable | Low |
| 7 | Cancellation token forwarding | Cancellation propagated throughout | Medium |

### tests/ZoTools.Web.UnitTests/PatientInfoImplTests.cs (40 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Auth boundary on patient update | LLM cannot update a patient not belonging to the call's session | **High** |
| 2 | Concurrent updates on same patientId | Race handling on simultaneous updates | **High** |
| 3 | Phone sanitization with extension | `+1 555 555-5555 ext 123` normalization | Medium |
| 4 | DOB validation | Future date, year < 1900, malformed | Medium |
| 5 | Name length boundaries | Empty and max-length names | Medium |
| 6 | Cancellation token forwarding | Cancellation propagated to service | Medium |
| 7 | ValidatePatient with multiple missing fields | Message format (list vs comma-joined) | Medium |

### tests/ZoTools.Web.UnitTests/PracticeFaqToolsImplTests.cs (8 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | locationId not matching any practice location | Validation/404 vs silent fallback | Medium |
| 2 | Empty content from service | E.g. practice has no Insurance FAQ configured | Medium |
| 3 | Cancellation token forwarding | Cancellation propagated | Medium |
| 4 | Localization of FAQ content | Content language matches accept-language | Low |

### tests/ZoTools.Web.UnitTests/PracticeInfoImplTests.cs (6 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Cache returns null payload | Response is 404 vs 200-with-empty | Medium |
| 2 | TTL header value asserted | Actual seconds value (not just presence) | Low |
| 3 | Repository throws (PhoneBot 5xx) | Response shape on downstream failure | **High** |
| 4 | Cancellation token forwarding | Cancellation propagated | Medium |
| 5 | Practice phone number normalization | E.g. +1 prefix handling | Low |

### tests/ZoTools.Web.UnitTests/PracticeInfoResponseExtensionsTests.cs (48 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Verify snapshot of fully populated top-level mapping | Catches new fields added later that MapsTopLevelFields misses | Medium |
| 2 | Multiple provider-locations with different IPAs | Which IPA wins during cross-population | Medium |
| 3 | Per-mapper snapshot for representative inputs | Locks the mapping contract more strongly than field-by-field asserts | Low |
| 4 | Field-level edge cases | Empty strings, very long strings, unicode in names | Low |
| 5 | Null inside lists | E.g. a List<Provider> with a null element — graceful vs throw | Medium |

### tests/ZoTools.Web.UnitTests/SmsImplTests.cs (38 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | ConfirmDetails with mixed valid + invalid ids | All-or-nothing vs partial send | Medium |
| 2 | BookingConfirmed after appointment cancelled | Whether SMS still sends or no-ops | Medium |
| 3 | International phone formats for SMS recipient | Recipient number normalization | Medium |
| 4 | Consent expiry | Whether N-day-old consent is still valid | Medium |
| 5 | Rapid successive SendSms calls | Rate-limiting / dedupe | Medium |
| 6 | Cancellation token forwarding | Cancellation propagated | Medium |
| 7 | SmsSendFailed error directive shape | Asserts transfer reason + error code, not just presence | Low |

### tests/ZoTools.Web.UnitTests/ToolSchemaResponseSerializationTests.cs (1 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Serialization with ToolDefinition.Returns = null | Null Returns branch serializes correctly | Medium |
| 2 | Empty parameters (RequiredParams + Properties empty) | JSON shape with no params | Medium |
| 3 | Verify snapshot of entire JSON output | Locks down full contract shape (per CS-006) | Medium |
| 4 | Round-trip idempotency | serialize → deserialize → serialize equality | Medium |
| 5 | Backward-compat of legacy fields | Cached LLM schemas don't break on field changes | **High** |

### tests/ZoTools.Web.UnitTests/ToolsSchemaImplTests.cs (5 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Empty/null agent name parameter | Argument validation | Medium |
| 2 | Provider's GetTools throws | Response shape on provider failure | Medium |
| 3 | Cancellation token forwarding | Cancellation propagated | Medium |
| 4 | HTTP caching headers | Stable-per-agent schema is cacheable | Low |
| 5 | Provider Version format | Version format verified (semver) | Low |

### tests/ZoTools.Web.UnitTests/ValidationToolsImplTests.cs (2 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | IsValid=false return path | Validation failure with explanation | Medium |
| 2 | Service throws unexpected exception | Error handling on downstream failure | Medium |
| 3 | Argument validation: required ids missing | Behavior when required ids absent | Medium |
| 4 | Cancellation token forwarding | Cancellation propagated | Medium |
| 5 | Time-zone handling for validated availability time | Correct tz handling of the availability time | Medium |
| 6 | Structured reason/suggestion when invalid | Response shape on invalid result | Low |

---

# 3. Web API Tests — `tests/ZoTools.Web.ApiTests/`

## 3.1 Admin / Tools surface

### tests/ZoTools.Web.ApiTests/AbApiTests.cs (20 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | IsFlagOn_FlagOnReturnsTrue | Every success case asserts IsFlagOn=false; the true branch is never exercised | Medium |
| 2 | IsFlagOn_FlagOverrideViaExperimentOverridesHeader_AppliesOverride | ZD-Experiment-Overrides header actually flips the flag | Medium |
| 3 | GetVariant_WithVariantOverride_ReturnsOverriddenValue | Positive assertion that overrides change the variant value | Medium |
| 4 | GetVariant_UnknownExperiment_ReturnsDefault | Unknown variant name returns documented default vs 400 | Medium |
| 5 | IsFlagOn_VisitorIdMismatch_ConsistencyAcrossCalls | A/B stickiness per visitor across calls | Medium |
| 6 | Response shape snapshot for IsFlagOnResult / GetVariantResult | Full schema snapshot vs single-field spot checks | Low |

### tests/ZoTools.Web.ApiTests/AdminSeedApiTests.cs (7 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | SeedPracticeInfo_WithoutAuth_Returns401_OrConfirmedOpen | No auth coverage at all; whether admin/seed endpoints are intentionally open (security risk if deployed non-local) | **High** |
| 2 | SeedAppointments_OverwriteExistingKey_LastWriteWins | Idempotency/overwrite behavior on repeated seed of same key | **High** |
| 3 | Concurrency under parallel writers | Shared state leaks (reason for [NonParallelizable]); race conditions on concurrent writes | **High** |
| 4 | SeedPracticeInfo_InvalidEntityShape_Returns400 | Malformed seed payload rejection | Medium |
| 5 | ClearByEntityType_UnknownEntityType_Returns400Or404 | Clearing an unknown entity type | Medium |
| 6 | ListSeededKeys_PaginationOrLargeResult | Listing response when many keys exist | Low |

### tests/ZoTools.Web.ApiTests/DebugToolsApiTests.cs (6 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Wrong-role JWT (PhoneBotReadPhi missing) returns 403 | RBAC/role enforcement on PHI-bearing debug endpoints (AUTH-001) | **High** |
| 2 | Audit logging assertion | Debug tools view PHI; audit event written per AUTH-005 | **High** |
| 3 | WhenCallSessionExists_Returns200WithPatients / Appointments | Only happy-path coverage for the debug surface (both endpoints) | **High** |
| 4 | WhenCallSessionNotFound_Returns404 | Not-found path (already TODO'd, blocked on test JWT) | Medium |
| 5 | WithPatientId_FiltersToSpecificPatient / WithoutPatientId_UsesFirstPatient | Query-param filtering semantics | Medium |
| 6 | Rate limit / abuse guard | PHI-serving debug endpoint should rate-limit | Medium |

### tests/ZoTools.Web.ApiTests/ToolsApiTests.cs (28 tests)

**Irrelevant Tests:**

| Test # | Test Name | Issue |
|--------|-----------|-------|
| (all) | Heavy overlap with AppointmentApiTests.cs | cancel_appointment (this file) vs appointments/{id}/cancel duplicate coverage; should be consolidated or labeled v1-legacy vs v2 |

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | CancelAppointment_Idempotency | Double-cancel same appointment is a no-op, not double-cancel | **High** |
| 2 | CancelAppointment audit log | Sensitive cancellation write is audit-logged (AUTH-005) | **High** |
| 3 | CancelAppointment_500_FromDownstream | Downstream/partial-failure path on a critical mutation | **High** |
| 4 | Concurrent CancelAppointment from two callers | Race/conflict (409) coverage | **High** |
| 5 | GetTransferNumber unknown call center => 404 | Not-found path (only happy path tested) | Medium |
| 6 | GetTransferNumber filtering by transfer number type | Type-filter behavior | Medium |
| 7 | GetAppointments large result pagination | Paging/capping when practice has many appointments | Medium |
| 8 | GetAppointments cache header behavior | Cache headers likely emitted but not asserted | Low |
| 9 | Response schema snapshot | Strict/Verify snapshot vs ExcludingMissingMembers letting drift pass | Low |

### tests/ZoTools.Web.ApiTests/ToolsSchemaApiTests.cs (13 tests)

**Irrelevant Tests:**

| Test # | Test Name | Issue |
|--------|-----------|-------|
| 2, 7, 10, 12 | GetToolsSchema_*_WithProductionSecret_ReturnsExpectedResponse | Copy-paste of the _WithTestSecret tests with secret swapped; should be [TestCase] over both secrets |
| 9 | GetToolsSchema_ManageAppointmentAgent_IncludesCancelAppointmentTool | Single-tool spot-check redundant with test 8; a snapshot of all tool names would be more durable |

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Schema-vs-impl contract | Every tool listed by schema has a matching endpoint accepting documented params | **High** |
| 2 | HasSideEffect flag matches HTTP method semantics | POST tools = side-effect true, GET = false, asserted globally | Medium |
| 3 | Coverage of remaining agents (select-provider, csat, sms, etc.) | Catalog drift across agents not under test | Medium |
| 4 | Snapshot (Verify) of full schema | Catch unintended tool add/remove/rename in CI | Medium |
| 5 | Schema versioning / forward-compat | Clients can ignore unknown fields | Low |
| 6 | Auth: role-gated vs open (JWT?) | Whether schema endpoint should also require JWT | Low |

## 3.2 AgentTools endpoints

### tests/ZoTools.Web.ApiTests/InsuranceToolsApiTests.cs (12 tests)

**Irrelevant Tests:**

| Test # | Test Name | Issue |
|--------|-----------|-------|
| 11 | SearchInsurances_WithBcbsCarrierClaimAndNoState_MayEmitDisambiguationDirective | Conditional assertion (only fires when bcbsMatches > 5); does nothing otherwise. Use a guaranteed fixture |

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | SearchInsurances_Audit log when carrier+plan returned | Insurance is sensitive PII; audit logging (AUTH-005) | **High** |
| 2 | SearchInsurances_500_FromInsuranceService | Downstream-failure path | **High** |
| 3 | SearchInsurances_WithBothCarrierIdAndCarrierClaim_PrefersOneOrErrors | Ambiguous-input behavior | Medium |
| 4 | SearchInsurances_Performance > 10 results capped | Overflow/cap enforcement with >10 raw matches | Medium |
| 5 | SearchInsurances_StateCodeWithLowercase / Mixed Case | usa_state_code casing handling | Medium |
| 6 | Response shape snapshot (Verify) | ExcludingMissingMembers lets schema drift go silent | Low |

### tests/ZoTools.Web.ApiTests/PracticeFaqToolsApiTests.cs (5 tests)

**Irrelevant Tests:**

| Test # | Test Name | Issue |
|--------|-----------|-------|
| 3, 4, 5 | GetPracticeFaq_General/Pricing/Insurance Topic | Three near-identical tests differing only by enum value; consolidate via [TestCase] |

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | GetPracticeFaq_UnknownTopic_Returns400 | Enum out-of-range rejection | Medium |
| 2 | GetPracticeFaq_MissingTopic_Returns400 | Empty body validation | Medium |
| 3 | GetPracticeFaq_WhenPracticeHasNoFaq_Returns404OrEmptyContent | Behavior for practice without configured FAQ | Medium |
| 4 | GetPracticeFaq_LongFaqContentTruncation | Length-cap behavior if any | Low |
| 5 | Multilingual FAQ (accept-language) | Language-variant content | Low |
| 6 | Snapshot of response shape | Full schema vs Content+Topic only | Low |

### tests/ZoTools.Web.ApiTests/PracticeInfoApiTests.cs (9 tests)

**Irrelevant Tests:**

| Test # | Test Name | Issue |
|--------|-----------|-------|
| 6 | GetPracticeInfo_ReturnsAllResponseFields | Every assertion wrapped in `if X is { Count: > 0 }`; silently passes on empty arrays. Use a seeded fixture |

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | GetPracticeInfo_500_FromDownstream | Downstream-failure path | **High** |
| 2 | GetPracticeInfo_UnknownPhoneNumber_Returns404 | Not-found path (only known phone tested) | Medium |
| 3 | GetPracticeInfo_AfterDataChange_CacheInvalidates | Cache invalidation when seeded data changes | Medium |
| 4 | GetPracticeInfo_PhoneNumberWithSpecialCharacters | Alternate URL-encoded phone formats | Medium |
| 5 | GetPracticeInfo_WithMissingNetworks/Locations_HandlesNullsGracefully | Deliberate empty-state assertion | Medium |
| 6 | Audit log when transfer-number (PII) fields returned | Audit on potentially-PII fields (AUTH-005) | Medium |
| 7 | Schema snapshot via Verify | Prevent silent shape drift | Low |

### tests/ZoTools.Web.ApiTests/SelectLocationToolsApiTests.cs (19 tests)

**Irrelevant Tests:**

| Test # | Test Name | Issue |
|--------|-----------|-------|
| 12 | FindLocationsByName_ReturnsValidResponse | `BeOneOf(Found, NoMatchesFound)` is "happy or empty" — a smoke test, not behavioral; make deterministic |

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | FindLocations*_MissingCallIdHeader_Returns400 | call-id header enforcement (required by other tools, unenforced here) | Medium |
| 2 | ValidateLocation_AuthInvalidSecret_Returns401 | Invalid-secret negative for validate_location (only no-auth tested) | Medium |
| 3 | FindLocationsNearZip_ValidZipNoMatches_ReturnsEmptyOrNoMatchesFound | Zero-result branch | Medium |
| 4 | FindLocationsByName_FindsExactMatch / NoMatch_ReturnsNoMatchesFound | Pin both outcomes deterministically | Medium |
| 5 | SearchLocations_ZipOutsidePracticeFootprint_ReturnsEmpty | Boundary case | Medium |
| 6 | search_locations validation (invalid filters / oversized claim) | Invalid filter combinations and oversized input | Medium |
| 7 | Schema-vs-impl contract | validate_location param shape matches ToolsSchema report | Low |

### tests/ZoTools.Web.ApiTests/SelectProviderToolsApiTests.cs (3 tests)

**Irrelevant Tests:**

| Test # | Test Name | Issue |
|--------|-----------|-------|
| 1 | SearchProviders_ReturnsValidResponse | Asserts only `Providers.Should().NotBeNull()`; an empty list passes — no real content check |

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | SearchProviders_AuditLog / PHI handling | Provider lookups can leak roster info; audit/PHI handling (AUTH-005) | **High** |
| 2 | SearchProviders_InvalidSecret_Returns401 | Invalid-secret negative (only no-auth tested) | Medium |
| 3 | SearchProviders_NoMatches_ReturnsEmptyList | Zero-result branch | Medium |
| 4 | SearchProviders_ResultShape | Deep field validation vs not-null smoke | Medium |
| 5 | SearchProviders_WithLocationFilter | Location filter behavior | Medium |
| 6 | SearchProviders_WithSpecialtyFilter | Specialty filter behavior | Medium |
| 7 | SearchProviders_PhoneticMatch_OrSpellingTolerance | Phonetic/spelling-tolerant search | Low |

### tests/ZoTools.Web.ApiTests/SelectVisitReasonToolsApiTests.cs (4 tests)

**Irrelevant Tests:**

| Test # | Test Name | Issue |
|--------|-----------|-------|
| 4 | SearchVisitReasons_WithLocationId_Returns200 | Status-only test; does not verify the location filter changes results — borderline irrelevant without a content assertion |

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | SearchVisitReasons_WithoutCallIdHeader_Returns400 | call-id header enforcement | Medium |
| 2 | SearchVisitReasons_NoMatches_ReturnsEmpty | Empty-result branch | Medium |
| 3 | SearchVisitReasons_IsNewPatientFalse_ReturnsExistingPatientReasons | Existing-patient branch (only is_new_patient=true tested) | Medium |
| 4 | SearchVisitReasons_EmptyUserClaim_ReturnsBadRequestOrAll | Empty-claim behavior | Medium |
| 5 | SearchVisitReasons_LocationIdMismatch_StillReturns | Nonexistent location_id handling | Medium |
| 6 | SearchVisitReasons_ResponseShape | Full shape (visit_reason_id, display name) vs NotBeEmpty only | Low |

### tests/ZoTools.Web.ApiTests/ValidationToolsApiTests.cs (4 tests)

**Irrelevant Tests:**

| Test # | Test Name | Issue |
|--------|-----------|-------|
| 4 | ValidateAvailability_WithProductionSecret_Returns200 | Duplicates test 3 with secret swapped; asserts only 200, no content check |

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | ValidateAvailability_InvalidProvider_ReturnsIsValidFalseWithIssues | Entire negative path missing (only happy "true" covered) | Medium |
| 2 | ValidateAvailability_InvalidLocation_ReturnsIsValidFalseWithIssues | Invalid-location negative path | Medium |
| 3 | ValidateAvailability_InvalidVisitReason_ReturnsIsValidFalseWithIssues | Invalid-visit-reason negative path | Medium |
| 4 | ValidateAvailability_MissingFields_Returns400 | Body validation | Medium |
| 5 | ValidateAvailability_MissingCallIdHeader_Returns400 | call-id header enforcement | Medium |
| 6 | Issues array shape | Fields present when Issues populated | Low |

### tests/ZoTools.Web.ApiTests/SelectTimeslotToolsApiTests.cs (24 tests)

**Irrelevant Tests:**

| Test # | Test Name | Issue |
|--------|-----------|-------|
| 23 | SearchAvailability_RescheduleWithNoSession_Throws | Expects 500 for a missing session; per API-001 should be 404/400 — documents a probable bug rather than desired behavior |
| 8, 17 | SearchAvailability/GetNextAvailable_TimeslotsAreSortedByStartTime | Near-identical sort-order tests; could be one parametrized test |

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | SearchAvailability_RescheduleWithNoSession should return 404/400 not 500 | Correct 4xx mapping on reschedule missing-session (API-001) | **High** |
| 2 | SearchAvailability_NoMatchingTimeslots_ReturnsEmpty | Zero-result branch | Medium |
| 3 | GetNextAvailable_NoMatches_ReturnsEmpty | Zero-result branch | Medium |
| 4 | SearchAvailability_DateRangeBeyondMax_Returns400 | Upper-bound date-range validation | Medium |
| 5 | SearchAvailability_DurationOverride_AffectsResults | duration_override effect on results | Medium |
| 6 | SearchAvailability_PaginationOrTimeslotCap | Result-cap behavior | Medium |
| 7 | Schema-vs-impl contract | ToolsSchema search_availability params match accepted body shape | Low |

## 3.3 Booking + Patient

### tests/ZoTools.Web.ApiTests/AppointmentApiTests.cs (28 tests)

**Irrelevant Tests:**

| Test # | Test Name | Issue |
|--------|-----------|-------|
| 28 | UpdateRescheduleLocation_NoSessionForCall_Returns200WithTransferDirective | Returns 200 with no session while sibling endpoints return 404; flag inconsistency (probable bug) |

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Audit logging for reschedule and cancel | Sensitive mutations audit-logged (AUTH-005) | **High** |
| 2 | CancelAppointment idempotency | Confirm-twice on same appointment behavior | **High** |
| 3 | Concurrency: reschedule + cancel in parallel | Race coverage on critical mutations | **High** |
| 4 | RescheduleAppointment_NewStartTime_InThePast_Returns400 | Date validation | Medium |
| 5 | RescheduleAppointment_PerformingProviderId_Mismatch_Returns400 | Body mismatch case | Medium |
| 6 | RescheduleAppointment_WithBothBlockedReasons_Returns200WithTransfer | Both-blocked scenario (only single-blocked tested) | Medium |

### tests/ZoTools.Web.ApiTests/BookingApiTests.cs (9 tests)

**Irrelevant Tests:**

| Test # | Test Name | Issue |
|--------|-----------|-------|
| 5, 6 | SubmitBooking_ConfirmFalse English/Spanish | Differ only by accept-language; consolidate via [TestCase] over locale |
| 7, 9 | SubmitBooking_ConfirmTrue English/Spanish | Same locale-only duplication |

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | SubmitBooking_Idempotency | Confirm-twice produces one appointment, not two | **High** |
| 2 | Audit logging | Booking write audit-logged (AUTH-005) | **High** |
| 3 | SubmitBooking_ConfirmTrue_DownstreamFailure_Returns500_OrSpecificError | Downstream-failure on critical mutation | **High** |
| 4 | SubmitBooking_ConfirmTrue_NoSession_Returns404 | Missing-session path (only happy paths use session) | **High** |
| 5 | SubmitBooking_ConfirmFalse_MissingRequiredFields_Returns400 | Body validation (missing start_time, provider_id) | Medium |
| 6 | SubmitBooking_PastStartTime_Returns400 | Temporal validation | Medium |
| 7 | SubmitBooking_DurationZeroOrNegative_Returns400 | Body range validation | Medium |
| 8 | SubmitBooking_ResponseShape (full) | Full schema snapshot vs AppointmentId+Speak only | Low |

### tests/ZoTools.Web.ApiTests/InsertPatientApiTests.cs (8 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | InsertPatient_AuditLog | Patient creation creates PHI; audit-logged (AUTH-005) | **High** |
| 2 | InsertPatient_Idempotency | Same body twice — contract for duplicate creation | **High** |
| 3 | InsertPatient_DuplicatePatient_Returns409 | Existing-patient conflict handling | **High** |
| 4 | InsertPatient_500_FromDownstream | Downstream-failure on a mutation | **High** |
| 5 | InsertPatient_FutureDateOfBirth_Returns400 | Temporal validation | Medium |
| 6 | InsertPatient_FirstNameTooLong_Returns400 | Oversized payload rejection | Medium |
| 7 | InsertPatient_FirstNameContainsControlChars_Returns400 | Input sanitization | Medium |
| 8 | InsertPatient_UnicodeName_AcceptedAndPersisted | i18n name handling | Low |
| 9 | Response shape (full) | Snapshot vs PatientId only | Low |

### tests/ZoTools.Web.ApiTests/UpdatePatientApiTests.cs (9 tests)

**Irrelevant Tests:**

| Test # | Test Name | Issue |
|--------|-----------|-------|
| 7, 8 | UpdatePatient_WithSeededPatient / WithAllFields_Returns200 | Assert only HTTP 200, no body check — functionally smoke tests; add response-body/Verify assertions |
| 6, 9 | UpdatePatient_WhenPatientNotInSession (RealOnly) / Fake | Assert effectively the same unknown-patient => 400; document why both or consolidate |

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | UpdatePatient_AuditLog | Patient update is sensitive PHI write; audit-logged (AUTH-005) | **High** |
| 2 | UpdatePatient_Idempotency | Same payload twice should be safe | **High** |
| 3 | UpdatePatient_500_FromDownstream | Downstream-failure on a mutation | **High** |
| 4 | UpdatePatient_InvalidEmail_Returns400 | Email format validation | Medium |
| 5 | UpdatePatient_InvalidPhone_Returns400 | Phone format validation | Medium |
| 6 | UpdatePatient_InvalidStateCode_Returns400 | State-code validation | Medium |
| 7 | UpdatePatient_InvalidZip_Returns400 | Zip format validation | Medium |
| 8 | UpdatePatient_NoFieldsProvided_NoOpOr400 | patient_id-only body behavior | Medium |
| 9 | UpdatePatient_ResponseShape | Body content/schema assertion | Low |

### tests/ZoTools.Web.ApiTests/ValidatePatientApiTests.cs (9 tests)

**Irrelevant Tests:**

| Test # | Test Name | Issue |
|--------|-----------|-------|
| 6, 9 | ValidatePatient_WhenPatientNotInSession (RealOnly) / Fake | Same unknown-patient => 400 outcome; document why both or consolidate |
| 8 | ValidatePatient_WithPartialPatient_ReturnsMissingFieldsStatusWithMessage | Asserts Message non-empty only; too loose — message guides agent behavior, pin/snapshot it |

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | ValidatePatient_AuditLog | Reading PHI; audit-logged (AUTH-005) | **High** |
| 2 | ValidatePatient_500_FromDownstream | Downstream-failure path | **High** |
| 3 | ValidatePatient_MessageContent_DescribesMissingFields | Message lists which fields are missing (agent uses it to prompt patient) | Medium |
| 4 | ValidatePatient_PartialPatient_VariousFieldCombinations | Different missing-field combos yield different messages | Medium |
| 5 | ValidatePatient_InvalidEmailInSession_ReturnsMissingFields | Patient with bad email present | Medium |
| 6 | ValidatePatient_ResponseShape (full) | Verify snapshot for silent shape changes | Low |

### tests/ZoTools.Web.ApiTests/PatientSearchApiTests.cs (8 tests)

**Irrelevant Tests:**

| Test # | Test Name | Issue |
|--------|-----------|-------|
| 8 | SearchPatients_WithTestSecret_Returns500 | Asserts a deliberate stub-throws-500 as contract; per API-001 exposing a 500 as documented behavior is questionable — flag |

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | SearchPatients_AuditLog | Searching patient PII is sensitive; audit-logged (AUTH-005) | **High** |
| 2 | SearchPatients_500_FromDownstream | Real downstream-failure path (only deliberate test-secret 500 exists) | **High** |
| 3 | SearchPatients_ZeroMatches_ReturnsEmptyList | Empty-result branch | Medium |
| 4 | SearchPatients_DateOfBirthOnly_NoPhone | At-least-one-criterion validation | Medium |
| 5 | SearchPatients_InvalidDateOfBirth_Returns400 | Body validation | Medium |
| 6 | SearchPatients_PartialMatch_FuzzyName | Fuzzy/substring search behavior | Low |

## 3.4 Telephony

### tests/ZoTools.Web.ApiTests/AudioApiTests.cs (4 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Audit-log verification (AUTH-005) | Generating a presigned URL is sensitive and should be logged | **High** |
| 2 | Downstream S3 / signing failure => 500 with structured error | 5xx/downstream-failure path | **High** |
| 3 | Idempotency: same callId returns same/working URLs | Documented replay behavior | Medium |
| 4 | URL expiry / TTL assertion | Presigned URL X-Amz-Expires (only literal fake string checked) | Medium |
| 5 | Empty / malformed callId path segment => 400 | Input validation (spaces, very long ids) | Medium |
| 6 | Production secret accepted variant | Prod-secret path (only TestSecret exercised) | Low |

### tests/ZoTools.Web.ApiTests/CallCompletedApiTests.cs (2 tests)

**Irrelevant Tests:**

| Test # | Test Name | Issue |
|--------|-----------|-------|
| 1, 2 | PostCallCompleted_WithoutAuth / WithWrongSecret_Returns401 | Auth gate is shared middleware; little marginal value beyond one negative test — duplicates every other endpoint |

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | PostCallCompleted_ValidSecret_Returns200 + persistence verification | Zero positive coverage; source-of-truth endpoint for every completed call (P0 gap) | **High** |
| 2 | Idempotency: replay same CallId does not double-record | Replay of a mutation endpoint | **High** |
| 3 | PHI handling: turn Text not leaked into errors/request logs | PHI scrubbing in middleware | **High** |
| 4 | Audit log (AUTH-005) on call finalization | Sensitive-event audit | **High** |
| 5 | Validation: missing CallId/PracticeId, StartTime>EndTime, empty Turns, future StartTime, bad TurnRole => 400 | Body validation | Medium |
| 6 | Large payloads (many turns / long Text) => accepted to limit then 413 | Payload-size limits | Medium |

### tests/ZoTools.Web.ApiTests/CsatToolApiTests.cs (10 tests)

**Irrelevant Tests:**

| Test # | Test Name | Issue |
|--------|-----------|-------|
| 10 | SubmitCsat_WithoutFeedback_ReturnsSuccess | Weakly differentiated from #8 (default body already covers null feedback); consolidate |

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | PHI scrubbing: free-text feedback not logged plaintext / stored encrypted | PHI handling on user free-text | **High** |
| 2 | Audit log (AUTH-005) per CSAT submission | Sensitive-event audit tying score to call | **High** |
| 3 | Idempotency: re-submit CSAT for same call_id (upsert or 409) | Replay of a mutation | **High** |
| 4 | Missing call_id and missing practice_phone_number each => 400 | Required-field validation (only missing stars tested) | Medium |
| 5 | Non-integer stars (4.5, "four", negative) => 400 | Type validation | Medium |
| 6 | Excessive feedback length (>5KB) => 400/413 | Payload-size validation | Medium |
| 7 | Feature-flag-off path returns no-op success | Flag gating | Low |

### tests/ZoTools.Web.ApiTests/InitializeCallApiTests.cs (15 tests)

**Irrelevant Tests:**

| Test # | Test Name | Issue |
|--------|-----------|-------|
| 6, 8 | InitializeCall_FirstRequest_ReturnsCacheMissHeader / DifferentPhoneNumbers_IndependentCacheState | Generate phone numbers from DateTime.UtcNow.Ticks — flaky under fast CI (tick collisions); use Guid |
| 12-15 | InitializeCall_WhenPhoneNumberLookup* | Very similar; consolidate via [TestCase] parameterization |

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Audit log (AUTH-005) per call init with call-id and caller-phone-number | Sensitive-event audit | **High** |
| 2 | PHI: caller-phone-number scrubbing in middleware logs | PHI handling | **High** |
| 3 | Unknown / unmapped practice phone => 404 (not 200 with empty defaults) | Not-found path | Medium |
| 4 | Cache eviction/expiry: after TTL or DELETE, X-Cache-Hit flips false | Cache invalidation | Medium |
| 5 | Cache-TTL countdown: later hit shows smaller TTL | Cache TTL semantics | Medium |
| 6 | Practice-phone URL encoding edge cases (intl, leading +, %2B) | Input encoding | Medium |
| 7 | Concurrency: parallel inits for same phone serialize cache-warm | Race coverage | Medium |
| 8 | Verify-snapshot of full InitializeCallData payload | Lock contract vs scattered field probes | Low |

### tests/ZoTools.Web.ApiTests/SmsApiTests.cs (26 tests)

**Irrelevant Tests:**

| Test # | Test Name | Issue |
|--------|-----------|-------|
| 10 | SendSms_Cancel_EmptyAppointmentIds_Returns400 | Duplicates #4 empty-list scenario for cancel; parameterize via [TestCase] |
| 13 | SendSms_BookingConfirmed_EmptyAppointmentIds_Returns400 | Duplicates #4/#10 empty-list scenario for booking_confirmed |
| 15 | SendSms_Reschedule_EmptyAppointmentIds_Returns400 | Duplicates #4/#10/#13 empty-list scenario for reschedule |

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Audit log (AUTH-005) for send-sms and consent change | Sensitive-event audit on both endpoints | **High** |
| 2 | PHI handling: provider/patient name in Directives.Transfer not leaked to logs | PHI scrubbing | **High** |
| 3 | Idempotent re-send: same confirm_details twice — spam vs short-circuit | Replay of a mutation | **High** |
| 4 | Patient with canReceiveSms=false (landline/anonymous) should not dispatch | Data-integrity / eligibility gating | **High** |
| 5 | Session with consent=null — dispatch behavior | Consent-gating behavior | Medium |
| 6 | Invalid/unknown sms_type enum => 400 | Input validation | Medium |
| 7 | Cancel-on-non-existent appointment id => 404 | Not-found path | Medium |
| 8 | set-sms-consent query param validation (missing/non-boolean) | Input validation | Medium |
| 9 | Concurrency: two SMS dispatches for same call-id racing | Double-send race coverage | Medium |
| 10 | Response-shape Verify snapshot for Directives.Transfer branch | Contract lock | Low |

---

# 4. Integration Tests

### tests/ZoTools.IntegrationTests/ExampleTests.cs (0 tests — placeholder)

**Irrelevant Tests:**

| Test # | Test Name | Issue |
|--------|-----------|-------|
| 1 | TokenEmptyTest | Asserts `1 + 1 == 2`; trivial placeholder giving a green CI signal with zero real integration coverage |

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | End-to-end agent tool flows against fakes | Wire `ManageAppointmentAgentTools`, `GreetingAgentTools`, etc. to fake downstream clients (Interop, Cistern, MarketplaceApi) and verify orchestration across multiple tools | **High** |
| 2 | InteropPlatformApi client integration | Real HTTP client / in-process fake host round-tripping for patient search, appointment fetch, reschedule, cancel to lock the contract | **High** |
| 3 | Audio/transcription pipeline | Initialize call -> stream audio -> emit transcript events, exercising real serialization and timing | **High** |
| 4 | CSAT tool flow | Full submit-feedback path with a fake upstream service, including failure / retry behavior | **High** |
| 5 | Cron / worker entrypoints | Run scheduled jobs end-to-end against fakes per PROC-002 / TEST-003 | Medium |
| 6 | Logging / metrics enrichment | Verify `CallIdLogEnrichmentMiddleware` and `HttpLoggingMiddleware` produce correct structured fields through a realistic request path | Medium |

### tests/ZoTools.Web.IntegrationTests/ExampleTests.cs (0 tests — placeholder)

**Irrelevant Tests:**

| Test # | Test Name | Issue |
|--------|-----------|-------|
| 1 | TokenEmptyTest | Asserts `1 + 1 == 2`; trivial placeholder, not an integration test |

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Real web-pipeline bootstrap test | Replace placeholder with a `WebApplicationFactory<>`-based test verifying DI / appsettings / test host wires up correctly as a foundation for the suite | **High** |

### tests/ZoTools.Web.IntegrationTests/LockedActionServiceIntegrationTests.cs (8 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Result deserialization failure | Corrupt / unknown JSON in stored `Result` should surface a clear error rather than silent null (data-integrity) | **High** |
| 2 | Stale (incomplete + old) lock recovery | Writer crashes after lock creation but before completion; next caller can take over after timeout window (idempotency/replay) | **High** |
| 3 | TTL / expiry behavior | Expired lock rows are treated as "no lock" so a stuck lock self-heals | Medium |
| 4 | Different `LockedActionType` values | Primary key construction does not collide across types for the same lock key | Medium |
| 5 | Multi-key isolation | Two different keys executing in parallel must not block each other | Medium |
| 6 | Unknown / future `Version` value | Defined behavior when stored version is unrecognized | Medium |
| 7 | Metric increment count | Waiter/polling path emits expected total number of metrics, not just `times = 1` | Low |
| 8 | Cancellation during wait | `CancellationToken` is honored while waiting on an existing lock | Medium |

### tests/ZoTools.Web.IntegrationTests/ZoCallSessionRepositoryTests.cs (9 tests)

**Irrelevant Tests:** None — all tests are relevant.

**Missing Tests:**

| # | Suggested Test | What It Would Test | Priority |
|---|----------------|-------------------|----------|
| 1 | Authorization branch (`RequestHasProductionSecret()` = false) | Repository denies / no-ops when the secret-validation gate fails; auth gate is currently untested | **High** |
| 2 | Audit logging | Every save/read of session data emits an audit call with correct operation + subject per AUTH-005 / AUTH-006 | **High** |
| 3 | PermissionService allow and deny outcomes | Repository behaves correctly for both permitted and denied access | **High** |
| 4 | Concurrent saves on the same callId | Last-write-wins vs conditional-update semantics under concurrent writers (data-integrity) | **High** |
| 5 | Cross-callId isolation | Writing session A must not affect session B | Medium |
| 6 | Multiple goals / multiple patients | Collections work past N=1 and goal-to-patient resolution holds with several patients | Medium |
| 7 | Delete / expire (TTL) | Session deletion or TTL-driven expiry behavior | Medium |
| 8 | Cancellation | Repository honors a non-`None` `CancellationToken` | Medium |
| 9 | Schema evolution | Forward/backward DTO variants deserialize tolerantly | Medium |
| 10 | Negative path (malformed input / repository error) | At least one non-happy-path case surfaces a clear error | Medium |

---

## High-Priority Gaps Summary

### Critical: systemic gaps that recur across the suite

These nine findings surfaced repeatedly across folders. They are **systemic**, not per-spec, and should be prioritized as cross-cutting workstreams.

| # | Systemic gap | Where it bites |
|---|--------------|----------------|
| 1 | **Audit logging (AUTH-005) is unverified across every mutation endpoint** | No API/service test asserts audit events for booking, cancel, reschedule, send-sms, set-sms-consent, csat-submit, insert-patient, update-patient, or admin seeding. |
| 2 | **Idempotency / replay semantics are undefined and untested for all mutating POSTs** | Voice-agent retries are likely; without idempotency keys or replay tests, double-book / double-cancel / double-SMS are real risks. |
| 3 | **Response-shape contracts use brittle field probes or `BeEquivalentTo + ExcludingMissingMembers`** | Verify snapshots (CS-006) are not in use, so contract drift slips through silently. |
| 4 | **PHI scrubbing in `HttpLoggingMiddleware` is unverified** | The middleware test file has one test — no coverage for header/body redaction, status, or exception-from-next. |
| 5 | **Cancellation-token forwarding is universally unverified across async tools** | None of the `*Impl` tests assert that an inbound `CancellationToken` reaches downstream calls. |
| 6 | **Mocks where fakes belong (TEST-002 violation)** | `IMetricRecorder`, `IAuditLoggerService`, and similar collaborators are mocked rather than replaced with recording fakes. |
| 7 | **LLM prompt/description contracts use `Contain(...)` string assertions** | Cosmetic prompt tweaks break tests without behavior changing — Verify snapshots are the right fit. |
| 8 | **No 5xx / downstream-failure paths in API tests** | Tests cover happy paths and 400-class validation only; nothing exercises Monolith/Interop timeouts or 5xx responses. |
| 9 | **Auth/permission negative tests missing in repository integration tests** | `ZoCallSessionRepository` and `LockedActionService` have no tests for unauthorized callers, permission denials, or audit failures. |

### Other high-priority, file-specific gaps

| # | Spec File | Missing Coverage |
|---|-----------|-----------------|
| 1 | CallCompletedApiTests.cs | **Zero positive coverage** — the source-of-truth endpoint for every completed call has no `ValidSecret => 200 + persistence` test (P0). |
| 2 | AdminSeedApiTests.cs | No auth coverage at all; whether admin/seed endpoints are intentionally open (security risk off-local). |
| 3 | DebugToolsApiTests.cs | No RBAC enforcement (`PhoneBotReadPhi`) and no audit-log test on PHI-bearing debug endpoints. |
| 4 | BookingApiTests.cs / ToolsApiTests.cs / AppointmentApiTests.cs | Idempotency + audit + 5xx + concurrency on the core booking/cancel/reschedule mutations. |
| 5 | InsertPatient / UpdatePatient / ValidatePatient / PatientSearch ApiTests.cs | Audit logging + 5xx paths on all patient-PHI mutations and reads. |
| 6 | SmsApiTests.cs | Eligibility gating (`canReceiveSms=false`), opt-out/consent, idempotent re-send, audit. |
| 7 | PermissionServiceTests.cs | AUTH-002 operation-on-domain matrix, expired claims, mid-session role change. |
| 8 | ZoCallSessionRepositoryTests.cs (unit + integration) | Dynamo-failure-does-not-emit-audit, audit-failure-does-not-mask, concurrent-access integrity. |
| 9 | TimezoneServiceTests.cs / availability + reschedule services | DST spring-forward/fall-back boundary math (known risk per SQUAWK-6090). |
| 10 | Integration projects (both) | Both are placeholders (`1+1==2`) — zero real end-to-end coverage of agent-tool orchestration. |

### Weak / shallow tests needing strengthening

| Spec File | Test | Issue |
|-----------|------|-------|
| SelectProviderToolsApiTests.cs | SearchProviders_ReturnsValidResponse | Asserts only `NotBeNull()`; empty list passes — no content check. |
| SelectLocationToolsApiTests.cs | FindLocationsByName_ReturnsValidResponse | `BeOneOf(Found, NoMatchesFound)` — "happy or empty" smoke test. |
| PracticeInfoApiTests.cs | GetPracticeInfo_ReturnsAllResponseFields | Assertions wrapped in `if Count > 0`; silently passes on empty arrays. |
| UpdatePatientApiTests.cs | WithSeededPatient / WithAllFields_Returns200 | Status-only; no response-body assertion. |
| IsNewPatientServiceTests.cs | *_AllValues_DoNotThrow (×3) | "DoNotThrow" assertions; no explicit expected outcome per value. |
| ValidationHelpersTests.cs | SanitizePhoneNumber_UnicodeEscapedPlus | Input/expected both literal — does not exercise the unicode case its name claims. |

### Probable-bug documentation (tests that lock in questionable behavior)

| Spec File | Test | Concern |
|-----------|------|---------|
| SelectTimeslotToolsApiTests.cs | SearchAvailability_RescheduleWithNoSession_Throws | Expects 500; per API-001 should be 404/400. |
| AppointmentApiTests.cs | UpdateRescheduleLocation_NoSessionForCall_Returns200WithTransferDirective | Returns 200 while sibling endpoints return 404 — inconsistent. |
| PatientSearchApiTests.cs | SearchPatients_WithTestSecret_Returns500 | Documents a 500 as contract. |
| BookingServiceTests.cs | ExecuteBooking_UnauthenticatedPatient_NullGender_DefaultsToFemale | Surprising default (gender bias) — flag for product review. |
| BookingServiceTests.cs | ExecuteBooking_WhenValidationThrows_ProceedsWithBooking | Silently tolerates validation exceptions. |

### Test category notes

| Category | Notes |
|----------|-------|
| `[RealOnly]` tests | Several patient tests (UpdatePatient, ValidatePatient) ship paired RealOnly + Fake variants asserting the same outcome — document why both exist or consolidate. |
| `[NonParallelizable]` | AdminSeed mutates shared state; the marker hints at concurrency hazards that are themselves untested. |
| Placeholder tests | Both integration projects and `ZoTools.Web.UnitTests/ExampleTests.cs` contain only `1+1==2` placeholders — recommend deletion once real coverage lands. |

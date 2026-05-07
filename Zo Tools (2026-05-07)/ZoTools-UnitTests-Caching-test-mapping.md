# Caching - Unit Test Mapping

> Repo: [Zocdoc/zo-tools](https://github.com/Zocdoc/zo-tools)  
> Folder: `tests/ZoTools.UnitTests/Caching/`  
> Generated: 2026-05-07

## CacheEntryTests.cs

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|-------------------|-----------|---------------|-------|---------|-------|
| 1 | | CacheEntry_StoresValueAndExpiration | Constructor of `CacheEntry<T>` correctly stores Value and ExpiresAtUtc | Arrange: build a UTC DateTime; Act: instantiate `CacheEntry<string>("cached-data", expiresAt)`; Assert: properties match. | Validates simple data record construction. | In scope: property persistence. Out of scope: expiration logic, type variance. |

### Suggested Missing Tests
- **CacheEntry_WithNullValue_StoresNull** — `CacheEntry<T>` is generic; ensure null reference values are accepted (or rejected) deterministically.
- **CacheEntry_WithDefaultDateTime_HandlesGracefully** — A `default(DateTime)` (i.e., MinValue) could cause downstream issues; verify the constructor's contract.
- **CacheEntry_WithNonUtcDateTime_BehaviorIsExplicit** — The field is named ExpiresAtUtc; assert what happens when a Local/Unspecified DateTime is supplied.

### Improvement Suggestions / Irrelevant Tests
- **Single-test file** — Only one happy-path test exists. The class is trivially small but at minimum it should also assert immutability semantics if the type is a record.

## CacheHeaderHelperTests.cs

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|-------------------|-----------|---------------|-------|---------|-------|
| 1 | | SetCacheHeaders_WhenHit_SetsHitHeaderToTrue | Hit response sets the `CacheHitHeader` to "true" | Build CacheResult(IsHit=true, ttl=3min); call helper; assert header value. | Hit-path header writing. | In scope: hit header. Out of scope: TTL math. |
| 2 | | SetCacheHeaders_WhenMiss_SetsHitHeaderToFalse | Miss response sets the `CacheHitHeader` to "false" | Build CacheResult(IsHit=false, ttl=null); call helper; assert header value. | Miss-path header writing. | In scope: miss header. Out of scope: presence of TTL header. |
| 3 | | SetCacheHeaders_WhenHitWithTtl_SetsTtlHeaderInSeconds | TTL header is written in whole seconds when TTL provided | Build CacheResult(IsHit=true, ttl=245s); call helper; assert TTL header equals "245". | TTL serialization in seconds. | In scope: integer TTL conversion. Out of scope: sub-second precision. |
| 4 | | SetCacheHeaders_WhenMiss_DoesNotSetTtlHeader | Miss skips TTL header entirely | Build CacheResult(IsHit=false, ttl=null); call helper; assert header absent. | TTL header omitted on miss. | In scope: header absence. Out of scope: any other headers. |
| 5 | | SetCacheHeaders_WhenHitWithNullTtl_DoesNotSetTtlHeader | Hit with null TTL still skips TTL header | Build CacheResult(IsHit=true, ttl=null); call helper; assert header absent. | Defensive null-TTL handling. | In scope: null TTL safety. Out of scope: TTL clamp behavior. |

### Suggested Missing Tests
- **SetCacheHeaders_WhenHitWithFractionalTtl_RoundsCorrectly** — Confirm e.g. TTL of 245.7s either rounds, truncates, or floors deterministically.
- **SetCacheHeaders_WhenHitWithZeroTtl_WritesZeroOrSkips** — Edge case for an immediately-expiring cache entry.
- **SetCacheHeaders_WhenHitWithNegativeTtl_BehaviorIsExplicit** — Negative TTL should not silently leak as a header value.
- **SetCacheHeaders_OverwritesPreviouslySetHeaders** — If the same response already carries headers, ensure expected idempotency / overwrite semantics.

### Improvement Suggestions / Irrelevant Tests
- **Tests share `SetCacheHeaders` private helper** — Coverage is good and well-factored; no concerns.

## CacheResultTests.cs

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|-------------------|-----------|---------------|-------|---------|-------|
| 1 | | CacheResult_WithHit_HasExpectedProperties | Hit cache result records value, IsHit=true, and provided TTL | Build CacheResult(value, IsHit=true, 3m); assert properties. | Hit constructor wiring. | In scope: hit fields. Out of scope: TTL validation. |
| 2 | | CacheResult_WithMiss_HasNullTtl | Miss cache result records value, IsHit=false, null TTL | Build CacheResult(value, IsHit=false, null); assert properties. | Miss constructor wiring. | In scope: miss fields. Out of scope: TTL clamping. |

### Suggested Missing Tests
- **CacheResult_WithHitButNullTtl_AllowedOrRejected** — Document whether IsHit=true with null TTL is a valid combination.
- **CacheResult_WithMissAndNonNullTtl_BehaviorIsExplicit** — Same — is IsHit=false with a TTL legal?
- **CacheResult_GenericTypeWorks_ForReferenceAndValueTypes** — Add coverage with int/struct payloads to lock the generic contract.

### Improvement Suggestions / Irrelevant Tests
- **Coverage is minimal** — Like CacheEntryTests, only happy-path constructors are tested. If the record has equality semantics, those should be asserted.

## PhoneBotRepositoryCacheTests.cs

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|-------------------|-----------|---------------|-------|---------|-------|
| 1 | | GetPracticeInfo_WhenCacheMiss_CallsRepository | Miss path delegates to repository | Setup empty cache + repo; call GetPracticeInfo; verify repo called once. | Miss delegates downstream. | In scope: cache-miss control flow. Out of scope: response shape. |
| 2 | | GetPracticeInfo_WhenCacheMiss_ReturnsDataWithIsHitFalse | Miss returns CacheResult with IsHit=false and null TTL | Setup repo response; call once; assert Value/IsHit/TTL. | Miss result envelope. | In scope: result fields. Out of scope: header writing. |
| 3 | | GetPracticeInfo_WhenCacheMiss_IncrementsMissMetric | DataDog metric tagged is_cache_hit:False is emitted on miss | Call once; verify metric recorder invoked with metric name + tag. | Miss instrumentation. | In scope: metric tag. Out of scope: latency tags. |
| 4 | | GetPracticeInfo_WhenCacheMiss_LogsMiss | Information log "Cache miss for practice info" is written on miss | Call once; verify ILogger.Log invoked once with text containing miss message. | Miss log line. | In scope: log message text. Out of scope: structured log fields. |
| 5 | | GetPracticeInfo_WhenCacheHit_DoesNotCallRepository | Second call uses cache; repo only called once | Call twice; verify repo invoked exactly once. | Cache absorbs second call. | In scope: hit short-circuit. Out of scope: TTL. |
| 6 | | GetPracticeInfo_WhenCacheHit_ReturnsCachedDataWithIsHitTrue | Second call returns IsHit=true with cached value | Call twice; assert IsHit=true and value preserved. | Hit result envelope. | In scope: hit fields. Out of scope: cache eviction. |
| 7 | | GetPracticeInfo_WhenCacheHit_ReturnsTtl | Hit result reports a positive TTL <= 5 min | Call twice; assert TTL > 0 and <= 300s. | TTL exposed on hit. | In scope: TTL bounds. Out of scope: exact TTL. |
| 8 | | GetPracticeInfo_WhenCacheHit_IncrementsHitMetric | DataDog metric tagged is_cache_hit:True is emitted on hit | Call twice; verify metric recorder invoked once with hit tag. | Hit instrumentation. | In scope: metric tag. Out of scope: miss tag overlap. |
| 9 | | GetPracticeInfo_WhenCacheHit_LogsHit | Information log "Cache hit for practice info" is written on hit | Call twice; verify logger invoked once with hit message. | Hit log line. | In scope: log message text. Out of scope: log frequency. |
| 10 | | GetProceduresForPractice_WhenCacheMiss_CallsRepository | Procedures miss delegates to repository with correct args | Call once; verify repo invoked with practice/location/callCenter/isNewPatient args. | Procedures miss flow. | In scope: arg passing. Out of scope: response shape. |
| 11 | | GetProceduresForPractice_WhenCacheMiss_ReturnsDataWithIsHitFalse | Procedures miss returns CacheResult with IsHit=false | Call once; assert Value/IsHit/TTL. | Procedures miss envelope. | In scope: result fields. Out of scope: TTL. |
| 12 | | GetProceduresForPractice_WhenCacheMiss_IncrementsMissMetric | DataDog metric tagged miss is emitted on procedures miss | Call once; verify metric. | Procedures miss instrumentation. | In scope: metric tag. Out of scope: hit metric. |
| 13 | | GetProceduresForPractice_WhenCacheHit_DoesNotCallRepository | Second procedures call uses cache | Call twice; verify repo invoked once. | Hit short-circuit. | In scope: hit cache path. Out of scope: cache key. |
| 14 | | GetProceduresForPractice_WhenCacheHit_ReturnsCachedDataWithIsHitTrue | Second procedures call returns IsHit=true with cached value | Call twice; assert IsHit=true. | Hit envelope. | In scope: hit fields. Out of scope: serialization. |
| 15 | | GetProceduresForPractice_WhenCacheHit_ReturnsTtl | Procedures hit reports a TTL within 0-300s | Call twice; assert TTL bounds. | Procedures TTL bounds. | In scope: TTL bounds. Out of scope: exact TTL. |
| 16 | | GetProceduresForPractice_DifferentLocationIds_CachedSeparately | Cache key uses locationId so different locations cache independently | Setup repo to return different procedure sets per location; call twice with different locations; assert distinct values + repo called twice. | Cache key uniqueness on location. | In scope: keying by location. Out of scope: practice/callCenter keying. |
| 17 | | GetPracticeInfo_DifferentPhoneNumbers_CachedSeparately | Cache key uses phone number so different numbers cache independently | Setup repo to return distinct practices per phone; call twice with different phones; assert distinct values + repo called twice. | Cache key uniqueness on phone. | In scope: phone-based keying. Out of scope: TTL. |

### Suggested Missing Tests
- **Cache key uniqueness on PracticeId / CallCenterId / IsNewPatient** — Only locationId is exercised; the same test pattern should apply to all key components.
- **Concurrent_GetPracticeInfo_OnlyCallsRepositoryOnce** — Test thundering-herd safety (e.g. concurrent miss).
- **Repository_ThrowsException_NotCachedAndPropagated** — If the repo throws, the cache must not store the failure and the exception should bubble up.
- **TTL_Expiration_TriggersRefetch** — Time-based TTL expiry should cause the next call to re-hit the repo (likely via injected clock).
- **Cancellation_TokenIsRespected** — Pass a cancelled token and assert OperationCanceledException propagates without poisoning the cache.
- **NullPracticeId / NullPhoneNumber input validation** — Defensive checks for invalid arguments.
- **Repository_ReturnsNull_BehaviorIsExplicit** — Whether nulls are cached or skipped.

### Improvement Suggestions / Irrelevant Tests
- **Heavy use of `Mock<IMemoryCache>`** — Tests reimplement a `MockCacheEntry` to mimic in-memory cache semantics. Consider using a real `MemoryCache` instance to exercise true cache behavior (TTL expiry, eviction) per CS-021.
- **Logger verification asserts message text** — Brittle; prefer asserting structured log properties or use a test logger that captures entries.
- **Metric tag string-literal matching** — Tags `is_cache_hit:False` (capital F) here vs `is_cache_hit:false` (lowercase) in `SpecialityProceduresCacheTests`; inconsistency suggests these come from production code and that tag casing is not standardized.
- **Each test re-stubs the repo identically** — Could be hoisted into the SetUp or a helper to reduce duplication.

## SpecialityProceduresCacheTests.cs

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|-------------------|-----------|---------------|-------|---------|-------|
| 1 | | GetAllSpecialitiesForProcedures_WhenCacheMiss_CallsMonolithService | Miss path delegates to MonolithApiService | Empty cache; call once; verify monolith service called once. | Miss delegates downstream. | In scope: miss flow. Out of scope: response shape. |
| 2 | | GetAllSpecialitiesForProcedures_WhenCacheMiss_LogsMiss | "Cache miss for speciality procedures." Information log emitted on miss | Call once; assert logger called with miss message. | Miss log line. | In scope: log message. Out of scope: log fields. |
| 3 | | GetAllSpecialitiesForProcedures_WhenCacheMiss_IncrementsMissMetric | DataDog metric with tag is_cache_hit:false emitted on miss | Call once; verify metric recorder. | Miss instrumentation. | In scope: metric tag. Out of scope: hit tag. |
| 4 | | GetAllSpecialitiesForProcedures_WhenCacheHit_DoesNotCallMonolithService | Second call uses cache; monolith only called once | Call twice; verify monolith service called once. | Hit short-circuits. | In scope: hit caching. Out of scope: TTL. |
| 5 | | GetAllSpecialitiesForProcedures_WhenCacheHit_LogsHit | "Cache hit for speciality procedures." Information log emitted on hit | Call twice; assert logger called with hit message. | Hit log line. | In scope: log text. Out of scope: log frequency. |
| 6 | | GetAllSpecialitiesForProcedures_WhenCacheHit_IncrementsHitMetric | DataDog metric tag is_cache_hit:true emitted on hit | Call twice; verify metric. | Hit instrumentation. | In scope: metric tag. Out of scope: log overlap. |
| 7 | | GetAllSpecialitiesForProcedures_WithKnownProcedure_ReturnsItsSpecialities | Returns unioned specialty IDs for a single procedure | Call with [Procedure1Id]; assert result equals [Specialty1Id, Specialty2Id]. | Single-procedure happy path. | In scope: union behavior. Out of scope: ordering. |
| 8 | | GetAllSpecialitiesForProcedures_WithMultipleProcedures_ReturnsAllSpecialities | Unions specialties across multiple procedures | Call with [Procedure1Id, Procedure3Id]; assert result equals 3 specialties. | Multi-procedure union. | In scope: union. Out of scope: dedupe. |
| 9 | | GetAllSpecialitiesForProcedures_WithUnknownProcedure_ReturnsEmptySet | Unknown procedure produces empty set | Call with ["proc_unknown"]; assert empty. | Unknown procedure handling. | In scope: missing key. Out of scope: error case. |
| 10 | | GetAllSpecialitiesForProcedures_WithEmptyProcedureList_ReturnsEmptySet | Empty input list yields empty result | Call with []; assert empty. | Empty input. | In scope: empty list. Out of scope: null. |
| 11 | | GetAllSpecialitiesForProcedures_WithDuplicateSpecialitiesAcrossProcedures_Deduplicates | Specialty appearing in multiple procedures appears once in result | Call with two procedures sharing sp_B; assert HashSet count is 3. | Dedupe across procedures. | In scope: HashSet semantics. Out of scope: ordering. |
| 12 | | GetAllSpecialitiesForProcedures_WithProcedureWithNoSpecialities_ReturnsEmptySet | Procedure with empty SpecialtyIds list yields empty | Call with [Procedure2Id]; assert empty. | Empty-specialty procedure. | In scope: per-procedure empty list. Out of scope: null SpecialtyIds. |
| 13 | | GetAllSpecialitiesForProcedures_WithNullProcedureIds_ThrowsArgumentNullException | Null input throws ArgumentNullException | Call with null!; await should throw. | Null guard. | In scope: null guard. Out of scope: empty list. |
| 14 | | GetAllSpecialitiesForProcedures_WhenMonolithReturnsDuplicateProcedureIds_DeduplicatesSpecialities | Duplicate ProcedureId entries from monolith are merged | Setup monolith with two procedures sharing the same id; call once; assert union of specialties. | Server-side duplicate handling. | In scope: response normalization. Out of scope: caching. |
| 15 | | GetAllSpecialitiesForProcedures_WhenMonolithReturnsNullProcedureId_SkipsThatEntry | Null ProcedureId rows are skipped | Setup monolith response with one null-id procedure; call once; assert only valid procedure's specialties returned. | Null-id resilience. | In scope: null-id skip. Out of scope: logging. |
| 16 | | GetSpecialtiesByProcedure_WithKnownProcedures_ReturnsPerProcedureSpecialties | Per-procedure dictionary contains correct specialty sets | Call with [Procedure1Id, Procedure3Id]; assert keys + values per procedure. | Per-procedure mapping. | In scope: dictionary mapping. Out of scope: union. |
| 17 | | GetSpecialtiesByProcedure_WithUnknownProcedure_OmitsFromResult | Unknown procedure id is absent from dictionary | Call with ["proc_unknown"]; assert empty. | Unknown skip. | In scope: omission. Out of scope: error. |
| 18 | | GetSpecialtiesByProcedure_WithEmptyList_ReturnsEmpty | Empty input -> empty dictionary | Call with []; assert empty. | Empty input. | In scope: empty input. Out of scope: null. |
| 19 | | GetSpecialtiesByProcedure_WithProcedureWithNoSpecialties_OmitsFromResult | Procedure with empty SpecialtyIds is omitted | Call with [Procedure2Id]; assert empty. | Per-procedure empty filter. | In scope: filtered out. Out of scope: included with empty value. |
| 20 | | GetSpecialtiesByProcedure_WithNullInput_ThrowsArgumentNullException | Null input throws ArgumentNullException | Call with null!; await should throw. | Null guard. | In scope: null guard. Out of scope: validation message. |

### Suggested Missing Tests
- **Concurrency_OnlyOneMonolithCall** — Concurrent calls must coalesce to a single monolith fetch (thundering herd).
- **MonolithService_ThrowsException_NotCached** — Verify a downstream failure does not poison the cache.
- **MonolithService_ReturnsEmptyResponse_HandledGracefully** — Defensive case.
- **TTL_Expiration_TriggersRefetch** — TTL bounds are tested for `PhoneBotRepositoryCache` but missing here.
- **CancellationToken_IsHonored** — Verify `OperationCanceledException` propagates and cache is not corrupted.

### Improvement Suggestions / Irrelevant Tests
- **Tag casing inconsistency** — Uses `is_cache_hit:false` (lowercase) while `PhoneBotRepositoryCacheTests` uses `is_cache_hit:False`. Indicates non-standardized DataDog tag conventions across the cache layer.
- **Tests directly mock IMemoryCache** — Same critique as `PhoneBotRepositoryCacheTests`. Using a real `MemoryCache` would test eviction/TTL behavior and reduce mock plumbing.
- **No cache-key uniqueness coverage** — Unlike `PhoneBotRepositoryCacheTests` (which tests by location and phone number), there is no test that asserts the cache key for `GetSpecialtiesByProcedure`/`GetAllSpecialitiesForProcedures` is invariant per call (since the underlying data is global), nor any negative test for collisions.

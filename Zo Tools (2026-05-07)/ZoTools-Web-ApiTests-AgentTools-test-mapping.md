# ZoTools.Web.ApiTests - API Test Mapping (AgentTools)

> Repo: [Zocdoc/zo-tools](https://github.com/Zocdoc/zo-tools)  
> Folder: `tests/ZoTools.Web.ApiTests/`  
> Generated: 2026-05-07
>
> These are HTTP-level integration-style API tests, exercising the controller surface end-to-end with stubbed downstream services. This file covers AI-agent tool endpoints: location/provider/visit-reason/timeslot search, validation, FAQ, insurance lookup, and practice info.

## InsuranceToolsApiTests.cs

`POST /zo-tools/v1/tools/practice/{phone}/search_insurances`.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|-------------------|-----------|---------------|-------|---------|-------|
| 1 | | SearchInsurances_WithoutAuth_Returns401 | Auth missing rejected | POST without zd-token; assert 401. | Reject anonymous. | In: auth. Out: validation. |
| 2 | | SearchInsurances_WithInvalidSecret_Returns401 | Invalid secret rejected | Wrong secret; POST; assert 401. | Reject bad token. | In: secret validation. Out: body validation. |
| 3 | | SearchInsurances_WithNeitherCarrierClaimNorCarrierId_Returns400 | At-least-one input enforced | Empty body; POST; assert 400. | Either user_carrier_claim or carrier_id required. | In: cross-field validation. Out: success. |
| 4 | | SearchInsurances_WithUnknownCarrierId_Returns400 | Unknown carrier id rejected | carrier_id=ic_does_not_exist; POST; assert 400. | Reject bad foreign key. | In: foreign key check. Out: provider/location refs. |
| 5 | | SearchInsurances_WithUnknownProviderId_Returns400 | Unknown provider id rejected | provider_id=prov_does_not_exist; POST; assert 400. | Provider lookup validated. | In: provider FK. Out: location FK. |
| 6 | | SearchInsurances_WithUnknownLocationId_Returns400 | Unknown location id rejected | location_id=loc_does_not_exist; POST; assert 400. | Location lookup validated. | In: location FK. Out: success. |
| 7 | | SearchInsurances_WithCarrierId_ReturnsCarrierOnlyRow | Carrier-only result | carrier_id=Aetna; POST; assert single result with no plan. | Carrier-level row when no plan claim. | In: carrier-only mode. Out: plan-level. |
| 8 | | SearchInsurances_WithCarrierClaim_ReturnsPhoneticMatches | Phonetic match path | user_carrier_claim="aetna"; POST; assert results contain Aetna and capped at 10. | Phonetic search path. | In: phonetic match. Out: state disambiguation. |
| 9 | | SearchInsurances_WithCarrierIdAndPlanClaim_ReturnsPlans | Plan-level lookup | carrier_id=Aetna, user_plan_claim="ppo"; POST; assert all results have plan and capped at 10. | Plan-level rows. | In: carrier+plan. Out: acceptance. |
| 10 | | SearchInsurances_AcceptanceReflectsProviderLocations | Acceptance flag computed | carrier+provider+location; POST; assert single result with Accepted=Yes. | Accepted reflects provider/location combo. | In: acceptance derivation. Out: single carrier filter. |
| 11 | | SearchInsurances_WithBcbsCarrierClaimAndNoState_MayEmitDisambiguationDirective | BCBS state-disambiguation hint | user_carrier_claim="blue cross"; POST; if many BCBS matches, assert AgentInstructions present. | Soft-conditional check. | In: directive emission. Out: state-specific. |
| 12 | | SearchInsurances_WithCarrierIdAndStateCode_ReturnsResult | State-code filter | Empire BCBS + state=NY; POST; assert single Empire BCBS row. | State filter narrows to one carrier. | In: state filter. Out: cross-state behavior. |

### Suggested Missing Tests
- **SearchInsurances_WithBothCarrierIdAndCarrierClaim_PrefersOneOrErrors** — ambiguous-input behavior unspecified.
- **SearchInsurances_StateCodeWithLowercase / Mixed Case** — `usa_state_code` casing not tested.
- **SearchInsurances_Performance > 10 results capped** — capping is asserted in a couple tests but not the overflow case (e.g. force >10 raw matches and ensure cap).
- **SearchInsurances_Audit log when carrier+plan returned** — insurance is sensitive PII; audit logging assertion missing.
- **SearchInsurances_500_FromInsuranceService** — no downstream-failure path.
- **Response shape snapshot (Verify)** — `BeEquivalentTo` with `ExcludingMissingMembers` lets schema drift go silent.

### Improvement Suggestions / Irrelevant Tests
- **`MayEmitDisambiguationDirective` is conditional** — the test only asserts when bcbsMatches > 5; otherwise it does nothing. Pass a fake fixture that guarantees the branch and remove the conditional.
- **No FakeOnly category on auth tests** — auth tests run anywhere; downstream-dependent tests are FakeOnly only. Reasonable but should be documented.

---

## PracticeFaqToolsApiTests.cs

`POST /zo-tools/v1/tools/practice/{phone}/get_practice_faq`.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|-------------------|-----------|---------------|-------|---------|-------|
| 1 | | GetPracticeFaq_WithoutAuth_Returns401 | Auth missing rejected | POST without token; assert 401. | Reject anonymous. | In: auth. Out: success. |
| 2 | | GetPracticeFaq_WithInvalidSecret_Returns401 | Invalid secret rejected | Wrong secret; POST; assert 401. | Reject bad token. | In: secret. Out: body. |
| 3 | | GetPracticeFaq_GeneralTopic_ReturnsGeneralFaq | General topic returns general FAQ | topic=General; POST; assert content matches expected office-hours blurb. | Topic routing for General. | In: General topic. Out: other topics. |
| 4 | | GetPracticeFaq_PricingTopic_ReturnsPricingFaq | Pricing topic returns pricing FAQ | topic=Pricing; POST; assert content matches expected self-pay blurb. | Topic routing for Pricing. | In: Pricing topic. Out: General/Insurance. |
| 5 | | GetPracticeFaq_InsuranceTopic_ReturnsInsuranceFaq | Insurance topic returns insurance FAQ | topic=Insurance; POST; assert content matches expected card blurb. | Topic routing for Insurance. | In: Insurance topic. Out: other topics. |

### Suggested Missing Tests
- **GetPracticeFaq_UnknownTopic_Returns400** — enum out-of-range case not exercised.
- **GetPracticeFaq_MissingTopic_Returns400** — empty body case.
- **GetPracticeFaq_WhenPracticeHasNoFaq_Returns404OrEmptyContent** — practice without configured FAQ behavior unspecified.
- **GetPracticeFaq_LongFaqContentTruncation** — if there's a length cap, untested.
- **Multilingual FAQ (accept-language)** — no language-variant test.
- **Snapshot of response shape** — only Content+Topic asserted; full schema not snapshotted.

### Improvement Suggestions / Irrelevant Tests
- **Three near-identical tests** — General/Pricing/Insurance are duplicates differing only by enum value. Use `[TestCase(PracticeFaqTopic.General, ExpectedGeneralFaq)]` to consolidate.
- **Hardcoded expected strings** — fragile if marketing edits the FAQ. Either use the seeded-data approach (assert what was seeded) or pin via a fixture.

---

## PracticeInfoApiTests.cs

`GET /zo-tools/v1/tools/practice/{phone}/info`.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|-------------------|-----------|---------------|-------|---------|-------|
| 1 | | GetPracticeInfo_WithoutAuth_Returns401 | Auth missing rejected | GET without token; assert 401. | Reject anonymous. | In: auth. Out: success. |
| 2 | | GetPracticeInfo_WithWrongHeaderValue_Returns401 | Wrong secret rejected | GET with bad token; assert 401. | Reject bad token. | In: secret. Out: success. |
| 3 | | GetPracticeInfo_WithProductionSecret_Returns200 | Prod secret happy path | GET; assert PracticeId and PracticeName non-empty. | Successful lookup with prod secret. | In: lookup. Out: full shape. |
| 4 | | GetPracticeInfo_WithTestSecret_Returns200 | Test secret happy path | GET; assert PracticeId and PracticeName non-empty. | Successful lookup with test secret. | In: lookup. Out: full shape. |
| 5 | | GetPracticeInfo_ReturnsRequiredV2Fields | V2 required fields populated | GET; assert cutoffs >= 0, voice/rx/sms-msgs not null. | V2 contract honored. | In: V2 shape. Out: enum values. |
| 6 | | GetPracticeInfo_ReturnsAllResponseFields | Deep response field validation | GET; iterate providers/networks/preflight/locations/transferNumbers/rxRefill enums; assert each is one of allowed values. | Enum/shape sanity for all complex sub-objects. | In: full nested shape. Out: cache. |
| 7 | | GetPracticeInfo_FirstRequest_ReturnsCacheMissHeader | Cache MISS header on first GET | Unique phone; GET; assert X-Cache-Hit=false, no X-Cache-TTL. | First read is uncached. | In: cache miss. Out: cache hit. |
| 8 | | GetPracticeInfo_SecondRequest_ReturnsCacheHitHeaderWithTtl | Cache HIT header on second GET | Unique phone; GET twice; assert second has X-Cache-Hit=true and TTL <=300s. | Second read is cached. | In: cache hit. Out: invalidation. |
| 9 | | GetPracticeInfo_DifferentPhoneNumbers_IndependentCacheState | Per-phone cache isolation | GET phone1, then GET phone2; assert phone2 is cache MISS. | Cache key includes phone. | In: cache key isolation. Out: TTL. |

### Suggested Missing Tests
- **GetPracticeInfo_UnknownPhoneNumber_Returns404** — only happy path tested for known phone.
- **GetPracticeInfo_AfterDataChange_CacheInvalidates** — no test for invalidation when seeded data changes.
- **GetPracticeInfo_500_FromDownstream** — downstream-failure not exercised.
- **GetPracticeInfo_PhoneNumberWithSpecialCharacters** — only one URL-encoded format tested.
- **GetPracticeInfo_WithMissingNetworks/Locations_HandlesNullsGracefully** — shape test conditionally checks (`if response.NearbyLocations is { Count: > 0 }`); branch where empty is hit but not asserted as a deliberate empty-state case.
- **Schema snapshot via Verify** — would prevent silent shape drift.
- **Audit log when PHI-like fields returned** — practice info is mostly public; transfer numbers could be PII; not asserted.

### Improvement Suggestions / Irrelevant Tests
- **Test 6 (`GetPracticeInfo_ReturnsAllResponseFields`) is conditional** — every assertion is wrapped in `if response.X is { Count: > 0 }`. If a deploy returns empty arrays, the test silently passes. Use a seeded fixture that guarantees populated arrays.
- **GetWithHeadersAsync helper duplicated with InitializeCallApiTests** — extract.
- **No assertion of Cache-Control / Vary HTTP standard headers** — only the X-Cache-* custom headers.

---

## SelectLocationToolsApiTests.cs

`/zo-tools/v1/tools/practice/{phone}/{validate_location|find_locations_near_zip|find_locations_in_state|find_locations_by_name|search_locations}`.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|-------------------|-----------|---------------|-------|---------|-------|
| 1 | | ValidateLocation_ValidLocation_ReturnsConfirmed | Known location confirmed | POST validate_location with loc_001; assert Status=Confirmed and Location populated. | Happy path validation. | In: confirmed status. Out: excluded/not found. |
| 2 | | ValidateLocation_ExcludedLocation_ReturnsExcludedWithTransfer | Excluded location => transfer | POST with loc_002; assert Status=ExcludedNeedsTransfer with transfer number. | Excluded path. | In: excluded with transfer. Out: confirmed. |
| 3 | | ValidateLocation_NotFoundLocation_ReturnsNotFound | Unknown location => NotFound | POST with loc_nonexistent; assert Status=NotFound. | Not-found path returns 200 with status. | In: not-found semantics. Out: 404 vs 200. |
| 4 | | ValidateLocation_WithoutAuth_Returns401 | Auth missing rejected | POST without token; assert 401. | Reject anonymous. | In: auth. Out: validation. |
| 5 | | FindLocationsNearZip_ValidZip_ReturnsLocations | Valid zip returns matches | POST find_locations_near_zip 10001; assert Status=Found, locations non-empty. | Geo lookup. | In: zip-based. Out: invalid zip. |
| 6 | | FindLocationsNearZip_InvalidZip_ReturnsInvalidZip | "abc" zip => InvalidZip status | POST with zip="abc"; assert Status=InvalidZip. | Validation as enum status. | In: invalid input semantics. Out: 400 vs status. |
| 7 | | FindLocationsNearZip_WithoutAuth_Returns401 | Auth missing rejected | POST without token; assert 401. | Reject anonymous. | In: auth. Out: validation. |
| 8 | | FindLocationsInState_ValidState_ReturnsLocations | Full state name accepted | POST with state="New York"; assert Found, abbreviation=NY. | State name -> abbrev. | In: name lookup. Out: abbrev input. |
| 9 | | FindLocationsInState_NJ_ReturnsNJLocations | Abbreviation accepted | POST with state="NJ"; assert single location loc_004. | Abbreviation lookup. | In: abbrev input. Out: name input. |
| 10 | | FindLocationsInState_InvalidState_ReturnsInvalidState | Bogus state => InvalidState | POST state="NotAState"; assert Status=InvalidState. | Validation-as-status. | In: invalid input semantics. Out: 400 vs status. |
| 11 | | FindLocationsInState_WithoutAuth_Returns401 | Auth missing rejected | POST without token; assert 401. | Reject anonymous. | In: auth. Out: validation. |
| 12 | | FindLocationsByName_ReturnsValidResponse | Name search returns valid response | POST find_locations_by_name with "Medical Plaza"; assert Status in {Found, NoMatchesFound}. | Name search smoke test. | In: name search smoke. Out: specific match assertions. |
| 13 | | FindLocationsByName_WithoutAuth_Returns401 | Auth missing rejected | POST without token; assert 401. | Reject anonymous. | In: auth. Out: validation. |
| 14 | | SearchLocations_WithoutAuth_Returns401 | Auth missing rejected | POST without token; assert 401. | Reject anonymous. | In: auth. Out: validation. |
| 15 | | SearchLocations_InvalidSecret_Returns401 | Invalid secret rejected | POST with bad token; assert 401. | Reject bad token. | In: secret. Out: success. |
| 16 | | SearchLocations_NoFilters_ReturnsLocations | No-filter returns all | Empty body; POST; assert non-empty. | Default case. | In: defaults. Out: filtered. |
| 17 | | SearchLocations_WithZipCode_ReturnsLocations | Zip filter | zip=10001; POST; assert non-empty. | Zip filter wired. | In: zip filter. Out: claim filter. |
| 18 | | SearchLocations_WithUserClaim_ReturnsLocationsWithStatusAndReasons | User claim returns status/reasons | UserClaim="Main"; POST; assert all entries have Status in enum and UnavailableReasons not null. | Claim-driven response shape. | In: claim filter. Out: numeric filtering. |
| 19 | | SearchLocations_ResponseHasExpectedShape | Response shape sanity | Empty body; POST; assert each entry has id/name/address/Status/UnavailableReasons. | Schema sanity check. | In: shape. Out: data correctness. |

### Suggested Missing Tests
- **FindLocationsNearZip_ValidZipNoMatches_ReturnsEmptyOrNoMatchesFound** — zero-result branch unclear.
- **FindLocationsByName_FindsExactMatch / NoMatch_ReturnsNoMatchesFound** — current `BeOneOf(Found, NoMatchesFound)` is too loose; pin both deterministically.
- **ValidateLocation_AuthInvalidSecret_Returns401** — only no-auth tested; missing the invalid-secret negative for validate_location.
- **FindLocations* missing-call-id-header_Returns400** — most other tools require call-id; not enforced here?
- **SearchLocations_ZipOutsidePracticeFootprint_ReturnsEmpty** — boundary case.
- **search_locations validation** — no test for invalid combinations of filters or oversized claim string.
- **Schema-vs-impl contract** — e.g. validate_location's parameter shape should match what ToolsSchemaApiTests reports.

### Improvement Suggestions / Irrelevant Tests
- **Auth tests duplicate across each tool** — one parametrized [TestCase] driver per route would consolidate.
- **`BeOneOf(Found, NoMatchesFound)` in test 12** — that's "happy or empty," which is essentially a smoke test, not a behavioral test. Make it deterministic.
- **No test names follow consistent `Endpoint_Scenario_Outcome` casing** — minor lint.

---

## SelectProviderToolsApiTests.cs

`POST /zo-tools/v1/tools/practice/{phone}/search_providers`.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|-------------------|-----------|---------------|-------|---------|-------|
| 1 | | SearchProviders_ReturnsValidResponse | Happy path returns providers | POST with provider_name_claim="Smith"; assert Providers not null. | Smoke test. | In: smoke. Out: deep shape. |
| 2 | | SearchProviders_WithoutAuth_Returns401 | Auth missing rejected | POST without token; assert 401. | Reject anonymous. | In: auth. Out: validation. |
| 3 | | SearchProviders_MissingCallIdHeader_Returns400 | Missing call-id rejected | Omit call-id; POST; assert 400. | Required header. | In: header. Out: body. |

### Suggested Missing Tests
- **SearchProviders_InvalidSecret_Returns401** — only no-auth tested.
- **SearchProviders_EmptyClaim_ReturnsAllOrError** — empty body / empty claim behavior unspecified.
- **SearchProviders_NoMatches_ReturnsEmptyList** — zero-result branch.
- **SearchProviders_ResultShape** — Test 1 is a smoke test asserting only "not null"; no fields are validated.
- **SearchProviders_WithLocationFilter** — likely supports location filter; not tested.
- **SearchProviders_WithSpecialtyFilter** — tool likely supports it; not tested.
- **SearchProviders_PhoneticMatch_OrSpellingTolerance** — phonetic search behavior not tested.
- **SearchProviders_AuditLog / PHI handling** — provider lookups can leak roster info; not asserted.

### Improvement Suggestions / Irrelevant Tests
- **Coverage is anemic** — 3 tests total (1 happy-path smoke + 2 negatives). This is the weakest agent-tool coverage in the suite. Flag for QA backlog.
- **Test 1 only asserts `Providers.Should().NotBeNull()`** — does not check that any provider is returned. A response of empty list would pass this test.

---

## SelectVisitReasonToolsApiTests.cs

`POST /zo-tools/v1/tools/practice/{phone}/search_visit_reasons`.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|-------------------|-----------|---------------|-------|---------|-------|
| 1 | | SearchVisitReasons_WithoutAuth_Returns401 | Auth missing rejected | POST without token; assert 401. | Reject anonymous. | In: auth. Out: validation. |
| 2 | | SearchVisitReasons_WithInvalidSecret_Returns401 | Invalid secret rejected | Wrong secret with call-id; POST; assert 401. | Reject bad token. | In: secret. Out: success. |
| 3 | | SearchVisitReasons_WithQuery_ReturnsFound | Query returns capped list | user_claim="joint pain", is_new_patient=true; POST; assert non-empty and <=5 results. | Capped result list. | In: query + cap. Out: location filter. |
| 4 | | SearchVisitReasons_WithLocationId_Returns200 | Location filter wired | user_claim, location_id=loc_001, is_new_patient=true; POST; assert 200. | Location filter accepted. | In: location filter. Out: result correctness. |

### Suggested Missing Tests
- **SearchVisitReasons_WithoutCallIdHeader_Returns400** — every other tool requires call-id; not asserted here.
- **SearchVisitReasons_NoMatches_ReturnsEmpty** — empty-result branch.
- **SearchVisitReasons_IsNewPatientFalse_ReturnsExistingPatientReasons** — only is_new_patient=true is tested.
- **SearchVisitReasons_EmptyUserClaim_ReturnsBadRequestOrAll** — empty claim behavior unspecified.
- **SearchVisitReasons_ResponseShape** — only `NotBeEmpty`; full shape (visit_reason_id, display name, etc.) not asserted.
- **SearchVisitReasons_LocationIdMismatch_StillReturns** — what happens if location_id doesn't exist?

### Improvement Suggestions / Irrelevant Tests
- **Test 4 (`SearchVisitReasons_WithLocationId_Returns200`) is a status-only test** — does not check whether the location filter actually changes results. Borderline irrelevant without a content assertion.
- **Cap size 5 is hardcoded** — if business rules change, this test silently breaks. Consider exposing cap as a config and asserting via that source of truth.

---

## ValidationToolsApiTests.cs

`POST /zo-tools/v1/tools/practice/{phone}/validate_availability`.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|-------------------|-----------|---------------|-------|---------|-------|
| 1 | | ValidateAvailability_WithoutAuth_Returns401 | Auth missing rejected | POST with empty headers; assert 401. | Reject anonymous. | In: auth. Out: validation. |
| 2 | | ValidateAvailability_WithInvalidSecret_Returns401 | Invalid secret rejected | Wrong secret; POST; assert 401. | Reject bad token. | In: secret. Out: validation. |
| 3 | | ValidateAvailability_WithTestSecret_ReturnsIsValidTrue | Happy path: valid combo | provider/visit-reason/location ids; POST with test secret; assert IsValid=true, Issues=[]. | Validates combo as available. | In: positive validation. Out: invalid combo. |
| 4 | | ValidateAvailability_WithProductionSecret_Returns200 | Prod secret happy path | POST with prod secret; assert 200. | Tier independence. | In: secret variants. Out: result correctness. |

### Suggested Missing Tests
- **ValidateAvailability_InvalidProvider_ReturnsIsValidFalseWithIssues** — entire negative path is missing. Today only the happy "true" case is covered.
- **ValidateAvailability_InvalidLocation_ReturnsIsValidFalseWithIssues**
- **ValidateAvailability_InvalidVisitReason_ReturnsIsValidFalseWithIssues**
- **ValidateAvailability_MissingFields_Returns400** — body validation not exercised.
- **ValidateAvailability_MissingCallIdHeader_Returns400** — most other agent tools enforce call-id.
- **Issues array shape** — when populated, what fields does it include? Untested.

### Improvement Suggestions / Irrelevant Tests
- **Test 4 only asserts 200** — the prod-secret variant duplicates test 3 without checking content. Either consolidate or add a body assertion.
- **Coverage is the weakest in the suite alongside SelectProvider** — only 4 tests, one of which is essentially a duplicate. Flag for QA backlog.
- **`Issues = System.Array.Empty<object>()` assertion against typed array** — `BeEquivalentTo` will pass even if `Issues` is null; consider explicit `.Should().BeEmpty()`.

---

## SelectTimeslotToolsApiTests.cs

`POST /zo-tools/v1/tools/practice/{phone}/search_availability` and `/get_next_available`.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|-------------------|-----------|---------------|-------|---------|-------|
| 1 | | SearchAvailability_WithoutAuth_Returns401 | Auth missing rejected | POST without token; assert 401. | Reject anonymous. | In: auth. Out: validation. |
| 2 | | SearchAvailability_WithInvalidSecret_Returns401 | Invalid secret rejected | Wrong secret; POST; assert 401. | Reject bad token. | In: secret. Out: validation. |
| 3 | | SearchAvailability_WithAppointmentIdButNoPatientId_ReturnsTimeslots | Appointment-only reschedule path returns timeslots | Seed call session; POST with appointment_id only; assert 200 with non-empty timeslots; cleanup. | Implicit-patient reschedule. | In: implicit patient. Out: explicit patient. |
| 4 | | SearchAvailability_WithoutVisitReasonId_Returns400 | Missing visit_reason_id rejected | Body missing visit_reason_id; POST; assert 400. | Body validation. | In: required field. Out: optional fields. |
| 5 | | SearchAvailability_MissingCallIdHeader_Returns400 | Missing call-id rejected | Omit call-id; POST; assert 400. | Required header. | In: header. Out: body. |
| 6 | | SearchAvailability_WithTestSecret_ReturnsTimeslots | Happy path returns timeslots | Headers+body; POST; assert 200 and non-empty timeslots. | Timeslot search smoke. | In: smoke. Out: deep shape. |
| 7 | | SearchAvailability_WithEmptyProviderIds_ReturnsTimeslotsForAllProviders | No provider filter | provider_ids=[]; POST; assert non-empty. | Empty filter -> all providers. | In: empty filter. Out: provider filtering. |
| 8 | | SearchAvailability_TimeslotsAreSortedByStartTime | Output sorted ascending | POST; assert timeslots are in ascending start time order. | Sort guarantee. | In: ordering. Out: dedup. |
| 9 | | SearchAvailability_WithEndDateBeforeStartDate_Returns400 | Inverted date range rejected | start=Apr10, end=Apr1; POST; assert 400. | Date range validation. | In: range check. Out: success. |
| 10 | | SearchAvailability_WithMalformedStartDate_Returns400 | Bad start date format rejected | start_date="not-a-date"; POST; assert 400. | Format validation. | In: parse error. Out: success. |
| 11 | | SearchAvailability_WithMalformedEndDate_Returns400 | Bad end date format rejected | end_date="2026/04/10"; POST; assert 400. | Format validation. | In: parse error. Out: success. |
| 12 | | GetNextAvailable_WithoutAuth_Returns401 | Auth missing rejected | POST without token; assert 401. | Reject anonymous. | In: auth. Out: validation. |
| 13 | | GetNextAvailable_WithInvalidSecret_Returns401 | Invalid secret rejected | POST with bad token; assert 401. | Reject bad token. | In: secret. Out: validation. |
| 14 | | GetNextAvailable_WithoutVisitReasonId_Returns400 | Missing visit_reason_id rejected | Omit; POST; assert 400. | Body validation. | In: required field. Out: success. |
| 15 | | GetNextAvailable_MissingCallIdHeader_Returns400 | Missing call-id rejected | Omit call-id; POST; assert 400. | Required header. | In: header. Out: body. |
| 16 | | GetNextAvailable_WithTestSecret_ReturnsTimeslots | Happy path returns timeslots | POST with test secret; assert 200 and non-empty. | Successful next-avail lookup. | In: smoke. Out: deep shape. |
| 17 | | GetNextAvailable_TimeslotsAreSortedByStartTime | Output sorted | POST; assert timeslots in ascending order. | Sort guarantee. | In: ordering. Out: count. |
| 18 | | SearchAvailability_WithTimeFilter_ReturnsOk | Time filter (Before10Am) works | time_filter=Before10Am; POST; assert 200. | Time filter wired. | In: filter. Out: result correctness. |
| 19 | | SearchAvailability_WithSeededAgentInstructions_ReturnsDirectivesInResponse | Seeded directive surfaces | Seed availability with AgentInstructions; POST; assert response.Directives.AgentInstructions matches; cleanup. | Directives passthrough. | In: directive surfacing. Out: directive composition. |
| 20 | | SearchAvailability_WithSeededSmallTimeslotList_DirectivesIsNull | No directives when no instruction seeded | Seed without AgentInstructions; POST; assert Directives=null. | Default-null directives. | In: default. Out: instruction trigger. |
| 21 | | SearchAvailability_WithPatientAndApptId_ReturnsTimeslots | Reschedule path | Seed call session with appointment+rescheduleParams; POST with patient_id+appointment_id; assert non-empty timeslots; cleanup. | Reschedule lookup. | In: explicit reschedule. Out: cancellation. |
| 22 | | SearchAvailability_RescheduleWithMissingCallId_Throws | Reschedule without call-id => 400 | POST with reschedule body and no call-id; assert 400. | Reschedule path requires session. | In: header required for reschedule. Out: success. |
| 23 | | SearchAvailability_RescheduleWithNoSession_Throws | Reschedule with no seeded session => 500 | POST with reschedule body, no session seeded; assert 500. | Missing session throws. | In: error semantics. Out: 4xx. |
| 24 | | SearchAvailability_RescheduleWithNullRescheduleParams_ReturnsBadRequest | Session lacks rescheduleParams => 400 | Seed session without reschedule params; POST; assert 400. | Bad reschedule state. | In: 400 vs 500. Out: success. |

### Suggested Missing Tests
- **SearchAvailability_PaginationOrTimeslotCap** — no test of result-cap behavior.
- **SearchAvailability_DateRangeBeyondMax_Returns400** — no upper bound on date range tested.
- **SearchAvailability_DurationOverride_AffectsResults** — `duration_override` accepted in body; no test of effect.
- **SearchAvailability_NoMatchingTimeslots_ReturnsEmpty** — zero-result branch missing.
- **SearchAvailability_RescheduleWithNoSession_Returns500** — test 23 expects 500 (server error). Per API standards (API-001), this should likely be 404 or 400 — flag as a probable bug or missing 4xx mapping.
- **GetNextAvailable_NoMatches_ReturnsEmpty** — zero-result branch missing.
- **Schema-vs-impl contract** — assert that ToolsSchema's `search_availability` parameters match the body shape this endpoint accepts.
- **PHI handling** — uses synthetic ids; no patient-name leakage; ok but no explicit assertion.

### Improvement Suggestions / Irrelevant Tests
- **Test 23 expects 500** — surfacing a 500 to the caller for a missing session is poor API hygiene; should be 404 or 400 like test 24. Flag this test as documenting a probable bug rather than desired behavior.
- **`SeedZoCallSession` is duplicated across 4+ files** with subtle variations. Extract to shared test fixture.
- **Tests 8 and 17 (sort order)** — could be one parametrized test; nearly identical.

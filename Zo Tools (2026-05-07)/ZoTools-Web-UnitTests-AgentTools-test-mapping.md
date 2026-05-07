# ZoTools.Web.UnitTests / Agent Tools Implementations - Unit Test Mapping

> Repo: [Zocdoc/zo-tools](https://github.com/Zocdoc/zo-tools)
> Folder: `tests/ZoTools.Web.UnitTests/` (subset: `*AgentToolsImplTests.cs`)
> Generated: 2026-05-07

This file maps unit tests for the per-agent tool implementation classes that back the schemas declared in `Providers/`. Each `*AgentToolsImpl` is the controller-side implementation of the tools the LLM calls during a Zo voice call. The remaining root-level specs (Ab, Appointment, CallCompleted, etc.) live in `ZoTools-Web-UnitTests-Other-test-mapping.md`.

Files mapped here:
- GreetingAgentToolsImplTests.cs
- ManageAppointmentAgentToolsImplTests.cs
- SelectLocationAgentToolsImplTests.cs
- SelectProviderAgentToolsImplTests.cs
- SelectTimeslotAgentToolsImplTests.cs
- SelectVisitReasonAgentToolsImplTests.cs

---

## GreetingAgentToolsImplTests.cs

Tests `GetTransferNumber` - the greeting agent's lookup of a per-call-center transfer number. The impl reads from a static or DI-bound mapping and returns 200 with the resolved number wrapped in a list.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | Yes | GetTransferNumber_ValidCallCenterNumber_ReturnsOKWithTransferNumber | Happy path: known call-center number returns 200 with the mapped transfer number | Call GetTransferNumber with valid number | 200 OK, transfer number list contains expected number | Tool impl |
| 2 | Yes | GetTransferNumber_DifferentNumbers_ReturnsCorrespondingNumber ([TestCase x3]) | Lookup differentiates by call-center number | Pass +11111111111, +12222222222, +13333333333 | Each yields its mapped transfer number | Tool impl |
| 3 | Yes | GetTransferNumber_EmptyString_ReturnsOKWithEmptyStringInList | Empty input -> 200 with empty list element (no crash) | Pass "" | 200 OK, empty string in result | Tool impl (edge) |

### Suggested Missing Tests

- Unknown call-center number (not in the mapping) - is it 404 or 200-with-empty? Today undefined.
- Null input - argument validation behavior.
- Non-E.164 input (`123`, `abc`) - validation behavior.
- Lookup is logged (or not) for observability when a number is missing.

### Improvement Suggestions / Irrelevant Tests

- All three tests are valid but coverage is shallow. The greeting agent is the entry point of every call; failure here transfers the caller to the wrong queue. Strongly recommend adding the unknown / null / malformed cases above.
- This file does NOT test `get_practice_faq` (the second tool exposed by the greeting provider). The FAQ impl is in `PracticeFaqToolsImplTests.cs` which is fine - call out that ownership is split so a future reader knows where to look.

---

## ManageAppointmentAgentToolsImplTests.cs

Tests two tools: `GetAppointments` (read upcoming + recent past appointments for a patient) and `CancelAppointment` (preview/confirm cancel). Also tests private mappers `MapApptBlockedReason`, `MapStatus`, `MapLocationType`, `MapToExternalPatientAppointments`, and `FilterUpcomingAndRecentPrevious`. `[SetUp]` at line 27 creates the system-under-test with mocked `IAppointmentManagementService`, `IPatientInfoService`, and `IAppointmentSessionRepository`.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | Yes | GetAppointments_WhenPatientInfoServiceReturnsAppointments_ReturnsOKWithMappedAppointments | Service returns N appts -> 200 with mapped DTOs | Mock service to return appts; call GetAppointments | 200 OK, mapped fields | Tool impl |
| 2 | Yes | GetAppointments_WhenRescheduleNotBlocked_ReturnsNullRescheduleBlockedReason | No blocked reason from upstream -> response field is null | Mock with null blocked reason | response.RescheduleBlockedReason is null | Tool impl |
| 3 | Yes | GetAppointments_WhenRescheduleBlocked_ReturnsMappedRescheduleBlockedReason | Blocked reason from upstream is mapped to api enum | Mock with blocked reason | Mapped value returned | Tool impl |
| 4 | Yes | GetAppointments_WhenPatientInfoServiceReturnsNull_ReturnsNotFound | Null service result -> 404 | Mock null | NotFound | Tool impl |
| 5 | Yes | GetAppointments_WhenPatientInfoServiceReturnsEmptyList_ReturnsOKWithEmptyList | Empty list -> 200 with empty appointments array (not 404) | Mock empty | 200 OK, empty list | Tool impl |
| 6 | Yes | GetAppointments_CallsPatientInfoServiceWithCorrectParameters | Service is called with correct params from request | Capture mock invocation args | Args match request | Tool impl |
| 7 | Yes | GetAppointments_WhenLocationIdsOmitted_PassesEmptyListToService | Missing location_ids in request -> empty list to service (not null) | Capture args | Empty list | Tool impl (contract) |
| 8 | Yes | GetAppointments_MapsLocationTypeToApiModel | Service location-type enum mapped to api enum | Mock with each location type | Mapping correct | Tool impl |
| 9 | Yes | GetAppointments_DefaultCount_IncludesRecentPreviousAppointments | Default `previousCount` includes recent past appts | Mock past + future | Recent past included | Tool impl |
| 10 | Yes | GetAppointments_WithPreviousCountZero_ExcludesPastAppointments | previousCount=0 excludes all past appts | Mock past + future | Only future returned | Tool impl |
| 11 | Yes | CancelAppointment_WhenAppointmentManagementServiceSucceeds_ReturnsOKWithCancellationId | Happy path | Mock service success | 200 OK with cancellation id | Tool impl |
| 12 | Yes | CancelAppointment_WhenAppointmentManagementServiceThrows_ReturnsNotFound | Service throws -> 404 | Mock throw | NotFound | Tool impl (error mapping) |
| 13 | Yes | CancelAppointment_WhenUnauthorized_ReturnsNotFound | Unauthorized exception -> 404 (not 401, intentional) | Mock unauthorized | NotFound | Tool impl (security/UX) |
| 14 | Yes | CancelAppointment_CallsAppointmentManagementServiceWithCorrectParameters | Args passed correctly | Capture args | Match | Tool impl |
| 15 | Yes | CancelAppointment_WhenMissingFields_ResolvesFromCache | When ehr_patient_id/sync_id missing, resolve from session cache | Provide partial request, set cache | Cache-resolved values used | Tool impl |
| 16 | Yes | CancelAppointment_WhenMissingFields_CallsServiceWithCachedValues | Verify the service receives the cached values | Capture args | Cached values passed | Tool impl |
| 17 | Yes | CancelAppointment_WhenMissingFieldsAndAppointmentNotInCache_ReturnsNotFound | Missing fields + no cache hit -> 404 | No cache; partial request | NotFound | Tool impl |
| 18 | Yes | CancelAppointment_WhenMissingFieldsAndNoSession_ReturnsNotFound | Missing fields + no session at all -> 404 | No session; partial request | NotFound | Tool impl |
| 19 | Yes | CancelAppointment_WhenFieldsProvided_UsesProvidedValues | If caller provides full fields, do not consult cache | Full request | Cache not read; provided values used | Tool impl |
| 20 | Yes | CancelAppointment_WhenOnlyEhrPatientIdMissing_ResolvesFromCache | Partial: only ehr_patient_id missing | Provide sync_id only | EHR id resolved from cache | Tool impl |
| 21 | Yes | CancelAppointment_WhenOnlySyncIdMissing_ResolvesFromCache | Partial: only sync_id missing | Provide ehr_patient_id only | sync_id resolved from cache | Tool impl |
| 22 | Yes | CancelAppointment_WhenCachedAppointmentHasCancelBlockedReason_ReturnsOKWithMessage | Cancel-blocked appt -> 200 with explanation | Set cache with blocked reason | 200 OK with message | Tool impl |
| 23 | Yes | CancelAppointment_WhenCachedAppointmentHasCancelBlockedReason_DoesNotCallCancelService | And cancel service is not invoked | Verify mock not called | Times.Never | Tool impl |
| 24 | Yes | CancelAppointment_WhenCachedAppointmentHasNoCancelBlockedReason_ProceedsWithCancel | No blocked reason -> normal flow proceeds | Set cache without blocked reason | Service is called | Tool impl |
| 25 | Yes | MapApptBlockedReason_WhenNull_ReturnsNull | Null mapper input | Pass null | Null | Mapper |
| 26 | Yes | MapApptBlockedReason_WhenWithinCutoffHours_ReturnsMappedValue | Single value mapping | Pass enum | Maps | Mapper |
| 27 | Yes | MapApptBlockedReason_WhenProvLocMappingIssue_ReturnsMappedValue | Single value mapping | Pass enum | Maps | Mapper |
| 28 | Yes | MapApptBlockedReason_WhenMultipleMappedProviders_ReturnsMappedValue | Single value mapping | Pass enum | Maps | Mapper |
| 29 | Yes | MapApptBlockedReason_WhenMultipleMappedHybridLocations_ReturnsMappedValue | Single value mapping | Pass enum | Maps | Mapper |
| 30 | Yes | MapApptBlockedReason_WhenExcludedProvider_ReturnsMappedValue | Single value mapping | Pass enum | Maps | Mapper |
| 31 | Yes | MapApptBlockedReason_WhenExcludedLocation_ReturnsMappedValue | Single value mapping | Pass enum | Maps | Mapper |
| 32 | Yes | MapApptBlockedReason_WhenVisitReasonMappingIssue_ReturnsMappedValue | Single value mapping | Pass enum | Maps | Mapper |
| 33 | Yes | MapApptBlockedReason_WhenExcludedVisitReason_ReturnsMappedValue | Single value mapping | Pass enum | Maps | Mapper |
| 34 | Yes | MapApptBlockedReason_AllValues_DoesNotThrow ([Values]) | Exhaustive enum coverage | Pass every enum value | None throws | Mapper (CS-021 friendly) |
| 35 | Yes | MapApptBlockedReason_InvalidValue_ThrowsArgumentOutOfRangeException | Out-of-range cast input throws | Cast invalid int; call | Throws | Mapper |
| 36 | Yes | MapStatus_WhenNull_ReturnsNone | Null status maps to None (not throws) | Pass null | None | Mapper |
| 37 | Yes | MapStatus_WhenNone_ReturnsNone | Identity for None | Pass None | None | Mapper |
| 38 | Yes | MapStatus_WhenConfirmed_ReturnsConfirmed | Single-value mapping | Pass | Maps | Mapper |
| 39 | Yes | MapStatus_WhenCancelled_ReturnsCancelled | Single-value mapping | Pass | Maps | Mapper |
| 40 | Yes | MapStatus_WhenUnconfirmed_ReturnsUnconfirmed | Single-value mapping | Pass | Maps | Mapper |
| 41 | Yes | MapStatus_WhenMatchedToOtherAppointment_ReturnsMatchedToOtherAppointment | Single-value mapping | Pass | Maps | Mapper |
| 42 | Yes | MapStatus_AllValues_DoesNotThrow ([Values]) | Exhaustive enum coverage | Each value | None throws | Mapper |
| 43 | Yes | MapStatus_InvalidValue_ReturnsNone | Unknown value falls back to None (NOT throws) | Cast invalid int | None | Mapper (lenient) |
| 44 | Yes | MapLocationType_Unknown_ReturnsUnknown | Identity | Pass | Maps | Mapper |
| 45 | Yes | MapLocationType_PhysicalOnly_ReturnsPhysicalOnly | Identity | Pass | Maps | Mapper |
| 46 | Yes | MapLocationType_VirtualOnly_ReturnsVirtualOnly | Identity | Pass | Maps | Mapper |
| 47 | Yes | MapLocationType_Hybrid_ReturnsHybrid | Identity | Pass | Maps | Mapper |
| 48 | Yes | MapLocationType_AllValues_DoesNotThrow ([Values]) | Exhaustive enum coverage | Each value | None throws | Mapper |
| 49 | Yes | MapLocationType_InvalidValue_ThrowsArgumentOutOfRangeException | Out-of-range cast throws | Cast invalid int | Throws | Mapper (strict, in contrast to MapStatus) |
| 50 | Yes | MapToExternalPatientAppointments_BlockedReasons_SetBlockedBooleansTrue | When server returns blocked reasons, the api booleans (`isCancelBlocked`, `isRescheduleBlocked`) are true | Build appts with blocked reasons | Booleans true | Mapper |
| 51 | Yes | MapToExternalPatientAppointments_NoBlockedReasons_SetBlockedBooleansFalse | None blocked -> booleans false | Build appts without blocked reasons | Booleans false | Mapper |
| 52 | Yes | MapToExternalPatientAppointments_MixedBlockedReasons_SetCorrectBooleans | One blocked / one not -> booleans correct per appt | Mixed list | Per-appt booleans correct | Mapper |
| 53 | Yes | FilterUpcomingAndRecentPrevious_FutureAppointment_IsIncluded | Future appt always included | Future-dated | Included | Filter |
| 54 | Yes | FilterUpcomingAndRecentPrevious_PastAppointment_WithinPreviousCount_IsIncluded | Past appt within `previousCount` window included | Past-dated, count=N | Included | Filter |
| 55 | Yes | FilterUpcomingAndRecentPrevious_UnparseableStartTime_IsIncluded | Cannot parse start time -> include (do not silently drop) | Unparseable string | Included | Filter (defensive) |
| 56 | Yes | FilterUpcomingAndRecentPrevious_MixOfAppointments_FiltersCorrectly | Mixed list filtered per rules | Build mix | Subset matches expected | Filter |
| 57 | Yes | FilterUpcomingAndRecentPrevious_EmptyList_ReturnsEmptyList | Empty input -> empty output | Empty | Empty | Filter |
| 58 | Yes | FilterUpcomingAndRecentPrevious_PreviousCountZero_ExcludesAllPastAppointments | count=0 excludes all past | Past list, count=0 | Empty (past dropped) | Filter |
| 59 | Yes | FilterUpcomingAndRecentPrevious_PreviousCountLessThanPastAppointments_ReturnsOnlyMostRecent | count<past.length returns most recent only | Past list, count<n | Most recent N returned | Filter |
| 60 | Yes | FilterUpcomingAndRecentPrevious_PreviousCountExceedsPastAppointments_ReturnsAllPast | count>past.length returns all past | Past list, count=large | All returned | Filter |

### Suggested Missing Tests

- `GetAppointments` returns appointments sorted (start time ascending) - sort order is not asserted.
- `GetAppointments` should redact / not return PHI fields the agent does not need (e.g., insurance member id) - schema-level protection.
- `CancelAppointment` should emit a structured log/observability event when blocked reason is hit (helps QA debug).
- `CancelAppointment` Spanish-language path - the `AppointmentImplTests.CancelAppointment_SpanishLanguage_*` tests cover the legacy controller, but this Tool impl's i18n behavior is not tested here.
- `MapStatus` invalid-value path returning `None` is inconsistent with `MapApptBlockedReason` and `MapLocationType` which throw. Either reconcile with a deliberate convention or add a test that pins the lenient behavior with a code comment.
- Boundary on `FilterUpcomingAndRecentPrevious`: an appointment whose start time equals "now" - included or excluded?
- Boundary on `FilterUpcomingAndRecentPrevious`: very large `previousCount` (e.g., int.MaxValue) - performance / exception?

### Improvement Suggestions / Irrelevant Tests

- The single-value mapper tests (#26-#33, #38-#41, #44-#47) plus the `[Values]` exhaustive test are partially redundant. The exhaustive `_AllValues_DoesNotThrow` is the better test because it auto-grows when a new enum value lands. Consider replacing the per-value tests with a `[TestCase]`-driven mapping table.
- All tests are otherwise relevant. Mapper-only tests qualify as legitimate (per CS-021) because they exercise pure functions, not just mocks.
- Consider Verify snapshots for the full DTO produced by `MapToExternalPatientAppointments` for representative inputs - locks down field-level regressions.

---

## SelectLocationAgentToolsImplTests.cs

Tests `ValidateLocation`, `FindLocationsNearZip`, `FindLocationsInState`, `FindLocationsByName`, and `SearchLocations` (the unified search). Most tests delegate to `ILocationService` / `ILocationSearchService` mocks and assert request mapping plus enum mapping for status/availability fields. Heavy use of `[Values]` for exhaustive enum coverage is a strength.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | Yes | ValidateLocation_DelegatesToLocationService | Happy path delegates to service with mapped args | Mock; call; verify args | Service called with mapped args | Tool impl |
| 2 | Yes | ValidateLocation_MapsExcludedStatus | Excluded status from service is mapped to api enum | Mock excluded | Mapping correct | Tool impl |
| 3 | Yes | ValidateLocation_MapsNotFoundStatus | NotFound status mapped | Mock NotFound | Mapping correct | Tool impl |
| 4 | Yes | ValidateLocation_MapsAllStatuses ([Values]) | Exhaustive: every Service.ValidateLocationStatus maps cleanly | Iterate all values | None throws | Tool impl (mapper) |
| 5 | Yes | FindLocationsNearZip_DelegatesToLocationService | Delegation with arg mapping | Mock; call; verify args | Service called correctly | Tool impl |
| 6 | Yes | FindLocationsNearZip_InvalidZip_ReturnsInvalidZipStatus | Invalid zip path returns InvalidZip | Mock invalid | Status maps | Tool impl |
| 7 | Yes | FindLocationsNearZip_MapsAllStatuses ([Values]) | Exhaustive enum coverage | All values | None throws | Tool impl (mapper) |
| 8 | Yes | FindLocationsInState_DelegatesToLocationService | Delegation with arg mapping | Mock; verify | Args match | Tool impl |
| 9 | Yes | FindLocationsInState_InvalidState_ReturnsInvalidStateStatus | Invalid state input -> InvalidState status | Mock invalid | Status maps | Tool impl |
| 10 | Yes | FindLocationsInState_MapsAllStatuses ([Values]) | Exhaustive | All values | None throws | Tool impl (mapper) |
| 11 | Yes | FindLocationsByName_DelegatesToLocationSearchService | Delegation to a different service | Mock; verify | Args match | Tool impl |
| 12 | Yes | FindLocationsByName_NoMatches_ReturnsNoMatchesFoundStatus | Empty match list maps to NoMatchesFound | Mock empty | Status returned | Tool impl |
| 13 | Yes | FindLocationsByName_MapsAllStatuses ([Values]) | Exhaustive | All values | None throws | Tool impl (mapper) |
| 14 | Yes | SearchLocations_DelegatesToService | Unified search delegates correctly | Mock; verify | Args match | Tool impl |
| 15 | Yes | SearchLocations_MapsAvailableStatus | Available -> Available | Mock available | Mapping | Tool impl |
| 16 | Yes | SearchLocations_MapsUnavailableStatusAndReasons | Unavailable status + reasons mapped onto each location | Mock with reasons | Mapping correct | Tool impl |
| 17 | Yes | SearchLocations_MapsAllAvailabilityStatuses ([Values]) | Exhaustive availability statuses | All values | None throws | Tool impl |
| 18 | Yes | SearchLocations_MapsAllUnavailableReasons ([Values]) | Exhaustive unavailable reasons | All values | None throws | Tool impl |

### Suggested Missing Tests

- Service throws (transient HTTP / 500) - is exception swallowed or propagated? Today untested.
- Empty input arrays (e.g., empty zip code) - validation path.
- For `FindLocationsByName`, fuzzy / case-insensitive matching is the LLM contract - test that the parameter is forwarded verbatim (no client-side trimming) so the service owns matching.
- `SearchLocations` with both filters (zip + name) - precedence behavior.
- Latency/timeout behavior (or at least cancellation token forwarding to the service).
- Logging on enum-mapping fallthrough (the [Values] tests assert no throw, but a log line on unknown values would aid debugging).

### Improvement Suggestions / Irrelevant Tests

- The pattern of "DelegatesToService" + "MapsXStatus" + "MapsAllStatuses" is consistent across all four methods - good.
- Tests #2, #3, #15, #16 individually assert single-value mappings already covered by the [Values] tests. The targeted ones earn their keep by also asserting the surrounding response shape (not just the enum). Keep both.
- No test is irrelevant.

---

## SelectProviderAgentToolsImplTests.cs

Tests `SearchProviders` - the only tool. Heavy focus on response mapping: provider list, blocked reasons, locations, virtual-flag, IPA-pronunciation cross-population, error directives. `[SetUp]` builds the impl with a mocked `IProviderSearchService`.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | Yes | SearchProviders_DelegatesToProviderSearchService | Delegation with arg mapping | Mock; verify args | Match | Tool impl |
| 2 | Yes | SearchProviders_MapsMultipleProviders | List response mapped element-by-element | Mock with N providers | All N mapped | Tool impl |
| 3 | Yes | SearchProviders_NoMatches_ReturnsEmptyList | Empty service result -> empty api list (not 404) | Mock empty | 200 OK with [] | Tool impl |
| 4 | Yes | SearchProviders_MapsBlockedReason | Blocked reason on a provider is mapped | Mock with blocked provider | Reason set on response | Tool impl |
| 5 | Yes | SearchProviders_WithErrorMessage_ReturnsDirectiveError | Service-level error message -> directive=error | Mock with error | response includes error directive | Tool impl |
| 6 | Yes | SearchProviders_MapsBlockedReasonsExactly | Multiple blocked reasons map exactly (1:1) | Mock with all enum values | Each value mapped | Tool impl (mapper) |
| 7 | Yes | SearchProviders_MapsLocationsFromServiceResult | Provider's locations list mapped onto response | Mock locations | Locations array matches | Tool impl |
| 8 | Yes | SearchProviders_MapsIsVirtualFromServiceResult | isVirtual flag mapped | Mock virtual | Flag true | Tool impl |
| 9 | Yes | SearchProviders_NullLocations_MapsToNull | Service returns null locations -> response null (not [] or NRE) | Mock null | Null | Tool impl |
| 10 | Yes | SearchProviders_MapsProviderPronunciation | Pronunciation (IPA) string mapped | Mock with pronunciation | Set on response | Tool impl |
| 11 | Yes | SearchProviders_NullProviderPronunciation_MapsToNull | Null pronunciation maps to null | Mock null | Null | Tool impl |

### Suggested Missing Tests

- Service throws - exception propagation or wrapping.
- Pagination / large-result truncation behavior (the LLM has a context budget; the impl may cap N).
- Provider with an empty-string pronunciation - is it null-coalesced or returned verbatim?
- Locations are sorted (e.g., by distance) - sort order not asserted.
- Description / boilerplate fields are localized - i18n behavior.
- Error directive must include a transferReason or similar - shape of the directive is not pinned.

### Improvement Suggestions / Irrelevant Tests

- Coverage is strong on mapping fidelity. None of the tests are irrelevant.
- Tests #6 (MapsBlockedReasonsExactly) and #4 (MapsBlockedReason) overlap; keep #6 because exhaustiveness matters more, but #4 reads more clearly per case.
- Consider Verify snapshots for the final response payload for representative scenarios (single provider, multiple providers, all blocked) to lock down the LLM contract.

---

## SelectTimeslotAgentToolsImplTests.cs

The largest single test file in the project (1201 lines, ~50 tests). Tests two tools (`SearchAvailability` and `GetNextAvailable`) plus the private `GetApptFromSession` helper. Both tools have multiple branches: direct params (provider/location/visit-reason explicit), reschedule path (driven by `appointment_id` + `patient_id`), patient-id-only (look up isNew from cache), default new-patient. Includes truncation-to-50 logic for context-window safety, availability-not-found event emission, and override-procedure-id logic.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | Yes | SearchAvailability_WithDirectParams_CallsAvailabilityService | Direct path: provider/location/visit-reason explicit | Mock service; call | Service called with mapped args | Tool impl |
| 2 | Yes | SearchAvailability_WithDirectParams_WithPatientId_ExistingPatient_MapsCorrectly | Direct + patient_id of existing patient -> patient type Existing | Mock existing | Mapping correct | Tool impl |
| 3 | Yes | SearchAvailability_EmptyTimeslots_ReturnsEmptyList | No availability returns empty list (not 404) | Mock empty | 200 OK with [] | Tool impl |
| 4 | Yes | SearchAvailability_InvalidSearchWindow_ReturnsBadRequest | Start>End or other invalid window -> 400 | Build invalid request | BadRequest | Tool impl (validation) |
| 5 | Yes | SearchAvailability_WithTimeFilter_PassesTimeFilterToService | time_filter forwarded to service | Mock; verify args | Filter forwarded | Tool impl |
| 6 | Yes | SearchAvailability_WithNullTimeFilter_PassesNullToService | Null time_filter -> null to service | Verify args | Null | Tool impl |
| 7 | Yes | SearchAvailability_AllTimeFilterValues_MapCorrectly ([Values]) | Exhaustive enum mapping | All values | Each maps | Tool impl (mapper) |
| 8 | Yes | SearchAvailability_WithPatientAndApptId_UsesRescheduleParams | When both ids present, use cached reschedule params (NOT direct params) | Set cache; call | Cached reschedule params used | Tool impl |
| 9 | Yes | SearchAvailability_WithPatientAndApptId_IsNewPatientTrue_MapsToPatientTypeNew | isNew true on cached appt -> patient type New | Cache; call | Mapping | Tool impl |
| 10 | Yes | SearchAvailability_WithPatientAndApptId_WithDurationInMinutes_PassesDurationOverride | Cached duration override forwarded | Cache w/ duration | Duration passed | Tool impl |
| 11 | Yes | SearchAvailability_WithPatientAndApptId_IgnoresDirectParams | Direct params from request are NOT used when reschedule path is active | Provide both; call | Direct params ignored | Tool impl (security/UX) |
| 12 | Yes | SearchAvailability_WithPatientAndApptId_MissingCallId_ThrowsArgumentException | callId required for cache lookup | Omit callId | Throws | Tool impl |
| 13 | Yes | SearchAvailability_WithOverrideAvailabilityProcedureId_UsesOverrideInsteadOfProcedureId | Override id beats normal id | Set override | Override used | Tool impl |
| 14 | Yes | SearchAvailability_WithNullOverrideProcedureId_UsesProcedureId | No override -> use original id | Null override | Original used | Tool impl |
| 15 | Yes | SearchAvailability_WithNullRescheduleParams_ReturnsBadRequestWithBlockedReason | Reschedule params null + blocked reason -> 400 with reason | Cache w/ null params + reason | 400 + reason | Tool impl |
| 16 | Yes | SearchAvailability_WithNullRescheduleParamsAndNoBlockedReason_ReturnsBadRequest | Reschedule params null + no blocked reason -> 400 (generic) | Cache w/ null params, no reason | BadRequest | Tool impl |
| 17 | Yes | SearchAvailability_WithAppointmentIdButNoPatientId_UsesRescheduleParams | apptId only (no patientId) still uses reschedule params | Cache; call | Reschedule path active | Tool impl |
| 18 | Yes | SearchAvailability_WithPatientIdOnly_CallsPatientInfoService | patientId-only triggers PatientInfoService lookup for isNew | Mock service; call | Service called | Tool impl |
| 19 | Yes | SearchAvailability_WithPatientIdOnly_IsExisting_PassesExistingPatientType | Existing patient from service -> patient type Existing | Mock existing | Type Existing | Tool impl |
| 20 | Yes | SearchAvailability_WithPatientIdOnly_IsNew_PassesNewPatientType | New patient from service -> patient type New | Mock new | Type New | Tool impl |
| 21 | Yes | SearchAvailability_WithoutPatientId_DefaultsToNewPatientType | No patientId -> default to New | No patientId | Type New | Tool impl |
| 22 | Yes | SearchAvailability_WithNullVisitReasonId_ReturnsBadRequest | Required field missing -> 400 | Null visitReasonId | BadRequest | Tool impl (validation) |
| 23 | Yes | SearchAvailability_UnderMaxTimeslots_ReturnsAllWithNullDirectives | Below cap -> all returned, no truncation directive | <50 timeslots | All returned, directives null | Tool impl (LLM context budget) |
| 24 | Yes | SearchAvailability_ExactlyMaxTimeslots_ReturnsAllWithNullDirectives | Exactly cap (50) -> still no truncation | =50 | All returned | Tool impl (boundary) |
| 25 | Yes | SearchAvailability_OverMaxTimeslots_TruncatesTo50WithAgentInstructions | >50 -> truncate to 50 + agent instruction directive | >50 | Truncated, directive set | Tool impl (LLM context budget) |
| 26 | Yes | SearchAvailability_OverMaxTimeslots_PreservesFirstTimeslots | Truncation keeps the EARLIEST 50 (not random) | >50 | First 50 in order | Tool impl |
| 27 | Yes | SearchAvailability_EmptyTimeslots_EmitsAvailabilityNotFoundEvent | Empty result emits structured event | Mock empty + event sink | Event emitted once | Tool impl (observability) |
| 28 | Yes | GetApptFromSession_SessionNotFound_ThrowsException | Helper throws when session is missing | No session | Throws | Helper |
| 29 | Yes | GetApptFromSession_AppointmentNotFound_ThrowsException | Helper throws when appt is missing | Session w/o appt | Throws | Helper |
| 30 | Yes | GetApptFromSession_ValidSession_ReturnsCachedAppointment | Helper returns the cached appt | Session w/ appt | Returns | Helper |
| 31 | Yes | GetApptFromSession_CallsRepositoryWithPatientId | Repository receives patientId for scoped lookup | Mock; verify args | patientId passed | Helper |
| 32 | Yes | GetApptFromSession_WithNullPatientId_CallsRepositoryWithNull | Null patientId is passed through | Verify | Null | Helper |
| 33 | Yes | GetApptFromSession_AppointmentMissingForPatientId_FallsBackToCrossPatientLookup | When scoped lookup misses, fall back to cross-patient | Set up only cross-patient appt | Fallback hit | Helper (resilience) |
| 34 | Yes | GetApptFromSession_AppointmentMissingEverywhere_ThrowsAfterFallback | Both scoped + fallback miss -> throws | Empty repo | Throws | Helper |
| 35 | Yes | GetNextAvailable_DelegatesToAvailabilityService | Delegation | Mock; verify | Match | Tool impl |
| 36 | Yes | GetNextAvailable_EmptyTimeslots_EmitsAvailabilityNotFoundEvent | Empty result emits event | Mock + sink | Event emitted | Tool impl (observability) |
| 37 | Yes | GetNextAvailable_WithTimeslots_DoesNotEmitAvailabilityNotFoundEvent | Non-empty result does not emit | Mock w/ slots + sink | Not emitted | Tool impl (observability) |
| 38 | Yes | GetNextAvailable_WhenArgumentException_ReturnsBadRequest | Service throws ArgumentException -> 400 | Mock throw | BadRequest | Tool impl (error mapping) |
| 39 | Yes | GetNextAvailable_WithPatientIdOnly_CallsPatientInfoService | Same as SearchAvailability sibling | | | Tool impl |
| 40 | Yes | GetNextAvailable_WithPatientIdOnly_IsExisting_PassesExistingPatientType | | | | Tool impl |
| 41 | Yes | GetNextAvailable_WithoutPatientId_DefaultsToNewPatientType | | | | Tool impl |
| 42 | Yes | GetNextAvailable_WithNullVisitReasonId_ReturnsBadRequest | | | | Tool impl (validation) |
| 43 | Yes | GetNextAvailable_WithPatientAndApptId_UsesRescheduleParams | | | | Tool impl |
| 44 | Yes | GetNextAvailable_WithPatientAndApptId_UseDurationStrategy_PassesDurationOverride | UseDuration strategy passes the override; other strategies do not | Mock w/ strategy | Conditional pass | Tool impl |
| 45 | Yes | GetNextAvailable_WithPatientAndApptId_IgnoresDirectParams | Same as #11 sibling | | | Tool impl |
| 46 | Yes | GetNextAvailable_WithPatientAndApptId_MissingCallId_ThrowsArgumentException | | | | Tool impl |
| 47 | Yes | GetNextAvailable_WithOverrideAvailabilityProcedureId_UsesOverrideInsteadOfProcedureId | | | | Tool impl |
| 48 | Yes | GetNextAvailable_WithNullOverrideProcedureId_UsesProcedureId | | | | Tool impl |
| 49 | Yes | GetNextAvailable_WithAppointmentIdButNoPatientId_UsesRescheduleParams | | | | Tool impl |
| 50 | Yes | GetNextAvailable_WithNullRescheduleParams_ReturnsBadRequestWithBlockedReason | | | | Tool impl |
| 51 | Yes | GetNextAvailable_WithNullRescheduleParamsAndNoBlockedReason_ReturnsBadRequest | | | | Tool impl |

### Suggested Missing Tests

- Boundary at 49 / 50 / 51 timeslots (only 50 and >50 are tested today; 49 is a boundary).
- The truncation directive's exact text (it's the LLM prompt instruction "ask for narrower window") is not snapshotted - a wording change would silently degrade UX.
- Cancellation token forwarding to the availability service (`SearchAvailability` is async; the `[Test]`s should pass a cancellation token).
- Time zone correctness: a search window straddling DST or with `Accept-Language` mismatch.
- AvailabilityNotFound event should include the attempted search window in its payload - shape not asserted.
- Concurrent reschedule path: appt cached but session got nulled mid-call (race) - probably handled by `GetApptFromSession_*` but worth a focused test.
- `SearchAvailability_WithDirectParams_WithPatientId_NewPatient_*` (mirror of #2 for new patients) is missing.
- The "duration_override" path is only tested in the reschedule branch; direct path with override missing.

### Improvement Suggestions / Irrelevant Tests

- This is the highest-risk file in the suite (largest blast radius if broken). Coverage is correspondingly thorough.
- Tests #18-#21 / #39-#41 / #43-#51 are very nearly mirrored between the two methods. A `[TestCaseSource]` matrix that drives both methods through the same scenarios would shrink the file by ~30% while widening coverage. Worth a refactor.
- The truncation tests (#23-#26) are excellent and protect the LLM context budget. Keep.
- No tests are irrelevant.

---

## SelectVisitReasonAgentToolsImplTests.cs

Tests `SearchVisitReasons` plus the auth-style validation (session/patient consistency, isNewPatient vs generated patientId rules). Notable that this is the only AgentTools impl that does cross-validation between caller-supplied `is_new_patient` and the patientId pattern (generated vs authenticated).

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | Yes | SearchVisitReasons_WhenFound_ReturnsMappedResult | Happy path | Mock results | Mapped | Tool impl |
| 2 | Yes | SearchVisitReasons_WhenNotFound_ReturnsEmptyVisitReasons | No matches -> empty list (not 404) | Mock empty | Empty list | Tool impl |
| 3 | Yes | SearchVisitReasons_WhenBlockedReasonExcluded_MapsToExcluded | blocked reason `Excluded` mapped | Mock | Maps | Tool impl |
| 4 | Yes | SearchVisitReasons_WhenBlockedReasonProviderDoesNotPerform_MapsCorrectly | `ProviderDoesNotPerform` reason mapped | Mock | Maps | Tool impl |
| 5 | Yes | SearchVisitReasons_MapAllBlockedReasonValues_DoesNotThrow ([Values]) | Exhaustive enum coverage | All values | None throws | Tool impl (mapper) |
| 6 | Yes | SearchVisitReasons_PassesCallIdAndPatientIdToService | Both ids forwarded | Verify args | Match | Tool impl |
| 7 | Yes | SearchVisitReasons_WithNullPatientId_PassesNullToService | Null patient -> null forwarded | Verify | Null | Tool impl |
| 8 | Yes | SearchVisitReasons_WithNullPatientId_SkipsSessionValidation | Null patientId skips session validation entirely | Mock; call w/ null | Session validation NOT performed | Tool impl (auth) |
| 9 | Yes | SearchVisitReasons_WithPatientIdNotInSession_ReturnsBadRequest | Patient not in session -> 400 | Cache w/o patient | BadRequest | Tool impl (auth) |
| 10 | Yes | SearchVisitReasons_WithPatientIdNotInSession_DoesNotCallService | And service is not called | Verify mock | Times.Never | Tool impl |
| 11 | Yes | SearchVisitReasons_WithAuthenticatedPatientId_CallsService | Authenticated patient in session -> service called | Cache; call | Called | Tool impl (auth) |
| 12 | Yes | SearchVisitReasons_IsNewPatientTrueWithNonGeneratedPatientId_ReturnsBadRequest | is_new=true but patientId is NOT generated -> 400 | Real patient id + is_new=true | BadRequest | Tool impl (auth/contract) |
| 13 | Yes | SearchVisitReasons_IsNewPatientTrueWithNonGeneratedPatientId_DoesNotCallService | And service is not called | Verify | Times.Never | Tool impl |
| 14 | Yes | SearchVisitReasons_IsNewPatientTrueWithGeneratedPatientId_CallsService | is_new=true + generated id -> service called | Generated id; call | Called | Tool impl (auth) |
| 15 | Yes | SearchVisitReasons_IsNewPatientTrueWithNoPatientId_CallsService | is_new=true + no patientId -> service called | No patientId; call | Called | Tool impl |
| 16 | Yes | SearchVisitReasons_IsNewPatientFalseWithNoPatientId_ReturnsBadRequest | is_new=false but no patientId -> 400 | No patientId; is_new=false | BadRequest | Tool impl (auth/contract) |
| 17 | Yes | SearchVisitReasons_IsNewPatientFalseWithNoPatientId_DoesNotCallService | And service not called | Verify | Times.Never | Tool impl |
| 18 | Yes | SearchVisitReasons_IsNewPatientFalseWithPatientId_CallsService | is_new=false + patientId -> service called | Both set; call | Called | Tool impl |

### Suggested Missing Tests

- The 400 responses (#9, #12, #16) should include a structured error code/reason - shape not asserted.
- These auth-style validations should emit a structured log/observability event so QA can debug failed calls. Not asserted.
- Service throws a transient exception - propagation behavior.
- Call ID is required - missing-callId behavior is not tested.
- The "generated patientId" check uses a string format / GUID prefix - the format check itself is not unit-tested in isolation.

### Improvement Suggestions / Irrelevant Tests

- The `is_new_patient` matrix tests (#12-#18) are valuable; they enforce the contract that prevents an LLM from forging an existing-patient lookup.
- Pairs of tests like #9/#10, #12/#13, #16/#17 always assert "returns 400" + "did not call service" - consider combining into single tests asserting both outcomes (per CS-021 "test outcomes, not mocks-only"). Today they are each fine, just verbose.
- No tests are irrelevant.

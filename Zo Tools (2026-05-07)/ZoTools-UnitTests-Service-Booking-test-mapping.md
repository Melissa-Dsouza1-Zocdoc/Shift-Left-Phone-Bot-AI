# ZoTools.UnitTests Service (Booking and Appointment) Test Mapping

Scope of this file: Service-folder unit tests covering booking, appointment retrieval/management, rescheduling, new-patient determination, availability search, and the InteropPlatform booking client.

Source files mapped:
- `tests/ZoTools.UnitTests/Service/AppointmentInfoServiceTests.cs` (85 tests)
- `tests/ZoTools.UnitTests/Service/AppointmentManagementServiceTests.cs` (16 tests)
- `tests/ZoTools.UnitTests/Service/AvailabilityServiceTests.cs` (13 tests)
- `tests/ZoTools.UnitTests/Service/AvailabilityServiceGetNextAvailableTests.cs` (8 tests)
- `tests/ZoTools.UnitTests/Service/BookingServiceTests.cs` (69 tests)
- `tests/ZoTools.UnitTests/Service/InteropPlatformBookingTests.cs` (9 tests)
- `tests/ZoTools.UnitTests/Service/IsNewPatientServiceTests.cs` (37 tests)
- `tests/ZoTools.UnitTests/Service/RescheduleAppointmentServiceTests.cs` (70 tests)
- `tests/ZoTools.UnitTests/Service/RescheduleParamServiceTests.cs` (31 tests)

Total: 338 tests across 9 spec files.

---

## AppointmentInfoServiceTests.cs

Resolves provider/location identities for Zo-mapped vs Zocdoc-mapped data, builds enriched appointment details (timezone, location formatting, cancel/reschedule blocked reasons), and re-generates reschedule params when needed.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | ResolveProviders_WhenEmpty_ReturnsEmptyAndLogsError | Empty provider list logs and returns empty | Call resolve with [] -> assert empty + log error | Empty input | Unit |
| 2 | | ResolveProviders_WhenSingle_ReturnsSingleProviderAndRecordsMetric | Single provider -> records single metric | Resolve 1 provider -> assert metric tag "single" | Metric | Unit |
| 3 | | ResolveProviders_WhenMultiple_ReturnsAllAndLogsWarning | Multiple providers -> warning log | Resolve >1 -> assert all returned + warning | Branching | Unit |
| 4 | | ResolveProviders_SetsProviderFieldsFromGqlData | Provider fields populated from GQL response | Resolve -> assert fields match GQL data | Data mapping | Unit |
| 5 | | ResolveProviders_WithDuplicateProviderIds_DeduplicatesAndReturnsSingle | Duplicate ids deduped | Resolve [a,a] -> assert 1 result | Dedup | Unit |
| 6 | | ResolveProviders_WhenProviderNotInGql_ExcludesAndLogsError | Missing provider in GQL excluded with error log | Resolve unknown id -> excluded, error log | Negative case | Unit |
| 7 | | ResolveProviders_WhenAllProvidersNotInGql_ReturnsEmptyAndRecordsNoneMetric | All missing -> empty + "none" metric | All unknown -> empty list + metric | Metric | Unit |
| 8 | | ResolveProviders_WhenMultipleProvidersAllNotInGql_ReturnsEmptyAndRecordsNoneMetric | Multi-input all missing -> none metric | Same as above with >1 ids | Metric | Unit |
| 9 | | ResolveProviders_WhenTwoProvidersOneNotInGql_ReturnsSingleAndRecordsSingleMetric | Partial miss -> single metric | One known, one unknown -> single + metric | Metric | Unit |
| 10 | | ResolveProviders_WhenSomeProvidersNotInGql_ReturnsOnlyFoundProviders | Skips missing providers | Mix of found/missing -> only found returned | Filtering | Unit |
| 11 | | ResolveProviders_WhenProviderIsFacility_SetsFacilityFlag | Facility flag set when professional type Facility | Resolve facility -> IsFacility=true | Flag mapping | Unit |
| 12 | | ResolveProviders_WhenProviderNotInCallCenter_SetsIsMappedToCallCenterFalse | IsMappedToCallCenter false when out of zo-set | Provider outside set -> flag false | Flag mapping | Unit |
| 13 | | ResolveLocationIds_WhenNull_ReturnsEmptyAndLogsError | Null locations -> empty + error | Pass null -> empty + error log | Input guard | Unit |
| 14 | | ResolveLocationIds_WhenEmpty_ReturnsEmptyAndLogsError | Empty locations -> empty + error | Pass [] -> empty + error log | Input guard | Unit |
| 15 | | ResolveLocationIds_WhenSingle_ReturnsSingleAndRecordsMetric | Single location -> single metric | Resolve 1 -> single + metric | Metric | Unit |
| 16 | | ResolveLocationIds_WhenMultiple_ReturnsAllAndLogsError | Multi-location -> error logged | Resolve >1 -> all + error log | Multi-loc handling | Unit |
| 17 | | ResolveProviderName_WhenSingleZoMappedFullProvider_ReturnsFullName | ZoMapped full provider returns name | Single zo-mapped -> full name | Happy | Unit |
| 18 | | ResolveProviderName_WhenOnlyZoMappedResource_ReturnsNull | Zo-mapped resource alone -> null | Resource only -> null | Edge | Unit |
| 19 | | ResolveProviderName_WhenOnlyZoMappedFacility_ReturnsNull | Zo-mapped facility alone -> null | Facility only -> null | Edge | Unit |
| 20 | | ResolveProviderName_WhenNoZoMapped_SingleZocdocFullProvider_ReturnsFallbackName | No zo-mapped -> falls back to Zocdoc full provider | Zocdoc only -> fallback name | Fallback | Unit |
| 21 | | ResolveProviderName_WhenNoZoMapped_SingleZocdocResource_ReturnsNull | Zocdoc resource only -> null | Resource only -> null | Edge | Unit |
| 22 | | ResolveProviderName_WhenEmpty_ReturnsNull | Empty -> null | [] -> null | Empty | Unit |
| 23 | | ResolveProviderName_WhenZoMappedResourceAndZocdocFullProvider_ReturnsFallbackName | Resource + Zocdoc full -> Zocdoc full | Mixed -> Zocdoc fallback name | Fallback | Unit |
| 24 | | ResolveProviderName_WhenSingleZoMappedFullAndSingleResource_ReturnsFullProviderName | Zo full + resource -> Zo full name | Mix -> Zo full name | Precedence | Unit |
| 25 | | ResolveProviderName_WithMultipleZoMappedFullProviders_ReturnsNull | Multiple Zo full -> null (ambiguity) | >1 zo full -> null | Ambiguity | Unit |
| 26 | | ResolveProviderName_WhenZoMappedHasResourceOrFacility_TagsMetricCorrectly | Tags resource/facility metric | Inspect metric tags | Metric | Unit |
| 27 | | ResolveProviderName_WithMultipleZoMappedFullProviders_DoesNotFallBackToZocdoc | Doesn't fall back when ambiguous zo full | >1 zo full -> no fallback | Precedence | Unit |
| 28 | | ResolveProviderName_WhenZoMappedOnlyHasResources_FallsBackToZocdoc | Resource-only zo set -> Zocdoc fallback | Zo resources only -> fallback | Fallback | Unit |
| 29 | | ResolveProviderName_WhenZoMappedOnlyHasFacilities_FallsBackToZocdoc | Facility-only zo set -> Zocdoc fallback | Zo facilities only -> fallback | Fallback | Unit |
| 30 | | ResolveLocationInfo_WhenFoundWithAddressAndCity_ReturnsFormattedAddress | Address+city formatted as expected | Resolve location -> assert formatted string | Formatting | Unit |
| 31 | | ResolveLocationInfo_WhenMissingAddress_ReturnsNameAndLogs | Missing address -> name fallback + log | No address -> name + log | Fallback | Unit |
| 32 | | ResolveLocationInfo_WhenMissingCity_ReturnsNameAndLogs | Missing city -> name fallback + log | No city -> name + log | Fallback | Unit |
| 33 | | ResolveLocationInfo_WhenNotFound_ReturnsNullAndLogs | Not found -> null + log | Bad id -> null + log | Negative | Unit |
| 34 | | ResolveLocationInfo_WhenEmptyLocationIds_ReturnsNull | Empty ids -> null | Pass [] -> null | Empty | Unit |
| 35 | | ResolveLocationInfo_WhenWhitespaceAddress_ReturnsNameFallback | Whitespace address -> name fallback | Whitespace -> name | Sanitization | Unit |
| 36 | | ResolveLocationInfo_WithMixedVirtualAndPhysical_ReturnsHybrid | Mixed -> Hybrid type | Mix locs -> Hybrid | Type mapping | Unit |
| 37 | | ResolveLocationInfo_WithOnlyVirtualLocations_ReturnsEmptyLocationNameAndIsVirtual | All virtual -> empty name + virtual type | All virtual -> empty + Virtual | Type mapping | Unit |
| 38 | | ResolveLocationInfo_WithMultipleVirtualLocations_ReturnsEmptyLocationNameAndIsVirtual | Multi virtual -> same | Multi virtual -> empty + Virtual | Type mapping | Unit |
| 39 | | ResolveLocationInfo_WithOnlyPhysicalLocations_ReturnsAddress | All physical -> address | Physical only -> address | Type mapping | Unit |
| 40 | | ResolveLocationInfo_WithPartialResolution_OneResolves_NotHybrid | Partial resolution does not flag Hybrid | One resolves -> not Hybrid | Edge case | Unit |
| 41 | | ResolveLocationInfo_WhenZoMappedLocationExists_UsesZoMappedLocation | Prefers zo-mapped location | Zo-mapped exists -> used | Precedence | Unit |
| 42 | | ResolveLocationInfo_WhenZoMappedLocationMissingAddress_FallsBackToName | Missing address on zo-mapped -> name | Zo-mapped no addr -> name | Fallback | Unit |
| 43 | | ResolveLocationInfo_WhenNoZoMappedLocation_FallsBackToZocdocMapped | No zo-mapped -> Zocdoc mapped | Zocdoc only -> Zocdoc | Fallback | Unit |
| 44 | | BuildAppointmentDetails_CallsTimezoneServiceWithCorrectParameters | Timezone service called with right args | Verify call args | Glue | Unit |
| 45 | | BuildAppointmentDetails_UsesTimezoneServiceResultsForTimeFields | Timezone result populates time fields | Mock TZ result -> assert fields | Data flow | Unit |
| 46 | | BuildAppointmentDetails_WhenTimezoneServiceReturnsNullLocal_SetsLocalTimeToNull | Null local time propagated | TZ returns null -> field null | Null handling | Unit |
| 47 | | BuildAppointmentDetails_BuildsCompleteAppointment | Full happy-path object construction | Build -> assert all fields populated | Happy path | Unit |
| 48 | | BuildAppointmentDetails_WhenZdProcedureIdsEmpty_SetsBothBlockedReasonsToVisitReasonMappingIssue | No procedure mapping -> blocked | Empty procedureIds -> blocked reason set | Blocked reason | Unit |
| 49 | | BuildAppointmentDetails_WhenNoProviders_SetsProviderNameToNull | No provider -> null name | No providers -> null | Null | Unit |
| 50 | | BuildAppointmentDetails_WhenLocationNotFound_SetsLocationNameToNull | Location not found -> null name | Bad loc id -> null | Null | Unit |
| 51 | | BuildAppointmentDetails_WithMultipleAppointments_ReturnsAll | Multi-appt path returns all | >1 appt -> all returned | Multi | Unit |
| 52 | | BuildAppointmentDetails_WithHybridLocations_SetsLocationTypeHybridAndEmptyLocationName | Hybrid location -> empty name | Hybrid -> empty name + type Hybrid | Type | Unit |
| 53 | | BuildAppointmentDetails_WithAllVirtualLocations_SetsLocationTypeVirtualOnlyAndEmptyLocationName | All-virtual -> VirtualOnly | All virtual -> VirtualOnly | Type | Unit |
| 54 | | BuildAppointmentDetails_WithEmptyLocationIds_SetsLocationTypeUnknown | Empty -> Unknown | [] -> Unknown | Type | Unit |
| 55 | | BuildAppointmentDetails_PassesThroughIsNewPatientFromService_WhenTrue | IsNewPatient=true propagated | Mock service true -> field true | Glue | Unit |
| 56 | | BuildAppointmentDetails_WithRealizedPastAppointment_SetsIsNewPatientFalse | Realized past -> false | Past realized -> false | Business rule | Unit |
| 57 | | BuildAppointmentDetails_PassesThroughIsNewPatientFromService_WhenMultipleAppointments | Multi-appt IsNewPatient propagation | Mock multi -> assert each | Glue | Unit |
| 58 | | BuildAppointmentDetails_CallsIsNewPatientServiceWithCorrectParameters | Service called with right args | Verify call args | Glue | Unit |
| 59 | | BuildAppointmentDetails_ComputesSeparateCancelAndRescheduleBlockedReasons | Distinct blocked reasons computed | Inspect output | Business rule | Unit |
| 60 | | BuildAppointmentDetails_WhenExclusionsIsNull_DoesNotThrow | Null exclusions tolerated | Null exclusions -> no throw | Null safety | Unit |
| 61 | | BuildAppointmentDetails_WhenRescheduleNotBlocked_PopulatesRescheduleParams | Not blocked -> params present | Not blocked -> params populated | Param flow | Unit |
| 62 | | BuildAppointmentDetails_WhenRescheduleBlocked_RescheduleParamsIsNull | Blocked -> params null | Blocked -> null | Param flow | Unit |
| 63 | | BuildAppointmentDetails_CallsGetPracticeInfoForApptsWithApptProviderIds | Practice info called with appt provider ids | Verify args | Glue | Unit |
| 64 | | BuildAppointmentDetails_PassesCorrectParamsToRescheduleParamService | Reschedule param service called correctly | Verify args | Glue | Unit |
| 65 | | BuildAppointmentDetails_WhenAppointmentIsInPast_RescheduleParamsIsNull | Past appt -> null params | Past -> null | Time guard | Unit |
| 66 | | BuildAppointmentDetails_WhenAppointmentIsInPast_DoesNotCallGetRescheduleParams | Past appt -> service not called | Verify not called | Optimization | Unit |
| 67 | | BuildAppointmentDetails_WhenNotInPast_CallsRescheduleParamService | Future appt -> service called | Verify called | Glue | Unit |
| 68 | | BuildAppointmentDetails_DoesNotCallGetProviderProcedureDurations | Confirms not invoked here | Verify not called | Coupling check | Unit |
| 69 | | BuildAppointmentDetails_PassesOnlyZoMappedProviderDurationsToRescheduleParamService | Only zo-mapped durations forwarded | Inspect args | Glue | Unit |
| 70 | | AvailabilityStrategy_Null_ReturnsNone | Null strategy maps to None | Pass null -> None | Mapping | Unit |
| 71 | | AvailabilityStrategy_None_ReturnsNone | None -> None | Pass None -> None | Mapping | Unit |
| 72 | | AvailabilityStrategy_UseZdProcedure_ReturnsUseZdProcedure | UseZdProcedure preserved | Pass enum -> same | Mapping | Unit |
| 73 | | AvailabilityStrategy_UseDuration_ReturnsUseDuration | UseDuration preserved | Pass enum -> same | Mapping | Unit |
| 74 | | AvailabilityStrategy_Null_IncrementsMetric | Null strategy emits metric | Pass null -> verify metric | Metric | Unit |
| 75 | | AvailabilityStrategy_None_IncrementsMetric | None emits metric | Pass None -> verify metric | Metric | Unit |
| 76 | | AvailabilityStrategy_UseZdProcedure_DoesNotIncrementMetric | Valid strategy -> no metric | Verify no metric | Metric | Unit |
| 77 | | AvailabilityStrategy_AllValues_DoesNotThrow | All enum values handled | Iterate all -> no throw | Robustness | Unit |
| 78 | | AvailabilityStrategy_InvalidValue_ThrowsArgumentOutOfRangeException | Invalid cast value throws | Cast bogus -> throw | Defensive | Unit |
| 79 | | RegenerateRescheduleParams_WhenStrategyIsNotUseZdProcedure_ReturnsExistingParams | Non-UseZd skips regen | Strategy=UseDuration -> existing returned | Short-circuit | Unit |
| 80 | | RegenerateRescheduleParams_WhenStrategyIsNone_ReturnsExistingParams | None skips regen | Strategy=None -> existing | Short-circuit | Unit |
| 81 | | RegenerateRescheduleParams_WhenRescheduleParamsIsNull_Throws | Null params -> throw | Null -> throw | Guard | Unit |
| 82 | | RegenerateRescheduleParams_WithInvalidLocationType_ThrowsArgumentException | Invalid location type -> throw | TestCase Hybrid/Unknown -> throw | Guard | Unit |
| 83 | | RegenerateRescheduleParams_PhysicalOnly_ReturnsParamsWithPhysicalLocationAndProvider | Physical regen returns physical loc/prov | Physical -> physical params | Branching | Unit |
| 84 | | RegenerateRescheduleParams_VirtualOnly_ReturnsParamsWithVirtualLocationAndProvider | Virtual regen returns virtual loc/prov | Virtual -> virtual params | Branching | Unit |
| 85 | | RegenerateRescheduleParams_PassesCorrectArgsToResolveZdProcedure | Args forwarded to ResolveZdProcedure | Verify call args | Glue | Unit |

### Suggested Missing Tests (AppointmentInfoServiceTests)
- Concurrency: many parallel BuildAppointmentDetails calls share GQL/cache safely.
- DST boundary appointments produce correct local time (currently delegated to TimezoneService - missing integration check at boundary).
- BuildAppointmentDetails when start time string is malformed.
- RegenerateRescheduleParams when ZoMapped procedure ids are null vs empty.

### Improvement Suggestions / Irrelevant Tests
- `ResolveLocationInfo_WithPartialResolution_OneResolves_NotHybrid` is testing-implementation rather than behavior - rename to capture user-visible expectation.
- `BuildAppointmentDetails_DoesNotCallGetProviderProcedureDurations` is a coupling-assertion. Acceptable, but call out that breaking it is fine if we change the strategy.
- Several Resolve* tests duplicate metric assertion - consider one parameterized test.

---

## AppointmentManagementServiceTests.cs

Cancel-appointment endpoint and reschedule validation rules.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | CancelAppointment_WithProductionSecret_CallsPhoneBotRepository | Production secret path calls repo | Mock prod secret -> verify repo called | Auth path | Unit |
| 2 | | CancelAppointment_WhenPermissionServiceThrows_ThrowsUnauthorizedAccessException | Unauthorized perms -> 401 | Mock perm throw -> assert UAE | Authz | Unit |
| 3 | | CancelAppointment_PracticeNotFound_ThrowsException | Missing practice -> throw | Bad practice id -> throw | Negative | Unit |
| 4 | | CancelAppointment_LogsAuditEvent | Audit logged on cancel | Verify audit logger called | Audit | Unit |
| 5 | | BuildAppointmentInfo_WhenRescheduleParamsDurationIsSet_UsesThat | Reschedule duration honored | Set duration -> assert used | Branching | Unit |
| 6 | | BuildAppointmentInfo_WhenRescheduleParamsDurationIsNull_FallsBackToAppointmentDuration | Falls back to appt duration | Null param duration -> appt duration | Fallback | Unit |
| 7 | | BuildAppointmentInfo_NewPatient_SetsPatientTypeNew | New patient flag sets PatientTypeNew | New patient -> patientType=New | Mapping | Unit |
| 8 | | BuildAppointmentInfo_ExistingPatient_SetsPatientTypeExisting | Existing patient flag | Existing -> Existing | Mapping | Unit |
| 9 | | BuildAppointmentInfo_SetsAllExpectedFields | Full field mapping | Full input -> assert all fields | Happy | Unit |
| 10 | | ValidateReschedule_HappyPath_ReturnsNull | All valid -> null error | Valid input -> null | Happy | Unit |
| 11 | | ValidateReschedule_WithinReschedulingCutoff_ReturnsError | Within cutoff -> error | Set cutoff -> error returned | Business rule | Unit |
| 12 | | ValidateReschedule_RescheduleBlockedReasonIsWithinCutoff_ReturnsError | Blocked-reason cutoff -> error | Pre-blocked -> error | Business rule | Unit |
| 13 | | ValidateReschedule_TimeInPast_ReturnsError | Past time -> error | New time in past -> error | Business rule | Unit |
| 14 | | ValidateReschedule_ExcludedProvider_ReturnsError | Excluded provider -> error | Excluded prov -> error | Exclusion | Unit |
| 15 | | ValidateReschedule_ExcludedLocation_ReturnsError | Excluded location -> error | Excluded loc -> error | Exclusion | Unit |
| 16 | | ValidateReschedule_ExcludedVisitReason_ReturnsError | Excluded visit reason -> error | Excluded VR -> error | Exclusion | Unit |

### Suggested Missing Tests (AppointmentManagementServiceTests)
- CancelAppointment with test secret (non-production) path
- CancelAppointment when ConsentRefused (parity with SmsService treatment)
- CancelAppointment when audit logger throws - does cancel still proceed
- CancellationToken propagation through CancelAppointment
- ValidateReschedule with multiple violations (priority order)

### Improvement Suggestions / Irrelevant Tests
- Validation tests duplicate logic asserted in `RescheduleAppointmentService.ValidateReschedule_*` - consider unifying or moving validation tests to one place to avoid drift.

---

## AvailabilityServiceTests.cs

Search availability via the GQL/availability layer; map providers, locations, and timeslots.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | SearchAvailability_WithSpecificProvider_ReturnsFilteredTimeslots | Provider-specific search returns only that provider's slots | Provide id -> assert filtered | Filter | Unit |
| 2 | | SearchAvailability_ExistingPatient_ReturnsAvailability | Existing-patient flag returns availability | Existing patient -> assert results | Patient flag | Unit |
| 3 | | SearchAvailability_EmptyProviderIds_SearchesAllProviders | Empty ids -> all providers | [] -> all searched | Default scope | Unit |
| 4 | | SearchAvailability_SortsTimeslotsByStartTime | Sort by start time | Disordered slots -> sorted output | Ordering | Unit |
| 5 | | SearchAvailability_NoAvailability_ReturnsEmptyList | No avail -> empty list | Mock empty -> empty | Empty | Unit |
| 6 | | SearchAvailability_ResourceProvider_MapsIsResourceTrue | Resource provider flag mapping | Resource provider -> IsResource=true | Mapping | Unit |
| 7 | | SearchAvailability_ConstructsCorrectProviderLocationIds | Builds correct provider-location combos | Inspect GQL args | Glue | Unit |
| 8 | | SearchAvailability_CalculatesNumDaysFromDateRange | Computes num days from range | Date range -> assert numDays | Calc | Unit |
| 9 | | SearchAvailability_EndDateBeforeStartDate_ThrowsInvalidSearchWindowException | End<Start -> throw | Bad range -> throw | Guard | Unit |
| 10 | | SearchAvailability_PassesTimeFilterToGqlService | Time filter forwarded | Pass filter -> verify GQL arg | Glue | Unit |
| 11 | | SearchAvailability_WithNullTimeFilter_PassesNullToGqlService | Null filter forwarded as null | Pass null -> verify | Null handling | Unit |
| 12 | | SearchAvailability_SortsTimeslotsByParsedDateTimeOffset | Sorts by parsed offset (not raw string) | Mixed offsets -> correctly sorted | Ordering | Unit |
| 13 | | SearchAvailability_WithDurationOverrideAndNoProcedure_CallsGqlWithNullProcedure | Duration-only overrides procedure | Provide duration only -> GQL gets null procedure | Branching | Unit |

### Suggested Missing Tests (AvailabilityServiceTests)
- Cancellation handling
- Very large date range (boundary)
- Timezone-aware sorting across DST transition
- Interaction with feature flags (e.g., NewAvailabilityStrategy)

### Improvement Suggestions / Irrelevant Tests
- `ConstructsCorrectProviderLocationIds` is implementation-coupled; rename to behavior-oriented assertion.

---

## AvailabilityServiceGetNextAvailableTests.cs

Next-available timeslot search, returns the earliest slot with provider name.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | GetNextAvailable_ReturnsTimeslotsWithProviderName | Returns slots with provider name | Mock setup -> assert names present | Happy | Unit |
| 2 | | GetNextAvailable_SkipsProviderLocationsWithNoAvailability | Skips empty provider/locs | No-avail combos absent in result | Filtering | Unit |
| 3 | | GetNextAvailable_SortsTimeslotsByStartTime | Sorted ascending | Verify order | Ordering | Unit |
| 4 | | GetNextAvailable_EmptyProviderIds_SearchesAllProviders | Empty -> all | [] -> all searched | Default scope | Unit |
| 5 | | GetNextAvailable_ConstructsCorrectProviderLocationIds | Builds combos | Inspect GQL args | Glue | Unit |
| 6 | | GetNextAvailable_NewPatient_PassesIsNewPatientTrue | New patient flag forwarded | New -> assert true | Glue | Unit |
| 7 | | GetNextAvailable_ExistingPatient_PassesIsNewPatientFalse | Existing -> false | Existing -> false | Glue | Unit |
| 8 | | GetNextAvailable_NoAvailability_ReturnsEmptyList | No availability | Mock empty -> empty | Empty | Unit |

### Suggested Missing Tests
- Cancellation
- Behavior when GQL throws
- Logging assertions parity with SearchAvailability

### Improvement Suggestions / Irrelevant Tests
- Same coupling concerns as AvailabilityServiceTests.

---

## BookingServiceTests.cs

Builds booking preview speech, executes booking via Interop, maps insurance, validates patient demographics, normalizes phone, computes durations from procedure context, and applies booking validation.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | BuildPreviewSpeak_WithProviderAndLocation_ReturnsFullSpeak | Full-speak speech with provider+location | Build -> assert components present | Happy | Unit |
| 2 | | BuildPreviewSpeak_WhenProviderNotFound_UsesGenericProvider | Missing provider -> generic | Bad provider -> generic substitute | Fallback | Unit |
| 3 | | BuildPreviewSpeak_WhenLocationNotFound_OmitsLocation | Missing location -> omit | Bad loc -> location absent | Fallback | Unit |
| 4 | | BuildPreviewSpeak_SpanishLanguage_ReturnsSpanishSpeak | Spanish locale | Locale=es -> Spanish | i18n | Unit |
| 5 | | BuildPreviewSpeak_InvalidStartTimeFormat_UsesGenericTime | Invalid time -> generic | Bad time -> generic | Fallback | Unit |
| 6 | | BuildPreviewSpeak_LooksUpProviderAndLocationFromPracticeInfo | Looks up via practice info | Verify practice info access | Glue | Unit |
| 7 | | BuildPreviewSpeak_WhenPracticeInfoNotFound_ThrowsInvalidOperationException | No practice info -> throw | Null practice -> throw | Guard | Unit |
| 8 | | BuildPreviewSpeak_DifferentTimezone_ExtractsCorrectLocalTime | TZ-aware local time | Other TZ -> correct local | TZ | Unit |
| 9 | | MapInteropError_AvailabilityNotFound_ReturnsCorrectStatus | Maps interop error to status | TestCase -> mapping | Mapping | Unit |
| 10 | | MapInteropError_PatientRuleException_ReturnsCorrectStatus | Patient rule mapping | TestCase -> mapping | Mapping | Unit |
| 11 | | MapInteropError_ProviderNotMappedInSync_ReturnsCorrectStatus | Provider-not-mapped mapping | TestCase -> mapping | Mapping | Unit |
| 12 | | MapInteropError_Unknown_ReturnsBookingFailed | Unknown -> BookingFailed | TestCase -> mapping | Mapping | Unit |
| 13 | | MapInteropError_AllValues_DoesNotThrow | All enum values handled | Iterate -> no throw | Robustness | Unit |
| 14 | | MapInteropError_InvalidValue_ReturnsBookingFailed | Invalid -> BookingFailed | Cast bogus -> BookingFailed | Defensive | Unit |
| 15 | | ExecuteBooking_WhenProviderExcluded_ReturnsExcludedProviderError | Excluded provider blocks | Excluded -> error | Exclusion | Unit |
| 16 | | ExecuteBooking_WhenLocationExcluded_ReturnsExcludedLocationError | Excluded location blocks | Excluded loc -> error | Exclusion | Unit |
| 17 | | ExecuteBooking_WhenVisitReasonExcluded_ReturnsExcludedVisitReasonError | Excluded VR blocks | Excluded VR -> error | Exclusion | Unit |
| 18 | | ExecuteBooking_WhenIpaSucceeds_ReturnsAppointmentIdAndSpeak | Happy path returns id + speak | Mock IPA success -> id+speak | Happy | Unit |
| 19 | | ExecuteBooking_WithVisitReasonNotes_SendsReasonOnAppointmentInfo | Notes forwarded | Provide notes -> assert in appt info | Glue | Unit |
| 20 | | ExecuteBooking_WithNullOrWhitespaceVisitReasonNotes_OmitsReasonOnAppointmentInfo | Empty notes -> omitted | Null/ws -> not sent | Sanitization | Unit |
| 21 | | ExecuteBooking_Success_ReturnsProviderAndLocationNames | Returns names on success | Verify both present | Happy | Unit |
| 22 | | ExecuteBooking_WhenIpaThrowsAvailabilityNotFound_ReturnsAvailabilityNotFoundError | Maps interop exception | Mock throw -> error | Error mapping | Unit |
| 23 | | ExecuteBooking_WithInsuranceCarrierId_LooksUpCarrierName | Carrier id -> name lookup | Provide carrier -> name resolved | Insurance | Unit |
| 24 | | ExecuteBooking_WithoutInsuranceCarrierId_DoesNotLookUpCarrierName | No carrier -> no lookup | No carrier -> verify no lookup | Optimization | Unit |
| 25 | | ExecuteBooking_SpanishLanguage_ReturnsSpeakInSpanish | Spanish speak | es -> Spanish output | i18n | Unit |
| 26 | | ExecuteBooking_WhenProviderNotFound_SpeakUsesGenericProvider | Missing prov -> generic in speak | Missing -> generic | Fallback | Unit |
| 27 | | ExecuteBooking_WhenLocationNotFound_SpeakOmitsLocation | Missing loc -> omitted | Missing -> omit | Fallback | Unit |
| 28 | | ExecuteBooking_PhoneWithPlus1Prefix_StripsPrefix | "+1" prefix removed | "+1XXX" -> "XXX" | Sanitization | Unit |
| 29 | | ExecuteBooking_PhoneWithoutPrefix_PassesUnchanged | No prefix -> unchanged | "XXX" -> "XXX" | Sanitization | Unit |
| 30 | | ExecuteBooking_UnauthenticatedPatient_MapsNameFromPatient | Unauth -> map from session.Patient | Verify name copied | Mapping | Unit |
| 31 | | ExecuteBooking_UnauthenticatedPatient_MapsDateOfBirthFromPatient | DOB mapped | Verify DOB copied | Mapping | Unit |
| 32 | | ExecuteBooking_UnauthenticatedPatient_MapsGenderToSex | Gender -> Sex enum | TestCase Male/Female | Mapping | Unit |
| 33 | | ExecuteBooking_UnauthenticatedPatient_NullGender_DefaultsToFemale | Null gender -> Female | Null -> Female | Default | Unit |
| 34 | | ExecuteBooking_UnauthenticatedPatient_MapsEmailToEmailAddress | Email mapped | Verify email | Mapping | Unit |
| 35 | | ExecuteBooking_UnauthenticatedPatient_MissingEmail_UsesPlaceholderEmail | Missing email -> placeholder | TestCase null/empty/ws | Default | Unit |
| 36 | | ExecuteBooking_UnauthenticatedPatient_MapsPhoneWithStrippedPrefix | Phone +1 stripped | Verify | Sanitization | Unit |
| 37 | | ExecuteBooking_UnauthenticatedPatient_FallsBackToCallerPhoneWhenPhoneNull | Falls back to caller phone | Patient phone null -> caller phone used | Fallback | Unit |
| 38 | | ExecuteBooking_UnauthenticatedPatient_MapsAddressFields | Address mapped | Verify address fields | Mapping | Unit |
| 39 | | ExecuteBooking_AuthenticatedPatient_UsesExistingPatientDetails | Auth -> existing patient | Auth flow -> uses existing | Branching | Unit |
| 40 | | ExecuteBooking_SendsCorrectAppointmentInfoToIpa | Appt info forwarded | Verify args | Glue | Unit |
| 41 | | MapGenderToSex_MapsCorrectly | Gender mapping | TestCase | Mapping | Unit |
| 42 | | MapGenderToSex_AllValues_DoesNotThrow | All enum values handled | Iterate | Robustness | Unit |
| 43 | | MapGenderToSex_InvalidValue_ThrowsArgumentOutOfRangeException | Invalid -> throw | Cast bogus -> throw | Defensive | Unit |
| 44 | | ExecuteBooking_InvalidStartTimeWithOffset_ReturnsInvalidStartTimeFormatError | Invalid time -> error | Bad time -> error | Negative | Unit |
| 45 | | ExecuteBooking_WhenSaveBookingRequestThrows_StillReturnsSuccess | Save error tolerated | Save throw -> still success | Resilience | Unit |
| 46 | | ExecuteBooking_WhenInsuranceCarrierLookupFails_ReturnsInsuranceLookupFailedError | Carrier lookup fail -> error | Mock fail -> error | Error path | Unit |
| 47 | | ExecuteBooking_WithSelfPayInsurance_SkipsCarrierLookup | Self-pay -> no lookup | Self-pay -> no carrier call | Optimization | Unit |
| 48 | | ExecuteBooking_WithSelfPayInsurance_SetsPlanNameToSelfpay | Self-pay -> "Selfpay" plan | Verify plan name | Mapping | Unit |
| 49 | | ExecuteBooking_WithMatchingInsurancePlan_ResolvesCarrierAndPlanName | Match -> both resolved | Verify | Happy | Unit |
| 50 | | ExecuteBooking_WithNonMatchingInsurancePlan_ResolvesCarrierNameOnly | No plan match -> carrier only | Verify plan null | Partial match | Unit |
| 51 | | ExecuteBooking_ResolvesPerformingProviderMonolithIdToCloudId | Monolith id -> cloud id translation | Verify | Glue | Unit |
| 52 | | ExecuteBooking_SendsCloudIdToPhoneBot | Cloud id forwarded to PhoneBot | Verify args | Glue | Unit |
| 53 | | ExecuteBooking_UnmappedPerformingProviderId_ReturnsPerformingProviderNotMappedError | Unmapped -> error | Unmapped -> error | Negative | Unit |
| 54 | | ExecuteBooking_DurationNull_NewPatientPhysical_UsesNewPatientDuration | Default duration: NP physical | Set context -> assert duration | Default | Unit |
| 55 | | ExecuteBooking_DurationNull_NewPatientVirtual_UsesNewVideoDuration | Default duration: NP virtual | Verify | Default | Unit |
| 56 | | ExecuteBooking_DurationNull_ExistingPatientPhysical_UsesEstablishedDuration | Default: EP physical | Verify | Default | Unit |
| 57 | | ExecuteBooking_DurationNull_ExistingPatientVirtual_UsesEstablishedVideoDuration | Default: EP virtual | Verify | Default | Unit |
| 58 | | ExecuteBooking_DurationNull_LocationNotInNearbyLocations_SendsNullDuration | Loc not nearby -> null duration | Verify null | Edge | Unit |
| 59 | | ExecuteBooking_DurationNull_ProcedureDurationsCallFails_SendsNullDuration | Lookup fail -> null duration | Mock fail -> null | Error path | Unit |
| 60 | | ExecuteBooking_DurationNull_ProcedureNotFoundInDurations_SendsNullDuration | Not in durations -> null | Verify | Edge | Unit |
| 61 | | ExecuteBooking_DurationNull_DurationFieldIsNull_SendsNullDuration | Field null -> null | Verify | Edge | Unit |
| 62 | | ExecuteBooking_WhenAgeRestricted_ReturnsAgeRestrictedError | Age-restricted -> error | Verify | Validation | Unit |
| 63 | | ExecuteBooking_WhenDoesNotPerformProcedure_ReturnsDoesNotPerformProcedureError | Procedure not performed -> error | Verify | Validation | Unit |
| 64 | | ExecuteBooking_WhenExistingPatientCannotSeeNewProvider_ReturnsError | Continuity rule -> error | Verify | Validation | Unit |
| 65 | | ExecuteBooking_WhenValidationReturnsExcludedIssue_BlocksBooking | Validation excluded -> block | Verify | Validation | Unit |
| 66 | | ExecuteBooking_WhenValidationThrowsCancellation_PropagatesException | Cancellation propagates | Throw OCE -> rethrow | Cancellation | Unit |
| 67 | | ExecuteBooking_WhenValidationThrows_ProceedsWithBooking | Validation throw (non-cancel) tolerated -> still books | Mock throw -> proceeds | Resilience | Unit |
| 68 | | ExecuteBooking_WhenValidationReturnsNoIssues_ProceedsWithBooking | No issues -> proceeds | Verify | Happy | Unit |
| 69 | | ExecuteBooking_PassesCorrectParametersToValidateBooking | Validation called with right args | Verify args | Glue | Unit |

### Suggested Missing Tests (BookingServiceTests)
- Concurrency: two simultaneous bookings for the same slot (should one fail?)
- LockedActionService coverage parity with reschedule (booking does not appear to lock here)
- Audit logging parity with cancel/reschedule
- CancellationToken propagation
- Case where booking succeeds but PhoneBot save throws repeatedly (retry / metric)

### Improvement Suggestions / Irrelevant Tests
- `ExecuteBooking_WhenValidationThrows_ProceedsWithBooking` is an unusual decision; consider whether tolerating validation exceptions silently is desired.
- `ExecuteBooking_UnauthenticatedPatient_NullGender_DefaultsToFemale` is a surprising default - flag for product review (gender bias).
- Tests rely heavily on big setup. Consider builders/fixtures.

---

## InteropPlatformBookingTests.cs

Thin wrapper around the InteropPlatform booking client; maps client errors to typed exceptions.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | BookAppointment_Success_ReturnsResponseBody | Success returns body | Mock success -> body | Happy | Unit |
| 2 | | BookAppointment_WhenClientReturnsError_ThrowsInteropBookingException | Error response -> typed exception | Mock error -> throw | Error mapping | Unit |
| 3 | | BookAppointment_TimeSlotNotAvailable_ThrowsWithAvailabilityNotFoundReason | Timeslot error -> AvailabilityNotFound reason | Mock specific error -> assert reason | Mapping | Unit |
| 4 | | BookAppointment_PatientRuleException_ThrowsWithPatientRuleExceptionReason | Patient rule -> reason | Mock -> assert | Mapping | Unit |
| 5 | | BookAppointment_ProviderNotMappedInSync_ThrowsWithProviderNotMappedInSyncReason | Provider not mapped -> reason | Mock -> assert | Mapping | Unit |
| 6 | | BookAppointment_UnknownError_ThrowsWithUnknownReason | Unknown -> Unknown reason | Mock -> assert | Mapping | Unit |
| 7 | | BookAppointment_AllErrorTypes_ThrowsInteropBookingException | All error types throw | Iterate -> verify | Robustness | Unit |
| 8 | | BookAppointment_WhenClientThrowsException_PropagatesException | Client throw -> propagate | Mock throw -> rethrow | Resilience | Unit |
| 9 | | BookAppointment_PassesParametersInCorrectOrderToClient | Param order to client | Verify args order | Glue | Unit |

### Suggested Missing Tests
- Cancellation token honored
- Retry policy if any
- Logging on each error reason

### Improvement Suggestions / Irrelevant Tests
- `PassesParametersInCorrectOrderToClient` is implementation-coupled; replace with named-argument assertions.

---

## IsNewPatientServiceTests.cs

Determines new-patient status for reschedules and visit reasons, by practice or by specialty, considering past realized appointments and overlap of specialties.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | DetermineIsNewPatientForReschedules_ForPractice_NoAppointments_ReturnsEmpty | No appts -> empty | [] -> empty | Empty | Unit |
| 2 | | DetermineIsNewPatientForReschedules_ForPractice_OnlyFutureAppointments_ReturnsTrue | Only future -> true | Future only -> true | Business rule | Unit |
| 3 | | DetermineIsNewPatientForReschedules_ForPractice_AllPastAppointmentsNonRealized_ReturnsTrue | Past non-realized -> true | Past cancelled -> true | Business rule | Unit |
| 4 | | DetermineIsNewPatientForReschedules_ForPractice_HasRealizedPastAppointment_ReturnsFalseForAllAppointments | Past realized -> false for all | Past realized -> all false | Business rule | Unit |
| 5 | | DetermineIsNewPatientForReschedules_ForPractice_MixedPastAppointments_ReturnsFalseWhenAnyRealized | Any realized -> false | Mix -> false | Business rule | Unit |
| 6 | | DetermineIsNewPatientForReschedules_ForPractice_AllStatusValues_DoNotThrow | All status values handled | Iterate -> no throw | Robustness | Unit |
| 7 | | DetermineIsNewPatientForReschedules_ForPractice_EmitsForPracticeMetric | Metric emitted | Verify metric | Metric | Unit |
| 8 | | DetermineIsNewPatientForReschedules_AllDefinitionValues_DoNotThrow | All definition values handled | Iterate -> no throw | Robustness | Unit |
| 9 | | DetermineIsNewPatientForReschedules_UnrecognizedDefinition_Throws | Unknown definition -> throw | Cast bogus -> throw | Guard | Unit |
| 10 | | DetermineIsNewPatientForReschedules_ForSpecialty_EmptyProcedureIds_ReturnsTrueWithoutCallingCache | Empty proc ids -> true, no cache call | [] -> true, verify no cache call | Optimization | Unit |
| 11 | | DetermineIsNewPatientForReschedules_ForSpecialty_CacheReturnsNoSpecialtiesForProcedure_ReturnsTrueWithLogError | No specialties -> true + log | Cache returns empty -> true + error log | Negative | Unit |
| 12 | | DetermineIsNewPatientForReschedules_ForSpecialty_NoPastRealizedAppointments_ReturnsTrue | No realized -> true | Verify | Business rule | Unit |
| 13 | | DetermineIsNewPatientForReschedules_ForSpecialty_PastRealizedAppointmentWithMatchingSpecialty_ReturnsFalse | Same specialty -> false | Past realized w/ overlap -> false | Business rule | Unit |
| 14 | | DetermineIsNewPatientForReschedules_ForSpecialty_PastRealizedAppointmentWithDifferentSpecialty_ReturnsTrue | Different specialty -> true | No overlap -> true | Business rule | Unit |
| 15 | | DetermineIsNewPatientForReschedules_ForSpecialty_SelfExclusion_PastAppointmentDoesNotMatchItself | Self-exclusion: appt does not match itself | Verify | Edge | Unit |
| 16 | | DetermineIsNewPatientForReschedules_ForSpecialty_AnyOverlappingSpecialtyCountsAsExistingPatient | Any overlap -> existing patient | Partial overlap -> false | Business rule | Unit |
| 17 | | DetermineIsNewPatientForReschedules_ForSpecialty_PastNonRealizedAppointment_NotCountedAsExistingPatient | Past non-realized -> not counted | Verify | Business rule | Unit |
| 18 | | DetermineIsNewPatientForReschedules_ForSpecialty_EmitsMetricOncePerAppointment | One metric per appt | Verify metric count | Metric | Unit |
| 19 | | DetermineIsNewPatientForVisitReason_ForPractice_NoAppointments_ReturnsTrue | No past -> true | Verify | Business rule | Unit |
| 20 | | DetermineIsNewPatientForVisitReason_ForPractice_OnlyFutureAppointments_ReturnsTrue | Only future -> true | Verify | Business rule | Unit |
| 21 | | DetermineIsNewPatientForVisitReason_ForPractice_HasRealizedPastAppointment_ReturnsFalse | Past realized -> false | Verify | Business rule | Unit |
| 22 | | DetermineIsNewPatientForVisitReason_ForPractice_AllPastNonRealized_ReturnsTrue | All non-realized past -> true | Verify | Business rule | Unit |
| 23 | | DetermineIsNewPatientForVisitReason_ForPractice_UnparseableStartTime_SkipsAppointmentAndReturnsTrue | Bad time skipped | Bad time -> skipped, true | Edge | Unit |
| 24 | | DetermineIsNewPatientForVisitReason_ForSpecialty_NoSpecialtiesForVisitReason_ReturnsTrue | No specialties -> true | Verify | Edge | Unit |
| 25 | | DetermineIsNewPatientForVisitReason_ForSpecialty_OverlappingSpecialty_ReturnsFalse | Overlap -> false | Verify | Business rule | Unit |
| 26 | | DetermineIsNewPatientForVisitReason_ForSpecialty_DifferentSpecialty_ReturnsTrue | Different -> true | Verify | Business rule | Unit |
| 27 | | DetermineIsNewPatientForVisitReason_ForSpecialty_PartialOverlap_ReturnsFalse | Partial overlap -> false | Verify | Business rule | Unit |
| 28 | | DetermineIsNewPatientForVisitReason_ForSpecialty_NonRealizedPastAppt_ReturnsTrue | Non-realized past -> true | Verify | Business rule | Unit |
| 29 | | DetermineIsNewPatientForVisitReason_ForSpecialty_PastApptWithEmptyProcedureIds_ReturnsTrue | Past appt w/ empty proc ids -> true | Verify | Edge | Unit |
| 30 | | DetermineIsNewPatientForVisitReason_AllDefinitionValues_DoNotThrow | All values handled | Iterate -> no throw | Robustness | Unit |
| 31 | | DetermineIsNewPatientForVisitReason_UnrecognizedDefinition_ThrowsArgumentOutOfRangeException | Unknown -> throw | Verify | Guard | Unit |
| 32 | | DetermineIsNewPatientForVisitReasons_ForPractice_MultipleVisitReasons_AllGetSameValue | Multi VRs share practice value | Verify all same | Bulk | Unit |
| 33 | | DetermineIsNewPatientForVisitReasons_ForSpecialty_MultipleVisitReasons_DifferentSpecialties | Different specialties -> different values | Verify | Bulk | Unit |
| 34 | | DetermineIsNewPatientForVisitReasons_ForSpecialty_NoPastRealizedAppointments_AllReturnTrue | No realized -> all true | Verify | Bulk | Unit |
| 35 | | DetermineIsNewPatientForVisitReasons_EmptyVisitReasonList_ReturnsEmpty | Empty -> empty | [] -> {} | Empty | Unit |
| 36 | | DetermineIsNewPatientForVisitReasons_DuplicateVisitReasonIds_DoesNotThrow | Dup ids tolerated | Verify | Edge | Unit |
| 37 | | DetermineIsNewPatientForVisitReasons_ForSpecialty_AllNonRealizedAppointments_AllReturnTrue | Non-realized only -> all true | Verify | Bulk | Unit |

### Suggested Missing Tests
- Caching: cache miss vs hit behavior on the procedure-specialty cache lookup
- DST/timezone-sensitive boundary: appt at midnight local boundary
- Self-exclusion case where same id appears twice in the input list
- Concurrency safety
- Metric tag content (not just count) for forSpecialty path

### Improvement Suggestions / Irrelevant Tests
- `AllStatusValues_DoNotThrow` and `AllDefinitionValues_DoNotThrow` are weak; replace with explicit expected outcome for each value.
- Specialty/practice tests share scaffolding; consider parameterized helpers.

---

## RescheduleAppointmentServiceTests.cs

Implements Preview/Confirm flow for rescheduling, locking, audit, validation, and lock-action coordination.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | Preview_WithValidAppointment_ReturnsSpeakWithDetails | Happy preview returns speak | Build preview -> assert speak | Happy | Unit |
| 2 | | Preview_SessionNotFound_ReturnsNull | No session -> null | Verify | Negative | Unit |
| 3 | | Preview_AppointmentNotFound_ReturnsNull | No appt -> null | Verify | Negative | Unit |
| 4 | | Preview_MissingProviderName_UsesFallback | No provider name -> fallback | Verify | Fallback | Unit |
| 5 | | Preview_MissingLocationName_OmitsLocation | No location name -> omit | Verify | Fallback | Unit |
| 6 | | Preview_SpanishLanguage_ReturnsSpanishSpeak | Spanish locale | Verify Spanish | i18n | Unit |
| 7 | | Preview_SpeakEndsWithConfirmationQuestion | Speak ends with confirm question | Inspect end | UX | Unit |
| 8 | | Preview_SpanishLanguage_SpeakEndsWithSpanishConfirmation | Spanish ending | Inspect end | UX | Unit |
| 9 | | Confirm_Success_ReturnsConfirmResult | Happy path returns result | Verify | Happy | Unit |
| 10 | | Confirm_Success_CallsInteropReschedule | Interop called | Verify call | Glue | Unit |
| 11 | | Confirm_Success_CallsPhoneBotSave | PhoneBot save called | Verify call | Glue | Unit |
| 12 | | Confirm_Success_SpanishLanguage_ReturnsSpanishSpeak | Spanish success speak | Verify | i18n | Unit |
| 13 | | Confirm_PhoneBotSaveFails_StillReturnsSuccess | PhoneBot save fail tolerated | Mock fail -> success | Resilience | Unit |
| 14 | | Confirm_InteropExternalAppointmentId_PassedToPhoneBotSave | External id forwarded | Verify | Glue | Unit |
| 15 | | Confirm_PhoneBotSaveFails_ReturnsSuccessWithOriginalAppointmentId | PhoneBot fail -> original id retained | Verify | Edge | Unit |
| 16 | | Confirm_SessionNotFound_ReturnsNulls | No session -> nulls | Verify | Negative | Unit |
| 17 | | Confirm_AppointmentNotFound_ReturnsNulls | No appt -> nulls | Verify | Negative | Unit |
| 18 | | Confirm_WithinReschedulingCutoff_ReturnsError | Within cutoff -> error | Verify | Business rule | Unit |
| 19 | | Confirm_NewTimeInPast_ReturnsError | Past new time -> error | Verify | Business rule | Unit |
| 20 | | Confirm_ExcludedProvider_ReturnsError | Excluded prov -> error | Verify | Exclusion | Unit |
| 21 | | Confirm_ExcludedLocation_ReturnsError | Excluded loc -> error | Verify | Exclusion | Unit |
| 22 | | Confirm_ExcludedVisitReason_ExistingPatient_ReturnsError | Excluded VR for EP -> error | Verify | Exclusion | Unit |
| 23 | | Confirm_ExcludedVisitReason_NewPatient_ReturnsError | Excluded VR for NP -> error | Verify | Exclusion | Unit |
| 24 | | Confirm_ZeroCutoffHours_DoesNotBlockReschedule | Cutoff=0 -> not blocked | Verify | Edge | Unit |
| 25 | | Confirm_InteropFails_ThrowsInteropRescheduleException | Interop fail -> throw typed | Verify | Error path | Unit |
| 26 | | Confirm_InteropFails_PhoneBotSaveIsNeverCalled | Interop fail -> no save | Verify | Order | Unit |
| 27 | | Confirm_Success_PassesCloudProviderIdToInterop | Cloud id passed to Interop | Verify | Glue | Unit |
| 28 | | Confirm_Success_PassesCloudProviderIdToPhoneBot | Cloud id passed to PhoneBot | Verify | Glue | Unit |
| 29 | | Confirm_ProviderNotInAllProviders_Throws | Unknown provider -> throw | Verify | Guard | Unit |
| 30 | | Confirm_WhenAllProvidersIsNull_Throws | Null providers -> throw | Verify | Guard | Unit |
| 31 | | Confirm_Success_SameBookingRequestIdSentToInteropAndPhoneBot | Same id in both calls | Verify same id | Idempotency | Unit |
| 32 | | Confirm_Success_PassesPerformingProviderIdToPhoneBot | Performing id passed | Verify | Glue | Unit |
| 33 | | BuildAppointmentInfo_UseZdProcedure_PassesProcedureFields | UseZd path passes procedure fields | Verify | Branching | Unit |
| 34 | | BuildAppointmentInfo_NullRescheduleParams_Throws | Null -> throw | Verify | Guard | Unit |
| 35 | | BuildAppointmentInfo_EmptyLocationId_Throws | Empty loc id -> throw | Verify | Guard | Unit |
| 36 | | BuildAppointmentInfo_UsesRescheduleParamsLocationId | Uses params.LocationId | Verify | Mapping | Unit |
| 37 | | BuildAppointmentInfo_UsesRescheduleParamsDurationInMinutes | Uses params duration | Verify | Mapping | Unit |
| 38 | | BuildAppointmentInfo_NullRescheduleParamsDuration_NullAppointmentDuration_ReturnsNull | Both null -> null | Verify | Edge | Unit |
| 39 | | BuildAppointmentInfo_UseDuration_NullProcedureId_AndSetsDurationFromRescheduleParams | UseDuration: procedure null, duration set | Verify | Branching | Unit |
| 40 | | BuildAppointmentInfo_AlwaysSetsExternalProcedureIdFromAppointment | External id always sourced from appt | Verify | Mapping | Unit |
| 41 | | BuildAppointmentInfo_PassesPerformingProviderIdAndStartTime | Performing prov + start time forwarded | Verify | Mapping | Unit |
| 42 | | BuildAppointmentInfo_NewPatient_SetsPatientTypeNew | NP -> patientType=New | Verify | Mapping | Unit |
| 43 | | BuildAppointmentInfo_ExistingPatient_SetsPatientTypeExisting | EP -> Existing | Verify | Mapping | Unit |
| 44 | | Confirm_Success_PassesRescheduleParamsLocationIdToPhoneBot | Params loc id forwarded | Verify | Glue | Unit |
| 45 | | Confirm_Success_PassesRescheduleParamsProcedureIdToPhoneBot | Params procedure forwarded | Verify | Glue | Unit |
| 46 | | Confirm_Success_PassesOldStartTimestampLocalToPhoneBot | Old local time forwarded | Verify | Glue | Unit |
| 47 | | Confirm_NullStartTimeLocal_PassesNullOldStartTimestampLocalToPhoneBot | Null local -> null forwarded | Verify | Null handling | Unit |
| 48 | | Confirm_NullRescheduleParams_Throws | Null params -> throw | Verify | Guard | Unit |
| 49 | | Confirm_Success_CallsPermissionCheckRecords | Permission check called | Verify | Authz | Unit |
| 50 | | Confirm_WhenUnauthorized_DoesNotCallInterop | Unauthorized -> Interop not called | Verify | Authz | Unit |
| 51 | | Confirm_WhenPatientIdNull_PassesEmptyStringToAuditLog | Null patient id -> "" in audit | Verify | Audit | Unit |
| 52 | | Confirm_CallsLockedActionServiceWithCorrectKey | Lock key correct | Verify | Locking | Unit |
| 53 | | Confirm_DifferentStartTimes_UseDifferentLockKeys | Different start times -> different keys | Verify | Locking | Unit |
| 54 | | Confirm_WhenLockTimesOut_ThrowsTimeoutException | Lock timeout -> typed | Verify | Locking | Unit |
| 55 | | ValidateReschedule_AllValid_ReturnsNull | Valid -> null | Verify | Happy | Unit |
| 56 | | ValidateReschedule_WithinCutoff_ReturnsWithinCutoffError | Within cutoff -> error | Verify | Business rule | Unit |
| 57 | | ValidateReschedule_RescheduleBlockedReasonIsWithinCutoff_ReturnsError | Pre-blocked -> error | Verify | Business rule | Unit |
| 58 | | ValidateReschedule_NewTimeInPast_ReturnsTimeInPastError | Past time -> error | Verify | Business rule | Unit |
| 59 | | ValidateReschedule_ExcludedProvider_ReturnsExcludedProviderError | Excluded prov -> error | Verify | Exclusion | Unit |
| 60 | | ValidateReschedule_ExcludedLocation_ReturnsExcludedLocationError | Excluded loc -> error | Verify | Exclusion | Unit |
| 61 | | ValidateReschedule_ExcludedVisitReason_ExistingPatient_ReturnsExcludedVisitReasonError | Excluded VR for EP -> error | Verify | Exclusion | Unit |
| 62 | | ValidateReschedule_ExcludedVisitReason_NewPatient_ReturnsExcludedVisitReasonError | Excluded VR for NP -> error | Verify | Exclusion | Unit |
| 63 | | ValidateReschedule_NullExclusions_DoesNotThrow | Null exclusions tolerated | Verify | Null safety | Unit |
| 64 | | ValidateReschedule_CutoffPriorityOverNewTimeInPast_WhenBothApply | Cutoff priority over time-in-past | Verify priority | Priority | Unit |
| 65 | | IsWithinReschedulingCutoff_WhenCutoffIsZero_ReturnsFalseWithoutCallingHelper | Zero cutoff -> false, helper not called | Verify | Optimization | Unit |
| 66 | | IsWithinReschedulingCutoff_WhenCutoffIsNegative_ReturnsFalseWithoutCallingHelper | Negative cutoff -> false | Verify | Edge | Unit |
| 67 | | IsWithinReschedulingCutoff_WhenInvalidString_ReturnsFalse | Invalid string -> false | Verify | Edge | Unit |
| 68 | | IsWithinReschedulingCutoff_WhenHelperReturnsTrue_ReturnsTrue | Helper true -> true | Verify | Glue | Unit |
| 69 | | IsWithinReschedulingCutoff_WhenHelperReturnsFalse_ReturnsFalse | Helper false -> false | Verify | Glue | Unit |
| 70 | | IsWithinReschedulingCutoff_PassesCorrectInstantAndCutoffToHelper | Correct args to helper | Verify args | Glue | Unit |

### Suggested Missing Tests
- DST transition during reschedule (cutoff math)
- Lock contention metric
- Concurrent reschedule attempts for same appt
- Audit log payload contents (currently asserts call but not payload shape)
- Behavior when Interop returns success but external id is missing
- Permission check disabled flag path

### Improvement Suggestions / Irrelevant Tests
- `Confirm_PhoneBotSaveFails_StillReturnsSuccess` and `Confirm_PhoneBotSaveFails_ReturnsSuccessWithOriginalAppointmentId` overlap; consolidate.
- `Confirm_DifferentStartTimes_UseDifferentLockKeys` is implementation-coupled; assert via key shape rather than equality of two calls.
- ValidateReschedule_* duplicated by AppointmentManagementService.ValidateReschedule_*; pick one.

---

## RescheduleParamServiceTests.cs

Resolves reschedule parameters: provider/location mapping, procedure/duration choice, special handling for the "any procedure" sentinel.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | GetRescheduleParams_UnknownLocationType_Throws | Unknown loc type -> throw | Verify | Guard | Unit |
| 2 | | GetRescheduleParams_UseZdProcedure_NewPatientHybrid_ReturnsProcedureId | NP hybrid happy path | Verify | Branching | Unit |
| 3 | | GetRescheduleParams_UseZdProcedure_EstablishedPatientHybrid_ReturnsProcedureId | EP hybrid happy path | Verify | Branching | Unit |
| 4 | | GetRescheduleParams_InvalidAvailabilityStrategyNone_Throws | None strategy -> throw | Verify | Guard | Unit |
| 5 | | GetRescheduleParams_InvalidAvailabilityStrategyCast_Throws | Bogus cast -> throw | Verify | Guard | Unit |
| 6 | | GetRescheduleParams_UseZdProcedure_NewPatientPhysical_ReturnsProcedureId | NP physical | Verify | Branching | Unit |
| 7 | | GetRescheduleParams_UseZdProcedure_NewPatientVirtual_ReturnsProcedureId | NP virtual | Verify | Branching | Unit |
| 8 | | GetRescheduleParams_UseZdProcedure_EstablishedPatientPhysical_ReturnsProcedureId | EP physical | Verify | Branching | Unit |
| 9 | | GetRescheduleParams_UseZdProcedure_EstablishedPatientVirtual_ReturnsProcedureId | EP virtual | Verify | Branching | Unit |
| 10 | | GetRescheduleParams_UseZdProcedure_NoProcedureDurationsForProvider_Throws | No durations -> throw | Verify | Guard | Unit |
| 11 | | GetRescheduleParams_UseZdProcedure_EmptyProcedureDurationsList_Throws | Empty durations -> throw | Verify | Guard | Unit |
| 12 | | GetRescheduleParams_UseZdProcedure_DurationMismatch_Throws | Duration mismatch -> throw | Verify | Guard | Unit |
| 13 | | GetRescheduleParams_UseZdProcedure_InactiveProcedure_Throws | Inactive procedure -> throw | Verify | Guard | Unit |
| 14 | | GetRescheduleParams_UseZdProcedure_ProcedureNotInZdVisitReasons_Throws | Not in ZD VRs -> throw | Verify | Guard | Unit |
| 15 | | GetRescheduleParams_UseDuration_ReturnsNullProcedureIdAndApptDuration | UseDuration -> null procedure | Verify | Branching | Unit |
| 16 | | GetRescheduleParams_UseDuration_NullDuration_Throws | UseDuration + null dur -> throw | Verify | Guard | Unit |
| 17 | | GetRescheduleParams_ProviderIdComesFromMappedProvider | ProviderId sourced from mapping | Verify | Mapping | Unit |
| 18 | | GetRescheduleParams_LocationIdComesFromFirstMappedLocation | LocationId sourced from first mapping | Verify | Mapping | Unit |
| 19 | | GetRescheduleParams_UseZdProcedure_SpecialMonolithAnyProcId_SetsOverrideToZero | Special "any" id sets override=0 | Verify | Special-case | Unit |
| 20 | | ResolveZdProcedure_MultipleProcedurescOnlyOneMatches_ReturnsMatchingProcedure | One match returned | Verify | Mapping | Unit |
| 21 | | ResolveZdProcedure_MultipleMatchingProcedures_ReturnsFirstMatch | Multi match -> first | Verify | Edge | Unit |
| 22 | | ResolveZdProcedure_AlwaysReturnsNullDurationInMinutes | Null duration always | Verify | Mapping | Unit |
| 23 | | ResolveZdProcedure_NullZdProcedureIds_Throws | Null -> throw | Verify | Guard | Unit |
| 24 | | ResolveZdProcedure_NullDurationInMinutes_Throws | Null duration -> throw | Verify | Guard | Unit |
| 25 | | ResolveZdProcedure_SingleSpecialMonolithAnyProcId_ReturnsSpecialIdAndOverrideZero | Single "any" -> special path | Verify | Special-case | Unit |
| 26 | | ResolveZdProcedure_SpecialIdWithAdditionalIds_DoesNotShortCircuit | Mixed special+others -> normal path | Verify | Edge | Unit |
| 27 | | ResolveZdProcedure_NonSpecialProcedureId_OverrideIsNull | Non-special -> override null | Verify | Mapping | Unit |
| 28 | | GetDurationForContext_NewPatientPhysicalOnly_ReturnsNewPatientDuration | Returns NP duration | Verify | Mapping | Unit |
| 29 | | GetDurationForContext_NewPatientVirtualOnly_ReturnsNewVideoDuration | NP video duration | Verify | Mapping | Unit |
| 30 | | GetDurationForContext_EstablishedPatientPhysicalOnly_ReturnsEstablishedDuration | EP duration | Verify | Mapping | Unit |
| 31 | | GetDurationForContext_EstablishedPatientVirtualOnly_ReturnsEstablishedVideoDuration | EP video duration | Verify | Mapping | Unit |

### Suggested Missing Tests
- Multi-procedure-match logging assertion (currently silent first-match is dangerous)
- Hybrid + UseDuration combination
- Non-Hybrid location types with duration override
- Zero/negative duration value in UseDuration
- Procedure marked as inactive in catalog vs. caching staleness

### Improvement Suggestions / Irrelevant Tests
- "Always" assertions ("AlwaysReturnsNullDurationInMinutes") tend to break when behavior is intentionally extended; rename to be explicit about WHY it's null.
- Coverage of the "special monolith any" sentinel is good but make a constant for the magic id used in tests so it reads better.

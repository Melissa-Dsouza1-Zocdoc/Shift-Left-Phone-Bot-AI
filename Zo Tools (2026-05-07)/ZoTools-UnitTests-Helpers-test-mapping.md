# Helpers - Unit Test Mapping

> Repo: [Zocdoc/zo-tools](https://github.com/Zocdoc/zo-tools)  
> Folder: `tests/ZoTools.UnitTests/Helpers/`  
> Generated: 2026-05-07

## AppointmentModificationHelperTests.cs

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|-------------------|-----------|---------------|-------|---------|-------|
| 1 | | IsWithinCutoff_WhenAppointmentIsInThePast_ReturnsTrue | Past appointments are always considered within cutoff | TestCase cutoffs 1/24/48; Now-1h; assert true. | Past appts are within-cutoff. | In scope: past handling. Out of scope: exact cutoff math. |
| 2 | | IsWithinCutoff_VariousDistances_ReturnsExpected | Boundary correctness around the cutoff in hours | Multiple TestCase rows for 1h and 24h cutoffs at 0/1/2/23/24/25 hours; assert. | Boundary math sanity. | In scope: <= boundary. Out of scope: timezone. |
| 3 | | IsWithinCutoff_AtExactBoundary_ReturnsTrue | At exactly cutoff, the result is true | Now+24h, cutoff 24; assert true. | Boundary inclusive. | In scope: equality boundary. Out of scope: epsilon. |
| 4 | | IsInThePast_WhenTimeIsInThePast_ReturnsTrue | Past OffsetDateTime detected | PastTime; assert true. | Past detect. | In scope: past detect. Out of scope: future. |
| 5 | | IsInThePast_WhenTimeIsInTheFuture_ReturnsFalse | Future OffsetDateTime detected as not past | FutureTime; assert false. | Future detect. | In scope: future detect. Out of scope: now. |
| 6 | | IsInThePast_WhenTimeEqualsNow_ReturnsFalse | Now is not considered past | ExactlyNow; assert false. | Now boundary. | In scope: now exclusion. Out of scope: ms precision. |
| 7 | | IsProviderExcluded_WhenProviderInExcludedList_ReturnsTrue | Provider id present in exclusions list | Build V2Exclusions with provider; assert true. | Exclusion match. | In scope: list contains. Out of scope: case-sensitivity. |
| 8 | | IsProviderExcluded_WhenProviderNotInExcludedList_ReturnsFalse | Provider id not in exclusions | Different provider; assert false. | Non-match. | In scope: non-match. Out of scope: null list. |
| 9 | | IsProviderExcluded_WhenExcludedListIsNull_ReturnsFalse | Null ProviderIds treated as no exclusions | Set ProviderIds=null; assert false. | Null safety. | In scope: null guard. Out of scope: empty. |
| 10 | | IsProviderExcluded_WhenExcludedListIsEmpty_ReturnsFalse | Empty ProviderIds treated as no exclusions | Empty list; assert false. | Empty safety. | In scope: empty guard. Out of scope: null. |
| 11 | | IsProviderExcluded_WhenAnyProviderMatches_ReturnsTrue | Any-match semantics across multiple appointment providers | Multiple providers, one excluded; assert true. | Any-match. | In scope: OR semantics. Out of scope: ordering. |
| 12 | | IsLocationExcluded_WhenLocationInExcludedList_ReturnsTrue | Same as provider, but for locations | Build V2Exclusions with location; assert true. | Location exclusion match. | In scope: location list. Out of scope: virtual flag. |
| 13 | | IsLocationExcluded_WhenLocationNotInExcludedList_ReturnsFalse | Non-match for locations | Assert false. | Non-match. | In scope: non-match. Out of scope: null. |
| 14 | | IsLocationExcluded_WhenExcludedListIsNull_ReturnsFalse | Null LocationIds treated as no exclusions | Null list; assert false. | Null safety. | In scope: null guard. Out of scope: empty. |
| 15 | | IsLocationExcluded_WhenExcludedListIsEmpty_ReturnsFalse | Empty LocationIds treated as no exclusions | Empty list; assert false. | Empty safety. | In scope: empty guard. Out of scope: null. |
| 16 | | IsLocationExcluded_WhenAnyLocationMatches_ReturnsTrue | Any-match across appointment locations | Multiple locations, one excluded; assert true. | Any-match. | In scope: OR semantics. Out of scope: virtuality. |
| 17 | | IsVisitReasonExcluded_WhenNewPatientAndReasonInNewPatientList_ReturnsTrue | New patient hits new-patient exclusion list | Build NewPatientVisitReasonIds; assert true. | New-patient exclusion. | In scope: new-patient list. Out of scope: existing list. |
| 18 | | IsVisitReasonExcluded_WhenExistingPatientAndReasonInExistingPatientList_ReturnsTrue | Existing patient hits existing-patient list | Build ExistingPatientVisitReasonIds; assert true. | Existing-patient exclusion. | In scope: existing-patient list. Out of scope: new list. |
| 19 | | IsVisitReasonExcluded_WhenNewPatientAndReasonOnlyInExistingPatientList_ReturnsFalse | New patient is not blocked by existing-patient list | Cross-list check; assert false. | List separation. | In scope: list isolation. Out of scope: union. |
| 20 | | IsVisitReasonExcluded_WhenVisitReasonNotInList_ReturnsFalse | Visit reason missing yields false | Mismatched id; assert false. | Non-match. | In scope: non-match. Out of scope: null. |
| 21 | | IsVisitReasonExcluded_WhenExcludedListIsNull_ReturnsFalse | Null lists treated as no exclusions | Default V2Exclusions; assert false. | Null safety. | In scope: null guard. Out of scope: empty. |
| 22 | | DetermineCancelModificationBlockedReason_WhenNegativeCutoff_ReturnsNull | Negative cutoff disables cutoff check entirely | Cutoff=-5; assert null. | Negative cutoff disables check. | In scope: negative cutoff. Out of scope: zero. |
| 23 | | DetermineCancelModificationBlockedReason_WhenAppointmentInPast_ReturnsWithinCutoffHours | Past appt -> WithinCutoffHours regardless of cutoff value | TestCase -5/0/24 with PastTime; assert WithinCutoffHours. | Past blocks cancel. | In scope: past handling. Out of scope: other rules. |
| 24 | | DetermineCancelModificationBlockedReason_WhenWithinCutoff_ReturnsWithinCutoffHours | Future appt within cutoff blocks cancel | FutureTime + cutoff 24; assert WithinCutoffHours. | Cutoff blocks cancel. | In scope: cutoff. Out of scope: exclusions. |
| 25 | | DetermineCancelModificationBlockedReason_WhenWithinCutoff_RecordsMetric | DataDog metric tagged within_cutoff_hours | Same setup; verify metric. | Cutoff metric. | In scope: tag. Out of scope: count. |
| 26 | | DetermineCancelModificationBlockedReason_WhenNoZoMappedProviders_ReturnsProvLocMappingIssue | No mapped providers -> ProvLocMappingIssue | Unmapped provider; assert. | Mapping required. | In scope: provider mapping. Out of scope: location. |
| 27 | | DetermineCancelModificationBlockedReason_WhenNoZoMappedLocations_ReturnsProvLocMappingIssue | No mapped locations -> ProvLocMappingIssue | Empty zoMappedLocations; assert. | Location mapping required. | In scope: location mapping. Out of scope: provider. |
| 28 | | DetermineCancelModificationBlockedReason_WhenNoZoMappedProvidersOrLocations_ReturnsProvLocMappingIssue | Both missing -> single ProvLocMappingIssue | Both empty/unmapped; assert. | Combined missing-mapping. | In scope: union. Out of scope: ordering. |
| 29 | | DetermineCancelModificationBlockedReason_WhenProvLocMappingIssue_RecordsMetric | Tag prov_loc_mapping_issue emitted | verify metric. | Mapping metric. | In scope: tag. Out of scope: count. |
| 30 | | DetermineCancelModificationBlockedReason_WhenMultipleZoMappedProviders_DoesNotBlock | Cancel allowed even with multiple mapped providers (unlike reschedule) | Two mapped providers; assert NOT MultipleMappedProviders. | Cancel does not require single provider. | In scope: cancel-vs-reschedule diff. Out of scope: reschedule. |
| 31 | | DetermineCancelModificationBlockedReason_WhenMixedVirtualAndPhysicalLocations_DoesNotBlock | Hybrid locations don't block cancel | Mixed virtual/physical; assert NOT MultipleMappedHybridLocations. | Hybrid OK for cancel. | In scope: hybrid. Out of scope: reschedule. |
| 32 | | DetermineCancelModificationBlockedReason_WhenProviderExcluded_ReturnsExcludedProvider | Excluded provider blocks cancel | Build exclusions with provider; assert. | Provider exclusion. | In scope: exclusion. Out of scope: location. |
| 33 | | DetermineCancelModificationBlockedReason_WhenProviderExcluded_RecordsMetric | Metric tag excluded_provider | verify metric. | Provider metric. | In scope: tag. Out of scope: count. |
| 34 | | DetermineCancelModificationBlockedReason_WhenLocationExcluded_ReturnsExcludedLocation | Excluded location blocks cancel | Build exclusions with location; assert. | Location exclusion. | In scope: exclusion. Out of scope: provider. |
| 35 | | DetermineCancelModificationBlockedReason_WhenLocationExcluded_RecordsMetric | Metric tag excluded_location | verify metric. | Location metric. | In scope: tag. Out of scope: count. |
| 36 | | DetermineCancelModificationBlockedReason_WhenNoZdVisitReasonIds_ReturnsVisitReasonMappingIssue | Empty visit reason list -> VisitReasonMappingIssue | empty zdVisitReasonIds; assert. | Visit-reason mapping required. | In scope: empty list. Out of scope: null. |
| 37 | | DetermineCancelModificationBlockedReason_WhenNoZdVisitReasonIds_RecordsMetric | Metric tag visit_reason_mapping_issue | verify metric. | Mapping metric. | In scope: tag. Out of scope: count. |
| 38 | | DetermineCancelModificationBlockedReason_WhenZdVisitReasonIdsNull_ReturnsVisitReasonMappingIssue | Null list also -> VisitReasonMappingIssue | Null list via context; assert. | Null safety. | In scope: null. Out of scope: empty. |
| 39 | | DetermineCancelModificationBlockedReason_WhenNewPatientVisitReasonExcluded_ReturnsExcludedVisitReason | Excluded visit reason for new patient | NewPatientVisitReasonIds; assert. | Visit-reason exclusion (new). | In scope: new exclusion. Out of scope: existing. |
| 40 | | DetermineCancelModificationBlockedReason_WhenExistingPatientVisitReasonExcluded_ReturnsExcludedVisitReason | Excluded visit reason for existing patient | ExistingPatientVisitReasonIds; assert. | Visit-reason exclusion (existing). | In scope: existing exclusion. Out of scope: new. |
| 41 | | DetermineCancelModificationBlockedReason_WhenVisitReasonExcluded_RecordsMetric | Metric tag excluded_visit_reason | verify metric. | Metric. | In scope: tag. Out of scope: count. |
| 42 | | DetermineCancelModificationBlockedReason_WhenNothingBlocked_ReturnsNull | All checks pass returns null | Default context; assert null. | Allowed path. | In scope: allowed. Out of scope: metrics. |
| 43 | | DetermineCancelModificationBlockedReason_WhenNothingBlocked_RecordsAllowedMetric | Allowed metric tag emitted | verify metric tag result:allowed. | Allowed metric. | In scope: tag. Out of scope: count. |
| 44 | | DetermineCancelModificationBlockedReason_WhenCutoffAndExclusionApply_PrioritizesCutoff | Priority: cutoff over exclusion | Combined; assert WithinCutoffHours wins. | Priority order. | In scope: priority. Out of scope: chained. |
| 45 | | DetermineCancelModificationBlockedReason_WhenProvLocAndExclusionApply_PrioritizesProvLoc | Priority: prov/loc mapping over exclusion | Combined; assert ProvLocMappingIssue wins. | Priority order. | In scope: priority. Out of scope: chained. |
| 46 | | DetermineCancelModificationBlockedReason_WhenNullExclusions_ReturnsNull | Null exclusions yields null reason (allowed) | Null V2Exclusions; assert null. | Null safety. | In scope: null exclusions. Out of scope: empty. |
| 47 | | DetermineCancelModificationBlockedReason_WhenNullProviders_ReturnsProvLocMappingIssue | Null providers list -> ProvLocMappingIssue | Null providers; assert. | Null providers. | In scope: null providers. Out of scope: empty. |
| 48 | | DetermineCancelModificationBlockedReason_WhenNullZoMappedLocations_ReturnsProvLocMappingIssue | Null locations list -> ProvLocMappingIssue | Null locations; assert. | Null locations. | In scope: null locations. Out of scope: empty. |
| 49 | | DetermineRescheduleModificationBlockedReason_WhenWithinCutoff_ReturnsWithinCutoffHours | Within cutoff blocks reschedule | FutureTime + 24h; assert. | Reschedule cutoff. | In scope: cutoff. Out of scope: cancel. |
| 50 | | DetermineRescheduleModificationBlockedReason_WhenAppointmentInPast_ReturnsWithinCutoffHours | Past appt blocks reschedule | TestCase -5/0/24 with PastTime; assert. | Past blocks reschedule. | In scope: past handling. Out of scope: cancel. |
| 51 | | DetermineRescheduleModificationBlockedReason_WhenWithinCutoff_RecordsMetric | DataDog metric reschedule_blocked_reason within_cutoff_hours | verify metric. | Metric. | In scope: tag. Out of scope: count. |
| 52 | | DetermineRescheduleModificationBlockedReason_WhenNoZoMappedProviders_ReturnsProvLocMappingIssue | No mapped providers blocks reschedule | Unmapped; assert. | Mapping required. | In scope: provider. Out of scope: location. |
| 53 | | DetermineRescheduleModificationBlockedReason_WhenNoZoMappedLocations_ReturnsProvLocMappingIssue | No mapped locations blocks reschedule | Empty locations; assert. | Mapping required. | In scope: location. Out of scope: provider. |
| 54 | | DetermineRescheduleModificationBlockedReason_WhenProvLocMappingIssue_RecordsMetric | Metric tag prov_loc_mapping_issue | verify metric. | Metric. | In scope: tag. Out of scope: count. |
| 55 | | DetermineRescheduleModificationBlockedReason_WhenMultipleMappedProviders_ReturnsMultipleMappedProviders | Reschedule requires a single mapped provider | Two mapped providers; assert MultipleMappedProviders. | Reschedule-specific rule. | In scope: multi-provider. Out of scope: cancel. |
| 56 | | DetermineRescheduleModificationBlockedReason_WhenMultipleMappedProviders_RecordsMetric | Metric tag multiple_mapped_providers | verify metric. | Metric. | In scope: tag. Out of scope: count. |
| 57 | | DetermineRescheduleModificationBlockedReason_WhenMixedVirtualAndPhysicalLocations_DoesNotBlock | Hybrid locations don't block reschedule | Mixed virtual/physical; assert null. | Hybrid OK. | In scope: hybrid. Out of scope: cancel. |
| 58 | | DetermineRescheduleModificationBlockedReason_WhenProviderExcluded_ReturnsExcludedProvider | Provider exclusion blocks reschedule | Excluded provider; assert. | Exclusion. | In scope: exclusion. Out of scope: location. |
| 59 | | DetermineRescheduleModificationBlockedReason_WhenProviderExcluded_RecordsMetric | Metric tag excluded_provider | verify metric. | Metric. | In scope: tag. Out of scope: count. |
| 60 | | DetermineRescheduleModificationBlockedReason_WhenLocationExcluded_ReturnsExcludedLocation | Location exclusion blocks reschedule | Excluded location; assert. | Exclusion. | In scope: exclusion. Out of scope: provider. |
| 61 | | DetermineRescheduleModificationBlockedReason_WhenLocationExcluded_RecordsMetric | Metric tag excluded_location | verify metric. | Metric. | In scope: tag. Out of scope: count. |
| 62 | | DetermineRescheduleModificationBlockedReason_WhenNoZdVisitReasonIds_ReturnsVisitReasonMappingIssue | Empty visit reason list -> VisitReasonMappingIssue | Empty list; assert. | Mapping. | In scope: empty list. Out of scope: null. |
| 63 | | DetermineRescheduleModificationBlockedReason_WhenNoZdVisitReasonIds_RecordsMetric | Metric tag visit_reason_mapping_issue | verify metric. | Metric. | In scope: tag. Out of scope: count. |
| 64 | | DetermineRescheduleModificationBlockedReason_WhenZdVisitReasonIdsNull_ReturnsVisitReasonMappingIssue | Null visit reason list -> VisitReasonMappingIssue | Null; assert. | Null safety. | In scope: null. Out of scope: empty. |
| 65 | | DetermineRescheduleModificationBlockedReason_WhenNewPatientVisitReasonExcluded_ReturnsExcludedVisitReason | New patient visit reason excluded | NewPatientVisitReasonIds; assert. | Exclusion (new). | In scope: new exclusion. Out of scope: existing. |
| 66 | | DetermineRescheduleModificationBlockedReason_WhenExistingPatientVisitReasonExcluded_ReturnsExcludedVisitReason | Existing patient visit reason excluded | ExistingPatientVisitReasonIds; assert. | Exclusion (existing). | In scope: existing. Out of scope: new. |
| 67 | | DetermineRescheduleModificationBlockedReason_WhenVisitReasonExcluded_RecordsMetric | Metric tag excluded_visit_reason | verify metric. | Metric. | In scope: tag. Out of scope: count. |
| 68 | | DetermineRescheduleModificationBlockedReason_WhenNothingBlocked_ReturnsNull | All checks pass; null | Default; assert null. | Allowed path. | In scope: allowed. Out of scope: metrics. |
| 69 | | DetermineRescheduleModificationBlockedReason_WhenNothingBlocked_RecordsAllowedMetric | Allowed metric tag | verify metric tag allowed. | Metric. | In scope: tag. Out of scope: count. |
| 70 | | DetermineRescheduleModificationBlockedReason_WhenTwoPhysicalLocations_DoesNotBlock | Two physical locations are allowed | Two physical; assert null. | Two physical allowed. | In scope: physical. Out of scope: virtual. |
| 71 | | DetermineRescheduleModificationBlockedReason_WhenTwoVirtualLocations_DoesNotBlock | Two virtual locations are allowed | Two virtual; assert null. | Two virtual allowed. | In scope: virtual. Out of scope: physical. |
| 72 | | DetermineRescheduleModificationBlockedReason_WhenCutoffAndMultipleProvidersApply_PrioritizesCutoff | Cutoff outranks multiple-providers rule | Combined; assert WithinCutoffHours. | Priority. | In scope: priority. Out of scope: chained. |
| 73 | | DetermineRescheduleModificationBlockedReason_WhenProvLocAndMultipleProvidersApply_PrioritizesProvLoc | ProvLoc outranks multiple-providers rule | Combined; assert ProvLocMappingIssue. | Priority. | In scope: priority. Out of scope: chained. |
| 74 | | DetermineRescheduleModificationBlockedReason_WhenNullExclusions_ReturnsNull | Null exclusions = allowed | Null V2Exclusions; assert null. | Null safety. | In scope: null. Out of scope: empty. |
| 75 | | DetermineRescheduleModificationBlockedReason_WhenNullProviders_ReturnsProvLocMappingIssue | Null providers -> ProvLocMappingIssue | Null providers; assert. | Null providers. | In scope: null. Out of scope: empty. |
| 76 | | DetermineRescheduleModificationBlockedReason_WhenNullZoMappedLocations_ReturnsProvLocMappingIssue | Null locations -> ProvLocMappingIssue | Null locations; assert. | Null locations. | In scope: null. Out of scope: empty. |
| 77 | | DetermineRescheduleModificationBlockedReason_WhenNegativeCutoff_ReturnsNull | Negative cutoff disables cutoff check | Cutoff=-5; assert null. | Negative cutoff. | In scope: negative cutoff. Out of scope: zero. |

### Suggested Missing Tests
- **DetermineCancelModificationBlockedReason_WhenMultipleHybridLocations_BlocksOrAllowsExplicitly** — There is a `MultipleMappedHybridLocations` enum value referenced for cancel ("DoesNotBlock") but no positive test asserts when that enum *is* returned.
- **TimezoneSensitivity** — All tests use `Offset.Zero`. Add cases that test cutoff math when the appointment's offset differs from the clock's offset.
- **DST_BoundaryAroundCutoff** — A spring-forward / fall-back appointment should be tested for off-by-one-hour bugs.
- **IsWithinCutoff_WithFractionalCutoff** — `cutoffInHours` is `int`, but business may want sub-hour rules; flag if fractional support is intentional.
- **VeryLargeCutoffOrFarFutureAppointment** — Check no overflow when cutoff = `int.MaxValue`.
- **EmptyProviderList vs NullProviderList parity** — Currently only null providers is tested; empty list may have different behavior.

### Improvement Suggestions / Irrelevant Tests
- **Heavy metric verification** — Many tests verify a metric tag was emitted; consider grouping into one parameterized test per method. Otherwise per CS-021 these become implementation-coupled.
- **Cut-off priority chains** — Only two priority pairs are tested for each method; consider parameterizing every priority comparison (cutoff > prov_loc > exclusion > visit_reason).
- **`CreateContext` vs explicit `new ModificationContext`** — Most tests use the helper, but null-path tests construct `ModificationContext` directly. Add an explicit nullable parameter to `CreateContext` to reduce duplication.

## LocationNameHelperTests.cs

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|-------------------|-----------|---------------|-------|---------|-------|
| 1 | | ExpandAddress_ReturnsExpected | End-to-end address expansion incl. street suffixes & directionals | TestCase rows for typical inputs incl. empty; assert. | Address expansion happy paths. | In scope: full address. Out of scope: state. |
| 2 | | ExpandAddressAbbreviations_ReturnsExpected | Per-abbreviation expansion table (St/Ave/Blvd/etc + directionals) | 30+ TestCase rows; assert. | Abbreviation table. | In scope: per-suffix. Out of scope: state. |
| 3 | | ExpandAddressAbbreviations_WithMultipleAbbreviations_ExpandsAll | Multiple abbrevs in one string all expand | "123 N Main St Ste 100" -> all expanded. | Multi-token. | In scope: composability. Out of scope: ordering. |
| 4 | | ExpandAddressAbbreviations_IsMatchedCaseInsensitively | Lowercase abbreviations also expand | "123 main st" -> "123 main Street". | Case insensitive match. | In scope: case. Out of scope: locale. |
| 5 | | ExpandAddressAbbreviations_WithNumbers_LeavesNumbersUnchanged | Numbers like "5th" preserved | "1234 5th Ave" -> "1234 5th Avenue". | Token-boundary safety. | In scope: numerics. Out of scope: punctuation. |
| 6 | | ExpandStateAbbreviation_KnownState_ReturnsFullName | All 51 abbreviations expand to full name | One TestCase per state + DC; assert. | State expansion table. | In scope: 50 + DC. Out of scope: territories. |
| 7 | | ExpandStateAbbreviation_IsMatchedCaseInsensitively | Lowercase state code expands | "ny" -> "New York". | Case insensitive. | In scope: case. Out of scope: trim. |
| 8 | | ExpandStateAbbreviation_UnknownCode_ReturnsOriginal | Unknown code passes through unchanged | "ZZ" -> "ZZ". | Unknown passthrough. | In scope: passthrough. Out of scope: throw. |
| 9 | | ResolveLocationName_WithAddressAndCity_ReturnsFormattedAddress | Address+city -> "<expanded address> in <city>" | Provide all three; assert. | Composition. | In scope: composition. Out of scope: fallback. |
| 10 | | ResolveLocationName_WithMissingAddress_ReturnsFallbackName | Null address falls back to provided name | Null address; assert fallback. | Null fallback. | In scope: null. Out of scope: empty. |
| 11 | | ResolveLocationName_WithMissingCity_ReturnsFallbackName | Null city falls back to provided name | Null city; assert fallback. | Null fallback. | In scope: null. Out of scope: empty. |
| 12 | | ResolveLocationName_WithAllNull_ReturnsEmptyString | All null inputs -> empty string | Pass nulls; assert "". | Empty fallback. | In scope: empty. Out of scope: null exception. |
| 13 | | ResolveLocationName_WithEmptyAddress_ReturnsFallbackName | Empty string address falls back | ""; assert fallback. | Empty fallback. | In scope: empty. Out of scope: null. |
| 14 | | ResolveLocationName_WithEmptyCity_ReturnsFallbackName | Empty string city falls back | ""; assert fallback. | Empty fallback. | In scope: empty. Out of scope: null. |

### Suggested Missing Tests
- **ExpandAddressAbbreviations_AbbreviationInsideWord_DoesNotExpand** — Confirm that "Stephanie" is not turned into "Streetphanie" (word boundary correctness).
- **ExpandAddressAbbreviations_TrailingPunctuation** — "123 Main St." (with period) should still expand.
- **ExpandStateAbbreviation_WithLeadingTrailingSpaces** — Currently no whitespace handling test.
- **ExpandStateAbbreviation_NullInput** — Behavior for null code (throw vs return null).
- **ResolveLocationName_AllEmptyStrings** — Returns empty? Symmetric with all-null case.
- **ResolveLocationName_AddressWithoutAbbreviations** — Confirm no double-expansion or whitespace artifacts when input is already expanded.

### Improvement Suggestions / Irrelevant Tests
- **Test `ExpandAddressAbbreviations_IsMatchedCaseInsensitively` only verifies lowercase** — Mixed case ("Mn ST") should also be tested.
- **All-states test is one big `[TestCase]` matrix** — Coverage is great, but it's 51 lines and obscures other tests; consider moving the state map to a fixture and asserting equivalence.

## TtsTimeFormatterTests.cs

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|-------------------|-----------|---------------|-------|---------|-------|
| 1 | | FormatForTts_MorningTime_ReturnsSpokenEnglish | Format LocalDateTime for TTS in morning English | LocalDateTime 10:00; lang en-US; assert exact phrase. | Morning English. | In scope: language. Out of scope: V2. |
| 2 | | FormatForTts_AfternoonWithMinutes_ReturnsSpokenEnglish | Afternoon hh:mm with PM in English | 14:30; assert "two thirty PM". | Afternoon English. | In scope: PM. Out of scope: V2. |
| 3 | | FormatForTts_Noon_ReturnsSpokenEnglish | 12:00 PM correctly says "twelve PM" | 12:00; assert. | Noon edge case. | In scope: noon. Out of scope: midnight. |
| 4 | | FormatForTts_EveningWithMinutes_ReturnsSpokenEnglish | Evening time formats correctly | 17:45; assert. | Evening. | In scope: PM. Out of scope: V2. |
| 5 | | FormatForTts_NullLanguage_DefaultsToEnglish | Null language -> English | language=null; assert English. | Null defaulting. | In scope: default. Out of scope: invalid. |
| 6 | | FormatForTts_MorningTime_ReturnsSpokenSpanish | Morning Spanish formatting | es-US; assert phrase. | Morning Spanish. | In scope: Spanish. Out of scope: V2. |
| 7 | | FormatForTts_AfternoonWithMinutes_ReturnsSpokenSpanish | Afternoon Spanish formatting | es-US 14:30; assert "tarde". | Afternoon Spanish. | In scope: Spanish. Out of scope: V2. |
| 8 | | FormatForTts_OneOClock_ReturnsSpokenSpanish | "a la una" singular article for 1pm | 13:00; assert "a la una de la tarde". | Singular article rule. | In scope: locale grammar. Out of scope: V2. |
| 9 | | FormatForTts_OneAM_ReturnsSingularArticle | 1am also uses "a la una" | 01:00; assert. | Singular article in AM. | In scope: AM grammar. Out of scope: PM. |
| 10 | | FormatForTts_NightTime_ReturnsNochePeriod | 21:00 -> "noche" period | 21:00; assert "de la noche". | Night period. | In scope: night. Out of scope: V2. |
| 11 | | FormatStartTimeForTts_ValidOffset_ReturnsFormattedTime | OffsetDateTime ISO string parsed and formatted | "2026-05-01T09:00:00+00:00"; assert. | OffsetDateTime parser. | In scope: parsing. Out of scope: invalid. |
| 12 | | FormatStartTimeForTts_NullInput_ReturnsNull | Null input -> null | null; assert null. | Null safety. | In scope: null. Out of scope: empty. |
| 13 | | FormatStartTimeForTts_InvalidFormat_ReturnsNull | Invalid string -> null | "not-a-date"; assert null. | Parse failure. | In scope: invalid. Out of scope: throw. |
| 14 | | FormatStartTimeForTts_SpanishLanguage_ReturnsSpanish | Spanish localization works through parser | es-US; assert contains "mayo". | Localization wiring. | In scope: locale wiring. Out of scope: full string. |
| 15 | | FormatLocalTimeForTts_ValidLocalTime_ReturnsFormattedTime | LocalDateTime ISO string parsed | "2026-05-01T09:00:00"; assert. | LocalDateTime parser. | In scope: parsing. Out of scope: offset. |
| 16 | | FormatLocalTimeForTts_AfternoonWithMinutes_ReturnsFormattedTime | Afternoon LocalDateTime | "2026-04-06T14:30:00"; assert. | Afternoon parse. | In scope: parsing. Out of scope: offset. |
| 17 | | FormatLocalTimeForTts_NullInput_ReturnsNull | Null input -> null | null; assert. | Null safety. | In scope: null. Out of scope: empty. |
| 18 | | FormatLocalTimeForTts_EmptyInput_ReturnsNull | Empty string -> null | ""; assert. | Empty safety. | In scope: empty. Out of scope: whitespace. |
| 19 | | FormatLocalTimeForTts_InvalidFormat_ReturnsNull | Invalid string -> null | "not-a-date"; assert null. | Invalid parse. | In scope: invalid. Out of scope: throw. |
| 20 | | FormatLocalTimeForTts_SpanishLanguage_ReturnsSpanish | Spanish through LocalDateTime parser | es-US; assert contains "mayo". | Localization wiring. | In scope: locale. Out of scope: full string. |
| 21 | | BuildBookingPreviewSpeak_English_ContainsTimeAndProvider | Booking preview English exact phrase | All inputs; assert exact string. | Preview English. | In scope: text. Out of scope: V2. |
| 22 | | BuildBookingPreviewSpeak_NullTime_UsesFallback | Null time uses "the requested time" fallback | Null time; assert contains fallback. | Null-time fallback. | In scope: fallback. Out of scope: V2. |
| 23 | | BuildBookingPreviewSpeak_NullProvider_UsesFallback | Null provider uses "your provider" fallback | Null provider; assert contains fallback. | Null-provider fallback. | In scope: fallback. Out of scope: V2. |
| 24 | | BuildBookingPreviewSpeak_WhitespaceOnlyProvider_UsesFallback | Whitespace provider -> fallback | "   "; assert fallback. | Whitespace handling. | In scope: whitespace. Out of scope: empty. |
| 25 | | BuildBookingPreviewSpeak_NullLocation_OmitsLocation | Null location omits "at <addr>" appendix | Null location; assert no "at 200 River Road". | Location omission. | In scope: omission. Out of scope: V2. |
| 26 | | BuildBookingPreviewSpeak_Spanish_ReturnsSpanish | Spanish preview phrase | es-US; assert contains key Spanish phrases. | Spanish preview. | In scope: Spanish. Out of scope: exact. |
| 27 | | BuildBookingConfirmSpeak_English_ContainsBookedAndProvider | Confirm English exact phrase | All inputs; assert exact. | Confirm English. | In scope: text. Out of scope: V2. |
| 28 | | BuildBookingConfirmSpeak_Spanish_ReturnsSpanish | Confirm Spanish | es-US; assert contains "ha sido reservada". | Spanish confirm. | In scope: Spanish. Out of scope: exact. |
| 29 | | BuildBookingConfirmSpeak_MissingProvider_UsesFallback | Confirm null provider fallback | Null provider; assert. | Fallback. | In scope: fallback. Out of scope: V2. |
| 30 | | BuildBookingConfirmSpeak_MissingLocation_OmitsLocation | Confirm null location omits | Null location; assert. | Omission. | In scope: omission. Out of scope: V2. |
| 31 | | BuildBookingConfirmSpeak_NullTime_UsesFallback | Confirm null time -> "the scheduled time" | Null time; assert. | Time fallback. | In scope: fallback. Out of scope: V2. |
| 32 | | BuildCancelPreviewSpeak_English_ContainsTimeAndProvider | Cancel preview English exact | All inputs; assert exact. | Cancel preview English. | In scope: text. Out of scope: V2. |
| 33 | | BuildCancelPreviewSpeak_Spanish_ReturnsSpanish | Cancel preview Spanish | es-US; assert. | Spanish. | In scope: Spanish. Out of scope: exact. |
| 34 | | BuildCancelPreviewSpeak_MissingProvider_UsesFallback | Null provider fallback | Null provider; assert. | Fallback. | In scope: fallback. Out of scope: V2. |
| 35 | | BuildCancelPreviewSpeak_MissingLocation_OmitsLocation | Null location omitted | Null location; assert. | Omission. | In scope: omission. Out of scope: V2. |
| 36 | | BuildCancelPreviewSpeak_NullTime_UsesFallback | Null time -> "the scheduled time" | Null time; assert. | Fallback. | In scope: fallback. Out of scope: V2. |
| 37 | | BuildCancelConfirmSpeak_English_ContainsCancelledAndProvider | Cancel confirm English | Asserts contains "cancelled" + provider. | Cancel confirm. | In scope: text. Out of scope: V2. |
| 38 | | BuildCancelConfirmSpeak_Spanish_ReturnsSpanish | Cancel confirm Spanish | es-US; assert "cancelada". | Spanish. | In scope: Spanish. Out of scope: exact. |
| 39 | | BuildCancelConfirmSpeak_MissingProvider_UsesFallback | Null provider fallback | Null provider; assert. | Fallback. | In scope: fallback. Out of scope: V2. |
| 40 | | BuildCancelConfirmSpeak_NullTime_UsesFallback | Null time fallback | Null time; assert. | Fallback. | In scope: fallback. Out of scope: V2. |
| 41 | | BuildReschedulePreviewSpeak_English_ContainsTimeAndProvider | Reschedule preview English | Exact; assert. | Reschedule preview. | In scope: text. Out of scope: V2. |
| 42 | | BuildReschedulePreviewSpeak_Spanish_ReturnsSpanish | Reschedule preview Spanish | es-US; assert. | Spanish. | In scope: Spanish. Out of scope: exact. |
| 43 | | BuildReschedulePreviewSpeak_MissingProvider_UsesFallback | Null provider fallback | Null; assert. | Fallback. | In scope: fallback. Out of scope: V2. |
| 44 | | BuildReschedulePreviewSpeak_MissingLocation_OmitsLocation | Null location omitted | Null; assert. | Omission. | In scope: omission. Out of scope: V2. |
| 45 | | BuildReschedulePreviewSpeak_SpanishMissingProvider_UsesSpanishFallback | Spanish "su proveedor" fallback | es-US, null provider; assert. | Spanish fallback. | In scope: Spanish fallback. Out of scope: V2. |
| 46 | | BuildReschedulePreviewSpeak_EmptyLocationName_OmitsLocationAppendix | Empty location string omitted | ""; assert. | Empty omission. | In scope: empty. Out of scope: null. |
| 47 | | BuildReschedulePreviewSpeak_WhitespaceLocationName_OmitsLocationAppendix | Whitespace location omitted | "   "; assert. | Whitespace omission. | In scope: whitespace. Out of scope: null. |
| 48 | | BuildReschedulePreviewSpeak_Spanish_WithLocation_IncludesSpanishPreposition | Spanish " en " preposition included | es-US + location; assert " en 200 River Road". | Spanish preposition. | In scope: Spanish. Out of scope: V2. |
| 49 | | BuildReschedulePreviewSpeak_EmptyProviderName_UsesFallback | Empty provider name fallback | ""; assert. | Empty fallback. | In scope: empty. Out of scope: null. |
| 50 | | BuildRescheduleConfirmSpeak_English_ContainsRescheduledAndProvider | Reschedule confirm English contains text | Asserts contains. | Reschedule confirm. | In scope: text. Out of scope: V2. |
| 51 | | BuildRescheduleConfirmSpeak_English_ExactFormat | Reschedule confirm English exact phrase | Exact. | Exact text. | In scope: text. Out of scope: V2. |
| 52 | | BuildRescheduleConfirmSpeak_Spanish_ExactFormat | Reschedule confirm Spanish exact phrase | Exact. | Spanish exact. | In scope: Spanish. Out of scope: V2. |
| 53 | | BuildRescheduleConfirmSpeak_Spanish_ReturnsSpanish | Reschedule confirm Spanish contains | Asserts contains. | Spanish. | In scope: Spanish. Out of scope: V2. |
| 54 | | BuildRescheduleConfirmSpeak_MissingProvider_UsesFallback | Null provider fallback | Null; assert. | Fallback. | In scope: fallback. Out of scope: V2. |
| 55 | | BuildRescheduleConfirmSpeak_SpanishMissingProvider_UsesSpanishFallback | Spanish "su proveedor" | es-US null provider; assert. | Spanish fallback. | In scope: Spanish fallback. Out of scope: V2. |
| 56 | | BuildRescheduleConfirmSpeak_EmptyProviderName_UsesFallback | Empty provider fallback | ""; assert. | Empty fallback. | In scope: empty. Out of scope: null. |
| 57 | | FormatForTts_V2_English_OmitsDayOfWeek | V2 omits day-of-week prefix in English | useVoiceQualityV2:true; assert no "Monday". | V2 English. | In scope: V2. Out of scope: V1. |
| 58 | | FormatForTts_V2_English_AfternoonWithMinutes | V2 English afternoon | V2; 14:30; assert. | V2 English afternoon. | In scope: V2. Out of scope: V1. |
| 59 | | FormatForTts_V2_NullLanguage_DefaultsToEnglishWithoutDayOfWeek | V2 null language defaults to English | V2; null lang; assert. | V2 default lang. | In scope: V2 default. Out of scope: V1. |
| 60 | | FormatForTts_V2_Spanish_OmitsDayOfWeek | V2 Spanish omits day-of-week | V2; es-US; assert. | V2 Spanish. | In scope: V2 Spanish. Out of scope: V1. |
| 61 | | FormatForTts_V2_Spanish_AfternoonWithMinutes | V2 Spanish afternoon | V2; es-US; 14:30; assert. | V2 Spanish afternoon. | In scope: V2 Spanish. Out of scope: V1. |
| 62 | | FormatStartTimeForTts_V2_OmitsDayOfWeek | V2 with offset string omits day | V2; ISO offset; assert. | V2 parser. | In scope: V2 parsing. Out of scope: V1. |
| 63 | | FormatLocalTimeForTts_V2_OmitsDayOfWeek | V2 with local string omits day | V2; ISO local; assert. | V2 parser. | In scope: V2 parsing. Out of scope: V1. |
| 64 | | BuildBookingConfirmSpeak_V2_English_Default | V2 booking confirm English default phrase | V2; all inputs; exact. | V2 confirm English. | In scope: V2. Out of scope: V1. |
| 65 | | BuildBookingConfirmSpeak_V2_English_NoTime_HasLocation | V2 confirm English w/o time | V2; null time; exact. | V2 fallback. | In scope: V2. Out of scope: V1. |
| 66 | | BuildBookingConfirmSpeak_V2_English_NoProvider_HasTimeAndLocation | V2 confirm English w/o provider | V2; null provider; exact. | V2 fallback. | In scope: V2. Out of scope: V1. |
| 67 | | BuildBookingConfirmSpeak_V2_English_NoLocation | V2 confirm English w/o location | V2; null loc; exact. | V2 fallback. | In scope: V2. Out of scope: V1. |
| 68 | | BuildBookingConfirmSpeak_V2_Spanish_Default | V2 confirm Spanish default | V2; es-US; exact. | V2 Spanish. | In scope: V2 Spanish. Out of scope: V1. |
| 69 | | BuildBookingConfirmSpeak_V2_Spanish_NoTime_HasLocation | V2 confirm Spanish w/o time | V2; null time; exact. | V2 fallback. | In scope: V2. Out of scope: V1. |
| 70 | | BuildBookingConfirmSpeak_V2_Spanish_NoProvider_HasTimeAndLocation | V2 confirm Spanish w/o provider | V2; null prov; exact. | V2 fallback. | In scope: V2. Out of scope: V1. |
| 71 | | BuildCancelConfirmSpeak_V2_English_Default | V2 cancel confirm English default | V2; exact. | V2 cancel English. | In scope: V2. Out of scope: V1. |
| 72 | | BuildCancelConfirmSpeak_V2_English_NoTime | V2 cancel English null time | V2; exact. | V2 fallback. | In scope: V2. Out of scope: V1. |
| 73 | | BuildCancelConfirmSpeak_V2_English_NoProvider | V2 cancel English null provider | V2; exact. | V2 fallback. | In scope: V2. Out of scope: V1. |
| 74 | | BuildCancelConfirmSpeak_V2_Spanish_Default | V2 cancel Spanish default | V2; es-US; exact. | V2 Spanish. | In scope: V2 Spanish. Out of scope: V1. |
| 75 | | BuildCancelConfirmSpeak_V2_Spanish_NoTime | V2 cancel Spanish null time | V2; exact. | V2 fallback. | In scope: V2. Out of scope: V1. |
| 76 | | BuildCancelConfirmSpeak_V2_Spanish_NoProvider | V2 cancel Spanish null provider | V2; exact. | V2 fallback. | In scope: V2. Out of scope: V1. |
| 77 | | BuildRescheduleConfirmSpeak_V2_English_Default | V2 reschedule confirm English default | V2; exact. | V2 reschedule. | In scope: V2. Out of scope: V1. |
| 78 | | BuildRescheduleConfirmSpeak_V2_English_NoProvider | V2 reschedule English null provider | V2; exact. | V2 fallback. | In scope: V2. Out of scope: V1. |
| 79 | | BuildRescheduleConfirmSpeak_V2_Spanish_Default | V2 reschedule Spanish default | V2; es-US; exact. | V2 Spanish. | In scope: V2 Spanish. Out of scope: V1. |
| 80 | | BuildRescheduleConfirmSpeak_V2_Spanish_NoProvider | V2 reschedule Spanish null provider | V2; exact. | V2 fallback. | In scope: V2. Out of scope: V1. |

### Suggested Missing Tests
- **Midnight (00:00) handling in English/Spanish** — Currently noon is tested but midnight is not.
- **00:30, 12:30 grammar correctness** — Confirm "twelve thirty AM/PM" specifically.
- **Spanish minute words for 1-9 minutes** — e.g. "y cinco" / "y nueve" — only "y treinta" is exercised.
- **Unsupported language code (e.g. "fr-FR") behavior** — Defaults to English? Throws? Document with a test.
- **Year boundary / leap-day formatting** — Feb 29 on a leap year, Dec 31 -> Jan 1.
- **V2 cancel/reschedule preview tests** — V1 has preview tests for both, V2 only has confirm tests for cancel/reschedule.
- **BuildBookingPreviewSpeak V2 missing** — V2 confirm exists but no V2 preview.
- **EmptyTime string handling** — Some methods handle null but not "".
- **HighSurrogate/EmojiInProviderName** — Exotic characters in provider names should not break exact-match assertions.

### Improvement Suggestions / Irrelevant Tests
- **Asserting exact phrase strings** — Brittle to copy edits. Consider snapshot tests (CS-006 Verify) instead, or assert key tokens.
- **V2 vs V1 duplication** — Many tests are near-identical between V1 and V2; could be parameterized via `[TestCase(useVoiceQualityV2: false)]` / `[TestCase(useVoiceQualityV2: true)]`.
- **Spanish tests rely on Spanish phrase contains** — Mixed strict-equals and contains across the suite; pick one approach for consistency.

## ValidationHelpersTests.cs

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|-------------------|-----------|---------------|-------|---------|-------|
| 1 | | IsValidEmail_ReturnsExpected | Email validation matrix (valid + invalid) | TestCase rows incl. typical, missing @, double @, missing domain, empty, whitespace; assert. | Email regex. | In scope: validation matrix. Out of scope: i18n emails. |
| 2 | | IsValidEmail_Null_ReturnsFalse | Null email -> false | null; assert false. | Null safety. | In scope: null. Out of scope: empty. |
| 3 | | IsDateOfBirthValid_DateInPast_ReturnsTrue | Yesterday is valid DOB | Today-1; assert true. | Past valid. | In scope: past. Out of scope: very old. |
| 4 | | IsDateOfBirthValid_Today_ReturnsFalse | Today is not a valid DOB | Today; assert false. | Today rejected. | In scope: today. Out of scope: timezone. |
| 5 | | IsDateOfBirthValid_FutureDate_ReturnsFalse | Future date rejected | Today+1; assert false. | Future rejected. | In scope: future. Out of scope: timezone. |
| 6 | | IsDateOfBirthValid_Null_ReturnsFalse | Null DOB rejected | null; assert false. | Null safety. | In scope: null. Out of scope: empty. |
| 7 | | IsValidUsPhoneNumber_ReturnsExpected | US phone validation matrix | TestCase rows incl. 10/11-digit, formatted, +1 prefix, alpha, too short/long, empty, whitespace; assert. | US phone regex. | In scope: matrix. Out of scope: int'l. |
| 8 | | IsValidUsPhoneNumber_Null_ReturnsFalse | Null phone -> false | null; assert. | Null safety. | In scope: null. Out of scope: empty. |
| 9 | | SanitizePhoneNumber_ReturnsExpected | Strip formatting / preserve "+" / trim whitespace | TestCase rows; assert. | Sanitization matrix. | In scope: formatting strip. Out of scope: validation. |
| 10 | | SanitizePhoneNumber_Null_ReturnsEmpty | Null returns empty string | null; assert "". | Null safety. | In scope: null. Out of scope: empty. |
| 11 | | SanitizePhoneNumber_UnicodeEscapedPlus_ReplacedWithPlus | Unicode escapes preserved as plus | "+15551234567"; assert "+15551234567". | Unicode plus. | In scope: unicode plus. Out of scope: other unicode. |
| 12 | | IsAnonymousNumber_ReturnsExpected | Anonymous detection matrix incl. case variants and "Restricted" | TestCase rows incl. "Anonymous", "Restricted", "*", "***", real numbers; assert. | Anonymous detection. | In scope: matrix. Out of scope: regex tweaks. |
| 13 | | IsAnonymousNumber_Null_ReturnsTrue | Null treated as anonymous | null; assert true. | Null treated as anonymous. | In scope: null. Out of scope: empty. |

### Suggested Missing Tests
- **IsValidEmail_VeryLongEmail** — RFC 5321 254-char limit should be tested.
- **IsValidEmail_PlusAddressing** — `user+tag@example.com` is common.
- **IsValidEmail_UnicodeDomain** — IDN/punycode domains.
- **IsDateOfBirthValid_VeryOldDate** — e.g. 150 years ago — is there an upper bound?
- **IsValidUsPhoneNumber_VanityNumber** — e.g. "1-800-FLOWERS" — should be invalid here per existing matrix; confirm explicitly.
- **SanitizePhoneNumber_LeadingPlusBeforeSpaces** — " +15551234567 " trimming.
- **IsAnonymousNumber_MixedAnonymousString** — "Anonymous Caller" partial match — currently "Caller Anonymous" returns true, which suggests substring match; clarify with positive/negative cases.
- **The "Unicode escaped plus" test seems to test a literal "+"** — Inputs and expected are both "+15551234567"; likely the test was intended to use a unicode-escaped sequence but lost it in source. Investigate and rewrite.

### Improvement Suggestions / Irrelevant Tests
- **`SanitizePhoneNumber_UnicodeEscapedPlus_ReplacedWithPlus`** — As above, the input does not appear to contain a unicode escape; the test does not test what its name claims.
- **The IsValidEmail TestCase " " (single space)** — Good edge case; consider also "\t" and "\n" to lock whitespace handling.
- **`IsDateOfBirthValid` uses `System.DateTime.Today`** — System clock dependency makes the test non-deterministic in some CI configurations. Inject a clock per CS-009.

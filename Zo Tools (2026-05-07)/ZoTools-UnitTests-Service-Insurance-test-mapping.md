# ZoTools.UnitTests Service (Insurance) Test Mapping

Scope of this file: Service-folder unit tests covering insurance domain (carrier/plan catalog, search, acceptance computation, carrier GQL).

Source files mapped:
- `tests/ZoTools.UnitTests/Service/InsuranceAcceptanceServiceTests.cs` (17 tests)
- `tests/ZoTools.UnitTests/Service/InsuranceCarrierGqlTests.cs` (1 test)
- `tests/ZoTools.UnitTests/Service/InsuranceCatalogServiceTests.cs` (24 tests)
- `tests/ZoTools.UnitTests/Service/InsuranceSearchServiceTests.cs` (39 tests)

Total: 81 tests across 4 spec files.

---

## InsuranceAcceptanceServiceTests.cs

Computes whether a carrier/plan is accepted at a practice/location/provider, returning Yes / No / UsuallyAccepted. The service performs filtered, bulk GQL queries and falls back to "top plans" when only a carrier id is provided.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | ComputeAcceptance_NoPlans_ReturnsEmpty | Returns empty result when no plan ids supplied | Arrange empty plan list -> call ComputeAcceptance -> assert empty | Empty input short-circuit | Unit |
| 2 | | ComputeAcceptance_AllAccept_ReturnsYes | All provider/locations accept plan -> Yes | Mock GQL all-accept -> call -> expect AcceptanceStatus.Yes | Happy-path Yes | Unit |
| 3 | | ComputeAcceptance_NoneAccept_ReturnsNo | All reject plan -> No | Mock GQL none-accept -> call -> expect No | Negative path | Unit |
| 4 | | ComputeAcceptance_Mixed_ReturnsUsuallyAccepted | Some accept, some don't -> UsuallyAccepted | Mock GQL mixed -> expect UsuallyAccepted | Mixed path | Unit |
| 5 | | ComputeAcceptance_GqlReturnsEmpty_ReturnsNo | GQL returns no rows -> No | Mock GQL empty list -> expect No | Empty GQL fallback | Unit |
| 6 | | ComputeAcceptance_GqlThrows_ReturnsNo | GQL exception caught -> No, no throw | Mock GQL throw -> assert No returned, no rethrow | Resilience | Unit |
| 7 | | ComputeAcceptance_NoProviderLocations_DefaultsToNoWithoutCallingGql | Skips GQL when no provider/locations | Empty providerLocations -> verify no GQL call -> No | Pre-check guard | Unit |
| 8 | | ComputeAcceptance_FiltersOutExcludedProvidersFromProviderLocationIds | Excluded providers omitted from GQL request | Setup excluded providers -> verify their ids not in GQL args | Exclusion filtering | Unit |
| 9 | | ComputeAcceptance_WhenLocationIdProvided_OnlyUsesThatLocation | Location filter scopes GQL request | Pass locationId -> verify GQL req filtered to that location | Location scope | Unit |
| 10 | | ComputeAcceptance_WhenProviderIdProvided_OnlyUsesThatProviderAcrossLocations | Provider filter applied across locations | Pass providerId -> verify GQL req filtered to that provider | Provider scope | Unit |
| 11 | | ComputeAcceptance_MultiplePlans_MakesSingleBulkGqlCall | Plans batched in single GQL request | Provide multiple plans -> verify exactly one GQL call | Batching | Unit |
| 12 | | ComputeAcceptance_CarrierOnly_ProbesTopPlans | Carrier without planId probes top plans for carrier | Provide carrier only -> verify top-plan ids used | Carrier-only fallback | Unit |
| 13 | | ComputeAcceptance_CarrierOnly_AnyTopPlanAccepted_ReturnsYes | Any top plan accepted -> Yes | Mock one accept -> Yes | Carrier-only Yes | Unit |
| 14 | | ComputeAcceptance_CarrierOnly_NoTopPlansAccepted_ReturnsNo | None of top plans accepted -> No | Mock none -> No | Carrier-only No | Unit |
| 15 | | ComputeAcceptance_CarrierOnly_MixedAcrossProviderLocations_ReturnsUsuallyAccepted | Mixed across providers -> UsuallyAccepted | Mock mixed -> UsuallyAccepted | Carrier-only mixed | Unit |
| 16 | | ComputeAcceptance_CarrierOnly_NoTopPlansForCarrier_ReturnsNoWithoutCallingGql | Carrier has no top plans -> No, no GQL | Configure carrier with no top plans -> verify no GQL call | Empty top-plan guard | Unit |
| 17 | | ComputeAcceptance_MixedSpecificAndCarrierOnly_QueriesAllPlanIdsInOneCall | Specific + carrier-only ids merged in single call | Mix inputs -> verify single GQL call with all plan ids | Mixed batching | Unit |

### Suggested Missing Tests (InsuranceAcceptanceServiceTests)
- Behavior with extremely large plan lists (boundary / pagination)
- Concurrency: same plan asked simultaneously by two callers
- Cancellation token honored on long GQL call
- Metric/log emission for each acceptance status outcome

### Improvement Suggestions / Irrelevant Tests
- "DefaultsToNoWithoutCallingGql" couples to implementation detail (GQL call count) - acceptable here as it asserts a perf guarantee, but tag as such.
- The Yes/No/UsuallyAccepted bucketing should ideally have an explicit boundary test for "single accept among many".

---

## InsuranceCarrierGqlTests.cs

Trivial DTO/contract test for `InsuranceCarrierInfo`.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | InsuranceCarrierInfo_WhenConstructed_HasExpectedProperties | DTO initializes with expected property defaults | Construct InsuranceCarrierInfo -> assert prop values | DTO smoke test | Unit |

### Suggested Missing Tests
- None high value - DTO smoke is sufficient.

### Improvement Suggestions / Irrelevant Tests
- This test is basically a constructor smoke test. Consider deleting if the type is a pure record with no logic; keep only if the GQL contract requires guaranteed shape.

---

## InsuranceCatalogServiceTests.cs

Loads embedded carrier/plan catalog data, normalizes carrier/plan names, exposes filtered lookups (by carrier id, BCBS by state, mistranscriptions). The `ProcessName_*` cases exercise the canonicalization logic.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | AllPlans_LoadsFromEmbeddedResource | Plans deserialize successfully from embedded JSON | New service -> AllPlans collection populated | Resource loading | Unit |
| 2 | | AllCarriers_OnlyContainsHealthAndDentalCategories | Catalog filter excludes other categories | Inspect AllCarriers categories | Filter rule | Unit |
| 3 | | AllCarriers_ContainsAtLeastOneHealthAndOneDentalCarrier | Sanity: both categories present | Inspect AllCarriers -> assert at least one each | Sanity | Unit |
| 4 | | AllCarriers_StripsHealthAndDentalSuffix | Carrier name suffixes "Health"/"Dental" stripped | Inspect carrier names | Name normalization | Unit |
| 5 | | AllPlans_StripsHealthAndDentalSuffixFromCarrierName | Plan-side carrier name normalization | Inspect plan.CarrierName | Name normalization | Unit |
| 6 | | AllCarriers_ReturnsDistinctCarriers | No duplicate carriers across categories | Group by id -> assert unique | Dedup | Unit |
| 7 | | GetCarrier_KnownCarrierId_ReturnsCarrier | Lookup by id returns carrier | GetCarrier(id) -> assert non-null with correct id | Happy path | Unit |
| 8 | | GetCarrier_UnknownCarrierId_ReturnsNull | Unknown id -> null | GetCarrier("bogus") -> null | Negative path | Unit |
| 9 | | GetPlansForCarrier_KnownCarrier_ReturnsPlansSortedByVolumeDesc | Plans sorted by volume desc | Inspect plan order | Sort order | Unit |
| 10 | | GetPlansForCarrier_UnknownCarrier_ReturnsEmpty | Unknown carrier -> empty plans | Lookup unknown -> empty | Negative path | Unit |
| 11 | | GetMistranscriptions_KnownCarrier_ReturnsPhrases | Mistranscriptions data exposed | Lookup known -> non-empty | Voice STT support | Unit |
| 12 | | GetMistranscriptions_UnknownCarrier_ReturnsEmpty | Unknown carrier -> empty | Lookup unknown -> empty | Negative path | Unit |
| 13 | | GetBcbsCarriersForFullStateName_KnownState_ReturnsCarriers | BCBS-by-state lookup happy path | Pass valid state name -> non-empty | BCBS state map | Unit |
| 14 | | GetBcbsCarriersForFullStateName_CaseInsensitive | State name lookup case-insensitive | Pass mixed-case -> matches | Case insensitivity | Unit |
| 15 | | GetBcbsCarriersForFullStateName_NormalizesWhitespace | Trims/normalizes whitespace in state name | Pass padded state name -> matches | Whitespace tolerance | Unit |
| 16 | | GetBcbsCarriersForFullStateName_UnknownState_ReturnsEmpty | Unknown state -> empty | Pass bogus state -> empty | Negative path | Unit |
| 17 | | GetBcbsCarriersForFullStateName_StripsHealthAndDentalSuffix | BCBS results respect suffix stripping | Inspect names | Name normalization | Unit |
| 18 | | ProcessName_NullOrWhitespace_ReturnsEmpty | Null/whitespace input -> empty string | TestCase null/empty/whitespace -> empty | Edge cases | Unit |
| 19 | | ProcessName_SplitsCamelCase | CamelCase split into spaces | TestCase "FooBar" -> "Foo Bar", etc. | Tokenization | Unit |
| 20 | | ProcessName_SplitsUpperRunBeforeLower | "USACar" -> "USA Car" | Test uppercase-run boundary | Tokenization edge | Unit |
| 21 | | ProcessName_StripsNonAlphaNumeric | Punctuation stripped | "Aetna,Inc." -> "Aetna Inc" | Sanitization | Unit |
| 22 | | ProcessName_CollapsesWhitespace | Multiple spaces -> single space | "Foo   Bar" -> "Foo Bar" | Whitespace normalization | Unit |
| 23 | | ProcessName_AppliesWordReplacements | Word replacements applied | "AEXCEL Plan" -> "excel Plan", "II" -> "2" | Replacement table | Unit |
| 24 | | ProcessName_OnlySpecialCharacters_ReturnsEmpty | Only punctuation -> empty | Pass "!@#$" -> empty | Edge case | Unit |

### Suggested Missing Tests
- Catalog loading failure (invalid JSON / missing resource) - currently no negative-path test for the embedded resource.
- Idempotency: calling AllCarriers twice returns same instance (singleton/caching guarantee).
- Carrier present in plans but not in carriers (orphan plan) - is it filtered or surfaced?
- BCBS lookup returns multiple carriers per state (some states do).
- ProcessName: very long names, unicode, accented characters.

### Improvement Suggestions / Irrelevant Tests
- `AllCarriers_ContainsAtLeastOneHealthAndOneDentalCarrier` is brittle to data; replace with assertion on category set rather than count.
- `AllPlans_LoadsFromEmbeddedResource` checks "is non-empty" - pin to a known plan id or count for stronger contract.

---

## InsuranceSearchServiceTests.cs

Carrier/plan resolver from a free-text "carrier claim" or carrier id, with phonetic match, BCBS state-aware disambiguation, dental/health practice filter, and acceptance enrichment. Also surfaces agent instructions to disambiguate.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | Search_WhenBothCarrierClaimAndCarrierIdAreNull_ReturnsValidationError | Both inputs null -> validation error | Call Search with no carrier info -> assert error | Input guard | Unit |
| 2 | | Search_WhenBothCarrierClaimAndCarrierIdAreWhitespace_ReturnsValidationError | Whitespace-only inputs -> validation error | Pass "  " for both -> error | Input guard | Unit |
| 3 | | Search_WhenCarrierIdUnknown_ReturnsValidationError | Unknown carrier id -> validation error | Pass bogus id -> error | Negative path | Unit |
| 4 | | Search_WhenProviderIdNotFound_ReturnsValidationError | Provider id not in practice -> error | Pass invalid provider -> error | Negative path | Unit |
| 5 | | Search_WhenLocationIdNotFound_ReturnsValidationError | Location id not in practice -> error | Pass invalid location -> error | Negative path | Unit |
| 6 | | Search_WhenCarrierIdProvided_UsesThatCarrierWithoutPhoneticMatch | Carrier id bypasses phonetic match | Provide id -> verify no phonetic call | Carrier-id fast path | Unit |
| 7 | | Search_WhenCarrierIdAndCarrierClaimBothProvided_PrefersCarrierId | Carrier id wins over claim | Provide both -> assert id used | Precedence rule | Unit |
| 8 | | Search_WhenCarrierClaimProvided_FindsCarrierByName | Phonetic name search produces match | Provide claim -> assert match | Happy path | Unit |
| 9 | | Search_WhenCarrierClaimMatchesAbbreviation_FindsBcbsCarriers | "BCBS" matches Blue Cross Blue Shield carriers | Pass abbrev -> non-empty BCBS carriers | Abbrev support | Unit |
| 10 | | Search_WhenCarrierClaimSaysHip_FindsLegacyEmblemHipCarrier | "HIP" -> legacy Emblem-HIP | Pass HIP -> assert legacy carrier id | Legacy alias | Unit |
| 11 | | Search_WhenCarrierClaimSaysGhi_FindsLegacyEmblemGhiCarrier | "GHI" -> legacy Emblem-GHI | Pass GHI -> assert legacy carrier id | Legacy alias | Unit |
| 12 | | Search_WhenCarrierClaimSaysEmblem_ExcludesLegacyFormerlyKnownAsCarriers | "Emblem" excludes legacy aliases | Pass Emblem -> legacy carriers absent | Alias filtering | Unit |
| 13 | | Search_LongerQueryProducesMoreSpecificResults | Longer claim -> more specific match | Compare result counts | Specificity | Unit |
| 14 | | Search_CapsCarriersAtFive | Carrier results capped at 5 | Many candidates -> assert count <=5 | Result cap | Unit |
| 15 | | Search_WhenCarrierClaimIsGibberish_DoesNotReturnValidationError | Gibberish input -> empty result, no error | Random claim -> assert no validation error | Soft fail | Unit |
| 16 | | Search_AppliesBcbsStateFilter | State filter limits BCBS carriers | Pass state -> assert only matching state BCBS | State filter | Unit |
| 17 | | Search_WhenManyBcbsMatches_AndNoLocationId_EmitsAskForStateInstruction | Ambiguous BCBS, no location -> agent instruction | Many BCBS, no location -> emit instruction | Disambiguation | Unit |
| 18 | | Search_WhenManyBcbsMatches_AndLocationIdProvided_ListsStateBcbsCarriers | Ambiguous BCBS w/ location -> state-filtered list | Provide location -> only state BCBS | Disambiguation | Unit |
| 19 | | Search_NonBcbsClaim_DoesNotEmitBcbsDisambiguationInstruction | Non-BCBS claim -> no BCBS instruction | TestCase Aetna/etc -> no instruction | Negative case | Unit |
| 20 | | Search_WhenCarrierIdProvided_AndStateMatches_ReturnsCarrier | Carrier id with state context returns carrier | Provide both -> match | Carrier-id + state | Unit |
| 21 | | Search_WhenCarrierIdProvided_AndStateDoesNotMatch_StillReturnsCarrier | Carrier id wins over state mismatch | Provide both, mismatch -> still returns | Precedence | Unit |
| 22 | | Search_WhenStateProvided_DoesNotFilterNonBcbsCarriers | State irrelevant for non-BCBS | Provide state -> non-BCBS not filtered | Filter scope | Unit |
| 23 | | Search_AppliesBcbsStateFilter_BeforeCarrierCap | State filter applied before 5-cap | Many BCBS -> state filter not lost to cap | Filter order | Unit |
| 24 | | Search_WhenInvalidStateCode_IgnoresFilterAndLogsError | Invalid state -> filter ignored, error logged | Bad state -> verify log + no filter | Invalid input handling | Unit |
| 25 | | Search_CarrierIdOnly_ReturnsSingleCarrierRowWithNullPlanFields | Carrier-only result has null plan info | Provide id only -> assert single row, null plan | Shape | Unit |
| 26 | | Search_CarrierClaimOnlyNoPlanClaim_ReturnsCarrierRowsWithNullPlanFields | Claim-only -> null plan fields | Provide claim only -> null plan fields | Shape | Unit |
| 27 | | Search_WithPlanClaim_FindsMatchingPlan | Plan claim matches plan | Provide plan claim -> match | Plan match | Unit |
| 28 | | Search_WithPlanClaim_CapsPlansAtFivePerCarrier | Plans capped at 5 per carrier | Many plans -> count<=5 | Result cap | Unit |
| 29 | | Search_WithPlanClaim_ShortQuery_ReturnsPlansSharingPrefix | Short plan claim -> prefix-share results | Pass short claim -> shared-prefix plans | Short-query handling | Unit |
| 30 | | Search_PassesPlansAndPracticeInfoToAcceptanceService | Acceptance service called with right inputs | Verify args to InsuranceAcceptanceService | Integration glue | Unit |
| 31 | | Search_WhenAcceptanceMissingForPlan_DefaultsToNo | Missing acceptance -> No | Mock no acceptance -> assert No | Default behavior | Unit |
| 32 | | Search_WhenNewPatientPlanRequired_AndCarrierOnlyResult_EmitsAgentInstruction | Plan required but carrier-only -> instruction | New-patient flag on -> verify instruction | Agent UX | Unit |
| 33 | | Search_WhenNewPatientPlanRequired_AndAllResultsHavePlans_DoesNotEmitInstruction | Plans present -> no instruction | All carriers w/ plans -> no instruction | Negative case | Unit |
| 34 | | Search_WhenNewPatientPlanNotRequired_DoesNotEmitInstruction | Flag off -> no instruction | Disable flag -> no instruction | Feature gating | Unit |
| 35 | | Search_DentalPractice_OnlyReturnsDentalCarriers | Dental practice filters to dental carriers | Configure dental practice -> only dental | Practice typing | Unit |
| 36 | | Search_DentalPractice_FiltersOutHealthOnlyCarrier | Dental practice removes health-only carriers | Configure dental + health-only carrier -> excluded | Practice typing | Unit |
| 37 | | Search_HealthPractice_FiltersOutDentalOnlyCarrier | Health practice removes dental-only carriers | Configure health + dental-only carrier -> excluded | Practice typing | Unit |
| 38 | | Search_MixedSpecialties_AnyDentalSpecialtyTreatsPracticeAsDental | Any dental specialty -> dental treatment | Mixed specialties incl dental -> dental rules | Practice typing | Unit |
| 39 | | Search_VariousDentalSpecialtyNames_TreatedAsDental | Dental synonyms recognized | TestCase dentist/Pediatric Dentist/etc | Specialty mapping | Unit |

### Suggested Missing Tests
- Cancellation/timeout for the acceptance call portion
- Phonetic threshold boundary (just over / just under)
- Combined filter: plan + state + dental practice (3-way intersection)
- Carrier-id with plan claim that does not belong to that carrier
- Logging of metric tags for disambiguation outcomes (state-disambig, plan-disambig)

### Improvement Suggestions / Irrelevant Tests
- The "agent instruction" assertions are textual; consider asserting on a typed instruction enum to avoid brittle string compares.
- `Search_WhenInvalidStateCode_IgnoresFilterAndLogsError` should also assert the metric/tag.
- Some Search_* tests overlap (carrier-id precedence is asserted in multiple tests); consider consolidating.

# ZoTools.Web.UnitTests / Providers - Unit Test Mapping

> Repo: [Zocdoc/zo-tools](https://github.com/Zocdoc/zo-tools)
> Folder: `tests/ZoTools.Web.UnitTests/Providers/`
> Generated: 2026-05-07

This file maps unit tests for the `IAgentToolsProvider` registry and per-agent tool-schema providers. These providers declare the tools (function-calling specs) exposed to each Zo voice agent. Because the schemas are the source of truth that a live LLM consumes, every required parameter, default, and description string is contractually significant.

---

## AgentToolsRegistryTests.cs

Tests `AgentToolsRegistry` - the dictionary-style lookup that maps `AgentName` -> `IAgentToolsProvider` and is consumed by `ToolsSchemaImpl`. Constructor enforces uniqueness so two providers registered for the same agent throw at startup with a helpful message.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | Yes | GetProvider_ExistingAgent_ReturnsProvider | Lookup returns the registered provider for a known agent | Build registry with stub provider for AgentName.X; call GetProvider(X) | Returns same instance | Registry |
| 2 | Yes | GetProvider_EmptyRegistry_ReturnsNull | Lookup is null when nothing is registered | Build registry with no providers; call GetProvider | Returns null | Registry |
| 3 | Yes | GetRegisteredAgents_ReturnsAllAgentNames | Enumeration returns all keys | Register two providers; call GetRegisteredAgents | Both AgentNames present | Registry |
| 4 | Yes | GetRegisteredAgents_EmptyProviders_ReturnsEmptyCollection | Empty registry yields empty enumeration | Build registry with no providers; call GetRegisteredAgents | Empty collection (not null) | Registry |
| 5 | Yes | Constructor_DuplicateProviders_ThrowsWithHelpfulMessage | Duplicate AgentName at registration -> exception | Pass two providers with same AgentName; expect throw with helpful message text | Misconfiguration fails fast at startup with actionable message | Registry |

### Suggested Missing Tests

- `GetProvider` is case-sensitive / strict on the AgentName enum value (negative lookup with adjacent-but-different enum value).
- Behavior when the registered providers list contains a null entry (today this likely NREs).
- Concurrent reads from `GetProvider` are safe (registry is built-once but consumed from many threads in ASP.NET).

### Improvement Suggestions / Irrelevant Tests

- All five tests pull their weight; coverage on the duplicate-detection path is especially valuable because the failure surfaces only at app startup.
- Could add a single integration-style test that constructs the real DI graph and asserts every `IAgentToolsProvider` registered via DI is reachable via `GetRegisteredAgents`. This would catch a missing DI registration (current design only catches duplicates).

---

## ToolDefinitionBuilderTests.cs

Tests the fluent `ToolDefinitionBuilder` used by every provider to declare a tool: `Create(name).WithEndpoint(...).WithMethod(...).WithDescription(...).WithSideEffect(...).WithParameter(...).WithReturns(...).Build()`. Defaults: `Method = "POST"`, `HasSideEffect = false`, `Returns = null`.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | Yes | Build_MinimalConfiguration_CreatesToolDefinition | Defaults populate when only Name supplied | Create("test_tool").Build() | Name set, Method defaults to POST, HasSideEffect defaults to false | Builder |
| 2 | Yes | Build_WithAllProperties_SetsAllValues | Every fluent setter persists | Chain WithEndpoint/WithMethod/WithDescription/WithSideEffect/WithParameter/WithReturns | All fields equal expected values | Builder |
| 3 | Yes | Build_WithRequiredParameter_IncludesInRequiredList | Required flag drives `RequiredParams` list | Add one required, one optional param | Required is in list, optional is not | Builder |
| 4 | Yes | Build_WithParameters_SetsPropertiesCorrectly | Type and description are stored on the property JObject | Add parameter user_id type=string desc=The user ID | Properties bag has correct type/description | Builder |
| 5 | Yes | Build_WithReturns_SetsReturnsSchema | `returns` schema captured | Call WithReturns("array", "...") | Returns JObject has type=array, description=... | Builder |
| 6 | Yes | Build_WithoutReturns_ReturnsIsNull | Default Returns is null | Build without WithReturns | tool.Returns is null | Builder |
| 7 | Yes | Build_FluentChaining_ReturnsSameBuilderInstance | Each setter returns the same builder | Chain all setters; compare reference | result is same instance as builder | Builder |

### Suggested Missing Tests

- Calling `Build()` without `Create("name")` is impossible (private ctor) - if a parameterless ctor exists, validate name-required.
- `WithParameter` called twice with the same name - last write wins or throws? Behavior is undefined today.
- `WithMethod("post")` (lowercase) - normalized to "POST" or preserved? Routing relies on this.
- `WithMethod("BREW")` (invalid HTTP verb) - validated or accepted?
- `WithEndpoint` validates that `:placeholder` segments are syntactically valid (or at least non-empty).
- `WithReturns` called twice - last write wins.
- Build is idempotent: calling Build() twice yields equivalent objects (or shared references) - matters for cached schema responses.

### Improvement Suggestions / Irrelevant Tests

- All seven tests are valuable; #7 in particular protects the fluent contract.
- Test #4 reaches into `Properties["user_id"]!["type"]!.ToString()` - asserting the JObject shape is brittle but correct for now. A Verify snapshot of the resulting JSON for a representative tool would be a stronger lock against unintended drift in the OpenAI schema contract.
- No tests are irrelevant.

---

## AuthenticatePatientAgentToolsProviderTests.cs

Setup-only file: the `AuthenticatePatientAgentToolsProvider` currently exposes NO tools. The tests are placeholders that protect the provider's identity (AgentName, Version) and lock in the empty tool list. This is correct: the provider is wired into the registry today even though the agent is not yet using LLM tools.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | Yes | AgentName_ReturnsAuthenticatePatientAgent | Identity bound to the right enum value | Read provider.AgentName | AgentName.AuthenticatePatientAgent | Provider |
| 2 | Yes | Version_ReturnsInitialVersion | Initial version is "1.0.0" | Read provider.Version | "1.0.0" | Provider |
| 3 | Yes | GetTools_ReturnsEmptyList | No tools defined yet | Call GetTools() | Empty collection | Provider |

### Suggested Missing Tests

- When tools ARE added to this agent, every test from the other Provider files (required params, returns schema, descriptions) will have to be added here. Worth a TODO comment in code.

### Improvement Suggestions / Irrelevant Tests

- Tests are minimal but appropriate. Keep them: they are the contract that prevents accidentally exposing tools before the agent is ready.
- Consider adding `Provider_IsDiscoverableViaAgentToolsRegistry` here too (matches `UnifiedNewAppointmentAgentToolsProviderTests`) for symmetry across providers.

---

## GreetingAgentToolsProviderTests.cs

Tests the `GreetingAgentToolsProvider`, which exposes two tools to the greeting agent: `get_transfer_number` (lookup of the practice's transfer number for a given call-center number) and `get_practice_faq` (fetch a topic-scoped FAQ - general / pricing / insurance). The first is `GET`, the second `POST`; both are non-side-effecting.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | Yes | AgentName_ReturnsGreetingAgent | Identity bound to GreetingAgent | Read provider.AgentName | AgentName.GreetingAgent | Provider |
| 2 | Yes | GetTools_ReturnsNonEmptyList | At least one tool is exposed | Call GetTools() | Not empty | Provider |
| 3 | Yes | GetTools_ReturnsExpectedTools | Names match exactly: get_transfer_number, get_practice_faq | Call GetTools(); project Names | Both expected names present | Provider |
| 4 | Yes | GetTools_ContainsGetTransferNumberTool | Method=GET, endpoint contains get_transfer_number, no side effect | Find tool; assert | Schema matches contract | Provider |
| 5 | Yes | GetTools_TransferNumberTool_HasRequiredParameter | call_center_number is required | Read RequiredParams | Contains call_center_number | Provider |
| 6 | Yes | GetTools_TransferNumberTool_HasReturnsSchema | returns schema is not null and type=object | Read Returns | type=object | Provider |
| 7 | Yes | GetTools_ContainsGetPracticeFaqTool | Method=POST, endpoint exact, no side effect | Find tool; assert Method/Endpoint/HasSideEffect | All match | Provider |
| 8 | Yes | GetTools_GetPracticeFaq_HasRequiredTopicParameter | Required = ["topic"], topic property present | Read RequiredParams and Properties | Matches | Provider |
| 9 | Yes | GetTools_GetPracticeFaq_HasOptionalLocationIdParameter | location_id is a property but not required | Read Properties and RequiredParams | location_id present, not required | Provider |
| 10 | Yes | GetTools_GetPracticeFaq_DescriptionMentionsAllTopics | Description guidance covers general, pricing, insurance | Read Description | All three substrings present | Provider |
| 11 | Yes | GetTools_GetPracticeFaq_HasReturnsSchema | returns schema not null and type=object | Read Returns | Matches | Provider |

### Suggested Missing Tests

- `Version` test (other providers verify Version = "1.0.0"). Inconsistent with peers.
- `get_practice_faq` description should also mention what valid values for `topic` look like (or that it is an enum) - test the enum value list.
- Endpoint value for `get_transfer_number` is asserted only via `Contain("get_transfer_number")`. Pin the full URL like the other tools (`/zo-tools/v1/tools/.../get_transfer_number`).
- `Provider_IsDiscoverableViaAgentToolsRegistry` for symmetry with UnifiedNewAppointment provider.
- The greeting agent must NOT expose any side-effecting tools - test that `GetTools().All(t => !t.HasSideEffect)`.

### Improvement Suggestions / Irrelevant Tests

- All tests are valuable; no removals.
- Description-content tests (#10) are brittle but legitimate: the description is the prompt the LLM sees, so a change can degrade greeting quality. Consider Verify snapshots for the description string.
- Endpoint URLs should be locked verbatim everywhere - inconsistency with `Contain("get_transfer_number")` could mask path mistakes.

---

## ManageAppointmentAgentToolsProviderTests.cs

Tests the `ManageAppointmentAgentToolsProvider`, the v1 (legacy) agent that exposes ONE side-effecting tool: `cancel_appointment`. The schema deliberately accepts only `appointment_id`; previously-exposed `ehr_patient_id` and `sync_id` are now resolved server-side from session cache, so test #6 actively guards against re-exposing PHI-adjacent identifiers to the LLM.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | Yes | AgentName_ReturnsManageAppointmentAgent | Identity bound to ManageAppointmentAgent | Read AgentName | Matches | Provider |
| 2 | Yes | Version_ReturnsInitialVersion | Version is "1.0.0" | Read Version | "1.0.0" | Provider |
| 3 | Yes | GetTools_ReturnsNonEmptyList | At least one tool | GetTools().Count > 0 | Not empty | Provider |
| 4 | Yes | GetTools_Returns1Tool | Exactly one tool today | GetTools().Count == 1 | Single tool | Provider |
| 5 | Yes | GetTools_ContainsCancelAppointmentTool | Method=POST, HasSideEffect=true, endpoint contains cancel_appointment | Find tool; assert | Schema matches | Provider |
| 6 | Yes | GetTools_CancelAppointmentTool_HasReturnsSchema | returns schema not null, type=object | Read Returns | Matches | Provider |
| 7 | Yes | GetTools_OnlyIncludesAppointmentId | RequiredParams = exactly ["appointment_id"] | Read RequiredParams | Single-element list | Provider |
| 8 | Yes | GetTools_DoesNotIncludeEhrPatientIdAndSyncIdProperties | EHR patient id and sync id are NOT exposed to the LLM | Read Properties | Neither key present | Provider (security) |

### Suggested Missing Tests

- Endpoint value is only checked via `Contain` - lock down the full path.
- Description content (the LLM prompt for cancellation) should be snapshotted - a wording change can alter the bot's cancel behavior.
- The tool must require user confirmation (HasSideEffect=true is asserted, but the description should mention "previewing" / "confirming" behavior - the impl is biphasic).
- `Provider_IsDiscoverableViaAgentToolsRegistry` for symmetry.
- A dedicated test that validates `appointment_id` schema is `string` type (not integer) - downstream impl uses string.

### Improvement Suggestions / Irrelevant Tests

- Test #8 is the most important test in this file - it prevents PHI/EHR identifiers from leaking into the LLM tool surface. Mark as do-not-delete in any future cleanup.
- Tests are well-scoped; nothing is irrelevant.

---

## SelectLocationAgentToolsProviderTests.cs

Tests the `SelectLocationAgentToolsProvider` which exposes four lookup tools to the location-selection agent: `validate_location`, `find_locations_near_zip`, `find_locations_in_state`, `find_locations_by_name`. All POST, none with side effects.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | Yes | AgentName_ReturnsSelectLocationAgent | Identity bound to SelectLocationAgent | Read AgentName | Matches | Provider |
| 2 | Yes | Version_Returns1_0_0 | Version is "1.0.0" | Read Version | Matches | Provider |
| 3 | Yes | GetTools_ReturnsFourTools | Exactly four tool names | Project GetTools().Names | Set matches expected | Provider |
| 4 | Yes | GetTools_ContainsValidateLocationTool | validate_location: POST, exact endpoint, no side effect | Find tool; assert | Schema matches | Provider |
| 5 | Yes | GetTools_ValidateLocation_HasRequiredParameters | Required: practice_phone_number, location_id; both as properties | Read Required and Properties | Both present | Provider |
| 6 | Yes | GetTools_ContainsFindLocationsNearZipTool | find_locations_near_zip: POST, exact endpoint, no side effect | Find tool; assert | Schema matches | Provider |
| 7 | Yes | GetTools_FindLocationsNearZip_HasRequiredParameters | Required: practice_phone_number, zip_code | Read | Both present | Provider |
| 8 | Yes | GetTools_ContainsFindLocationsInStateTool | find_locations_in_state: POST, exact endpoint | Find tool; assert | Schema matches | Provider |
| 9 | Yes | GetTools_FindLocationsInState_HasRequiredParameters | Required: practice_phone_number, state | Read | Both present | Provider |
| 10 | Yes | GetTools_ContainsFindLocationsByNameTool | find_locations_by_name: POST, exact endpoint | Find tool; assert | Schema matches | Provider |
| 11 | Yes | GetTools_FindLocationsByName_HasRequiredParameters | Required: practice_phone_number, location_query | Read | Both present | Provider |
| 12 | Yes | GetTools_AllToolsHaveReturnsSchema | Every tool returns an object schema | Loop tools; assert Returns.type == object | All four | Provider |

### Suggested Missing Tests

- Description snapshot/Verify per tool to lock the prompt copy that the LLM sees.
- `radius_miles` (or equivalent) optional parameter for `find_locations_near_zip` - properties are not asserted at all.
- `state` parameter format/enum (USA two-letter code) - description should call this out and be tested.
- `location_query` description should mention partial-match behavior - test it.
- Negative test: no tool has `HasSideEffect=true` (`GetTools().All(t => !t.HasSideEffect)`).
- `Provider_IsDiscoverableViaAgentToolsRegistry` for symmetry.

### Improvement Suggestions / Irrelevant Tests

- Tests 4/6/8/10 are very similar - consider a single `[TestCase]`-driven test to reduce duplication, but the explicit form aids debugging.
- Tests are otherwise focused and valuable.

---

## UnifiedManageAppointmentAgentToolsProviderTests.cs

Tests the unified manage-appointment provider which exposes four tools: `search_availability`, `get_next_available`, `update-reschedule-location` (the only side-effecting tool), and `get_practice_faq`. This provider hides direct-search params (`provider_ids`, `location_ids`, `procedure_id`, `patient_type`, `duration_override`) because the LLM should reschedule using the cached appointment, not pick raw fields.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | Yes | AgentName_ReturnsUnifiedManageAppointmentAgent | Identity bound | Read AgentName | Matches | Provider |
| 2 | Yes | GetTools_ReturnsExpectedTools | Names: search_availability, get_next_available, update-reschedule-location, get_practice_faq | Project Names | Set matches | Provider |
| 3 | Yes | GetTools_ContainsSearchAvailabilityTool | POST, exact endpoint, no side effect | Find tool; assert | Matches | Provider |
| 4 | Yes | GetTools_SearchAvailability_HasRequiredParameters | Required: practice_phone_number, start_date, end_date, patient_id, appointment_id | Read RequiredParams | All five required | Provider |
| 5 | Yes | GetTools_SearchAvailability_HasOptionalParameters | time_filter is optional but present; patient_id and appointment_id are required | Read Properties and RequiredParams | time_filter optional, IDs required | Provider |
| 6 | Yes | GetTools_SearchAvailability_DoesNotExposeDirectParams | provider_ids, location_ids, procedure_id, patient_type, duration_override NOT exposed | Read Properties | None of the five present | Provider (contract) |
| 7 | Yes | GetTools_ContainsGetNextAvailableTool | POST, exact endpoint, no side effect | Find tool; assert | Matches | Provider |
| 8 | Yes | GetTools_GetNextAvailable_HasRequiredParameters | Required: practice_phone_number, patient_id, appointment_id | Read | Three required | Provider |
| 9 | Yes | GetTools_GetNextAvailable_DoesNotExposeDirectParams | Direct-search params not exposed | Read Properties | None present | Provider (contract) |
| 10 | Yes | GetTools_ContainsUpdateRescheduleLocationTool | POST, exact endpoint includes appointment_id, HasSideEffect=true | Find tool; assert | Matches | Provider |
| 11 | Yes | GetTools_UpdateRescheduleLocation_HasRequiredParameters | Required: practice_phone_number, appointment_id, location_type | Read | All required | Provider |
| 12 | Yes | GetTools_ContainsGetPracticeFaqTool | POST, exact endpoint, no side effect | Find tool; assert | Matches | Provider |
| 13 | Yes | GetTools_GetPracticeFaq_HasRequiredTopicParameter | Required = ["topic"]; topic property present | Read | Matches | Provider |
| 14 | Yes | GetTools_GetPracticeFaq_HasOptionalLocationIdParameter | location_id present, not required | Read | Matches | Provider |
| 15 | Yes | GetTools_GetPracticeFaq_DescriptionMentionsAllTopics | Description lists general, pricing, insurance | Read Description | All present | Provider |
| 16 | Yes | GetTools_AllToolsHaveReturnsSchema | All tools return object schema | Loop; assert | All four | Provider |

### Suggested Missing Tests

- `Version` is not tested for this provider (peers test it).
- `location_type` enum values for update-reschedule-location are not asserted (PhysicalOnly / VirtualOnly / Hybrid). The impl rejects Hybrid and Unknown - test the schema documents the supported values.
- `time_filter` optional values (Morning / Afternoon / Evening / Anytime) should be in the schema and tested.
- `update-reschedule-location` is the only side-effecting tool - assert via `GetTools().Count(t => t.HasSideEffect) == 1`.
- `Provider_IsDiscoverableViaAgentToolsRegistry` for symmetry with UnifiedNewAppointment provider.
- The unified provider must NOT expose `cancel_appointment` (which lives on legacy ManageAppointmentAgentToolsProvider).

### Improvement Suggestions / Irrelevant Tests

- Tests 6 and 9 (DoesNotExposeDirectParams) are critical guards on the LLM contract and should remain.
- The endpoint string `update-reschedule-location` (kebab-case) is inconsistent with sibling tools' snake_case (`get_next_available`). Worth a follow-up to align (separate from tests).

---

## UnifiedNewAppointmentAgentToolsProviderTests.cs

Tests the unified new-appointment provider - the largest provider with six tools: `search_availability`, `get_next_available`, `search_visit_reasons`, `search_locations`, `search_insurances`, `get_practice_faq`. The most extensive coverage is on `search_insurances`, whose description and per-parameter description strings change dynamically based on practice insurance configuration (NotAsked / Asked / Required for new vs existing patients, plus self-pay-transfer policy).

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | Yes | AgentName_ReturnsUnifiedNewAppointmentAgent | Identity bound | Read AgentName | Matches | Provider |
| 2 | Yes | GetTools_ReturnsExpectedTools | Six expected tool names | Project Names | Set matches | Provider |
| 3 | Yes | GetTools_ContainsSearchAvailabilityTool | POST, exact endpoint, no side effect | Find; assert | Matches | Provider |
| 4 | Yes | GetTools_SearchAvailability_HasRequiredParameters | Required: provider_ids, location_ids, visit_reason_id, start_date, end_date | Read | All five | Provider |
| 5 | Yes | GetTools_SearchAvailability_HasOptionalParameters | time_filter is optional but present | Read | Matches | Provider |
| 6 | Yes | GetTools_SearchAvailability_DoesNotExposeAppointmentId | New-appointment flow must not expose appointment_id | Read Properties | Not present | Provider (contract) |
| 7 | Yes | GetTools_SearchAvailability_ExposesPatientId | patient_id is exposed (used to detect existing patients) | Read Properties | Present | Provider |
| 8 | Yes | GetTools_SearchAvailability_DoesNotExposeDurationOverride | duration_override is internal only | Read Properties | Not present | Provider (contract) |
| 9 | Yes | GetTools_ContainsGetNextAvailableTool | POST, exact endpoint, no side effect | Find; assert | Matches | Provider |
| 10 | Yes | GetTools_GetNextAvailable_HasRequiredParameters | Required: provider_ids, location_ids, visit_reason_id | Read | All three | Provider |
| 11 | Yes | GetTools_GetNextAvailable_DoesNotExposeAppointmentId | Same contract as #6 | Read | Not present | Provider (contract) |
| 12 | Yes | GetTools_GetNextAvailable_ExposesPatientId | Same contract as #7 | Read | Present | Provider |
| 13 | Yes | GetTools_GetNextAvailable_DoesNotExposeDurationOverride | Same as #8 | Read | Not present | Provider (contract) |
| 14 | Yes | GetTools_AllToolsHaveReturnsSchema | All six tools return object schema | Loop; assert | Matches | Provider |
| 15 | Yes | GetTools_ContainsSearchVisitReasonsTool | POST, exact endpoint, no side effect | Find; assert | Matches | Provider |
| 16 | Yes | GetTools_SearchVisitReasons_HasRequiredParameters | Required: user_claim, is_new_patient | Read | Matches | Provider |
| 17 | Yes | GetTools_SearchVisitReasons_HasOptionalParameters | specialty, provider_id, location_id, patient_id are optional but present | Read | All present, none required | Provider |
| 18 | Yes | GetTools_ContainsSearchLocationsTool | POST, exact endpoint, no side effect | Find; assert | Matches | Provider |
| 19 | Yes | GetTools_SearchLocations_HasNoRequiredParameters | All params optional | Read RequiredParams | Empty | Provider |
| 20 | Yes | GetTools_SearchLocations_HasOptionalParameters | user_claim, provider_ids, zip_code, radius_miles, visit_reason_id, carrier_id, plan_id all present | Read Properties | All seven present and non-required | Provider |
| 21 | Yes | GetTools_ContainsSearchInsurancesTool | POST, exact endpoint, no side effect | Find; assert | Matches | Provider |
| 22 | Yes | GetTools_SearchInsurances_HasNoRequiredParameters | All params optional | Read RequiredParams | Empty | Provider |
| 23 | Yes | GetTools_SearchInsurances_HasExpectedParameters | Properties contain user_carrier_claim, user_plan_claim, carrier_id, usa_state_code, provider_id, location_id | Read Properties | All present | Provider |
| 24 | Yes | GetTools_SearchInsurances_DescriptionIncludesBaseGuidance | Base description mentions "Search insurance carriers", user_carrier_claim, usa_state_code, carrier_id | Read Description | All substrings present | Provider |
| 25 | Yes | GetTools_SearchInsurances_NoPracticeInfo_DescriptionDoesNotIncludeRequirementsBlurb | Without practice info, description omits "Practice insurance collection rules" | Read | Substring NOT present | Provider |
| 26 | Yes | GetTools_SearchInsurances_PracticeInfoWithoutPatientInformation_NoRequirementsBlurb | Same when patientInformation is null | Read | NOT present | Provider |
| 27 | Yes | GetTools_SearchInsurances_SelfPayTransferDisabled_DescriptionAllowsSelfPayPatients | Self-pay-transfer disabled -> description allows self-pay/no-insurance flow | Build practiceInfo with shouldEnableTransferSelfPayPatients=false | Description contains accept-self-pay copy | Provider |
| 28 | Yes | GetTools_SearchInsurances_SelfPayTransferEnabled_DescriptionRequiresTransferForSelfPayPatients | Self-pay-transfer enabled -> description requires transfer for self-pay | Build with true | Description contains transfer-required copy | Provider |
| 29 | Yes | GetTools_SearchInsurances_PracticeInfoWithEmptyPatientInformation_NoRequirementsBlurb | Empty patientInformation -> no rules blurb | Read | NOT present | Provider |
| 30 | Yes | GetTools_SearchInsurances_AllRequired_DescriptionStatesRequiredForAllPatients | All three (carrier/plan/memberId) Required -> "must be collected for all patients" three times | Build practiceInfo; read Description | Contains all three lines | Provider |
| 31 | Yes | GetTools_SearchInsurances_AllNotAsked_DescriptionStatesShouldNotBeAsked | All NotAsked -> "should not be asked for all patients" three times | Build; read | Contains all three lines | Provider |
| 32 | Yes | GetTools_SearchInsurances_AskedLevel_DescriptionMarksAsOptional | Asked -> "should be asked but is optional for all patients" | Build; read | Substring present | Provider |
| 33 | Yes | GetTools_SearchInsurances_DifferentLevelsForNewVsExisting_DescriptionDistinguishesPatientTypes | Different levels for new vs existing -> sentence distinguishes patient types | Build with carrier=Required(new) Asked(existing); read | Distinguished sentence present | Provider |
| 34 | Yes | GetTools_SearchInsurances_OnlyCarrierConfigured_OmitsPlanAndMemberIdSentences | Only InsuranceCarrier configured -> plan/memberId sentences absent | Build patientInformation with only InsuranceCarrier set | Plan and memberId substrings NOT present | Provider |
| 35 | Yes | GetTools_SearchInsurances_NoPracticeInfo_CarrierClaimUsesBaseDescription | user_carrier_claim parameter description falls back to base when no practiceInfo | Read parameter description | Contains "Required unless carrier_id is supplied" but NOT "This practice" | Provider (param desc) |
| 36 | Yes | GetTools_SearchInsurances_NoPracticeInfo_PlanClaimUsesBaseOptionalDescription | user_plan_claim parameter description falls back to base | Read | Contains "Optional" but NOT "This practice" | Provider (param desc) |
| 37 | Yes | GetTools_SearchInsurances_CarrierRequired_CarrierClaimSaysCollect | Carrier=Required -> param description says "This practice requires insurance carrier" | Read | Substring present | Provider (param desc) |
| 38 | Yes | GetTools_SearchInsurances_CarrierNotAsked_CarrierClaimSaysSkip | Carrier=NotAsked -> "does not collect insurance carrier" + "do not call this tool" | Read | Both substrings | Provider (param desc) |
| 39 | Yes | GetTools_SearchInsurances_CarrierAsked_CarrierClaimSaysOptional | Carrier=Asked -> "asks for insurance carrier but does not require it" | Read | Substring present | Provider (param desc) |
| 40 | Yes | GetTools_SearchInsurances_PlanRequired_PlanClaimSaysCollect | Plan=Required -> "This practice requires insurance plan" + "ask the caller for their plan" | Read | Both | Provider (param desc) |
| 41 | Yes | GetTools_SearchInsurances_PlanNotAsked_PlanClaimSaysOmit | Plan=NotAsked -> "does not collect insurance plan" + "omit" | Read | Both | Provider (param desc) |
| 42 | Yes | GetTools_SearchInsurances_PlanRequiredForNewPatientsOnly_PlanClaimDistinguishesPatientTypes | Plan=Required(new), Asked(existing) -> "requires insurance plan for new patients only" | Read | Substring present | Provider (param desc) |
| 43 | Yes | GetTools_SearchInsurances_CarrierRequiredForExistingPatientsOnly_CarrierClaimDistinguishesPatientTypes | Carrier=Asked(new), Required(existing) -> "requires insurance carrier for existing patients only" | Read | Substring present | Provider (param desc) |
| 44 | Yes | GetTools_ContainsGetPracticeFaqTool | POST, exact endpoint, no side effect | Find; assert | Matches | Provider |
| 45 | Yes | GetTools_GetPracticeFaq_HasRequiredTopicParameter | Required = ["topic"] | Read | Matches | Provider |
| 46 | Yes | GetTools_GetPracticeFaq_HasOptionalLocationIdParameter | location_id present and optional | Read | Matches | Provider |
| 47 | Yes | GetTools_GetPracticeFaq_DescriptionMentionsAllTopics | Description lists general, pricing, insurance | Read | All present | Provider |
| 48 | Yes | Provider_IsDiscoverableViaAgentToolsRegistry | Provider is reachable via AgentToolsRegistry lookup | Build registry; lookup | Same instance returned | Provider/Registry |

### Suggested Missing Tests

- `Version` test (peers test it; this provider does not).
- `MemberId` permutations are tested by the bulk levels tests (#30-#33) but not by per-param description tests (#37-#43 only cover carrier and plan). Add `user_member_id_claim` permutations for parity.
- `time_filter` enum values for search_availability (matches the impl `SearchAvailabilityTimeFilter` enum) are not validated in the schema.
- `is_new_patient` schema for search_visit_reasons should be type=boolean - not asserted.
- `search_locations.radius_miles` should have a sensible default and bounded range - description and schema not tested.
- `usa_state_code` description should call out the two-letter code format - test.
- A dedicated test that the only side-effecting tool count is zero (`GetTools().All(t => !t.HasSideEffect)`).
- Snapshot/Verify the full description text for `search_insurances` across the matrix of practice configurations - currently asserted via piecewise `Contain`, which is brittle when the prompt copy is iterated.

### Improvement Suggestions / Irrelevant Tests

- This file is the most thorough provider test in the suite; the granular description-content tests (#27-#43) directly affect LLM behavior and should be retained.
- That said, ~17 of these tests use string-substring matching against prompt copy. Wording iteration (which is frequent for these prompts) will break tests cosmetically. Strongly recommend converting to Verify snapshots (per CS-006) of the full description string per scenario - faster iteration and clearer diffs on PRs.
- Tests #25 and #26 are nearly identical (both confirm "no rules blurb"). Could collapse via TestCase, but readability is fine.
- No test is irrelevant.

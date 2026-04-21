## tests/LlmEvaluations.IntegrationTests/BotCoherenceTest.cs

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 |  | BotCoherence_Score5_PerfectFlow_GoalCompleted | Bot coherence evaluator returns top score for a perfect transcript with completed goal | Arrange transcript sample -> Invoke BotCoherence evaluator via container -> Assert score is 5 | Verifies LLM-based bot coherence scoring rates an ideal call at 5 | Integration - Bot coherence evaluator. In scope: real LLM evaluation on sample transcript; score asserted equals 5. Out of scope: downstream DB persistence; queue flow. |
| 2 |  | BotCoherence_Score4_MinorIssues_GoalMostlyCompleted | Bot coherence returns 4 when minor issues occur but goal mostly completed | Arrange transcript with minor issues -> Invoke evaluator -> Assert score is 4 | Verifies 4-point scoring for mostly coherent conversation | Integration - Bot coherence evaluator. In scope: real LLM scoring for minor-issue scenario. Out of scope: persistence; queue flow. |
| 3 |  | BotCoherence_Score3_ModerateIssues_PartialCompletion | Bot coherence returns 3 for moderate issues and partial completion | Arrange moderate-issue transcript -> Invoke evaluator -> Assert score is 3 | Verifies mid-range score assignment | Integration - Bot coherence evaluator. In scope: real LLM scoring on mid-quality transcript. Out of scope: persistence; queue. |
| 4 |  | BotCoherence_Score2_SignificantIssues_GoalNotCompleted | Bot coherence returns 2 for significant issues with unmet goal | Arrange low-quality transcript -> Invoke evaluator -> Assert score is 2 | Verifies low-range scoring for degraded transcripts | Integration - Bot coherence evaluator. In scope: LLM scoring for poor-quality conversation. Out of scope: persistence; queue. |
| 5 |  | BotCoherence_Score1_SevereIssues_CompleteFailure | Bot coherence returns 1 for severe incoherence | Arrange failure transcript -> Invoke evaluator -> Assert score is 1 | Verifies minimum score assignment for broken conversation | Integration - Bot coherence evaluator. In scope: LLM scoring on worst-case transcript. Out of scope: persistence; queue. |
| 6 |  | BotCoherence_EmptyTranscript_HandlesGracefully | Bot coherence handles empty transcript without throwing | Arrange empty transcript -> Invoke evaluator -> Assert handled gracefully with valid outcome | Verifies defensive handling of empty input | Integration - Bot coherence evaluator edge case. In scope: empty-input resilience. Out of scope: persistence; queue. |
| 7 |  | BotCoherence_SingleTurn_HandlesShortConversation | Bot coherence handles single-turn transcript | Arrange single-turn transcript -> Invoke evaluator -> Assert valid score returned | Verifies evaluator can score very short conversations | Integration - Bot coherence evaluator edge case. In scope: short-input handling. Out of scope: persistence; queue. |
| 8 |  | BotCoherence_TemplateHeavyConversation_DetectsIssues | Bot coherence detects template leakage issues | Arrange template-heavy transcript -> Invoke evaluator -> Assert issue detected and reflected in score | Verifies detection of template leakage pattern | Integration - Bot coherence evaluator. In scope: template-leakage detection. Out of scope: persistence; queue. |

## tests/LlmEvaluations.IntegrationTests/ContextRetentionTest.cs

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 9 |  | FindsContextRetentionIssue_NoIssues | Context retention evaluator finds no issues in coherent transcript | Arrange coherent transcript -> Invoke context retention evaluator -> Assert no issues found | Verifies clean pass on ideal transcript | Integration - Context retention evaluator. In scope: LLM-based retention evaluation on clean transcript. Out of scope: persistence; queue. |
| 10 |  | FindsContextRetentionIssue_ForgetsOneThing | Context retention flags single forgotten context item | Arrange transcript where bot forgets one fact -> Invoke evaluator -> Assert one retention issue found | Verifies detection of single retention failure | Integration - Context retention evaluator. In scope: single-issue detection. Out of scope: persistence; queue. |
| 11 |  | FindsContextRetentionIssue_ForgetsTwoThings | Context retention flags multiple forgotten context items | Arrange transcript where bot forgets two facts -> Invoke evaluator -> Assert two retention issues found | Verifies detection of multiple retention failures | Integration - Context retention evaluator. In scope: multi-issue detection. Out of scope: persistence; queue. |

## tests/LlmEvaluations.IntegrationTests/ConversationFlowTest.cs

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 12 |  | ConversationFlow_Excellent_Score5 | Conversation flow scores 5 for excellent conversational flow | Arrange excellent-flow transcript -> Invoke flow evaluator -> Assert score is 5 | Verifies top flow score | Integration - Conversation flow evaluator. In scope: LLM-based flow evaluation on ideal transcript. Out of scope: persistence; queue. |
| 13 |  | ConversationFlow_GoodWithHesitation_Score4 | Conversation flow scores 4 with minor hesitations | Arrange good-with-hesitation transcript -> Invoke evaluator -> Assert score is 4 | Verifies slightly-degraded flow scoring | Integration - Conversation flow evaluator. In scope: hesitation detection. Out of scope: persistence; queue. |
| 14 |  | ConversationFlow_AverageWithConfusion_Score3 | Conversation flow scores 3 for average flow with confusion | Arrange average-with-confusion transcript -> Invoke evaluator -> Assert score is 3 | Verifies mid-range flow scoring | Integration - Conversation flow evaluator. In scope: confusion detection. Out of scope: persistence; queue. |
| 15 |  | ConversationFlow_PoorWithInterruptions_Score2 | Conversation flow scores 2 for poor flow with interruptions | Arrange poor-flow transcript -> Invoke evaluator -> Assert score is 2 | Verifies low-range flow scoring | Integration - Conversation flow evaluator. In scope: interruption detection. Out of scope: persistence; queue. |
| 16 |  | ConversationFlow_VeryPoor_Score1 | Conversation flow scores 1 for very poor flow | Arrange very-poor-flow transcript -> Invoke evaluator -> Assert score is 1 | Verifies minimum flow score | Integration - Conversation flow evaluator. In scope: worst-case flow scoring. Out of scope: persistence; queue. |

## tests/LlmEvaluations.IntegrationTests/GoalCompletionTest.cs

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 17 |  | GoalCompletion_Score5_GoalFullyCompleted | Goal completion evaluator returns 5 when goal fully completed | Arrange completed-goal transcript -> Invoke goal-completion evaluator -> Assert score is 5 | Verifies top goal-completion scoring | Integration - Goal completion evaluator. In scope: LLM evaluation on fully-met goal. Out of scope: persistence; queue. |
| 18 |  | GoalCompletion_Score3_GoalPartiallyCompleted | Goal completion returns 3 for partial goal completion | Arrange partially-completed transcript -> Invoke evaluator -> Assert score is 3 | Verifies mid-range goal scoring | Integration - Goal completion evaluator. In scope: partial-completion detection. Out of scope: persistence; queue. |
| 19 |  | GoalCompletion_Score3_AmbiguousOutcome | Goal completion returns 3 for ambiguous outcome | Arrange ambiguous transcript -> Invoke evaluator -> Assert score is 3 | Verifies handling of ambiguous outcomes | Integration - Goal completion evaluator. In scope: ambiguous-outcome scoring. Out of scope: persistence; queue. |
| 20 |  | GoalCompletion_Score2_MinimalProgress | Goal completion returns 2 for minimal progress toward goal | Arrange minimal-progress transcript -> Invoke evaluator -> Assert score is 2 | Verifies low-progress scoring | Integration - Goal completion evaluator. In scope: minimal-progress detection. Out of scope: persistence; queue. |
| 21 |  | GoalCompletion_Score1_GoalNotCompleted | Goal completion returns 1 when goal not completed | Arrange unmet-goal transcript -> Invoke evaluator -> Assert score is 1 | Verifies minimum goal-completion score | Integration - Goal completion evaluator. In scope: unmet-goal scoring. Out of scope: persistence; queue. |

## tests/LlmEvaluations.IntegrationTests/GoalIdentificationTest.cs

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 22 |  | GoalIdentification_Score5_ClearGoal | Goal identification returns 5 for clearly stated goal | Arrange clear-goal transcript -> Invoke evaluator -> Assert score is 5 | Verifies top identification scoring | Integration - Goal identification evaluator. In scope: LLM evaluation on clear-goal transcript. Out of scope: persistence; queue. |
| 23 |  | GoalIdentification_Score3Or4_SomeAmbiguity | Goal identification returns 3 or 4 for goal with some ambiguity | Arrange ambiguous-goal transcript -> Invoke evaluator -> Assert score in 3-4 range | Verifies mid-range identification scoring | Integration - Goal identification evaluator. In scope: ambiguous-goal handling. Out of scope: persistence; queue. |
| 24 |  | GoalIdentification_Score1Or2_UnclearGoal | Goal identification returns 1 or 2 for unclear goal | Arrange unclear-goal transcript -> Invoke evaluator -> Assert score in 1-2 range | Verifies low-range identification scoring | Integration - Goal identification evaluator. In scope: unclear-goal detection. Out of scope: persistence; queue. |
| 25 |  | GoalIdentification_Score2_PartiallyIdentified | Goal identification returns 2 for partially identified goal | Arrange partial-identification transcript -> Invoke evaluator -> Assert score is 2 | Verifies partial-identification scoring | Integration - Goal identification evaluator. In scope: partial-identification handling. Out of scope: persistence; queue. |
| 26 |  | GoalIdentification_Score1_NoGoalIdentified | Goal identification returns 1 when no goal identified | Arrange no-goal transcript -> Invoke evaluator -> Assert score is 1 | Verifies minimum identification scoring | Integration - Goal identification evaluator. In scope: no-goal detection. Out of scope: persistence; queue. |

## tests/LlmEvaluations.IntegrationTests/Service/Evaluators/AudioQualityEvaluatorIntegrationTests.cs

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 27 |  | EvaluateAsync_WithRealAudioFile_ReturnsValidScore | Audio quality evaluator returns a valid score against a real audio file | Arrange real audio file -> Invoke audio quality evaluator -> Assert valid score returned | Verifies end-to-end audio quality evaluation path | Integration - Audio quality evaluator. In scope: real-audio LLM evaluation path. Out of scope: persistence; queue. |

## tests/LlmEvaluations.IntegrationTests/WorkerIntegrationTests.cs

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 28 |  | ProcessMessage_FromQueue_SavesResultToDynamoDB | Worker pulls a queue message; runs evaluation; persists result to DynamoDB | Enqueue message via SNS/SQS -> Worker consumes and orchestrates evaluation -> Assert result saved in DynamoDB | Verifies end-to-end worker flow saves results | Integration - Worker pipeline. In scope: SQS consumer; orchestrator; DynamoDB write. Out of scope: HTTP API; auth. |
| 29 |  | Orchestrator_ExecutesEvaluations_ReturnsResults | Orchestrator executes configured evaluations and returns results | Build orchestrator -> Execute evaluations for sample request -> Assert results populated | Verifies orchestrator execution path | Integration - Evaluation orchestrator. In scope: real evaluator execution pipeline. Out of scope: queue consumption; HTTP. |

## tests/LlmEvaluations.UnitTests/Auth/JwtRoleHelperTests.cs

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 30 |  | IsAuthorizedForRoles_NonPhiRequest_ReturnsTrue | Non-PHI requests are always authorized | Build non-PHI claims -> Call IsAuthorizedForRoles -> Assert returns true | Verifies non-PHI bypass | Unit - JwtRoleHelper. In scope: authorization branching for non-PHI. Out of scope: JWT parsing. |
| 31 |  | IsAuthorizedForRoles_PhiRequest_UserHasRole_ReturnsTrue | PHI request with user holding required role is authorized | Build claims with required role -> Call IsAuthorizedForRoles -> Assert true | Verifies role-based PHI authorization | Unit - JwtRoleHelper. In scope: role-match authorization. Out of scope: JWT parsing. |
| 32 |  | IsAuthorizedForRoles_PhiRequest_UserMissingRole_ReturnsFalse | PHI request with user missing role is rejected | Build claims lacking role -> Call IsAuthorizedForRoles -> Assert false | Verifies rejection when required role missing | Unit - JwtRoleHelper. In scope: missing-role rejection. Out of scope: JWT parsing. |
| 33 |  | IsAuthorizedForRoles_PhiRequest_NoRolesRequired_ReturnsFalse | PHI request with no allowed roles is rejected | Build PHI request with empty roles -> Call IsAuthorizedForRoles -> Assert false | Verifies empty-roles safety default | Unit - JwtRoleHelper. In scope: empty-roles handling. Out of scope: JWT parsing. |
| 34 |  | IsAuthorizedForRoles_PhiRequest_EmptyUserRoles_ReturnsFalse | PHI request where user has no roles is rejected | Build empty user roles -> Call IsAuthorizedForRoles -> Assert false | Verifies rejection for unauthenticated user | Unit - JwtRoleHelper. In scope: no-user-role rejection. Out of scope: JWT parsing. |
| 35 |  | IsAuthorizedForRoles_PhiRequest_CaseInsensitiveMatch_ReturnsTrue | Role matching is case-insensitive | Build mismatched-case role -> Call IsAuthorizedForRoles -> Assert true | Verifies case-insensitive role comparison | Unit - JwtRoleHelper. In scope: case-insensitive match. Out of scope: JWT parsing. |
| 36 |  | IsAuthorizedForRoles_PhiRequest_MultipleRolesUserHasOne_ReturnsTrue | User is authorized if they have at least one allowed role | Build multi-role request; user has one -> Call helper -> Assert true | Verifies OR-logic across roles | Unit - JwtRoleHelper. In scope: any-match authorization. Out of scope: JWT parsing. |
| 37 |  | ValidateRoleNames_AllValidRoles_ReturnsEmptyList | Role-name validation returns empty list when all names valid | Provide valid role names -> Call ValidateRoleNames -> Assert empty list | Verifies valid-role acceptance | Unit - JwtRoleHelper. In scope: role-name validation. Out of scope: JWT parsing. |
| 38 |  | ValidateRoleNames_InvalidRoles_ReturnsInvalidList | Role-name validation returns invalid names | Provide mix of valid/invalid -> Call ValidateRoleNames -> Assert invalid ones returned | Verifies detection of invalid role names | Unit - JwtRoleHelper. In scope: invalid-role detection. Out of scope: JWT parsing. |
| 39 |  | ValidateRoleNames_EmptyList_ReturnsEmptyList | Empty role list validation returns empty | Provide empty role list -> Call ValidateRoleNames -> Assert empty | Verifies no-op handling for empty list | Unit - JwtRoleHelper. In scope: empty-input handling. Out of scope: JWT parsing. |
| 40 |  | ValidateRoleNames_NumericIds_AreValid | Numeric role IDs pass validation | Provide numeric ID strings -> Call ValidateRoleNames -> Assert empty invalid list | Verifies numeric-ID acceptance | Unit - JwtRoleHelper. In scope: numeric-ID validation. Out of scope: JWT parsing. |
| 41 |  | ValidateRoleNames_CaseInsensitive_AcceptsVariants | Validation accepts case-insensitive role names | Provide mixed-case names -> Call ValidateRoleNames -> Assert all treated as valid | Verifies case-insensitive validation | Unit - JwtRoleHelper. In scope: case-insensitive validation. Out of scope: JWT parsing. |
| 42 |  | IsAuthorizedForRoles_NumericRoleId_UserHasId_ReturnsTrue | Numeric role ID matches user role ID | Build request requiring numeric ID user holds -> Assert authorized | Verifies numeric role authorization | Unit - JwtRoleHelper. In scope: numeric-ID authorization. Out of scope: JWT parsing. |
| 43 |  | IsAuthorizedForRoles_NumericRoleId_UserMissingId_ReturnsFalse | Numeric role ID not held is rejected | Build request requiring numeric ID user lacks -> Assert rejected | Verifies numeric-ID rejection | Unit - JwtRoleHelper. In scope: numeric-ID missing-role rejection. Out of scope: JWT parsing. |

## tests/LlmEvaluations.UnitTests/Config/LlmPricingConfigTests.cs

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 44 |  | GetPricingForModel_KnownModel_ReturnsPricing | Pricing config returns rates for a known model | Create config with model pricing -> Call GetPricingForModel -> Assert rates match | Verifies known-model lookup | Unit - LlmPricingConfig. In scope: model-pricing lookup. Out of scope: configuration loading. |
| 45 |  | GetPricingForModel_UnknownModel_ReturnsDefault | Pricing config returns default rates for unknown model | Create config -> Call GetPricingForModel with unknown -> Assert default returned | Verifies fallback to default pricing | Unit - LlmPricingConfig. In scope: default fallback. Out of scope: configuration loading. |
| 46 |  | GetPricingForModel_CaseInsensitive_ReturnsPricing | Model lookup is case-insensitive | Use mismatched-case model name -> Call GetPricingForModel -> Assert found | Verifies case-insensitive lookup | Unit - LlmPricingConfig. In scope: case handling. Out of scope: configuration loading. |

## tests/LlmEvaluations.UnitTests/Config/SamplingConfigTests.cs

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 47 |  | DefaultSamplingRate_WhenNotSet_DefaultsToOne | Default sampling rate is 1.0 when not configured | Build default SamplingConfig -> Read DefaultSamplingRate -> Assert equals 1.0 | Verifies default-rate behavior | Unit - SamplingConfig. In scope: default value. Out of scope: rate application. |
| 48 |  | DefaultSamplingRate_WhenSet_UsesConfiguredValue | Sampling rate uses configured value when set | Build SamplingConfig with explicit value -> Read DefaultSamplingRate -> Assert equals configured | Verifies configured override | Unit - SamplingConfig. In scope: explicit-value handling. Out of scope: rate application. |
| 49 |  | DefaultSamplingRate_Zero_IsValid | Zero sampling rate is valid | Build config with 0.0 -> Validate -> Assert valid | Verifies 0 boundary | Unit - SamplingConfig. In scope: boundary validation. Out of scope: rate application. |
| 50 |  | DefaultSamplingRate_One_IsValid | One sampling rate is valid | Build config with 1.0 -> Validate -> Assert valid | Verifies 1 boundary | Unit - SamplingConfig. In scope: boundary validation. Out of scope: rate application. |

## tests/LlmEvaluations.UnitTests/Config/SsmGoogleCredentialsProviderTests.cs

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 51 |  | GetCredentialsAsync_ValidSsmParameter_ReturnsCredentials | Provider returns credentials from SSM parameter | Mock SSM to return valid JSON -> Call GetCredentialsAsync -> Assert credentials returned | Verifies happy path | Unit - SsmGoogleCredentialsProvider. In scope: SSM parameter retrieval. Out of scope: real AWS SSM. |
| 52 |  | GetCredentialsAsync_MissingParameter_Throws | Provider throws when SSM parameter missing | Mock SSM to throw parameter-not-found -> Call GetCredentialsAsync -> Assert exception | Verifies missing-param error path | Unit - SsmGoogleCredentialsProvider. In scope: error handling. Out of scope: real AWS SSM. |
| 53 |  | GetCredentialsAsync_InvalidJson_Throws | Provider throws when SSM returns invalid JSON | Mock SSM to return garbage JSON -> Call GetCredentialsAsync -> Assert exception | Verifies invalid-payload handling | Unit - SsmGoogleCredentialsProvider. In scope: JSON validation. Out of scope: real AWS SSM. |
| 54 |  | GetCredentialsAsync_CachesResult_OnlyCallsSsmOnce | Provider caches credentials after first fetch | Call GetCredentialsAsync twice -> Assert SSM called only once | Verifies caching | Unit - SsmGoogleCredentialsProvider. In scope: caching behavior. Out of scope: real AWS SSM. |

## tests/LlmEvaluations.UnitTests/Model/CostTrackingTests.cs

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 55 |  | LlmCost_TotalCost_SumsTextAndAudioCost | Total cost sums text and audio costs | Build LlmCost with text/audio costs -> Read TotalCost -> Assert equals sum | Verifies summation property | Unit - LlmCost model. In scope: total-cost computation. Out of scope: pricing lookup. |
| 56 |  | LlmCost_TotalTokens_SumsAllTokenCounts | Total tokens sums all token counts | Build LlmCost with token counts -> Read TotalTokens -> Assert equals sum | Verifies token summation | Unit - LlmCost model. In scope: total-token computation. Out of scope: pricing lookup. |
| 57 |  | LlmCost_Empty_ReturnsZero | Empty cost returns zero totals | Build empty LlmCost -> Read totals -> Assert zero | Verifies zero-state behavior | Unit - LlmCost model. In scope: default state. Out of scope: pricing lookup. |
| 58 |  | LlmCost_AggregatesMultiple_CorrectlySums | Aggregation across multiple costs correctly sums | Aggregate list of LlmCosts -> Read result -> Assert totals sum correctly | Verifies aggregate-sum logic | Unit - LlmCost aggregation. In scope: aggregate computation. Out of scope: pricing lookup. |

## tests/LlmEvaluations.UnitTests/Repository/AuditLoggerTests.cs

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 59 |  | LogEvaluationRequestAccess_NonPhi_DoesNotLog | Non-PHI access is not audit-logged | Setup non-PHI request -> Call LogEvaluationRequestAccess -> Assert logger not invoked | Verifies no-log for non-PHI | Unit - AuditLogger. In scope: non-PHI short-circuit. Out of scope: underlying transport. |
| 60 |  | LogEvaluationRequestAccess_Phi_LogsRead | PHI read access logs audit entry | Setup PHI request -> Call LogEvaluationRequestAccess with Read -> Assert logger invoked with Read | Verifies PHI read logging | Unit - AuditLogger. In scope: Read audit logging. Out of scope: underlying transport. |
| 61 |  | LogEvaluationRequestAccess_Phi_LogsUpdate | PHI update access logs audit entry | Setup PHI request -> Call LogEvaluationRequestAccess with Update -> Assert logger invoked with Update | Verifies PHI update logging | Unit - AuditLogger. In scope: Update audit logging. Out of scope: underlying transport. |
| 62 |  | LogEvaluationRequestAccess_UnauthorizedPhi_LogsUnauthorized | Unauthorized PHI access logs as denied | Setup unauthorized PHI request -> Call logger -> Assert authorization=false logged | Verifies denied-access logging | Unit - AuditLogger. In scope: unauthorized-access logging. Out of scope: underlying transport. |
| 63 |  | LogEvaluationRequestAccess_MissingPracticeId_StillLogs | Missing practice id still logs entry | Setup PHI request without practice id -> Call logger -> Assert logged with null practice id | Verifies nullable-field handling | Unit - AuditLogger. In scope: null practice id. Out of scope: underlying transport. |
| 64 |  | LogEvaluationRequestAccess_MissingObjectNamespace_StillLogs | Missing object namespace still logs entry | Setup PHI request without object namespace -> Call logger -> Assert logged | Verifies nullable-namespace handling | Unit - AuditLogger. In scope: null namespace. Out of scope: underlying transport. |
| 65 |  | LogEvaluationRequestAccess_WithFullMetadata_PropagatesAllFields | Full metadata is propagated into audit entry | Setup PHI with full metadata -> Call logger -> Assert all fields populated on log | Verifies field propagation | Unit - AuditLogger. In scope: metadata propagation. Out of scope: underlying transport. |
| 66 |  | LogEvaluationRequestAccess_WithJwtClaims_UsesUserFromClaims | User identity from JWT claims is used in log | Setup claims principal -> Call logger -> Assert user from claims | Verifies user-identity extraction | Unit - AuditLogger. In scope: claim-based user extraction. Out of scope: underlying transport. |
| 67 |  | LogEvaluationRequestAccess_NoJwtClaims_UsesSystemUser | Missing JWT claims defaults to system user | Setup empty claims principal -> Call logger -> Assert system-user logged | Verifies fallback user identity | Unit - AuditLogger. In scope: fallback user. Out of scope: underlying transport. |
| 68 |  | LogEvaluationRequestAccess_MultipleRoles_LogsJoinedRoles | Multiple roles are logged joined together | Setup principal with multi-role -> Call logger -> Assert roles combined | Verifies multi-role formatting | Unit - AuditLogger. In scope: role formatting. Out of scope: underlying transport. |

## tests/LlmEvaluations.UnitTests/Repository/EvaluationRepositoryTests.cs

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 69 |  | GetByIdAsync_WhenRecordNotFound_ReturnsNull | Returns null when record not in Dynamo | Mock Dynamo to return null -> Call GetByIdAsync -> Assert null | Verifies not-found path | Unit - EvaluationRepository. In scope: null-return semantics. Out of scope: real DynamoDB. |
| 70 |  | GetByIdAsync_WhenRecordFound_ReturnsRecord | Returns record when present | Mock Dynamo to return DTO -> Call GetByIdAsync -> Assert DTO returned | Verifies happy-path read | Unit - EvaluationRepository. In scope: read mapping. Out of scope: real DynamoDB. |
| 71 |  | GetByIdAsync_NonPhi_DoesNotInvokeAuditLogger | Non-PHI reads skip audit logging | Return non-PHI DTO -> Call GetByIdAsync -> Assert audit logger not called | Verifies non-PHI audit bypass | Unit - EvaluationRepository. In scope: audit branching. Out of scope: real DynamoDB. |
| 72 |  | GetByIdAsync_Phi_AuthorizedUser_InvokesAuditLoggerAsAuthorized | PHI read by authorized user logs authorized access | Return PHI DTO with matching role -> Call GetByIdAsync -> Assert audit logged authorized=true | Verifies authorized PHI audit | Unit - EvaluationRepository. In scope: authorized audit. Out of scope: real DynamoDB. |
| 73 |  | GetByIdAsync_Phi_UnauthorizedUser_ThrowsUnauthorized | PHI read by unauthorized user throws | Return PHI DTO lacking user role -> Call GetByIdAsync -> Assert unauthorized exception | Verifies unauthorized rejection | Unit - EvaluationRepository. In scope: unauthorized rejection. Out of scope: real DynamoDB. |
| 74 |  | GetByIdAsync_Phi_UnauthorizedUser_LogsAuditAsUnauthorized | Unauthorized PHI read still logs audit entry | Return PHI DTO unauthorized -> Call GetByIdAsync; catch -> Assert audit called with authorized=false | Verifies denied-access logging | Unit - EvaluationRepository. In scope: denied-access audit. Out of scope: real DynamoDB. |
| 75 |  | SaveAsync_SavesRecord | Save writes DTO to Dynamo | Build DTO -> Call SaveAsync -> Assert Dynamo save invoked with DTO | Verifies happy-path save | Unit - EvaluationRepository. In scope: save call. Out of scope: real DynamoDB. |
| 76 |  | SaveAsync_PropagatesCancellationToken | Save propagates cancellation token | Call SaveAsync with token -> Assert Dynamo invoked with same token | Verifies cancellation propagation | Unit - EvaluationRepository. In scope: token flow. Out of scope: real DynamoDB. |
| 77 |  | UpdateStatusAsync_UpdatesStatusAndTimestamp | Update status writes new status and updated timestamp | Call UpdateStatusAsync -> Assert update command contains status and UpdatedAt | Verifies status-update payload | Unit - EvaluationRepository. In scope: status update. Out of scope: real DynamoDB. |
| 78 |  | UpdateStatusAsync_NonPhi_DoesNotInvokeAuditLogger | Non-PHI status update skips audit logging | Build non-PHI record -> Call update -> Assert audit not called | Verifies audit bypass | Unit - EvaluationRepository. In scope: audit branching. Out of scope: real DynamoDB. |
| 79 |  | UpdateStatusAsync_Phi_InvokesAuditLogger | PHI status update logs audit | Build PHI record -> Call update -> Assert audit called with Update | Verifies PHI audit | Unit - EvaluationRepository. In scope: PHI audit. Out of scope: real DynamoDB. |
| 80 |  | SaveResultAsync_SavesResult | SaveResult writes results and score | Call SaveResultAsync -> Assert update command contains results/score | Verifies result-save payload | Unit - EvaluationRepository. In scope: result save. Out of scope: real DynamoDB. |
| 81 |  | SaveResultAsync_NonPhi_DoesNotInvokeAuditLogger | Non-PHI result save skips audit | Non-PHI record -> SaveResultAsync -> Assert audit not called | Verifies audit bypass | Unit - EvaluationRepository. In scope: audit branching. Out of scope: real DynamoDB. |
| 82 |  | SaveResultAsync_Phi_InvokesAuditLogger | PHI result save logs audit | PHI record -> SaveResultAsync -> Assert audit called with Update | Verifies PHI audit | Unit - EvaluationRepository. In scope: PHI audit. Out of scope: real DynamoDB. |
| 83 |  | SaveResultAsync_WithErrorMessage_PersistsError | Error message is persisted on result save | Call SaveResultAsync with error -> Assert update includes error field | Verifies error persistence | Unit - EvaluationRepository. In scope: error persistence. Out of scope: real DynamoDB. |
| 84 |  | SaveResultAsync_WithCost_PersistsCostFields | Cost fields persisted on result save | Call SaveResultAsync with LlmCost -> Assert cost fields persisted | Verifies cost persistence | Unit - EvaluationRepository. In scope: cost persistence. Out of scope: real DynamoDB. |
| 85 |  | QueryAsync_FiltersByClientName | Query filters by client name | Call QueryAsync with client filter -> Assert query expression contains client | Verifies client filter | Unit - EvaluationRepository. In scope: query filter. Out of scope: real DynamoDB. |
| 86 |  | QueryAsync_FiltersByTimeRange | Query filters by time range | Call QueryAsync with time range -> Assert expression contains range filter | Verifies time-range filter | Unit - EvaluationRepository. In scope: query filter. Out of scope: real DynamoDB. |
| 87 |  | QueryAsync_FiltersByStatus | Query filters by status | Call QueryAsync with status -> Assert expression contains status filter | Verifies status filter | Unit - EvaluationRepository. In scope: query filter. Out of scope: real DynamoDB. |
| 88 |  | QueryAsync_ReturnsMappedResults | Query maps Dynamo results to models | Mock Dynamo to return DTO list -> Call QueryAsync -> Assert mapped models | Verifies result mapping | Unit - EvaluationRepository. In scope: mapping. Out of scope: real DynamoDB. |
| 89 |  | QueryAsync_EmptyResults_ReturnsEmpty | Empty Dynamo result returns empty list | Mock Dynamo empty -> Call QueryAsync -> Assert empty list | Verifies empty-result handling | Unit - EvaluationRepository. In scope: empty mapping. Out of scope: real DynamoDB. |
| 90 |  | QueryAsync_PaginatesResults | Pagination token drives multi-page query | Mock Dynamo to return pagination token -> Call QueryAsync -> Assert second query executed | Verifies pagination | Unit - EvaluationRepository. In scope: pagination. Out of scope: real DynamoDB. |
| 91 |  | DeleteAsync_DeletesRecord | Delete removes record from Dynamo | Call DeleteAsync -> Assert Dynamo delete invoked | Verifies delete path | Unit - EvaluationRepository. In scope: delete call. Out of scope: real DynamoDB. |

## tests/LlmEvaluations.UnitTests/Repository/RepoHelperTests.cs

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 92 |  | ToDto_ConvertsEvaluationRequestToDto | Converts model to DTO shape | Build EvaluationRequest -> Call ToDto -> Assert DTO fields match | Verifies model-to-DTO mapping | Unit - RepoHelper. In scope: ToDto mapping. Out of scope: persistence. |
| 93 |  | ToDto_SetsPrimaryAndSortKeys | Primary and sort keys populated on DTO | Call ToDto -> Assert keys set per scheme | Verifies key construction | Unit - RepoHelper. In scope: key construction. Out of scope: persistence. |
| 94 |  | ToDto_SerializesEvalConfigs | EvalConfigs list serialized onto DTO | Build request with configs -> Call ToDto -> Assert list copied | Verifies config list mapping | Unit - RepoHelper. In scope: list mapping. Out of scope: persistence. |
| 95 |  | ToDto_SetsTimestamps | Timestamps set on DTO | Call ToDto -> Assert EnqueuedAt/UpdatedAt populated | Verifies timestamp mapping | Unit - RepoHelper. In scope: timestamp mapping. Out of scope: persistence. |
| 96 |  | ToModel_ConvertsDtoToEvaluationRequest | DTO converts back to model | Build DTO -> Call ToModel -> Assert model populated | Verifies DTO-to-model mapping | Unit - RepoHelper. In scope: ToModel mapping. Out of scope: persistence. |
| 97 |  | ToModel_ParsesStatusEnum | Status string parses to enum | Build DTO with status string -> Call ToModel -> Assert enum parsed | Verifies enum parsing | Unit - RepoHelper. In scope: enum parsing. Out of scope: persistence. |
| 98 |  | ToModel_HandlesNullResults | Null results handled gracefully | Build DTO with null results -> Call ToModel -> Assert empty dictionary | Verifies null-safe mapping | Unit - RepoHelper. In scope: null handling. Out of scope: persistence. |
| 99 |  | ToEvalResult_ConvertsDtoToEvalResult | Converts DTO into public EvalResult shape | Build DTO -> Call ToEvalResult -> Assert fields match | Verifies EvalResult mapping | Unit - RepoHelper. In scope: EvalResult mapping. Out of scope: persistence. |
| 100 |  | ToEvalResult_MapsResultsDictionary | Inner results dictionary mapped | Build DTO with outcomes -> Call ToEvalResult -> Assert dictionary mapped | Verifies outcome mapping | Unit - RepoHelper. In scope: outcome mapping. Out of scope: persistence. |
| 101 |  | MkPrimaryKey_BuildsExpectedKey | Builds primary key with expected prefix | Call MkPrimaryKey(id) -> Assert returns prefixed value | Verifies key format | Unit - RepoHelper. In scope: key formatting. Out of scope: persistence. |
| 102 |  | MkPrimaryKey_WithEmptyId_Throws | Empty id throws | Call MkPrimaryKey with empty -> Assert exception | Verifies guard clause | Unit - RepoHelper. In scope: input validation. Out of scope: persistence. |
| 103 |  | SortKeyValue_IsExpectedConstant | Sort key constant matches expected | Read SortKeyValue -> Assert equals expected | Verifies constant value | Unit - RepoHelper. In scope: constant value. Out of scope: persistence. |
| 104 |  | ToOutcomeDto_MapsScoreAndStatus | Outcome DTO includes score and status | Build EvalOutcome -> Call ToOutcomeDto -> Assert mapping | Verifies outcome DTO mapping | Unit - RepoHelper. In scope: outcome DTO mapping. Out of scope: persistence. |
| 105 |  | ToOutcomeDto_MapsNotes | Outcome notes mapped | Build outcome with notes -> Call ToOutcomeDto -> Assert notes mapped | Verifies notes mapping | Unit - RepoHelper. In scope: notes mapping. Out of scope: persistence. |
| 106 |  | FromOutcomeDto_MapsBack | DTO maps back to outcome | Build DTO -> Call FromOutcomeDto -> Assert outcome | Verifies reverse mapping | Unit - RepoHelper. In scope: reverse mapping. Out of scope: persistence. |
| 107 |  | ToDto_CopiesMetadata | Metadata dictionary copied to DTO | Build request with metadata -> Call ToDto -> Assert metadata copied | Verifies metadata mapping | Unit - RepoHelper. In scope: metadata mapping. Out of scope: persistence. |
| 108 |  | ToDto_CopiesRolesWithAccess | Roles list copied to DTO | Build request with roles -> Call ToDto -> Assert roles copied | Verifies roles mapping | Unit - RepoHelper. In scope: roles mapping. Out of scope: persistence. |
| 109 |  | ToDto_SetsIsPhiFlag | IsPhi flag propagates to DTO | Build PHI request -> Call ToDto -> Assert IsPhi=true on DTO | Verifies PHI flag mapping | Unit - RepoHelper. In scope: IsPhi mapping. Out of scope: persistence. |
| 110 |  | ToDto_SetsClientName | ClientName propagates to DTO | Build request -> Call ToDto -> Assert ClientName on DTO | Verifies client-name mapping | Unit - RepoHelper. In scope: client-name mapping. Out of scope: persistence. |

## tests/LlmEvaluations.UnitTests/Service/EvaluationOrchestratorTests.cs

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 111 |  | ExecuteAsync_NoEvaluators_ReturnsEmptyResults | Executing with no configured evaluators returns empty result set | Build request with empty configs -> Call ExecuteAsync -> Assert empty results | Verifies empty-config path | Unit - EvaluationOrchestrator. In scope: empty-config handling. Out of scope: real evaluators. |
| 112 |  | ExecuteAsync_SingleEvaluator_ReturnsResult | Executing with one evaluator returns its result | Build request with one config -> Call ExecuteAsync -> Assert single result returned | Verifies single-evaluator flow | Unit - EvaluationOrchestrator. In scope: single evaluator. Out of scope: real evaluators. |
| 113 |  | ExecuteAsync_MultipleEvaluators_ReturnsAllResults | Multiple evaluators return all results | Build request with multiple configs -> Call ExecuteAsync -> Assert all results returned | Verifies multi-evaluator flow | Unit - EvaluationOrchestrator. In scope: multi-evaluator. Out of scope: real evaluators. |
| 114 |  | ExecuteAsync_EvaluatorThrows_ReturnsFailedOutcome | One evaluator throwing returns Failed outcome for that evaluator | Make evaluator throw -> Call ExecuteAsync -> Assert its outcome is Failed with error | Verifies failure isolation | Unit - EvaluationOrchestrator. In scope: failure isolation. Out of scope: real evaluators. |
| 115 |  | ExecuteAsync_EvaluatorThrows_OtherEvaluatorsStillRun | One evaluator throwing does not stop others | Make one evaluator throw -> Call ExecuteAsync -> Assert other results still returned | Verifies partial-success guarantee | Unit - EvaluationOrchestrator. In scope: partial-success. Out of scope: real evaluators. |
| 116 |  | ExecuteAsync_AllEvaluatorsFail_OverallStatusFailed | All evaluators failing yields overall Failed status | Make all evaluators throw -> Call ExecuteAsync -> Assert overall status Failed | Verifies aggregate-failure status | Unit - EvaluationOrchestrator. In scope: aggregate failure. Out of scope: real evaluators. |
| 117 |  | ExecuteAsync_MixedResults_ComputesOverallScore | Overall score computed from mixed outcomes | Provide mixed outcomes -> Call ExecuteAsync -> Assert overall score computed | Verifies score aggregation | Unit - EvaluationOrchestrator. In scope: score aggregation. Out of scope: real evaluators. |
| 118 |  | ExecuteAsync_UnknownEvaluatorConfig_FailsGracefully | Unknown evaluator config yields failure for that config | Provide unknown config -> Call ExecuteAsync -> Assert failed outcome with reason | Verifies unknown-config handling | Unit - EvaluationOrchestrator. In scope: unknown-config handling. Out of scope: real evaluators. |
| 119 |  | ExecuteAsync_AggregatesLlmCost | Aggregated LLM cost is sum of per-evaluator cost | Provide evaluators returning cost -> Call ExecuteAsync -> Assert aggregated cost summed | Verifies cost aggregation | Unit - EvaluationOrchestrator. In scope: cost aggregation. Out of scope: real evaluators. |
| 120 |  | ExecuteAsync_PropagatesCancellationToken | Cancellation token flows to evaluators | Call ExecuteAsync with token -> Assert evaluators invoked with same token | Verifies token flow | Unit - EvaluationOrchestrator. In scope: cancellation. Out of scope: real evaluators. |
| 121 |  | ExecuteAsync_RespectsParallelism | Evaluators run concurrently up to parallelism limit | Configure parallelism -> Call ExecuteAsync -> Assert concurrency bounded | Verifies parallelism control | Unit - EvaluationOrchestrator. In scope: concurrency. Out of scope: real evaluators. |
| 122 |  | ExecuteAsync_EmitsMetrics | Metrics recorder invoked during execution | Call ExecuteAsync -> Assert metric recorder invoked | Verifies metric emission | Unit - EvaluationOrchestrator. In scope: metrics. Out of scope: real evaluators. |
| 123 |  | ExecuteAsync_LogsEvaluatorFailure | Logs error on evaluator failure | Make evaluator throw -> Call ExecuteAsync -> Assert logger Error invoked | Verifies failure logging | Unit - EvaluationOrchestrator. In scope: logging. Out of scope: real evaluators. |

## tests/LlmEvaluations.UnitTests/Service/EvaluationQueryingServiceTests.cs

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 124 |  | Normalize_OneToFiveScale_MapsToZeroOne | Normalizes 1-5 scale scores into 0-1 range | Provide score in 1-5 -> Call normalize -> Assert 0-1 result | Verifies 1-5 to 0-1 mapping | Unit - Score normalization. In scope: 1-5 scale. Out of scope: DB. |
| 125 |  | Normalize_Score1_ReturnsZero | Score 1 on 1-5 scale returns 0 | Provide 1 -> Normalize -> Assert 0 | Verifies boundary | Unit - Score normalization. In scope: min boundary. Out of scope: DB. |
| 126 |  | Normalize_Score5_ReturnsOne | Score 5 on 1-5 scale returns 1 | Provide 5 -> Normalize -> Assert 1 | Verifies boundary | Unit - Score normalization. In scope: max boundary. Out of scope: DB. |
| 127 |  | Normalize_Score3_Returns0_5 | Score 3 maps to 0.5 | Provide 3 -> Normalize -> Assert 0.5 | Verifies midpoint | Unit - Score normalization. In scope: midpoint. Out of scope: DB. |
| 128 |  | Normalize_ZeroOneScale_ReturnsUnchanged | Scores already 0-1 are returned unchanged | Provide 0-1 score -> Normalize -> Assert same value | Verifies pass-through | Unit - Score normalization. In scope: already-normalized. Out of scope: DB. |
| 129 |  | Normalize_NullScore_ReturnsNull | Null score normalizes to null | Provide null -> Normalize -> Assert null | Verifies null pass-through | Unit - Score normalization. In scope: null handling. Out of scope: DB. |
| 130 |  | Normalize_NegativeScore_Throws | Negative score throws | Provide negative -> Normalize -> Assert exception | Verifies guard | Unit - Score normalization. In scope: validation. Out of scope: DB. |
| 131 |  | Normalize_ScoreOutOfRange_Throws | Out-of-range score throws | Provide value > 5 -> Normalize -> Assert exception | Verifies upper guard | Unit - Score normalization. In scope: validation. Out of scope: DB. |
| 132 |  | TimeBucket_Day_BucketsByDay | Day bucket groups by day | Provide instants within same day -> Call bucket -> Assert same bucket | Verifies daily bucketing | Unit - Time bucketing. In scope: day bucket. Out of scope: DB. |
| 133 |  | TimeBucket_Hour_BucketsByHour | Hour bucket groups by hour | Provide instants within same hour -> Call bucket -> Assert same bucket | Verifies hourly bucketing | Unit - Time bucketing. In scope: hour bucket. Out of scope: DB. |
| 134 |  | TimeBucket_Week_BucketsByWeek | Week bucket groups by week | Provide instants within same week -> Call bucket -> Assert same bucket | Verifies weekly bucketing | Unit - Time bucketing. In scope: week bucket. Out of scope: DB. |
| 135 |  | TimeBucket_DifferentBuckets_DifferentGroups | Different bucket granularities yield different groupings | Bucket same instants under different granularities -> Assert differing groups | Verifies granularity differences | Unit - Time bucketing. In scope: granularity handling. Out of scope: DB. |
| 136 |  | TimeBucket_UtcAlignment_AlignsToUtc | Bucket boundaries aligned to UTC | Provide instant near midnight UTC -> Bucket -> Assert UTC alignment | Verifies UTC alignment | Unit - Time bucketing. In scope: UTC alignment. Out of scope: DB. |
| 137 |  | Stats_EmptyResults_ReturnsZeroStats | Stats over empty input returns zero stats | Provide empty -> Compute stats -> Assert zero counts | Verifies empty-input stats | Unit - Stats calc. In scope: empty input. Out of scope: DB. |
| 138 |  | Stats_SingleResult_ReturnsSingleResultStats | Stats with single result returns that single value | Provide one result -> Compute stats -> Assert count=1 avg=value | Verifies single-input stats | Unit - Stats calc. In scope: single input. Out of scope: DB. |
| 139 |  | Stats_MultipleResults_ComputesAverageAndCount | Stats compute average and count across multiple results | Provide list -> Compute stats -> Assert avg and count | Verifies multi-input stats | Unit - Stats calc. In scope: aggregation. Out of scope: DB. |
| 140 |  | Stats_GroupsByEvalConfigType | Stats grouped by evaluation type | Provide mixed types -> Compute stats -> Assert grouping | Verifies group-by behavior | Unit - Stats calc. In scope: grouping. Out of scope: DB. |

## tests/LlmEvaluations.UnitTests/Service/EvaluationQueueConsumerTests.cs

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 141 |  | ConsumeMessagesAsync_NoMessages_NoOp | No queue messages results in no work | Mock SQS empty receive -> Call ConsumeMessagesAsync -> Assert no orchestrator call | Verifies empty-queue no-op | Unit - EvaluationQueueConsumer. In scope: empty poll. Out of scope: real SQS. |
| 142 |  | ConsumeMessagesAsync_SingleMessage_InvokesOrchestrator | Single message triggers orchestrator | Mock SQS returning one message -> Call consume -> Assert orchestrator invoked | Verifies single-message flow | Unit - EvaluationQueueConsumer. In scope: single-message processing. Out of scope: real SQS. |
| 143 |  | ConsumeMessagesAsync_MultipleMessages_InvokesOrchestratorPerMessage | Multiple messages trigger orchestrator per message | Mock SQS returning multiple -> Call consume -> Assert orchestrator called per message | Verifies batch processing | Unit - EvaluationQueueConsumer. In scope: batch processing. Out of scope: real SQS. |
| 144 |  | ConsumeMessagesAsync_OrchestratorSucceeds_DeletesMessage | Successful processing deletes message from queue | Mock orchestrator success -> Call consume -> Assert DeleteMessage invoked | Verifies success delete | Unit - EvaluationQueueConsumer. In scope: success delete. Out of scope: real SQS. |
| 145 |  | ConsumeMessagesAsync_OrchestratorThrows_DoesNotDeleteMessage | Orchestrator throwing keeps message in queue | Orchestrator throws -> Call consume -> Assert DeleteMessage not invoked | Verifies error-retain behavior | Unit - EvaluationQueueConsumer. In scope: error handling. Out of scope: real SQS. |
| 146 |  | ConsumeMessagesAsync_OrchestratorThrows_LogsError | Orchestrator throwing logs error | Orchestrator throws -> Call consume -> Assert logger Error invoked | Verifies error logging | Unit - EvaluationQueueConsumer. In scope: logging. Out of scope: real SQS. |
| 147 |  | ConsumeMessagesAsync_InvalidMessageBody_LogsErrorAndDeletes | Invalid body is logged and removed | Mock SQS returning bad body -> Call consume -> Assert logged and deleted | Verifies poison-message handling | Unit - EvaluationQueueConsumer. In scope: poison-message handling. Out of scope: real SQS. |
| 148 |  | ConsumeMessagesAsync_RespectsCancellation | Cancellation stops processing | Cancel token during consume -> Assert processing stops | Verifies cancellation | Unit - EvaluationQueueConsumer. In scope: cancellation. Out of scope: real SQS. |
| 149 |  | ConsumeMessagesAsync_RespectsMaxConcurrency | Concurrent processing bounded by config | Provide many messages; small max-concurrency -> Assert concurrency bounded | Verifies concurrency bound | Unit - EvaluationQueueConsumer. In scope: concurrency. Out of scope: real SQS. |
| 150 |  | ConsumeMessagesAsync_EmitsMetrics | Metric recorder invoked per message | Consume a message -> Assert metric recorder invoked | Verifies metric emission | Unit - EvaluationQueueConsumer. In scope: metrics. Out of scope: real SQS. |
| 151 |  | ConsumeMessagesAsync_SnsEnvelopeMessage_UnwrapsInnerPayload | SNS-wrapped SQS messages are unwrapped | Provide SNS envelope body -> Call consume -> Assert inner payload used | Verifies SNS envelope handling | Unit - EvaluationQueueConsumer. In scope: envelope unwrap. Out of scope: real SQS. |

## tests/LlmEvaluations.UnitTests/Service/Evaluators/BotCoherenceEvaluatorTests.cs

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 152 |  | EvaluateAsync_ValidTranscript_ReturnsSucceeded | Valid transcript returns Succeeded outcome | Mock LLM to return structured score -> Call EvaluateAsync -> Assert Succeeded | Verifies happy path | Unit - BotCoherenceEvaluator. In scope: success path. Out of scope: real LLM. |
| 153 |  | EvaluateAsync_ValidTranscript_PopulatesScore | Score populated from LLM response | Mock LLM with score 4 -> Call EvaluateAsync -> Assert score value | Verifies score mapping | Unit - BotCoherenceEvaluator. In scope: score mapping. Out of scope: real LLM. |
| 154 |  | EvaluateAsync_ValidTranscript_PopulatesNotes | Notes populated from LLM response | Mock LLM with notes -> Call EvaluateAsync -> Assert notes on outcome | Verifies notes mapping | Unit - BotCoherenceEvaluator. In scope: notes mapping. Out of scope: real LLM. |
| 155 |  | EvaluateAsync_LlmThrows_ReturnsFailed | LLM exception yields Failed outcome | Mock LLM to throw -> Call EvaluateAsync -> Assert Failed with error | Verifies failure handling | Unit - BotCoherenceEvaluator. In scope: error path. Out of scope: real LLM. |
| 156 |  | EvaluateAsync_EmptyTranscript_ReturnsFailed | Empty transcript returns Failed without LLM call | Provide empty transcript -> Call EvaluateAsync -> Assert Failed; LLM not invoked | Verifies empty-guard | Unit - BotCoherenceEvaluator. In scope: input guard. Out of scope: real LLM. |
| 157 |  | EvaluateAsync_NullTranscript_ReturnsFailed | Null transcript returns Failed | Provide null -> Call EvaluateAsync -> Assert Failed | Verifies null-guard | Unit - BotCoherenceEvaluator. In scope: input guard. Out of scope: real LLM. |
| 158 |  | EvaluateAsync_LlmReturnsInvalidScore_ReturnsFailed | Invalid score from LLM yields Failed | Mock LLM returning out-of-range score -> Call EvaluateAsync -> Assert Failed | Verifies LLM-output validation | Unit - BotCoherenceEvaluator. In scope: output validation. Out of scope: real LLM. |
| 159 |  | EvaluateAsync_NormalizesScoreToZeroOne | Raw 1-5 score normalized to 0-1 | Mock LLM returning 5 -> Call EvaluateAsync -> Assert stored score normalized | Verifies normalization | Unit - BotCoherenceEvaluator. In scope: normalization. Out of scope: real LLM. |
| 160 |  | EvaluateAsync_PopulatesLlmCost | Cost from LLM response populated | Mock LLM with token counts -> Call EvaluateAsync -> Assert cost fields set | Verifies cost propagation | Unit - BotCoherenceEvaluator. In scope: cost propagation. Out of scope: real LLM. |
| 161 |  | EvaluateAsync_UsesConfiguredModel | Evaluator invokes LLM with configured model | Configure model -> Call EvaluateAsync -> Assert LLM client received configured model | Verifies model config | Unit - BotCoherenceEvaluator. In scope: configuration wiring. Out of scope: real LLM. |
| 162 |  | EvaluateAsync_UsesConfiguredPrompt | Evaluator invokes LLM with configured prompt | Configure prompt -> Call EvaluateAsync -> Assert prompt sent | Verifies prompt wiring | Unit - BotCoherenceEvaluator. In scope: prompt wiring. Out of scope: real LLM. |
| 163 |  | EvaluateAsync_PropagatesCancellation | Cancellation token flows to LLM client | Call with token -> Assert LLM client received same token | Verifies cancellation | Unit - BotCoherenceEvaluator. In scope: cancellation. Out of scope: real LLM. |
| 164 |  | EvaluateAsync_EmitsMetrics | Metrics recorder invoked | Call EvaluateAsync -> Assert metrics recorded | Verifies metric emission | Unit - BotCoherenceEvaluator. In scope: metrics. Out of scope: real LLM. |
| 165 |  | EvaluateAsync_LogsOnFailure | Logs error when LLM fails | Mock LLM throw -> Call EvaluateAsync -> Assert logger Error invoked | Verifies failure logging | Unit - BotCoherenceEvaluator. In scope: logging. Out of scope: real LLM. |
| 166 |  | EvaluateAsync_RespectsTimeout | Evaluator respects configured timeout | Configure short timeout; slow LLM -> Call EvaluateAsync -> Assert timeout-driven Failed | Verifies timeout behavior | Unit - BotCoherenceEvaluator. In scope: timeout. Out of scope: real LLM. |

## tests/LlmEvaluations.UnitTests/Service/Evaluators/ContextRetentionEvaluatorTests.cs

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 167 |  | EvaluateAsync_ValidTranscript_ReturnsSucceeded | Valid transcript returns Succeeded outcome | Mock LLM structured response -> Call EvaluateAsync -> Assert Succeeded | Verifies happy path | Unit - ContextRetentionEvaluator. In scope: success path. Out of scope: real LLM. |
| 168 |  | EvaluateAsync_ValidTranscript_PopulatesScore | Score populated from LLM response | Mock LLM with score -> Call EvaluateAsync -> Assert score | Verifies score mapping | Unit - ContextRetentionEvaluator. In scope: score mapping. Out of scope: real LLM. |
| 169 |  | EvaluateAsync_ValidTranscript_PopulatesNotes | Notes populated on outcome | Mock LLM with notes -> Call EvaluateAsync -> Assert notes | Verifies notes mapping | Unit - ContextRetentionEvaluator. In scope: notes mapping. Out of scope: real LLM. |
| 170 |  | EvaluateAsync_LlmThrows_ReturnsFailed | Exception yields Failed outcome | Mock LLM throw -> Call EvaluateAsync -> Assert Failed | Verifies failure handling | Unit - ContextRetentionEvaluator. In scope: error path. Out of scope: real LLM. |
| 171 |  | EvaluateAsync_EmptyTranscript_ReturnsFailed | Empty transcript returns Failed without LLM call | Provide empty -> Call EvaluateAsync -> Assert Failed | Verifies empty-guard | Unit - ContextRetentionEvaluator. In scope: input guard. Out of scope: real LLM. |
| 172 |  | EvaluateAsync_NullTranscript_ReturnsFailed | Null transcript returns Failed | Provide null -> Call EvaluateAsync -> Assert Failed | Verifies null-guard | Unit - ContextRetentionEvaluator. In scope: input guard. Out of scope: real LLM. |
| 173 |  | EvaluateAsync_LlmReturnsInvalidScore_ReturnsFailed | Invalid score from LLM yields Failed | Mock LLM invalid score -> Call EvaluateAsync -> Assert Failed | Verifies output validation | Unit - ContextRetentionEvaluator. In scope: output validation. Out of scope: real LLM. |
| 174 |  | EvaluateAsync_NormalizesScoreToZeroOne | Score normalized to 0-1 | Mock LLM 1-5 score -> Call EvaluateAsync -> Assert normalized | Verifies normalization | Unit - ContextRetentionEvaluator. In scope: normalization. Out of scope: real LLM. |
| 175 |  | EvaluateAsync_PopulatesLlmCost | Cost populated from LLM response | Mock LLM costs -> Call EvaluateAsync -> Assert cost fields | Verifies cost propagation | Unit - ContextRetentionEvaluator. In scope: cost. Out of scope: real LLM. |
| 176 |  | EvaluateAsync_UsesConfiguredModel | Evaluator uses configured model | Configure model -> Call EvaluateAsync -> Assert model sent | Verifies model wiring | Unit - ContextRetentionEvaluator. In scope: config wiring. Out of scope: real LLM. |
| 177 |  | EvaluateAsync_UsesConfiguredPrompt | Evaluator uses configured prompt | Configure prompt -> Call EvaluateAsync -> Assert prompt sent | Verifies prompt wiring | Unit - ContextRetentionEvaluator. In scope: prompt wiring. Out of scope: real LLM. |
| 178 |  | EvaluateAsync_PropagatesCancellation | Cancellation token flows to LLM | Call with token -> Assert LLM gets same token | Verifies cancellation | Unit - ContextRetentionEvaluator. In scope: cancellation. Out of scope: real LLM. |
| 179 |  | EvaluateAsync_EmitsMetrics | Metrics recorder invoked | Call EvaluateAsync -> Assert metric recorder called | Verifies metric emission | Unit - ContextRetentionEvaluator. In scope: metrics. Out of scope: real LLM. |
| 180 |  | EvaluateAsync_LogsOnFailure | Logs error when LLM fails | Mock LLM throw -> Call EvaluateAsync -> Assert logger Error invoked | Verifies failure logging | Unit - ContextRetentionEvaluator. In scope: logging. Out of scope: real LLM. |
| 181 |  | EvaluateAsync_RespectsTimeout | Evaluator respects configured timeout | Configure short timeout; slow LLM -> Call EvaluateAsync -> Assert Failed due to timeout | Verifies timeout handling | Unit - ContextRetentionEvaluator. In scope: timeout. Out of scope: real LLM. |

## tests/LlmEvaluations.UnitTests/Service/Evaluators/EvaluatorRegistryTests.cs

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 182 |  | Resolve_KnownEvaluator_ReturnsInstance | Registry returns registered evaluator by name | Register evaluators -> Resolve by name -> Assert instance returned | Verifies name-based resolution | Unit - EvaluatorRegistry. In scope: resolution. Out of scope: DI container. |
| 183 |  | Resolve_UnknownEvaluator_Throws | Unknown name throws | Call Resolve with unknown name -> Assert exception | Verifies missing-lookup handling | Unit - EvaluatorRegistry. In scope: error handling. Out of scope: DI container. |

## tests/LlmEvaluations.UnitTests/Service/Evaluators/GoalCompletionEvaluatorTests.cs

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 184 |  | EvaluateAsync_ValidTranscript_ReturnsSucceeded | Valid transcript returns Succeeded | Mock LLM response -> Call EvaluateAsync -> Assert Succeeded | Verifies happy path | Unit - GoalCompletionEvaluator. In scope: success path. Out of scope: real LLM. |
| 185 |  | EvaluateAsync_ValidTranscript_PopulatesScore | Score populated from response | Mock LLM score -> Call EvaluateAsync -> Assert score | Verifies score mapping | Unit - GoalCompletionEvaluator. In scope: score mapping. Out of scope: real LLM. |
| 186 |  | EvaluateAsync_ValidTranscript_PopulatesNotes | Notes populated | Mock LLM notes -> Call EvaluateAsync -> Assert notes | Verifies notes mapping | Unit - GoalCompletionEvaluator. In scope: notes mapping. Out of scope: real LLM. |
| 187 |  | EvaluateAsync_LlmThrows_ReturnsFailed | Exception yields Failed | Mock throw -> Call EvaluateAsync -> Assert Failed | Verifies failure handling | Unit - GoalCompletionEvaluator. In scope: error path. Out of scope: real LLM. |
| 188 |  | EvaluateAsync_EmptyTranscript_ReturnsFailed | Empty input returns Failed | Empty transcript -> Call EvaluateAsync -> Assert Failed | Verifies empty-guard | Unit - GoalCompletionEvaluator. In scope: input guard. Out of scope: real LLM. |
| 189 |  | EvaluateAsync_NullTranscript_ReturnsFailed | Null input returns Failed | Null transcript -> Call EvaluateAsync -> Assert Failed | Verifies null-guard | Unit - GoalCompletionEvaluator. In scope: input guard. Out of scope: real LLM. |
| 190 |  | EvaluateAsync_LlmReturnsInvalidScore_ReturnsFailed | Invalid score yields Failed | Mock invalid score -> Call EvaluateAsync -> Assert Failed | Verifies output validation | Unit - GoalCompletionEvaluator. In scope: output validation. Out of scope: real LLM. |
| 191 |  | EvaluateAsync_NormalizesScoreToZeroOne | Score normalized | Mock score 3 -> Call EvaluateAsync -> Assert ~0.5 | Verifies normalization | Unit - GoalCompletionEvaluator. In scope: normalization. Out of scope: real LLM. |
| 192 |  | EvaluateAsync_PopulatesLlmCost | Cost populated | Mock tokens -> Call EvaluateAsync -> Assert cost set | Verifies cost propagation | Unit - GoalCompletionEvaluator. In scope: cost. Out of scope: real LLM. |
| 193 |  | EvaluateAsync_UsesConfiguredModel | Uses configured model | Configure model -> Call EvaluateAsync -> Assert sent | Verifies model wiring | Unit - GoalCompletionEvaluator. In scope: config wiring. Out of scope: real LLM. |
| 194 |  | EvaluateAsync_UsesConfiguredPrompt | Uses configured prompt | Configure prompt -> Call EvaluateAsync -> Assert sent | Verifies prompt wiring | Unit - GoalCompletionEvaluator. In scope: prompt wiring. Out of scope: real LLM. |
| 195 |  | EvaluateAsync_PropagatesCancellation | Cancellation token flows to LLM | Call with token -> Assert LLM gets same | Verifies cancellation | Unit - GoalCompletionEvaluator. In scope: cancellation. Out of scope: real LLM. |
| 196 |  | EvaluateAsync_EmitsMetrics | Metrics recorder invoked | Call EvaluateAsync -> Assert metric called | Verifies metric emission | Unit - GoalCompletionEvaluator. In scope: metrics. Out of scope: real LLM. |
| 197 |  | EvaluateAsync_LogsOnFailure | Logs error on failure | Mock throw -> Call EvaluateAsync -> Assert Error logged | Verifies failure logging | Unit - GoalCompletionEvaluator. In scope: logging. Out of scope: real LLM. |
| 198 |  | EvaluateAsync_RespectsTimeout | Respects timeout | Short timeout -> Call EvaluateAsync -> Assert Failed on timeout | Verifies timeout handling | Unit - GoalCompletionEvaluator. In scope: timeout. Out of scope: real LLM. |
| 199 |  | EvaluateAsync_WithPartialCompletion_ReturnsMidScore | Partial completion maps to mid score | Mock LLM mid-score -> Call EvaluateAsync -> Assert mid value returned | Verifies partial-handling | Unit - GoalCompletionEvaluator. In scope: mid-range scoring. Out of scope: real LLM. |

## tests/LlmEvaluations.UnitTests/Service/Evaluators/GoalIdentificationEvaluatorTests.cs

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 200 |  | EvaluateAsync_ValidTranscript_ReturnsSucceeded | Valid transcript returns Succeeded | Mock LLM response -> Call EvaluateAsync -> Assert Succeeded | Verifies happy path | Unit - GoalIdentificationEvaluator. In scope: success path. Out of scope: real LLM. |
| 201 |  | EvaluateAsync_ValidTranscript_PopulatesScore | Score populated | Mock LLM score -> Call EvaluateAsync -> Assert score | Verifies score mapping | Unit - GoalIdentificationEvaluator. In scope: score mapping. Out of scope: real LLM. |
| 202 |  | EvaluateAsync_ValidTranscript_PopulatesNotes | Notes populated | Mock LLM notes -> Call EvaluateAsync -> Assert notes | Verifies notes mapping | Unit - GoalIdentificationEvaluator. In scope: notes mapping. Out of scope: real LLM. |
| 203 |  | EvaluateAsync_LlmThrows_ReturnsFailed | Exception yields Failed | Mock throw -> Call EvaluateAsync -> Assert Failed | Verifies failure handling | Unit - GoalIdentificationEvaluator. In scope: error path. Out of scope: real LLM. |
| 204 |  | EvaluateAsync_EmptyTranscript_ReturnsFailed | Empty input returns Failed | Empty transcript -> Call EvaluateAsync -> Assert Failed | Verifies empty-guard | Unit - GoalIdentificationEvaluator. In scope: input guard. Out of scope: real LLM. |
| 205 |  | EvaluateAsync_NullTranscript_ReturnsFailed | Null input returns Failed | Null transcript -> Call EvaluateAsync -> Assert Failed | Verifies null-guard | Unit - GoalIdentificationEvaluator. In scope: input guard. Out of scope: real LLM. |
| 206 |  | EvaluateAsync_LlmReturnsInvalidScore_ReturnsFailed | Invalid score yields Failed | Mock invalid score -> Call EvaluateAsync -> Assert Failed | Verifies output validation | Unit - GoalIdentificationEvaluator. In scope: output validation. Out of scope: real LLM. |
| 207 |  | EvaluateAsync_NormalizesScoreToZeroOne | Score normalized | Mock score 5 -> Call EvaluateAsync -> Assert 1.0 | Verifies normalization | Unit - GoalIdentificationEvaluator. In scope: normalization. Out of scope: real LLM. |
| 208 |  | EvaluateAsync_PopulatesLlmCost | Cost populated | Mock tokens -> Call EvaluateAsync -> Assert cost set | Verifies cost propagation | Unit - GoalIdentificationEvaluator. In scope: cost. Out of scope: real LLM. |
| 209 |  | EvaluateAsync_UsesConfiguredModel | Uses configured model | Configure model -> Call EvaluateAsync -> Assert sent | Verifies model wiring | Unit - GoalIdentificationEvaluator. In scope: config wiring. Out of scope: real LLM. |
| 210 |  | EvaluateAsync_UsesConfiguredPrompt | Uses configured prompt | Configure prompt -> Call EvaluateAsync -> Assert sent | Verifies prompt wiring | Unit - GoalIdentificationEvaluator. In scope: prompt wiring. Out of scope: real LLM. |
| 211 |  | EvaluateAsync_PropagatesCancellation | Cancellation token flows to LLM | Call with token -> Assert same on LLM | Verifies cancellation | Unit - GoalIdentificationEvaluator. In scope: cancellation. Out of scope: real LLM. |
| 212 |  | EvaluateAsync_EmitsMetrics | Metrics recorder invoked | Call EvaluateAsync -> Assert metric called | Verifies metric emission | Unit - GoalIdentificationEvaluator. In scope: metrics. Out of scope: real LLM. |
| 213 |  | EvaluateAsync_LogsOnFailure | Logs error on failure | Mock throw -> Call EvaluateAsync -> Assert Error logged | Verifies failure logging | Unit - GoalIdentificationEvaluator. In scope: logging. Out of scope: real LLM. |
| 214 |  | EvaluateAsync_RespectsTimeout | Respects timeout | Short timeout -> Call EvaluateAsync -> Assert Failed on timeout | Verifies timeout handling | Unit - GoalIdentificationEvaluator. In scope: timeout. Out of scope: real LLM. |

## tests/LlmEvaluations.UnitTests/Service/Evaluators/InferenceAccuracyEvaluatorTests.cs

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 215 |  | EvaluateAsync_AllInferencesCorrect_ReturnsPerfectScore | All correct inferences yield perfect score | Provide all-correct inferences -> Call EvaluateAsync -> Assert score 1.0 | Verifies perfect-accuracy scoring | Unit - InferenceAccuracyEvaluator. In scope: perfect case. Out of scope: real LLM. |
| 216 |  | EvaluateAsync_AllInferencesIncorrect_ReturnsZeroScore | All incorrect inferences yield zero | Provide all-wrong inferences -> Call EvaluateAsync -> Assert score 0.0 | Verifies zero-accuracy scoring | Unit - InferenceAccuracyEvaluator. In scope: worst case. Out of scope: real LLM. |
| 217 |  | EvaluateAsync_PartialCorrect_ReturnsProportionalScore | Partial correct yields proportional score | Provide half-correct -> Call EvaluateAsync -> Assert ~0.5 | Verifies proportional scoring | Unit - InferenceAccuracyEvaluator. In scope: proportional scoring. Out of scope: real LLM. |
| 218 |  | EvaluateAsync_NoExpectedInferences_ReturnsFailed | Missing expected returns Failed | Provide empty expected -> Call EvaluateAsync -> Assert Failed | Verifies input-guard | Unit - InferenceAccuracyEvaluator. In scope: input guard. Out of scope: real LLM. |
| 219 |  | EvaluateAsync_NoActualInferences_ReturnsFailed | Missing actual returns Failed | Provide empty actual -> Call EvaluateAsync -> Assert Failed | Verifies input-guard | Unit - InferenceAccuracyEvaluator. In scope: input guard. Out of scope: real LLM. |
| 220 |  | EvaluateAsync_MismatchedFieldCount_ReturnsFailed | Mismatched count yields Failed | Provide differing counts -> Call EvaluateAsync -> Assert Failed | Verifies count validation | Unit - InferenceAccuracyEvaluator. In scope: validation. Out of scope: real LLM. |
| 221 |  | EvaluateAsync_CaseInsensitiveMatching | String comparison is case-insensitive | Provide mismatched-case equal values -> Call EvaluateAsync -> Assert correct | Verifies case handling | Unit - InferenceAccuracyEvaluator. In scope: string comparison. Out of scope: real LLM. |
| 222 |  | EvaluateAsync_WhitespaceTrimmed | Leading/trailing whitespace ignored | Provide values with whitespace -> Call EvaluateAsync -> Assert matches | Verifies trim behavior | Unit - InferenceAccuracyEvaluator. In scope: string comparison. Out of scope: real LLM. |
| 223 |  | EvaluateAsync_NullFields_HandledGracefully | Null fields handled without throw | Provide nulls -> Call EvaluateAsync -> Assert no throw; Failed or handled | Verifies null handling | Unit - InferenceAccuracyEvaluator. In scope: null handling. Out of scope: real LLM. |
| 224 |  | EvaluateAsync_PopulatesNotes | Notes populated with field-level details | Provide inferences -> Call EvaluateAsync -> Assert notes include fields | Verifies notes content | Unit - InferenceAccuracyEvaluator. In scope: notes mapping. Out of scope: real LLM. |
| 225 |  | EvaluateAsync_PropagatesCancellation | Cancellation token respected | Call with token -> Assert cancellation observed | Verifies cancellation | Unit - InferenceAccuracyEvaluator. In scope: cancellation. Out of scope: real LLM. |
| 226 |  | EvaluateAsync_EmitsMetrics | Metrics recorder invoked | Call EvaluateAsync -> Assert metric called | Verifies metric emission | Unit - InferenceAccuracyEvaluator. In scope: metrics. Out of scope: real LLM. |
| 227 |  | EvaluateAsync_LogsOnFailure | Logs on error path | Trigger error -> Call EvaluateAsync -> Assert Error logged | Verifies logging | Unit - InferenceAccuracyEvaluator. In scope: logging. Out of scope: real LLM. |

## tests/LlmEvaluations.UnitTests/Service/Evaluators/TurnTakingAudioEvaluatorTests.cs

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 228 |  | EvaluateAsync_ValidAudio_ReturnsSucceeded | Valid audio returns Succeeded | Mock LLM audio response -> Call EvaluateAsync -> Assert Succeeded | Verifies happy path | Unit - TurnTakingAudioEvaluator. In scope: success path. Out of scope: real LLM/audio. |
| 229 |  | EvaluateAsync_ValidAudio_PopulatesScore | Score populated | Mock LLM score -> Call EvaluateAsync -> Assert score | Verifies score mapping | Unit - TurnTakingAudioEvaluator. In scope: score mapping. Out of scope: real LLM/audio. |
| 230 |  | EvaluateAsync_ValidAudio_PopulatesNotes | Notes populated | Mock LLM notes -> Call EvaluateAsync -> Assert notes | Verifies notes mapping | Unit - TurnTakingAudioEvaluator. In scope: notes mapping. Out of scope: real LLM/audio. |
| 231 |  | EvaluateAsync_LlmThrows_ReturnsFailed | Exception yields Failed | Mock throw -> Call EvaluateAsync -> Assert Failed | Verifies failure handling | Unit - TurnTakingAudioEvaluator. In scope: error path. Out of scope: real LLM/audio. |
| 232 |  | EvaluateAsync_NullAudio_ReturnsFailed | Null audio returns Failed | Provide null -> Call EvaluateAsync -> Assert Failed | Verifies null-guard | Unit - TurnTakingAudioEvaluator. In scope: null handling. Out of scope: real LLM/audio. |
| 233 |  | EvaluateAsync_EmptyAudio_ReturnsFailed | Empty audio returns Failed | Provide empty bytes -> Call EvaluateAsync -> Assert Failed | Verifies empty-guard | Unit - TurnTakingAudioEvaluator. In scope: empty-input guard. Out of scope: real LLM/audio. |
| 234 |  | EvaluateAsync_AudioFetchFails_ReturnsFailed | Audio fetch failure yields Failed | Mock fetcher throws -> Call EvaluateAsync -> Assert Failed | Verifies fetch error handling | Unit - TurnTakingAudioEvaluator. In scope: fetch error. Out of scope: real LLM/audio. |
| 235 |  | EvaluateAsync_LlmReturnsInvalidScore_ReturnsFailed | Invalid score yields Failed | Mock invalid score -> Call EvaluateAsync -> Assert Failed | Verifies output validation | Unit - TurnTakingAudioEvaluator. In scope: output validation. Out of scope: real LLM/audio. |
| 236 |  | EvaluateAsync_NormalizesScoreToZeroOne | Score normalized | Mock score 4 -> Call EvaluateAsync -> Assert normalized | Verifies normalization | Unit - TurnTakingAudioEvaluator. In scope: normalization. Out of scope: real LLM/audio. |
| 237 |  | EvaluateAsync_PopulatesLlmCost | Cost populated | Mock tokens -> Call EvaluateAsync -> Assert cost set | Verifies cost propagation | Unit - TurnTakingAudioEvaluator. In scope: cost. Out of scope: real LLM/audio. |
| 238 |  | EvaluateAsync_UsesConfiguredModel | Uses configured model | Configure model -> Call EvaluateAsync -> Assert sent | Verifies model wiring | Unit - TurnTakingAudioEvaluator. In scope: config wiring. Out of scope: real LLM/audio. |
| 239 |  | EvaluateAsync_UsesConfiguredPrompt | Uses configured prompt | Configure prompt -> Call EvaluateAsync -> Assert sent | Verifies prompt wiring | Unit - TurnTakingAudioEvaluator. In scope: prompt wiring. Out of scope: real LLM/audio. |
| 240 |  | EvaluateAsync_PropagatesCancellation | Cancellation flows to LLM | Call with token -> Assert LLM gets same | Verifies cancellation | Unit - TurnTakingAudioEvaluator. In scope: cancellation. Out of scope: real LLM/audio. |
| 241 |  | EvaluateAsync_EmitsMetrics | Metrics recorder invoked | Call EvaluateAsync -> Assert metric called | Verifies metric emission | Unit - TurnTakingAudioEvaluator. In scope: metrics. Out of scope: real LLM/audio. |
| 242 |  | EvaluateAsync_LogsOnFailure | Logs on error path | Mock throw -> Call EvaluateAsync -> Assert Error logged | Verifies failure logging | Unit - TurnTakingAudioEvaluator. In scope: logging. Out of scope: real LLM/audio. |

## tests/LlmEvaluations.UnitTests/Service/FakeAudioFetcherTests.cs

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 243 |  | FetchAsync_ReturnsDeterministicBytes | FakeAudioFetcher returns deterministic audio bytes | Call FetchAsync -> Assert returns non-empty bytes | Verifies deterministic test double | Unit - FakeAudioFetcher. In scope: fake behavior. Out of scope: real audio source. |
| 244 |  | FetchAsync_RespectsCancellation | Cancellation token respected | Call FetchAsync with cancelled token -> Assert OperationCanceledException | Verifies cancellation | Unit - FakeAudioFetcher. In scope: cancellation. Out of scope: real audio source. |

## tests/LlmEvaluations.UnitTests/Service/LlmClient/StructuredLlmResponseTests.cs

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 245 |  | Parse_ValidJson_ReturnsTypedResponse | Parses valid JSON into typed response | Provide JSON -> Call Parse -> Assert typed object returned | Verifies happy-path parse | Unit - StructuredLlmResponse. In scope: JSON parsing. Out of scope: LLM. |
| 246 |  | Parse_InvalidJson_Throws | Invalid JSON throws | Provide garbage -> Call Parse -> Assert exception | Verifies error handling | Unit - StructuredLlmResponse. In scope: error handling. Out of scope: LLM. |
| 247 |  | Parse_MissingRequiredField_Throws | Missing required field throws | Provide JSON without required field -> Call Parse -> Assert exception | Verifies schema validation | Unit - StructuredLlmResponse. In scope: schema validation. Out of scope: LLM. |

## tests/LlmEvaluations.UnitTests/Service/LlmClient/VertexAiLlmClientTests.cs

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 248 |  | GenerateContentAsync_ReturnsResponseWithText | Vertex LLM client returns response with text | Mock HTTP to return Vertex JSON -> Call GenerateContentAsync -> Assert text returned | Verifies happy-path generation | Unit - VertexAiLlmClient. In scope: response parsing. Out of scope: real Vertex AI. |
| 249 |  | GenerateContentAsync_PopulatesUsage | Token usage is populated from response | Mock HTTP with usage metadata -> Call GenerateContentAsync -> Assert usage populated | Verifies usage mapping | Unit - VertexAiLlmClient. In scope: usage mapping. Out of scope: real Vertex AI. |
| 250 |  | GenerateContentAsync_HttpError_Throws | HTTP error surfaces as exception | Mock HTTP 500 -> Call GenerateContentAsync -> Assert exception | Verifies error handling | Unit - VertexAiLlmClient. In scope: error handling. Out of scope: real Vertex AI. |

## tests/LlmEvaluations.UnitTests/Service/PhoneBotAudioFetcherTests.cs

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 251 |  | FetchAsync_ValidCallId_ReturnsAudioBytes | Valid call id returns audio bytes | Mock PhoneBot HTTP to return audio -> Call FetchAsync -> Assert bytes returned | Verifies happy path | Unit - PhoneBotAudioFetcher. In scope: HTTP fetch. Out of scope: real PhoneBot. |
| 252 |  | FetchAsync_NotFound_Throws | Not-found response throws | Mock HTTP 404 -> Call FetchAsync -> Assert exception | Verifies 404 handling | Unit - PhoneBotAudioFetcher. In scope: 404 handling. Out of scope: real PhoneBot. |
| 253 |  | FetchAsync_ServerError_Throws | Server error throws | Mock HTTP 500 -> Call FetchAsync -> Assert exception | Verifies 5xx handling | Unit - PhoneBotAudioFetcher. In scope: 5xx handling. Out of scope: real PhoneBot. |
| 254 |  | FetchAsync_PropagatesCancellation | Cancellation propagates to HTTP client | Call FetchAsync with token -> Assert HTTP call receives same token | Verifies cancellation | Unit - PhoneBotAudioFetcher. In scope: cancellation. Out of scope: real PhoneBot. |
| 255 |  | FetchAsync_UsesConfiguredBaseUrl | Client uses configured base URL | Configure base URL -> Call FetchAsync -> Assert correct URL called | Verifies URL construction | Unit - PhoneBotAudioFetcher. In scope: URL construction. Out of scope: real PhoneBot. |

## tests/LlmEvaluations.UnitTests/Service/PhoneBotTranscriptFetcherTests.cs

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 256 |  | FetchAsync_ValidCallId_ReturnsTranscript | Valid call id returns transcript object | Mock HTTP to return transcript JSON -> Call FetchAsync -> Assert transcript parsed | Verifies happy path | Unit - PhoneBotTranscriptFetcher. In scope: HTTP fetch/parse. Out of scope: real PhoneBot. |
| 257 |  | FetchAsync_NotFound_ReturnsNull | Not-found returns null transcript | Mock HTTP 404 -> Call FetchAsync -> Assert null returned | Verifies 404 handling | Unit - PhoneBotTranscriptFetcher. In scope: 404 handling. Out of scope: real PhoneBot. |
| 258 |  | FetchAsync_ServerError_Throws | Server error throws | Mock HTTP 500 -> Call FetchAsync -> Assert exception | Verifies 5xx handling | Unit - PhoneBotTranscriptFetcher. In scope: 5xx handling. Out of scope: real PhoneBot. |
| 259 |  | FetchAsync_InvalidJson_Throws | Invalid JSON throws | Mock HTTP returning garbage body -> Call FetchAsync -> Assert exception | Verifies parse-error handling | Unit - PhoneBotTranscriptFetcher. In scope: JSON validation. Out of scope: real PhoneBot. |
| 260 |  | FetchAsync_PropagatesCancellation | Cancellation propagates to HTTP client | Call FetchAsync with token -> Assert HTTP receives same | Verifies cancellation | Unit - PhoneBotTranscriptFetcher. In scope: cancellation. Out of scope: real PhoneBot. |
| 261 |  | FetchAsync_UsesConfiguredBaseUrl | Uses configured base URL | Configure base URL -> Call FetchAsync -> Assert correct URL called | Verifies URL construction | Unit - PhoneBotTranscriptFetcher. In scope: URL construction. Out of scope: real PhoneBot. |
| 262 |  | FetchAsync_EmptyBody_ReturnsNull | Empty response body returns null | Mock HTTP empty -> Call FetchAsync -> Assert null | Verifies empty-body handling | Unit - PhoneBotTranscriptFetcher. In scope: empty body. Out of scope: real PhoneBot. |
| 263 |  | FetchAsync_LogsOnError | Logs on HTTP error | Mock HTTP error -> Call FetchAsync -> Assert Error logged | Verifies error logging | Unit - PhoneBotTranscriptFetcher. In scope: logging. Out of scope: real PhoneBot. |

## tests/LlmEvaluations.UnitTests/Service/TranscriptFetcherTests.cs

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 264 |  | FetchAsync_ReturnsFixedTranscript | Fake transcript fetcher returns fixed transcript | Call FetchAsync -> Assert fixed sample returned | Verifies test double | Unit - FakeTranscriptFetcher. In scope: deterministic output. Out of scope: real source. |
| 265 |  | FetchAsync_RespectsCancellation | Cancellation token respected | Call FetchAsync with cancelled token -> Assert OperationCanceledException | Verifies cancellation | Unit - FakeTranscriptFetcher. In scope: cancellation. Out of scope: real source. |
| 266 |  | FetchAsync_UnknownCallId_ReturnsNull | Unknown call id returns null | Call FetchAsync with unknown id -> Assert null returned | Verifies null-on-miss | Unit - FakeTranscriptFetcher. In scope: miss handling. Out of scope: real source. |
| 267 |  | FetchAsync_Deterministic_ForSameCallId | Same id returns identical transcript across calls | Call twice with same id -> Assert identical results | Verifies determinism | Unit - FakeTranscriptFetcher. In scope: determinism. Out of scope: real source. |

## tests/LlmEvaluations.UnitTests/Web/EvaluationsImplTests.cs

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 268 |  | EnqueueEvaluation_ForceEvaluateTrue_BypassesSampling | Force-evaluate flag bypasses sampling decision | Build body with force_evaluate=true; sampling_rate=0 -> Call EnqueueEvaluation -> Assert Evaluated=true and persistence + SNS invoked | Verifies force-evaluate override | Unit - EvaluationsImpl (web). In scope: sampling bypass; repository + SNS calls. Out of scope: HTTP pipeline. |
| 269 |  | EnqueueEvaluation_SamplingRateZero_AlwaysSkips | Zero sampling rate always skips | Body with sampling_rate=0 -> Call EnqueueEvaluation -> Assert Evaluated=false; status Skipped; no DB/SNS | Verifies always-skip behavior | Unit - EvaluationsImpl (web). In scope: sampling skip. Out of scope: HTTP pipeline. |
| 270 |  | EnqueueEvaluation_SamplingRateOne_AlwaysEvaluates | 100% sampling always evaluates | Body with sampling_rate=1 -> Call EnqueueEvaluation -> Assert Evaluated=true; status Enqueued; DB+SNS invoked | Verifies always-evaluate behavior | Unit - EvaluationsImpl (web). In scope: sampling pass. Out of scope: HTTP pipeline. |
| 271 |  | EnqueueEvaluation_NoSamplingRateProvided_UsesDefaultRate | Default rate used when sampling_rate omitted | Configure default 0.5 -> Body without sampling_rate -> Call -> Assert sampling_rate_used=0.5 | Verifies default-rate use | Unit - EvaluationsImpl (web). In scope: default-rate config. Out of scope: HTTP pipeline. |
| 272 |  | EnqueueEvaluation_SamplingRateZeroPointFive_EvaluatesRoughlyHalfTheTime | 50% sampling evaluates approximately half the time | Loop 100 iterations at sampling_rate=0.5 -> Assert counts in 30-70 range each | Verifies probabilistic behavior | Unit - EvaluationsImpl (web). In scope: statistical sampling. Out of scope: HTTP pipeline. |
| 273 |  | EnqueueEvaluation_SkippedRequest_DoesNotWriteToDatabase | Skipped request never writes to DB | Body sampling_rate=0 -> Call -> Verify repository SaveAsync never called | Verifies DB short-circuit on skip | Unit - EvaluationsImpl (web). In scope: skip semantics. Out of scope: HTTP pipeline. |
| 274 |  | EnqueueEvaluation_SkippedRequest_DoesNotPublishToSns | Skipped request never publishes to SNS | Body sampling_rate=0 -> Call -> Verify queue publisher PublishAsync never called | Verifies SNS short-circuit on skip | Unit - EvaluationsImpl (web). In scope: skip semantics. Out of scope: HTTP pipeline. |
| 275 |  | EnqueueEvaluation_ResponseIncludesSamplingDecision | Response body includes sampling fields | Body sampling_rate=1.0 -> Call -> Assert SamplingApplied=true; SamplingRateUsed=1.0; Evaluated=true | Verifies response contract | Unit - EvaluationsImpl (web). In scope: response fields. Out of scope: HTTP pipeline. |
| 276 |  | EnqueueEvaluation_MissingClientName_ReturnsBadRequest | Missing client_name returns BadRequest | Body without ClientName -> Call -> Assert BadRequest with client_name required message | Verifies input validation | Unit - EvaluationsImpl (web). In scope: validation. Out of scope: HTTP pipeline. |
| 277 |  | EnqueueEvaluation_PhiWithoutRoles_ReturnsBadRequest | PHI request without roles returns BadRequest | Body IsPhi=true; RolesWithAccess=null -> Call -> Assert BadRequest with PHI roles message | Verifies PHI roles guard | Unit - EvaluationsImpl (web). In scope: PHI validation. Out of scope: HTTP pipeline. |
| 278 |  | EnqueueEvaluation_PhiWithEmptyRoles_ReturnsBadRequest | PHI request with empty roles returns BadRequest | Body IsPhi=true; RolesWithAccess=[] -> Call -> Assert BadRequest with PHI roles message | Verifies PHI empty-roles guard | Unit - EvaluationsImpl (web). In scope: PHI validation. Out of scope: HTTP pipeline. |
| 279 |  | EnqueueEvaluation_RecordsSamplingRateMetric | Records sampling rate to metrics with caller tag | Body sampling_rate=0.75 -> Call -> Verify MetricRecorder.Histogram("sampling_rate",0.75,…,caller_service tag) | Verifies metric emission | Unit - EvaluationsImpl (web). In scope: metric emission. Out of scope: HTTP pipeline. |
| 280 |  | EnqueueEvaluation_NoSamplingRateProvided_RecordsDefaultSamplingRate | Default sampling rate recorded to metrics when omitted | Default rate=0.5 -> Body without sampling_rate -> Call -> Verify Histogram(sampling_rate,0.5,…) | Verifies default-rate metric | Unit - EvaluationsImpl (web). In scope: metric emission. Out of scope: HTTP pipeline. |

## tests/LlmEvaluations.UnitTests/Worker/AuditLoggerForWorkerTests.cs

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 281 |  | LogEvaluationRequestAccessInWorker_LogsWithReadAction | Read access logs audit with Read action | Call with Read -> Assert underlying audit logger invoked with Read | Verifies Read logging | Unit - AuditLoggerForWorker. In scope: Read logging. Out of scope: transport. |
| 282 |  | LogEvaluationRequestAccessInWorker_LogsWithUpdateAction | Update access logs audit with Update action | Call with Update -> Assert underlying audit logger invoked with Update | Verifies Update logging | Unit - AuditLoggerForWorker. In scope: Update logging. Out of scope: transport. |
| 283 |  | LogEvaluationRequestAccessInWorker_LogsWithAuthorizedTrue | Authorized access logs isAuthorized=true | Call with isAuthorized=true -> Assert logged | Verifies authorized flag | Unit - AuditLoggerForWorker. In scope: authorization flag. Out of scope: transport. |
| 284 |  | LogEvaluationRequestAccessInWorker_LogsWithAuthorizedFalse | Unauthorized logs isAuthorized=false | Call with isAuthorized=false -> Assert logged | Verifies unauthorized flag | Unit - AuditLoggerForWorker. In scope: authorization flag. Out of scope: transport. |
| 285 |  | LogEvaluationRequestAccessInWorker_PropagatesObjectId | Object id propagates into log | Call -> Assert log contains object id | Verifies object-id propagation | Unit - AuditLoggerForWorker. In scope: field propagation. Out of scope: transport. |
| 286 |  | LogEvaluationRequestAccessInWorker_PropagatesPracticeId | Practice id propagates into log | Call -> Assert log contains practice id | Verifies practice-id propagation | Unit - AuditLoggerForWorker. In scope: field propagation. Out of scope: transport. |
| 287 |  | LogEvaluationRequestAccessInWorker_PropagatesObjectNamespace | Object namespace propagates | Call -> Assert log contains object namespace | Verifies namespace propagation | Unit - AuditLoggerForWorker. In scope: field propagation. Out of scope: transport. |
| 288 |  | LogEvaluationRequestAccessInWorker_NullPracticeId_StillLogs | Null practice id still logs | Call with null practice id -> Assert logged | Verifies null handling | Unit - AuditLoggerForWorker. In scope: null handling. Out of scope: transport. |
| 289 |  | LogEvaluationRequestAccessInWorker_NullObjectNamespace_StillLogs | Null object namespace still logs | Call with null namespace -> Assert logged | Verifies null handling | Unit - AuditLoggerForWorker. In scope: null handling. Out of scope: transport. |
| 290 |  | LogEvaluationRequestAccessInWorker_UsesWorkerIdentity | Uses worker identity as user in log | Call -> Assert worker-identity user recorded | Verifies worker identity | Unit - AuditLoggerForWorker. In scope: identity source. Out of scope: transport. |
| 291 |  | LogEvaluationRequestAccessInWorker_DoesNotThrowOnError | Underlying logger throwing does not propagate | Mock underlying logger throws -> Call -> Assert no throw surfaced | Verifies error-isolation | Unit - AuditLoggerForWorker. In scope: error isolation. Out of scope: transport. |

## tests/LlmEvaluations.UnitTests/Worker/EvaluationRepositoryForWorkerTests.cs

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 292 |  | GetByIdAsync_WhenRecordNotFound_ReturnsNull | Not-found record returns null | Persistence returns null -> Call GetByIdAsync -> Assert null | Verifies not-found path | Unit - EvaluationRepositoryForWorker. In scope: null return. Out of scope: real Dynamo. |
| 293 |  | GetByIdAsync_WhenRecordNotFound_DoesNotLogToAuditLogger | Not-found read does not audit-log | Persistence returns null -> Call GetByIdAsync -> Verify audit logger not called | Verifies no audit on miss | Unit - EvaluationRepositoryForWorker. In scope: audit branching. Out of scope: real Dynamo. |
| 294 |  | GetByIdAsync_WhenRecordIsNotPhi_ReturnsRecordWithoutAuditLogging | Non-PHI read returns record without audit log | Persistence returns non-PHI DTO -> Call GetByIdAsync -> Assert DTO returned; audit not called | Verifies non-PHI bypass | Unit - EvaluationRepositoryForWorker. In scope: audit branching. Out of scope: real Dynamo. |
| 295 |  | GetByIdAsync_WhenRecordIsPhi_ReturnsRecordAndLogsToAuditLogger | PHI read returns record and logs audit Read | Persistence returns PHI DTO -> Call GetByIdAsync -> Assert DTO returned and audit logger called with Read authorized=true | Verifies PHI audit logging | Unit - EvaluationRepositoryForWorker. In scope: PHI audit. Out of scope: real Dynamo. |
| 296 |  | GetByIdAsync_WhenRecordIsPhiWithNoRoles_StillReturnsRecord | PHI record with empty roles still returned | PHI DTO with empty roles -> Call GetByIdAsync -> Assert record returned | Verifies returns-even-without-roles in worker | Unit - EvaluationRepositoryForWorker. In scope: worker read semantics. Out of scope: real Dynamo. |
| 297 |  | UpdateStatusAsync_UpdatesStatus | Update status calls persistence with new status and timestamp | Call UpdateStatusAsync -> Verify persistence.UpdateStatusAsync called with args | Verifies persistence delegation | Unit - EvaluationRepositoryForWorker. In scope: update call. Out of scope: real Dynamo. |
| 298 |  | UpdateStatusAsync_WhenRecordIsNotPhi_DoesNotLogToAuditLogger | Non-PHI update does not audit-log | Non-PHI record -> UpdateStatusAsync -> Verify audit logger not called | Verifies audit bypass | Unit - EvaluationRepositoryForWorker. In scope: audit branching. Out of scope: real Dynamo. |
| 299 |  | UpdateStatusAsync_WhenRecordIsPhi_LogsToAuditLogger | PHI update logs audit Update | PHI record -> UpdateStatusAsync -> Verify audit logger called with Update authorized=true | Verifies PHI update audit | Unit - EvaluationRepositoryForWorker. In scope: PHI audit. Out of scope: real Dynamo. |
| 300 |  | UpdateStatusAsync_WhenRecordNotFound_StillCallsPersistenceService | Update still calls persistence even when record absent | Persistence returns null -> UpdateStatusAsync -> Verify UpdateStatusAsync called with args | Verifies delegation without read | Unit - EvaluationRepositoryForWorker. In scope: delegation. Out of scope: real Dynamo. |
| 301 |  | SaveResultAsync_SavesResult | SaveResultAsync delegates to persistence with expected fields | Call SaveResultAsync -> Verify persistence.SaveResultAsync called with matching EvaluationResult | Verifies persistence delegation | Unit - EvaluationRepositoryForWorker. In scope: save delegation. Out of scope: real Dynamo. |
| 302 |  | SaveResultAsync_WhenRecordIsNotPhi_DoesNotLogToAuditLogger | Non-PHI save does not audit-log | Non-PHI record -> SaveResultAsync -> Verify audit logger not called | Verifies audit bypass | Unit - EvaluationRepositoryForWorker. In scope: audit branching. Out of scope: real Dynamo. |
| 303 |  | SaveResultAsync_WhenRecordIsPhi_LogsToAuditLogger | PHI save logs audit Update | PHI record -> SaveResultAsync -> Verify audit logger called with Update authorized=true | Verifies PHI save audit | Unit - EvaluationRepositoryForWorker. In scope: PHI audit. Out of scope: real Dynamo. |
| 304 |  | SaveResultAsync_WhenRecordNotFound_StillCallsPersistenceService | Save still calls persistence when record absent | Persistence returns null -> SaveResultAsync -> Verify persistence.SaveResultAsync called | Verifies delegation without read | Unit - EvaluationRepositoryForWorker. In scope: delegation. Out of scope: real Dynamo. |
| 305 |  | SaveResultAsync_WithErrorMessage_PassesErrorMessage | Error message flows through to persistence | Call SaveResultAsync with error -> Verify persistence call contains error | Verifies error propagation | Unit - EvaluationRepositoryForWorker. In scope: error propagation. Out of scope: real Dynamo. |

## tests/LlmEvaluations.UnitTests/Worker/EvaluationWorkerTests.cs

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 306 |  | CleanupCompletedTasks_WithNoTasks_DoesNotThrow | Empty task list cleanup does not throw | Empty List<Task> -> Call CleanupCompletedTasks -> Assert list remains empty | Verifies empty-list no-op | Unit - EvaluationWorker. In scope: cleanup no-op. Out of scope: orchestration. |
| 307 |  | CleanupCompletedTasks_WithCompletedTask_RemovesTask | Completed task is removed from list | List with Task.CompletedTask -> Call CleanupCompletedTasks -> Assert list empty | Verifies removal of completed tasks | Unit - EvaluationWorker. In scope: cleanup. Out of scope: orchestration. |
| 308 |  | CleanupCompletedTasks_WithRunningTask_KeepsTask | Running task is kept | List with running Task -> Call CleanupCompletedTasks -> Assert list retains task | Verifies preservation of running tasks | Unit - EvaluationWorker. In scope: cleanup. Out of scope: orchestration. |
| 309 |  | CleanupCompletedTasks_WithMixedTasks_RemovesOnlyCompleted | Mixed list retains only running tasks | List with completed+running -> Call CleanupCompletedTasks -> Assert only running remains | Verifies selective removal | Unit - EvaluationWorker. In scope: cleanup. Out of scope: orchestration. |
| 310 |  | CleanupCompletedTasks_WithFaultedTask_RemovesTaskAndLogsError | Faulted task removed and error logged | List with Task.FromException -> Call CleanupCompletedTasks -> Assert list empty; logger Error invoked | Verifies fault logging | Unit - EvaluationWorker. In scope: fault handling. Out of scope: orchestration. |
| 311 |  | CleanupCompletedTasks_WithCancelledTask_RemovesTask | Cancelled task is removed | List with cancelled Task -> Call CleanupCompletedTasks -> Assert list empty | Verifies cancelled-task cleanup | Unit - EvaluationWorker. In scope: cleanup. Out of scope: orchestration. |
| 312 |  | WaitForInFlightTasksAsync_WithNoTasks_ReturnsImmediately | No tasks completes immediately | Empty list -> Call WaitForInFlightTasksAsync -> Assert completes without throwing | Verifies empty-list fast return | Unit - EvaluationWorker. In scope: wait no-op. Out of scope: orchestration. |
| 313 |  | WaitForInFlightTasksAsync_WithCompletedTasks_ReturnsSuccessfully | Already-completed tasks return successfully | List of completed tasks -> Call WaitForInFlightTasksAsync -> Assert completes | Verifies fast path | Unit - EvaluationWorker. In scope: wait success. Out of scope: orchestration. |
| 314 |  | WaitForInFlightTasksAsync_WithFaultedTask_LogsErrorAndCompletes | Faulted task logs error and completes | Task.FromException -> Call WaitForInFlightTasksAsync -> Assert logger Error invoked | Verifies fault swallow + logging | Unit - EvaluationWorker. In scope: fault handling. Out of scope: orchestration. |
| 315 |  | WaitForInFlightTasksAsync_WithCancelledTask_LogsWarningAndCompletes | Cancelled task logs warning and completes | Task.FromCanceled -> Call WaitForInFlightTasksAsync -> Assert logger Warning invoked | Verifies cancellation handling | Unit - EvaluationWorker. In scope: cancellation handling. Out of scope: orchestration. |
| 316 |  | WaitForInFlightTasksAsync_WhenTasksCompleteWithinTimeout_WaitsForAll | Tasks completing within timeout are awaited | Two running tasks -> Start wait; complete both -> Assert awaits all | Verifies full-drain wait | Unit - EvaluationWorker. In scope: drain behavior. Out of scope: orchestration. |
| 317 |  | WaitForInFlightTasksAsync_WhenTimeoutExceeded_LogsWarningAndReturns | Timeout triggers warning and return | Config short timeout; long-running task -> Call WaitForInFlightTasksAsync -> Assert logger Warning invoked and returns | Verifies timeout behavior | Unit - EvaluationWorker. In scope: shutdown timeout. Out of scope: orchestration. |

## tests/LlmEvaluations.Web.ApiTests/EvaluationApiTests.cs

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 318 |  | EnqueueEvaluation_ValidRequest_ReturnsAcceptedAndSavesToDynamoDB | POST /evals/enqueue with valid body returns 202 and persists to DynamoDB | POST /llm-evaluations/v1/evals/enqueue with valid body + auth -> Assert 202 Accepted -> Load DTO from LocalStack DynamoDB -> Assert ObjectId/Status/EvalConfigTypes match | Verifies end-to-end enqueue persists request | API - Enqueue endpoint. In scope: HTTP API + LocalStack DynamoDB persistence; auth header flow. Out of scope: SNS delivery to worker; real LLM. |
| 319 |  | GetEvaluation_ExistingRequest_ReturnsOkWithEvalResult | GET /evals/{id} returns 200 with EvalResult for existing record | Seed EvaluationRequestDto in DynamoDB -> GET /llm-evaluations/v1/evals/{id} with auth -> Assert 200 OK -> Parse EvalResult -> Assert fields and results populated | Verifies GET returns persisted evaluation | API - Get endpoint. In scope: HTTP API + DynamoDB read; auth. Out of scope: worker execution. |
| 320 |  | GetEvaluation_NonExistentRequest_ReturnsNotFound | GET /evals/{id} returns 404 for missing record | GET with random id + auth -> Assert 404 | Verifies not-found handling | API - Get endpoint. In scope: HTTP 404 path. Out of scope: worker. |
| 321 |  | EnqueueEvaluation_MissingCallId_ReturnsBadRequest | POST /evals/enqueue without call_id returns 400 | POST without call_id + auth -> Assert 400 BadRequest | Verifies input validation at API boundary | API - Enqueue endpoint validation. In scope: HTTP 400 path. Out of scope: persistence. |

## tests/LlmEvaluations.Web.ApiTests/EvaluationsImplTests.cs

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 322 |  | EnqueueEvaluation_WithoutAuth_ReturnsUnauthorized | POST /evals/enqueue without auth returns 401 | POST without auth headers -> Assert 401 Unauthorized | Verifies auth is required | API - Auth. In scope: unauthenticated rejection. Out of scope: persistence. |
| 323 |  | EnqueueEvaluation_WithAuth_ReturnsAccepted | POST /evals/enqueue with auth returns 202 | POST with CsrJwt headers -> Assert 202 Accepted | Verifies auth success path | API - Auth. In scope: authenticated acceptance. Out of scope: persistence. |
| 324 |  | EnqueueEvaluation_WithInvalidRole_ReturnsBadRequest | PHI request with invalid role name returns 400 | POST PHI request with invalid role name -> Assert 400; body contains role-name error | Verifies role-name validation | API - Enqueue validation. In scope: invalid-role rejection. Out of scope: persistence. |
| 325 |  | EnqueueEvaluation_WithValidRole_ReturnsAccepted | PHI request with valid role name accepted and persisted with role | POST PHI request with role PhoneBotReadPhi -> Assert 202 -> Load DTO from Dynamo -> Assert IsPhi=true and role persisted | Verifies valid-role happy path | API - Enqueue validation. In scope: role persistence; LocalStack DynamoDB. Out of scope: worker. |
| 326 |  | EnqueueEvaluation_WithMultipleInvalidRoles_ReturnsBadRequestWithAllInvalidRoles | Multiple invalid roles returns 400 listing all invalid role names | POST with multiple invalid roles -> Assert 400; body contains all invalid names | Verifies multi-invalid-role reporting | API - Enqueue validation. In scope: validation error content. Out of scope: persistence. |
| 327 |  | EnqueueEvaluation_PhiWithoutRoles_ReturnsBadRequest | PHI request without roles_with_access returns 400 | POST PHI without roles -> Assert 400; body contains PHI roles error | Verifies PHI roles guard | API - Enqueue validation. In scope: PHI validation. Out of scope: persistence. |
| 328 |  | EnqueueEvaluation_PhiWithEmptyRoles_ReturnsBadRequest | PHI request with empty roles returns 400 | POST PHI with empty array -> Assert 400; body contains PHI roles error | Verifies empty-roles guard | API - Enqueue validation. In scope: PHI validation. Out of scope: persistence. |
| 329 |  | GetEvaluationResult_WithoutAuth_ReturnsUnauthorized | GET /evals/{id} without auth returns 401 | GET without auth -> Assert 401 | Verifies auth required on GET | API - Auth. In scope: unauthenticated GET. Out of scope: persistence. |
| 330 |  | GetEvaluationResult_WithAuth_NotFound_ReturnsNotFound | GET /evals/{id} with auth but nonexistent id returns 404 | GET random id with auth -> Assert 404 | Verifies not-found path | API - Get endpoint. In scope: 404 path. Out of scope: persistence. |
| 331 |  | GetEvaluation_PhiRecordWithoutRequiredRole_ReturnsForbidden | GET on PHI record without required role returns 401 | Seed PHI DTO -> GET without auth -> Assert 401 Unauthorized | Verifies unauth rejection for PHI | API - PHI auth. In scope: unauth rejection. Out of scope: worker. |
| 332 |  | GetEvaluation_NonPhiRecord_ReturnsOk | GET on non-PHI record returns 200 regardless of roles | Seed non-PHI DTO -> GET with auth -> Assert 200; EvalRequestId matches | Verifies non-PHI read path | API - Get endpoint. In scope: non-PHI read. Out of scope: worker. |
| 333 |  | GetEvaluation_PhiRecordWithNumericRoleId214_ReturnsOk | PHI record with numeric role 214 is accessible by user with role id 214 | Seed PHI DTO with role "214" -> GET with CsrJwt(214) -> Assert 200 | Verifies numeric-role auth | API - PHI auth. In scope: numeric-role match. Out of scope: worker. |
| 334 |  | GetEvaluation_PhiRecordWithRole_0_and_214_ReturnsOK | PHI record is accessible when user claims role 0 or 214 | Seed PHI DTO with role "214" -> GET with CsrJwt(0, 214) -> Assert 200 | Verifies multi-role authorization | API - PHI auth. In scope: any-match authorization. Out of scope: worker. |

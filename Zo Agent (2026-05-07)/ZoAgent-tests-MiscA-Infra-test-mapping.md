# Miscellaneous Tests - Part A-Infra - Unit Test Mapping

> Repo: [Zocdoc/zo-agent](https://github.com/Zocdoc/zo-agent)
> Folder: `tests/` (root)
> Generated: 2026-05-07
> Stack: Python / pytest / LiveKit Agents

## test_ab_service.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 |  | `TestIsFlagOn.test_returns_true_when_flag_is_on` | Returns true when flag is on | Calls _make_ab_service, is_flag_on.assert_called_once_with, service.is_flag_on. Asserts result is True. | Returns true when flag is on [async] | In scope: TestIsFlagOn. Out of scope: integration with live services. |
| 2 |  | `TestIsFlagOn.test_returns_false_when_flag_is_off` | Returns false when flag is off | Calls _make_ab_service, service.is_flag_on. Asserts result is False. | Returns false when flag is off [async] | In scope: TestIsFlagOn. Out of scope: integration with live services. |
| 3 |  | `TestIsFlagOn.test_caches_result_on_session_state` | Caches result on session state | Calls _make_ab_service, is_flag_on.assert_called_once, service.is_flag_on. Asserts state.ab_cache['flag:my_flag:practice_id:p1'] is True. | Caches result on session state [async] | In scope: TestIsFlagOn. Out of scope: integration with live services. |
| 4 |  | `TestIsFlagOn.test_cache_distinguishes_different_flags` | Cache distinguishes different flags | Calls _make_ab_service, service.is_flag_on. Asserts 3 conditions on result. | Cache distinguishes different flags [async] | In scope: TestIsFlagOn. Out of scope: integration with live services. |
| 5 |  | `TestIsFlagOn.test_cache_distinguishes_different_visitor_types` | Cache distinguishes different visitor types | Calls _make_ab_service, service.is_flag_on. Asserts 3 conditions on result. | Cache distinguishes different visitor types [async] | In scope: TestIsFlagOn. Out of scope: integration with live services. |
| 6 |  | `TestIsFlagOn.test_returns_default_false_on_error` | Returns default false on error | Calls _make_ab_service, Exception, service.is_flag_on. Asserts result is False. | Returns default false on error [async] | In scope: TestIsFlagOn. Out of scope: integration with live services. |
| 7 |  | `TestIsFlagOn.test_returns_custom_default_on_error` | Returns custom default on error | Calls _make_ab_service, Exception, service.is_flag_on. Asserts result is True. | Returns custom default on error [async] | In scope: TestIsFlagOn. Out of scope: integration with live services. |
| 8 |  | `TestIsFlagOn.test_does_not_cache_errors` | Does not cache errors | Calls _make_ab_service, Exception, service.is_flag_on. Asserts 3 conditions on result. | Does not cache errors [async] | In scope: TestIsFlagOn. Out of scope: integration with live services. |
| 9 |  | `TestGetVariant.test_returns_variant_value` | Returns variant value | Calls _make_ab_service, get_variant.assert_called_once_with, service.get_variant. Asserts result == 'treatment'. | Returns variant value [async] | In scope: TestGetVariant. Out of scope: integration with live services. |
| 10 |  | `TestGetVariant.test_caches_result_on_session_state` | Caches result on session state | Calls _make_ab_service, get_variant.assert_called_once, service.get_variant. Asserts state.ab_cache['variant:exp:practice_id:p1'] == 'control'. | Caches result on session state [async] | In scope: TestGetVariant. Out of scope: integration with live services. |
| 11 |  | `TestGetVariant.test_cache_distinguishes_different_experiments` | Cache distinguishes different experiments | Calls _make_ab_service, service.get_variant. Asserts 3 conditions on result. | Cache distinguishes different experiments [async] | In scope: TestGetVariant. Out of scope: integration with live services. |
| 12 |  | `TestGetVariant.test_returns_default_empty_string_on_error` | Returns default empty string on error | Calls _make_ab_service, Exception, service.get_variant. Asserts result == ''. | Returns default empty string on error [async] | In scope: TestGetVariant. Out of scope: integration with live services. |
| 13 |  | `TestGetVariant.test_returns_custom_default_on_error` | Returns custom default on error | Calls _make_ab_service, Exception, service.get_variant. Asserts result == 'fallback'. | Returns custom default on error [async] | In scope: TestGetVariant. Out of scope: integration with live services. |
| 14 |  | `TestGetVariant.test_does_not_cache_errors` | Does not cache errors | Calls _make_ab_service, Exception, service.get_variant. Asserts 3 conditions on result. | Does not cache errors [async] | In scope: TestGetVariant. Out of scope: integration with live services. |

### Suggested Missing Tests
- **Setup-only file** - No test functions found — A/B service is what gates feature flags; absence is high-risk.

### Improvement Suggestions / Irrelevant Tests
- **Empty file** - No detected `test_*` functions.

## test_background_audio.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 15 |  | `TestBackgroundAudioSetup.test_assets_dir_points_to_project_root` | ASSETS_DIR should resolve to <project_root>/assets/. | Calls Path. Asserts expected == ASSETS_DIR. | Assets dir points to project root | In scope: TestBackgroundAudioSetup. Out of scope: integration with live services. |
| 16 |  | `TestBackgroundAudioSetup.test_comfort_noise_file_exists` | The generated comfort noise file must exist at the expected path. | Calls comfort_noise_path.exists. Asserts comfort_noise_path.exists(). | Comfort noise file exists | In scope: TestBackgroundAudioSetup. Out of scope: integration with live services. |

### Suggested Missing Tests
- **Audio loop seam** - After loop wraparound, ensure no audible click.
- **Volume ducking on TTS** - When agent speaks, background audio must duck.

### Improvement Suggestions / Irrelevant Tests
- **Asset path hard-coded** - Tests reference real audio files; consider stubbing the audio source.

## test_base_client.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 17 |  | `TestBaseApiClient.test_init` | Test BaseApiClient initialization. | Fixtures: mock_config. Calls BaseApiClient. Asserts 3 conditions on result. | Init | In scope: TestBaseApiClient. Out of scope: real LLM/network calls (mocked). |
| 18 |  | `TestBaseApiClient.test_get_headers_includes_call_id` | Test header construction includes call-id. | Fixtures: mock_config. Calls BaseApiClient, client._get_headers. Asserts 3 conditions on result. | Get headers includes call id | In scope: TestBaseApiClient. Out of scope: real LLM/network calls (mocked). |
| 19 |  | `TestBaseApiClient.test_get_headers_includes_caller_phone_number` | Test header includes caller-phone-number when provided. | Fixtures: mock_config. Calls BaseApiClient, client._get_headers. Asserts headers['caller-phone-number'] == '+15551234567'. | Get headers includes caller phone number | In scope: TestBaseApiClient. Out of scope: real LLM/network calls (mocked). |
| 20 |  | `TestBaseApiClient.test_get_headers_includes_practice_phone_number` | Test header includes practice-phone-number when provided. | Fixtures: mock_config. Calls BaseApiClient, client._get_headers. Asserts headers['practice-phone-number'] == '+15557654321'. | Get headers includes practice phone number | In scope: TestBaseApiClient. Out of scope: real LLM/network calls (mocked). |
| 21 |  | `TestBaseApiClient.test_get_headers_omits_caller_phone_number_when_none` | Test header omits caller-phone-number when not provided. | Fixtures: mock_config. Calls BaseApiClient, client._get_headers. Asserts 'caller-phone-number' not in headers. | Get headers omits caller phone number when none | In scope: TestBaseApiClient. Out of scope: real LLM/network calls (mocked). |
| 22 |  | `TestBaseApiClient.test_get_headers_omits_practice_phone_number_when_none` | Test header omits practice-phone-number when not provided. | Fixtures: mock_config. Calls BaseApiClient, client._get_headers. Asserts 'practice-phone-number' not in headers. | Get headers omits practice phone number when none | In scope: TestBaseApiClient. Out of scope: real LLM/network calls (mocked). |
| 23 |  | `TestBaseApiClient.test_make_request_success` | Test successful API request. | Fixtures: base_client. Calls Mock, patch.object, AsyncMock. Asserts result == {'success': True}. | Make request success [async] | In scope: TestBaseApiClient. Out of scope: real LLM/network calls (mocked). |
| 24 |  | `TestBaseApiClient.test_make_request_401_auth_error` | Test handling of 401 authentication error. | Fixtures: base_client. Calls Mock, httpx.HTTPStatusError, patch.object. Asserts mock_client.request.call_count == 1. | Make request 401 auth error [async] | In scope: TestBaseApiClient. Out of scope: real LLM/network calls (mocked). |
| 25 |  | `TestBaseApiClient.test_make_request_timeout_with_retry` | Test timeout handling with retries. | Fixtures: base_client. Calls patch.object, AsyncMock, httpx.TimeoutException. Asserts mock_client.request.call_count == 3. | Make request timeout with retry [async] | In scope: TestBaseApiClient. Out of scope: real LLM/network calls (mocked). |
| 26 |  | `TestBaseApiClient.test_make_request_404_no_retry` | Test 404 error is not retried. | Fixtures: base_client. Calls Mock, httpx.HTTPStatusError, patch.object. Asserts mock_client.request.call_count == 1. | Make request 404 no retry [async] | In scope: TestBaseApiClient. Out of scope: real LLM/network calls (mocked). |
| 27 |  | `TestBaseApiClient.test_make_request_500_with_retry` | Test 500 error is retried. | Fixtures: base_client. Calls Mock, httpx.HTTPStatusError, patch.object. Asserts mock_client.request.call_count == 3. | Make request 500 with retry [async] | In scope: TestBaseApiClient. Out of scope: real LLM/network calls (mocked). |
| 28 |  | `TestBaseApiClient.test_cleanup` | Test cleanup closes the HTTP client. | Fixtures: base_client. Calls AsyncMock, aclose.assert_called_once, base_client.cleanup. Asserts base_client._client is None. | Cleanup [async] | In scope: TestBaseApiClient. Out of scope: real LLM/network calls (mocked). |
| 29 |  | `TestBaseApiClient.test_substitute_path_params_single` | Test substituting a single path parameter. | Fixtures: base_client. Calls base_client._substitute_path_params. Asserts result == '/users/123/profile'. | Substitute path params single | In scope: TestBaseApiClient. Out of scope: integration with live services. |
| 30 |  | `TestBaseApiClient.test_substitute_path_params_multiple` | Test substituting multiple path parameters. | Fixtures: base_client. Calls base_client._substitute_path_params. Asserts result == '/users/123/posts/456'. | Substitute path params multiple | In scope: TestBaseApiClient. Out of scope: integration with live services. |
| 31 |  | `TestBaseApiClient.test_substitute_path_params_no_match` | Test path params that don't match placeholders are ignored. | Fixtures: base_client. Calls base_client._substitute_path_params. Asserts result == '/users/123'. | Substitute path params no match | In scope: TestBaseApiClient. Out of scope: integration with live services. |
| 32 |  | `TestBaseApiClient.test_substitute_path_params_type_conversion` | Test path params are converted to strings. | Fixtures: base_client. Calls base_client._substitute_path_params. Asserts result == '/items/42'. | Substitute path params type conversion | In scope: TestBaseApiClient. Out of scope: integration with live services. |
| 33 |  | `TestBaseApiClient.test_make_request_with_path_params` | Test _make_request with path parameter substitution. | Fixtures: base_client. Calls Mock, patch.object, AsyncMock. Asserts 2 conditions on result. | Make request with path params [async] | In scope: TestBaseApiClient. Out of scope: real LLM/network calls (mocked). |
| 34 |  | `TestBaseApiClient.test_make_request_empty_response_body` | Test that an empty response body returns an empty dict instead of raising. | Fixtures: base_client. Calls Mock, patch.object, AsyncMock. Asserts result == {}. | Make request empty response body [async] | In scope: TestBaseApiClient. Out of scope: real LLM/network calls (mocked). |
| 35 |  | `TestBaseApiClient.test_make_request_without_path_params` | Test _make_request works without path params (backward compatible). | Fixtures: base_client. Calls Mock, patch.object, AsyncMock. Asserts 2 conditions on result. | Make request without path params [async] | In scope: TestBaseApiClient. Out of scope: real LLM/network calls (mocked). |
| 36 |  | `TestBaseApiClient.test_make_request_timeout_override` | Test per-request timeout override is passed to httpx. | Fixtures: base_client. Calls Mock, patch.object, AsyncMock. Asserts 2 conditions on result. | Make request timeout override [async] | In scope: TestBaseApiClient. Out of scope: real LLM/network calls (mocked). |
| 37 |  | `TestBaseApiClient.test_make_request_no_timeout_override_omits_timeout_kwarg` | Test that no timeout override does not pass timeout to httpx (uses client default). | Fixtures: base_client. Calls Mock, patch.object, AsyncMock. Asserts 'timeout' not in call_args[1]. | Make request no timeout override omits timeout kwarg [async] | In scope: TestBaseApiClient. Out of scope: real LLM/network calls (mocked). |
| 38 |  | `TestBaseApiClient.test_make_request_max_retries_override` | Test per-request max_retries override limits retry attempts. | Fixtures: base_client. Calls patch.object, AsyncMock, httpx.TimeoutException. Asserts mock_client.request.call_count == 2. | Make request max retries override [async] | In scope: TestBaseApiClient. Out of scope: real LLM/network calls (mocked). |
| 39 |  | `TestBaseApiClient.test_make_request_overrides_do_not_mutate_config` | Test that per-request overrides don't change the client config. | Fixtures: base_client. Calls Mock, patch.object, AsyncMock. Asserts 2 conditions on result. | Make request overrides do not mutate config [async] | In scope: TestBaseApiClient. Out of scope: real LLM/network calls (mocked). |

### Suggested Missing Tests
- **Connection pool exhaustion** - High-fanout scenarios — does the client reuse connections or leak?
- **Cancellation propagation** - When caller cancels, does the underlying httpx request abort?

### Improvement Suggestions / Irrelevant Tests
- **Mocked transport** - Consider using `respx`/`httpx.MockTransport` for higher-fidelity coverage.

## test_config.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 40 |  | `test_livekit_config_requires_all_fields` | Test that LiveKitConfig requires url, api_key, and api_secret. | Calls value.errors, environ.update, environ.pop. Asserts required_fields == {'url', 'api_key', 'api_secret'}. | Livekit config requires all fields | In scope: unit-level behavior. Out of scope: integration with live services. |
| 41 |  | `test_livekit_config_with_valid_values` | Test that LiveKitConfig loads from environment variables. | Calls LiveKitConfig, api_secret.get_secret_value. Asserts 3 conditions on result. | Livekit config with valid values | In scope: unit-level behavior. Out of scope: integration with live services. |
| 42 |  | `test_model_config_has_defaults` | Test that ModelConfig provides sensible defaults. | Calls ModelConfig, environ.update, environ.pop. Asserts config.llm_model == 'google/gemini-2.5-flash'. | Model config has defaults | In scope: unit-level behavior. Out of scope: integration with live services. |
| 43 |  | `test_model_config_allows_overrides` | Test that ModelConfig can be overridden via environment variables. | Calls ModelConfig. Asserts config.llm_model == 'openai/gpt-4o'. | Model config allows overrides | In scope: unit-level behavior. Out of scope: integration with live services. |
| 44 |  | `test_deepgram_config_cloud_mode` | Test DeepgramConfig in cloud mode (no base_url). | Calls environ.keys, key.startswith, DeepgramConfig. Asserts 5 conditions on result. | Deepgram config cloud mode | In scope: unit-level behavior. Out of scope: integration with live services. |
| 45 |  | `test_deepgram_config_on_prem_mode` | Test DeepgramConfig in on-prem mode (base_url set). | Calls environ.keys, key.startswith, DeepgramConfig. Asserts 3 conditions on result. | Deepgram config on prem mode | In scope: unit-level behavior. Out of scope: integration with live services. |
| 46 |  | `test_deepgram_config_api_key_optional` | Test that DeepgramConfig does not require api_key. | Calls environ.keys, key.startswith, DeepgramConfig. Asserts 2 conditions on result. | Deepgram config api key optional | In scope: unit-level behavior. Out of scope: integration with live services. |
| 47 |  | `test_elevenlabs_config_requires_api_key` | Test that ElevenLabsConfig requires api_key (voice configs have defaults). | Calls environ.keys, key.startswith, value.errors. Asserts 'api_key' in required_fields. | Elevenlabs config requires api key | In scope: unit-level behavior. Out of scope: integration with live services. |
| 48 |  | `test_app_config_requires_credentials` | Test that AppConfig requires livekit, zotools, and elevenlabs credentials. | Calls environ.keys, key.startswith, value.errors. Asserts 3 conditions on result. | App config requires credentials | In scope: unit-level behavior. Out of scope: integration with live services. |
| 49 |  | `test_app_config_with_valid_credentials` | Test that AppConfig loads successfully with all required fields. | Calls environ.keys, key.startswith, AppConfig. Asserts 22 conditions on result. | App config with valid credentials | In scope: unit-level behavior. Out of scope: integration with live services. |
| 50 |  | `test_secret_str_not_exposed_in_repr` | Test that SecretStr values are masked in string representation. | Calls AppConfig. Asserts 3 conditions on result. | Secret str not exposed in repr | In scope: unit-level behavior. Out of scope: integration with live services. |
| 51 |  | `test_zotools_config_from_env` | Test loading zo-tools config from environment. | Calls ZoToolsConfig. Asserts 2 conditions on result. | Zotools config from env | In scope: unit-level behavior. Out of scope: integration with live services. |
| 52 |  | `test_zotools_config_validation` | Test zo-tools config validates required fields. | Calls environ.update, environ.pop, pytest.raises. Verifies behavior via mocks/return value. | Zotools config validation | In scope: unit-level behavior. Out of scope: real LLM/network calls (mocked), no explicit assertions. |
| 53 |  | `test_app_config_includes_zotools` | Test AppConfig includes zo-tools configuration. | Calls AppConfig. Asserts 2 conditions on result. | App config includes zotools | In scope: unit-level behavior. Out of scope: integration with live services. |
| 54 |  | `TestElevenLabsVoiceSettings.test_get_voice_settings_returns_none_when_no_settings` | Get voice settings returns none when no settings | Calls ElevenLabsVoiceConfig, config.get_voice_settings. Asserts config.get_voice_settings() is None. | Get voice settings returns none when no settings | In scope: TestElevenLabsVoiceSettings. Out of scope: integration with live services. |
| 55 |  | `TestElevenLabsVoiceSettings.test_get_voice_settings_with_all_settings` | Get voice settings with all settings | Calls ElevenLabsVoiceConfig, config.get_voice_settings. Asserts 6 conditions on result. | Get voice settings with all settings | In scope: TestElevenLabsVoiceSettings. Out of scope: integration with live services. |
| 56 |  | `TestElevenLabsVoiceSettings.test_get_voice_settings_with_partial_settings` | Get voice settings with partial settings | Calls ElevenLabsVoiceConfig, config.get_voice_settings. Asserts 3 conditions on result. | Get voice settings with partial settings | In scope: TestElevenLabsVoiceSettings. Out of scope: integration with live services. |
| 57 |  | `TestElevenLabsVoiceSettings.test_voice_settings_from_env` | Voice settings from env | Calls ElevenLabsConfig, en.get_voice_settings, environ.pop. Asserts 4 conditions on result. | Voice settings from env | In scope: TestElevenLabsVoiceSettings. Out of scope: integration with live services. |

### Suggested Missing Tests
- **Missing required env var** - Config should fail fast at startup with clear message.
- **Invalid type coercion** - Boolean string "false" vs "False" vs "0".

### Improvement Suggestions / Irrelevant Tests
- **Env-var leakage** - Tests should use monkeypatch for env vars, not mutate global state.

## test_datadog_metrics_consumer.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 58 |  | `TestCallStartedEvent.test_emits_calls_started_increment` | Emits calls started increment | Calls make_mock_metrics, EventBus, DatadogMetricsConsumer. Asserts 3 conditions on result. | Emits calls started increment [async] | In scope: TestCallStartedEvent. Out of scope: real LLM/network calls (mocked). |
| 59 |  | `TestCallStartedEvent.test_test_call_sets_is_test_true_tag` | Test call sets is test true tag | Calls make_mock_metrics, EventBus, DatadogMetricsConsumer. Asserts 'is_test:true' in kwargs['tags']. | Test call sets is test true tag [async] | In scope: TestCallStartedEvent. Out of scope: real LLM/network calls (mocked). |
| 60 |  | `TestCallEndedEvent.test_emits_calls_ended_and_duration` | Emits calls ended and duration | Calls make_mock_metrics, EventBus, DatadogMetricsConsumer. Asserts 3 conditions on result. | Emits calls ended and duration [async] | In scope: TestCallEndedEvent. Out of scope: real LLM/network calls (mocked). |
| 61 |  | `TestTurnCompleteEvent.test_user_turn_emits_eou_histograms` | User turn emits eou histograms | Calls make_mock_metrics, EventBus, DatadogMetricsConsumer. Asserts 4 conditions on result. | User turn emits eou histograms [async] | In scope: TestTurnCompleteEvent. Out of scope: real LLM/network calls (mocked). |
| 62 |  | `TestTurnCompleteEvent.test_user_turn_does_not_emit_llm_or_tts` | User turn does not emit llm or tts | Calls make_mock_metrics, EventBus, DatadogMetricsConsumer. Asserts 2 conditions on result. | User turn does not emit llm or tts [async] | In scope: TestTurnCompleteEvent. Out of scope: real LLM/network calls (mocked). |
| 63 |  | `TestTurnCompleteEvent.test_assistant_turn_emits_llm_and_tts_histograms` | Assistant turn emits llm and tts histograms | Calls make_mock_metrics, EventBus, DatadogMetricsConsumer. Asserts 3 conditions on result. | Assistant turn emits llm and tts histograms [async] | In scope: TestTurnCompleteEvent. Out of scope: real LLM/network calls (mocked). |
| 64 |  | `TestTurnCompleteEvent.test_none_fields_are_skipped` | None fields are skipped | Calls make_mock_metrics, EventBus, DatadogMetricsConsumer. Asserts 'turns.tts_duration_ms' not in histogram_names. | None fields are skipped [async] | In scope: TestTurnCompleteEvent. Out of scope: real LLM/network calls (mocked). |
| 65 |  | `TestTurnCompleteEvent.test_perceived_latency_combines_eou_delay_and_llm_ttfb` | Perceived latency = eou_end_of_utterance_delay + eou_transcription_delay + llm_ttfb. | Calls make_mock_metrics, EventBus, DatadogMetricsConsumer. Asserts 2 conditions on result. | Perceived latency combines eou delay and llm ttfb [async] | In scope: TestTurnCompleteEvent. Out of scope: real LLM/network calls (mocked). |
| 66 |  | `TestTurnCompleteEvent.test_perceived_latency_not_emitted_without_prior_eou` | Perceived latency not emitted without prior eou | Calls make_mock_metrics, EventBus, DatadogMetricsConsumer. Asserts 'turns.perceived_latency_ms' not in distribution_names. | Perceived latency not emitted without prior eou [async] | In scope: TestTurnCompleteEvent. Out of scope: real LLM/network calls (mocked). |
| 67 |  | `TestTurnCompleteEvent.test_eou_cleared_by_assistant_turn_with_no_llm_ttfb` | EOU delay cached from user turn is discarded when assistant turn has llm_ttfb_ms=None. | Calls make_mock_metrics, EventBus, DatadogMetricsConsumer. Asserts 'turns.perceived_latency_ms' not in distribution_names. | Eou cleared by assistant turn with no llm ttfb [async] | In scope: TestTurnCompleteEvent. Out of scope: real LLM/network calls (mocked). |
| 68 |  | `TestToolCallEvent.test_emits_tools_calls_increment_and_duration_histogram` | Emits tools calls increment and duration histogram | Calls make_mock_metrics, EventBus, DatadogMetricsConsumer. Asserts 4 conditions on result. | Emits tools calls increment and duration histogram [async] | In scope: TestToolCallEvent. Out of scope: real LLM/network calls (mocked). |
| 69 |  | `TestToolCallEvent.test_failed_tool_call_sets_success_false_tag` | Failed tool call sets success false tag | Calls make_mock_metrics, EventBus, DatadogMetricsConsumer. Asserts 'success:false' in tags. | Failed tool call sets success false tag [async] | In scope: TestToolCallEvent. Out of scope: real LLM/network calls (mocked). |
| 70 |  | `TestErrorEvent.test_emits_errors_increment` | Emits errors increment | Calls make_mock_metrics, EventBus, DatadogMetricsConsumer. Asserts 3 conditions on result. | Emits errors increment [async] | In scope: TestErrorEvent. Out of scope: real LLM/network calls (mocked). |
| 71 |  | `TestApptModificationBlockedEvent.test_emits_appt_modification_blocked_increment` | Emits appt modification blocked increment | Calls make_mock_metrics, EventBus, DatadogMetricsConsumer. Asserts 4 conditions on result. | Emits appt modification blocked increment [async] | In scope: TestApptModificationBlockedEvent. Out of scope: real LLM/network calls (mocked). |
| 72 |  | `TestApptModificationBlockedEvent.test_is_test_tag_propagates_to_blocked_event` | Is test tag propagates to blocked event | Calls make_mock_metrics, EventBus, DatadogMetricsConsumer. Asserts 'is_test:true' in tags. | Is test tag propagates to blocked event [async] | In scope: TestApptModificationBlockedEvent. Out of scope: real LLM/network calls (mocked). |
| 73 |  | `TestIsTestCallPropagation.test_is_test_tag_propagates_to_turn_events` | Is test tag propagates to turn events | Calls make_mock_metrics, EventBus, DatadogMetricsConsumer. Asserts any(('is_test:true' in c[1]['tags'] for c in histogram_calls)). | Is test tag propagates to turn events [async] | In scope: TestIsTestCallPropagation. Out of scope: real LLM/network calls (mocked). |

### Suggested Missing Tests
- **Setup-only file** - No test functions — DataDog metric pipeline is critical for SLOs/alerting; coverage gap.

### Improvement Suggestions / Irrelevant Tests
- **Empty file** - No coverage.

## test_emit_coverage_stats.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 74 |  | `TestParseCoberturaStats.test_parses_all_fields` | Parses all fields | Fixtures: cobertura_xml. Calls parse_cobertura_stats, pytest.approx. Asserts 6 conditions on result. | Parses all fields | In scope: TestParseCoberturaStats. Out of scope: integration with live services. |
| 75 |  | `TestFormatTeamcityMessages.test_emits_four_stat_lines` | Emits four stat lines | Calls format_teamcity_messages. Asserts 4 conditions on result. | Emits four stat lines | In scope: TestFormatTeamcityMessages. Out of scope: integration with live services. |
| 76 |  | `TestFormatTeamcityMessages.test_emits_human_readable_summary` | Emits human readable summary | Calls format_teamcity_messages, any. Asserts 2 conditions on result. | Emits human readable summary | In scope: TestFormatTeamcityMessages. Out of scope: integration with live services. |

### Suggested Missing Tests
- **Stats emission failure** - When DataDog is down, stats emission must not block call shutdown.

### Improvement Suggestions / Irrelevant Tests
- **3 tests, narrow scope** - Add error-path coverage.

## test_metrics.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 77 |  | `TestDebugMetricsClient.test_increment_logs_correctly` | Increment logs correctly | Fixtures: caplog. Calls DebugMetricsClient, caplog.at_level, client.increment. Asserts 2 conditions on result. | Increment logs correctly | In scope: TestDebugMetricsClient. Out of scope: integration with live services. |
| 78 |  | `TestDebugMetricsClient.test_count_logs_correctly` | Count logs correctly | Fixtures: caplog. Calls DebugMetricsClient, caplog.at_level, client.count. Asserts 2 conditions on result. | Count logs correctly | In scope: TestDebugMetricsClient. Out of scope: integration with live services. |
| 79 |  | `TestDebugMetricsClient.test_histogram_logs_correctly` | Histogram logs correctly | Fixtures: caplog. Calls DebugMetricsClient, caplog.at_level, client.histogram. Asserts 2 conditions on result. | Histogram logs correctly | In scope: TestDebugMetricsClient. Out of scope: integration with live services. |
| 80 |  | `TestDebugMetricsClient.test_timer_logs_correctly` | Timer logs correctly | Fixtures: caplog. Calls DebugMetricsClient, caplog.at_level, client.timer. Asserts 2 conditions on result. | Timer logs correctly | In scope: TestDebugMetricsClient. Out of scope: integration with live services. |
| 81 |  | `TestDebugMetricsClient.test_applies_namespace` | Applies namespace | Fixtures: caplog. Calls DebugMetricsClient, caplog.at_level, client.increment. Asserts f'{METRIC_NAMESPACE}.my_metric' in caplog.text. | Applies namespace | In scope: TestDebugMetricsClient. Out of scope: integration with live services. |
| 82 |  | `TestDebugMetricsClient.test_applies_default_tag` | Applies default tag | Fixtures: caplog. Calls DebugMetricsClient, caplog.at_level, client.increment. Asserts DEFAULT_TAG in caplog.text. | Applies default tag | In scope: TestDebugMetricsClient. Out of scope: integration with live services. |
| 83 |  | `TestDebugMetricsClient.test_merges_tags` | Merges tags | Fixtures: caplog. Calls DebugMetricsClient, caplog.at_level, client.increment. Asserts 2 conditions on result. | Merges tags | In scope: TestDebugMetricsClient. Out of scope: integration with live services. |
| 84 |  | `TestDatadogMetricsClient.test_created_with_namespace` | Created with namespace | Fixtures: mock_dogstatsd_class. Calls DatadogMetricsClient, mock_dogstatsd_class.assert_called_once_with. Verifies behavior via mocks/return value. | Created with namespace | In scope: TestDatadogMetricsClient. Out of scope: real LLM/network calls (mocked). |
| 85 |  | `TestDatadogMetricsClient.test_increment_calls_statsd` | Increment calls statsd | Fixtures: mock_dogstatsd_class. Calls MagicMock, DatadogMetricsClient, client.increment. Asserts 2 conditions on result. | Increment calls statsd | In scope: TestDatadogMetricsClient. Out of scope: real LLM/network calls (mocked). |
| 86 |  | `TestDatadogMetricsClient.test_count_calls_statsd` | Count calls statsd | Fixtures: mock_dogstatsd_class. Calls MagicMock, DatadogMetricsClient, client.count. Asserts 2 conditions on result. | Count calls statsd | In scope: TestDatadogMetricsClient. Out of scope: real LLM/network calls (mocked). |
| 87 |  | `TestDatadogMetricsClient.test_histogram_calls_statsd` | Histogram calls statsd | Fixtures: mock_dogstatsd_class. Calls MagicMock, DatadogMetricsClient, client.histogram. Asserts 2 conditions on result. | Histogram calls statsd | In scope: TestDatadogMetricsClient. Out of scope: real LLM/network calls (mocked). |
| 88 |  | `TestDatadogMetricsClient.test_timer_calls_statsd` | Timer calls statsd | Fixtures: mock_dogstatsd_class. Calls MagicMock, DatadogMetricsClient, client.timer. Asserts 2 conditions on result. | Timer calls statsd | In scope: TestDatadogMetricsClient. Out of scope: real LLM/network calls (mocked). |
| 89 |  | `TestDatadogMetricsClient.test_applies_default_tag` | Applies default tag | Fixtures: mock_dogstatsd_class. Calls MagicMock, DatadogMetricsClient, client.increment. Asserts DEFAULT_TAG in call_args[1]['tags']. | Applies default tag | In scope: TestDatadogMetricsClient. Out of scope: real LLM/network calls (mocked). |
| 90 |  | `TestDatadogMetricsClient.test_merges_tags` | Merges tags | Fixtures: mock_dogstatsd_class. Calls MagicMock, DatadogMetricsClient, client.increment. Asserts 2 conditions on result. | Merges tags | In scope: TestDatadogMetricsClient. Out of scope: real LLM/network calls (mocked). |
| 91 |  | `TestCreateMetricsClient.test_creates_datadog_client_in_ci` | Creates datadog client in ci | Fixtures: monkeypatch. Calls monkeypatch.setenv, patch, create_metrics_client. Asserts isinstance(client, DatadogMetricsClient). | Creates datadog client in ci | In scope: TestCreateMetricsClient. Out of scope: real LLM/network calls (mocked). |
| 92 |  | `TestCreateMetricsClient.test_creates_datadog_client_in_pr201` | Creates datadog client in pr201 | Fixtures: monkeypatch. Calls monkeypatch.setenv, patch, create_metrics_client. Asserts isinstance(client, DatadogMetricsClient). | Creates datadog client in pr201 | In scope: TestCreateMetricsClient. Out of scope: real LLM/network calls (mocked). |
| 93 |  | `TestCreateMetricsClient.test_creates_debug_client_in_dev` | Creates debug client in dev | Fixtures: monkeypatch. Calls monkeypatch.setenv, create_metrics_client. Asserts isinstance(client, DebugMetricsClient). | Creates debug client in dev | In scope: TestCreateMetricsClient. Out of scope: real LLM/network calls (mocked). |
| 94 |  | `TestCreateMetricsClient.test_creates_debug_client_when_app_env_not_set` | Creates debug client when app env not set | Fixtures: monkeypatch. Calls monkeypatch.delenv, create_metrics_client. Asserts isinstance(client, DebugMetricsClient). | Creates debug client when app env not set | In scope: TestCreateMetricsClient. Out of scope: real LLM/network calls (mocked). |

### Suggested Missing Tests
- **Tag cardinality bound** - Verify high-cardinality tags (call_id) are not emitted to DataDog.
- **Histogram buckets pinned** - Latency buckets should match SLO bucket definition.

### Improvement Suggestions / Irrelevant Tests
- **No SLO assertion** - Metrics names changing breaks alerting silently — add a snapshot of metric names.

## test_noise_cancellation.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 95 |  | `TestResolveNoiseCancellation.test_known_variant_returns_correct_model` | Known variant returns correct model | Fixtures: variant, expected_model_path. Calls _resolve_noise_cancellation. Asserts 2 conditions on result. | Known variant returns correct model (parametrized) | In scope: TestResolveNoiseCancellation. Out of scope: integration with live services. |
| 96 |  | `TestResolveNoiseCancellation.test_variant_ignores_participant_kind` | Variant ignores participant kind | Calls _resolve_noise_cancellation. Asserts 3 conditions on result. | Variant ignores participant kind | In scope: TestResolveNoiseCancellation. Out of scope: integration with live services. |
| 97 |  | `TestResolveNoiseCancellation.test_none_variant_returns_none_for_sip` | None variant returns none for sip | Calls _resolve_noise_cancellation. Asserts _resolve_noise_cancellation('none', SIP) is None. | None variant returns none for sip | In scope: TestResolveNoiseCancellation. Out of scope: integration with live services. |
| 98 |  | `TestResolveNoiseCancellation.test_none_variant_returns_none_for_standard` | None variant returns none for standard | Calls _resolve_noise_cancellation. Asserts _resolve_noise_cancellation('none', STANDARD) is None. | None variant returns none for standard | In scope: TestResolveNoiseCancellation. Out of scope: integration with live services. |
| 99 |  | `TestResolveNoiseCancellation.test_empty_variant_defaults_to_bvc_telephony_for_sip` | Empty variant defaults to bvc telephony for sip | Calls _resolve_noise_cancellation, noise_cancellation.BVCTelephony. Asserts 2 conditions on result. | Empty variant defaults to bvc telephony for sip | In scope: TestResolveNoiseCancellation. Out of scope: integration with live services. |
| 100 |  | `TestResolveNoiseCancellation.test_empty_variant_defaults_to_bvc_for_standard` | Empty variant defaults to bvc for standard | Calls _resolve_noise_cancellation, noise_cancellation.BVC. Asserts 2 conditions on result. | Empty variant defaults to bvc for standard | In scope: TestResolveNoiseCancellation. Out of scope: integration with live services. |
| 101 |  | `TestResolveNoiseCancellation.test_unrecognised_variant_defaults_to_bvc_telephony_for_sip` | Unrecognised variant defaults to bvc telephony for sip | Calls _resolve_noise_cancellation, noise_cancellation.BVCTelephony. Asserts 2 conditions on result. | Unrecognised variant defaults to bvc telephony for sip | In scope: TestResolveNoiseCancellation. Out of scope: integration with live services. |
| 102 |  | `TestResolveNoiseCancellation.test_unrecognised_variant_defaults_to_bvc_for_standard` | Unrecognised variant defaults to bvc for standard | Calls _resolve_noise_cancellation, noise_cancellation.BVC. Asserts 2 conditions on result. | Unrecognised variant defaults to bvc for standard | In scope: TestResolveNoiseCancellation. Out of scope: integration with live services. |

### Suggested Missing Tests
- **Disabled-by-config path** - Verify NC disabled gives raw audio path.
- **NC failure fallback** - NC plugin crashes — agent should degrade, not hang.

### Improvement Suggestions / Irrelevant Tests
- **Audio fixtures** - Consider real noisy audio captures for fidelity.

## test_sentry.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 103 |  | `TestInitSentry.test_init_sentry_enabled_in_ci` | Sentry initializes when APP_ENV=ci and SENTRY_DSN is set. | Fixtures: monkeypatch. Calls monkeypatch.setenv, patch, init_sentry. Asserts call_kwargs['dsn'] == 'https://test@sentry.io/123'. | Init sentry enabled in ci | In scope: TestInitSentry. Out of scope: real LLM/network calls (mocked). |
| 104 |  | `TestInitSentry.test_init_sentry_enabled_in_pr201` | Sentry initializes when APP_ENV=pr201 and SENTRY_DSN is set. | Fixtures: monkeypatch. Calls monkeypatch.setenv, patch, init_sentry. Verifies behavior via mocks/return value. | Init sentry enabled in pr201 | In scope: TestInitSentry. Out of scope: real LLM/network calls (mocked). |
| 105 |  | `TestInitSentry.test_init_sentry_disabled_in_dev` | Sentry does NOT initialize when APP_ENV=dev. | Fixtures: monkeypatch. Calls monkeypatch.setenv, patch, init_sentry. Verifies behavior via mocks/return value. | Init sentry disabled in dev | In scope: TestInitSentry. Out of scope: real LLM/network calls (mocked). |
| 106 |  | `TestInitSentry.test_init_sentry_disabled_without_dsn` | Sentry does NOT initialize when SENTRY_DSN is missing. | Fixtures: monkeypatch. Calls monkeypatch.setenv, monkeypatch.delenv, patch. Verifies behavior via mocks/return value. | Init sentry disabled without dsn | In scope: TestInitSentry. Out of scope: real LLM/network calls (mocked). |
| 107 |  | `TestInitSentry.test_init_sentry_disabled_with_empty_dsn` | Sentry does NOT initialize when SENTRY_DSN is empty string. | Fixtures: monkeypatch. Calls monkeypatch.setenv, patch, init_sentry. Verifies behavior via mocks/return value. | Init sentry disabled with empty dsn | In scope: TestInitSentry. Out of scope: real LLM/network calls (mocked). |
| 108 |  | `TestSetSentryTags.test_set_sentry_tags_sets_call_id` | set_sentry_tags sets call_id tag on scope. | Calls patch, set_sentry_tags, mock_set_tag.assert_called_once_with. Verifies behavior via mocks/return value. | Set sentry tags sets call id | In scope: TestSetSentryTags. Out of scope: real LLM/network calls (mocked). |
| 109 |  | `TestSetSentryTags.test_set_sentry_tags_sets_call_viewer_context` | set_sentry_tags sets Call Viewer context with URL. | Calls patch, set_sentry_tags, mock_set_context.assert_called_once_with. Verifies behavior via mocks/return value. | Set sentry tags sets call viewer context | In scope: TestSetSentryTags. Out of scope: real LLM/network calls (mocked). |
| 110 |  | `TestSetSentryTags.test_set_sentry_tags_safe_when_sentry_disabled` | set_sentry_tags is safe to call when Sentry is not initialized. | Fixtures: monkeypatch. Calls monkeypatch.setenv, monkeypatch.delenv, set_sentry_tags. Verifies behavior via mocks/return value. | Set sentry tags safe when sentry disabled | In scope: TestSetSentryTags. Out of scope: real LLM/network calls (mocked), no explicit assertions. |
| 111 |  | `TestLoggingIntegration.test_logger_error_captured_with_tags` | logger.error() triggers Sentry capture with call_id tag. | Fixtures: monkeypatch. Calls monkeypatch.setenv, patch, init_sentry. Asserts 3 conditions on result. | Logger error captured with tags | In scope: TestLoggingIntegration. Out of scope: real LLM/network calls (mocked). |
| 112 |  | `TestAgentErrorHandling.test_conversation_item_error_logs_error` | Verify logger.error is called when conversation item processing fails. | Calls MagicMock, property, logging.getLogger. Asserts 2 conditions on result. | Conversation item error logs error | In scope: TestAgentErrorHandling. Out of scope: real LLM/network calls (mocked). |

### Suggested Missing Tests
- **Sentry filtering of PHI** - Verify the `before_send` hook strips patient data.
- **Sentry envelope size** - Big payloads should be truncated.

### Improvement Suggestions / Irrelevant Tests
- **Mocked Sentry SDK** - Use `sentry_sdk.client.Client` test transport for higher fidelity.

## test_turn_metrics_accumulator.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 113 |  | `TestEouMetrics.test_eou_combined_with_stt_in_user_turn` | Eou combined with stt in user turn | Fixtures: accumulator, event_bus. Calls event_bus.subscribe, make_metrics_event, received.append. Asserts 4 conditions on result. | Eou combined with stt in user turn [async] | In scope: TestEouMetrics. Out of scope: integration with live services. |
| 114 |  | `TestEouMetrics.test_stt_without_eou_has_none_eou_fields` | Stt without eou has none eou fields | Fixtures: accumulator, event_bus. Calls event_bus.subscribe, received.append, accumulator.process. Asserts 2 conditions on result. | Stt without eou has none eou fields [async] | In scope: TestEouMetrics. Out of scope: integration with live services. |
| 115 |  | `TestEouMetrics.test_eou_cleared_after_consumption` | EOU data is consumed by the first STT; second STT has no EOU. | Fixtures: accumulator, event_bus. Calls event_bus.subscribe, make_metrics_event, received.append. Asserts 2 conditions on result. | Eou cleared after consumption [async] | In scope: TestEouMetrics. Out of scope: integration with live services. |
| 116 |  | `TestSttMetrics.test_stt_emits_user_turn_event` | Stt emits user turn event | Fixtures: accumulator, event_bus. Calls event_bus.subscribe, make_metrics_event, received.append. Asserts 5 conditions on result. | Stt emits user turn event [async] | In scope: TestSttMetrics. Out of scope: integration with live services. |
| 117 |  | `TestSttMetrics.test_stt_does_not_emit_llm_data` | Stt does not emit llm data | Fixtures: accumulator, event_bus. Calls event_bus.subscribe, received.append, accumulator.process. Asserts 2 conditions on result. | Stt does not emit llm data [async] | In scope: TestSttMetrics. Out of scope: integration with live services. |
| 118 |  | `TestLlmMetrics.test_llm_does_not_emit_immediately` | Llm does not emit immediately | Fixtures: accumulator, event_bus. Calls event_bus.subscribe, make_metrics_event, received.append. Asserts len(received) == 0. | Llm does not emit immediately [async] | In scope: TestLlmMetrics. Out of scope: integration with live services. |
| 119 |  | `TestTtsMetrics.test_tts_after_llm_emits_assistant_turn` | Tts after llm emits assistant turn | Fixtures: accumulator, event_bus. Calls event_bus.subscribe, make_metrics_event, received.append. Asserts 9 conditions on result. | Tts after llm emits assistant turn [async] | In scope: TestTtsMetrics. Out of scope: integration with live services. |
| 120 |  | `TestTtsMetrics.test_tts_without_prior_llm_emits_assistant_turn_with_only_tts` | Tts without prior llm emits assistant turn with only tts | Fixtures: accumulator, event_bus. Calls event_bus.subscribe, make_metrics_event, received.append. Asserts 4 conditions on result. | Tts without prior llm emits assistant turn with only tts [async] | In scope: TestTtsMetrics. Out of scope: integration with live services. |
| 121 |  | `TestFlush.test_flush_emits_pending_llm_without_tts` | Flush emits pending llm without tts | Fixtures: accumulator, event_bus. Calls event_bus.subscribe, make_metrics_event, received.append. Asserts 4 conditions on result. | Flush emits pending llm without tts [async] | In scope: TestFlush. Out of scope: integration with live services. |
| 122 |  | `TestFlush.test_flush_is_noop_when_no_pending` | Flush is noop when no pending | Fixtures: accumulator, event_bus. Calls event_bus.subscribe, received.append, accumulator.flush. Asserts len(received) == 0. | Flush is noop when no pending [async] | In scope: TestFlush. Out of scope: integration with live services. |

### Suggested Missing Tests
- **Setup-only file** - No tests — turn-level metrics drive every dashboard.

### Improvement Suggestions / Irrelevant Tests
- **Empty file** - No coverage.

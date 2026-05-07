# Helpers and Utilities Tests - Part B - Unit Test Mapping

> Repo: [Zocdoc/zo-agent](https://github.com/Zocdoc/zo-agent)
> Folder: `tests/` (root)
> Generated: 2026-05-07
> Stack: Python / pytest / LiveKit Agents

## test_no_split_tokenizer.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 |  | `TestNoSplitSentenceTokenizer.test_tokenize_returns_input_as_single_token` | Tokenize returns input as single token | Calls NoSplitSentenceTokenizer, tokenizer.tokenize. Asserts tokenizer.tokenize('Hello, world!') == ['Hello, world!']. | Tokenize returns input as single token | In scope: TestNoSplitSentenceTokenizer. Out of scope: integration with live services. |
| 2 |  | `TestNoSplitSentenceTokenizer.test_tokenize_strips_surrounding_whitespace` | Tokenize strips surrounding whitespace | Calls NoSplitSentenceTokenizer, tokenizer.tokenize. Asserts tokenizer.tokenize(' hi there ') == ['hi there']. | Tokenize strips surrounding whitespace | In scope: TestNoSplitSentenceTokenizer. Out of scope: integration with live services. |
| 3 |  | `TestNoSplitSentenceTokenizer.test_tokenize_does_not_split_on_punctuation` | Tokenize does not split on punctuation | Calls NoSplitSentenceTokenizer, tokenizer.tokenize. Asserts tokenizer.tokenize(text) == [text]. | Tokenize does not split on punctuation | In scope: TestNoSplitSentenceTokenizer. Out of scope: integration with live services. |
| 4 |  | `TestNoSplitSentenceTokenizer.test_tokenize_empty_returns_empty_list` | Tokenize empty returns empty list | Calls NoSplitSentenceTokenizer, tokenizer.tokenize. Asserts tokenizer.tokenize('') == []. | Tokenize empty returns empty list | In scope: TestNoSplitSentenceTokenizer. Out of scope: integration with live services. |
| 5 |  | `TestNoSplitSentenceTokenizer.test_tokenize_whitespace_only_returns_empty_list` | Tokenize whitespace only returns empty list | Calls NoSplitSentenceTokenizer, tokenizer.tokenize. Asserts tokenizer.tokenize(' \n\t ') == []. | Tokenize whitespace only returns empty list | In scope: TestNoSplitSentenceTokenizer. Out of scope: integration with live services. |
| 6 |  | `TestNoSplitSentenceTokenizer.test_stream_returns_no_split_sentence_stream` | Stream returns no split sentence stream | Calls NoSplitSentenceTokenizer, tokenizer.stream. Asserts isinstance(tokenizer.stream(), NoSplitSentenceStream). | Stream returns no split sentence stream | In scope: TestNoSplitSentenceTokenizer. Out of scope: integration with live services. |
| 7 |  | `TestNoSplitSentenceStream.test_push_then_flush_emits_single_token` | Push then flush emits single token | Calls NoSplitSentenceStream. Asserts 2 conditions on result. | Push then flush emits single token [async] | In scope: TestNoSplitSentenceStream. Out of scope: integration with live services. |
| 8 |  | `TestNoSplitSentenceStream.test_multiple_pushes_accumulate_into_one_emit` | Multiple pushes accumulate into one emit | Calls NoSplitSentenceStream. Asserts 2 conditions on result. | Multiple pushes accumulate into one emit [async] | In scope: TestNoSplitSentenceStream. Out of scope: integration with live services. |
| 9 |  | `TestNoSplitSentenceStream.test_end_input_emits_buffer_and_closes` | End input emits buffer and closes | Calls NoSplitSentenceStream. Asserts 3 conditions on result. | End input emits buffer and closes [async] | In scope: TestNoSplitSentenceStream. Out of scope: integration with live services. |
| 10 |  | `TestNoSplitSentenceStream.test_end_input_with_empty_buffer_emits_nothing` | End input with empty buffer emits nothing | Calls NoSplitSentenceStream. Asserts 2 conditions on result. | End input with empty buffer emits nothing [async] | In scope: TestNoSplitSentenceStream. Out of scope: integration with live services. |
| 11 |  | `TestNoSplitSentenceStream.test_whitespace_only_buffer_emits_nothing` | Whitespace only buffer emits nothing | Calls NoSplitSentenceStream. Asserts tokens == []. | Whitespace only buffer emits nothing [async] | In scope: TestNoSplitSentenceStream. Out of scope: integration with live services. |
| 12 |  | `TestNoSplitSentenceStream.test_flush_does_not_close_stream` | Flush does not close stream | Calls NoSplitSentenceStream. Asserts [t.token for t in tokens] == ['first', 'second']. | Flush does not close stream [async] | In scope: TestNoSplitSentenceStream. Out of scope: integration with live services. |
| 13 |  | `TestNoSplitSentenceStream.test_does_not_split_on_punctuation_or_whitespace` | Does not split on punctuation or whitespace | Calls NoSplitSentenceStream. Asserts 2 conditions on result. | Does not split on punctuation or whitespace [async] | In scope: TestNoSplitSentenceStream. Out of scope: integration with live services. |
| 14 |  | `TestNoSplitSentenceStream.test_push_after_end_input_raises` | Push after end input raises | Calls NoSplitSentenceStream, pytest.raises. Verifies behavior via mocks/return value. | Push after end input raises | In scope: TestNoSplitSentenceStream. Out of scope: real LLM/network calls (mocked), no explicit assertions. |
| 15 |  | `TestNoSplitSentenceStream.test_aclose_is_idempotent` | Aclose is idempotent | Calls NoSplitSentenceStream. Asserts 2 conditions on result. | Aclose is idempotent [async] | In scope: TestNoSplitSentenceStream. Out of scope: integration with live services. |
| 16 |  | `TestNoSplitSentenceStream.test_aclose_after_end_input_does_not_raise` | Aclose after end input does not raise | Calls NoSplitSentenceStream. Asserts stream.closed. | Aclose after end input does not raise [async] | In scope: TestNoSplitSentenceStream. Out of scope: integration with live services. |

### Suggested Missing Tests
- **Multibyte / accented chars** - Spanish names with tildes — verify tokenizer keeps them whole.

### Improvement Suggestions / Irrelevant Tests
- **Tokenizer parametrization** - Use parametrize for the input/output table style.

## test_session_state.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 17 |  | `test_session_state_initialization` | Test that SessionState initializes with required fields and None values for optional fields. | Fixtures: mock_practice_info. Calls SessionState. Asserts 18 conditions on result. | Session state initialization | In scope: unit-level behavior. Out of scope: real LLM/network calls (mocked). |
| 18 |  | `test_session_state_with_values` | Test that SessionState can be initialized with values. | Fixtures: mock_practice_info. Calls SessionState. Asserts 4 conditions on result. | Session state with values | In scope: unit-level behavior. Out of scope: real LLM/network calls (mocked). |
| 19 |  | `test_session_state_flow_control_fields` | Test that flow control fields work correctly. | Fixtures: mock_practice_info. Calls SessionState. Asserts 7 conditions on result. | Session state flow control fields | In scope: unit-level behavior. Out of scope: real LLM/network calls (mocked). |
| 20 |  | `TestSessionStateBookingIdFields.test_provider_id_defaults_to_none` | Provider id defaults to none | Calls make_session_state. Asserts make_session_state().provider_id is None. | Provider id defaults to none | In scope: TestSessionStateBookingIdFields. Out of scope: integration with live services. |
| 21 |  | `TestSessionStateBookingIdFields.test_location_id_defaults_to_none` | Location id defaults to none | Calls make_session_state. Asserts make_session_state().location_id is None. | Location id defaults to none | In scope: TestSessionStateBookingIdFields. Out of scope: integration with live services. |
| 22 |  | `TestSessionStateBookingIdFields.test_is_new_patient_defaults_to_none` | Is new patient defaults to none | Calls make_session_state. Asserts make_session_state().is_new_patient is None. | Is new patient defaults to none | In scope: TestSessionStateBookingIdFields. Out of scope: integration with live services. |
| 23 |  | `TestSessionStateBookingIdFields.test_previous_location_id_defaults_to_none` | Previous location id defaults to none | Calls make_session_state. Asserts make_session_state().previous_location_id is None. | Previous location id defaults to none | In scope: TestSessionStateBookingIdFields. Out of scope: integration with live services. |
| 24 |  | `TestSessionStateBookingIdFields.test_previous_provider_id_defaults_to_none` | Previous provider id defaults to none | Calls make_session_state. Asserts make_session_state().previous_provider_id is None. | Previous provider id defaults to none | In scope: TestSessionStateBookingIdFields. Out of scope: integration with live services. |
| 25 |  | `TestSessionStateBookingIdFields.test_previous_provider_defaults_to_none` | Previous provider defaults to none | Calls make_session_state. Asserts make_session_state().previous_provider is None. | Previous provider defaults to none | In scope: TestSessionStateBookingIdFields. Out of scope: integration with live services. |

### Suggested Missing Tests
- **State serialization round-trip** - Pickle / JSON dump and load — does state preserve all fields?
- **Concurrent mutation** - Two coroutines updating state simultaneously.

### Improvement Suggestions / Irrelevant Tests
- **State setter tests duplicate** - Many tests just set + read; collapse via parametrize.

## test_shared_prompts.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 26 |  | `TestGetLanguageResponseInstructions.test_english_returns_empty` | English returns empty | Calls get_language_response_instructions. Asserts get_language_response_instructions('en-US') == ''. | English returns empty | In scope: TestGetLanguageResponseInstructions. Out of scope: integration with live services. |
| 27 |  | `TestGetLanguageResponseInstructions.test_bare_english_returns_empty` | Bare english returns empty | Calls get_language_response_instructions. Asserts get_language_response_instructions('en') == ''. | Bare english returns empty | In scope: TestGetLanguageResponseInstructions. Out of scope: integration with live services. |
| 28 |  | `TestGetLanguageResponseInstructions.test_spanish_includes_language_directive` | Spanish includes language directive | Calls get_language_response_instructions. Asserts 'MUST respond in Spanish' in result. | Spanish includes language directive | In scope: TestGetLanguageResponseInstructions. Out of scope: integration with live services. |
| 29 |  | `TestGetLanguageResponseInstructions.test_spanish_includes_formal_register` | Spanish includes formal register | Calls get_language_response_instructions. Asserts 'formal register' in result. | Spanish includes formal register | In scope: TestGetLanguageResponseInstructions. Out of scope: integration with live services. |
| 30 |  | `TestGetLanguageResponseInstructions.test_bare_spanish_locale` | Bare spanish locale | Calls get_language_response_instructions. Asserts 'MUST respond in Spanish' in result. | Bare spanish locale | In scope: TestGetLanguageResponseInstructions. Out of scope: integration with live services. |

### Suggested Missing Tests
- **Variable interpolation missing arg** - Prompt template references `{patient_name}` but no value provided — current behavior?

### Improvement Suggestions / Irrelevant Tests
- **Snapshot drift risk** - Prompts are large strings; use snapshot files instead of inline triple-quotes.

## test_sip_helpers.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 31 |  | `TestExtractSipInfo.test_extracts_all_attributes_from_sip_participant` | Should extract phone numbers, twilio_call_sid, identity, and hostname. | Calls _make_mock_participant, _make_mock_config, extract_sip_info. Asserts 5 conditions on result. | Extracts all attributes from sip participant | In scope: TestExtractSipInfo. Out of scope: real LLM/network calls (mocked). |
| 32 |  | `TestExtractSipInfo.test_optional_fields_none_when_missing` | Should set twilio_call_sid and hostname to None when not present. | Calls _make_mock_participant, _make_mock_config, extract_sip_info. Asserts 5 conditions on result. | Optional fields none when missing | In scope: TestExtractSipInfo. Out of scope: real LLM/network calls (mocked). |
| 33 |  | `TestExtractSipInfo.test_raises_when_to_phone_number_missing` | Should raise RuntimeError when sip.trunkPhoneNumber is missing. | Calls _make_mock_participant, _make_mock_config, pytest.raises. Verifies behavior via mocks/return value. | Raises when to phone number missing | In scope: TestExtractSipInfo. Out of scope: real LLM/network calls (mocked), no explicit assertions. |
| 34 |  | `TestExtractSipInfo.test_raises_when_from_phone_number_missing` | Should raise RuntimeError when sip.phoneNumber is missing. | Calls _make_mock_participant, _make_mock_config, pytest.raises. Verifies behavior via mocks/return value. | Raises when from phone number missing | In scope: TestExtractSipInfo. Out of scope: real LLM/network calls (mocked), no explicit assertions. |
| 35 |  | `TestExtractSipInfo.test_returns_mock_info_for_standard_participant_in_dev_ci` | Should return mock info for STANDARD participant in dev/ci. | Fixtures: app_env. Calls _make_mock_participant, _make_mock_config, extract_sip_info. Asserts 5 conditions on result. | Returns mock info for standard participant in dev ci (parametrized) | In scope: TestExtractSipInfo. Out of scope: real LLM/network calls (mocked). |
| 36 |  | `TestExtractSipInfo.test_raises_for_standard_participant_in_production` | Should raise RuntimeError for STANDARD participant in non-dev/ci. | Fixtures: app_env. Calls _make_mock_participant, _make_mock_config, pytest.raises. Verifies behavior via mocks/return value. | Raises for standard participant in production (parametrized) | In scope: TestExtractSipInfo. Out of scope: real LLM/network calls (mocked), no explicit assertions. |
| 37 |  | `TestExtractSipInfo.test_raises_for_standard_participant_without_defaults` | Should raise RuntimeError when STANDARD participant and no defaults. | Fixtures: app_env. Calls _make_mock_participant, _make_mock_config, pytest.raises. Verifies behavior via mocks/return value. | Raises for standard participant without defaults (parametrized) | In scope: TestExtractSipInfo. Out of scope: real LLM/network calls (mocked), no explicit assertions. |
| 38 |  | `TestExtractSipInfo.test_raises_for_unexpected_participant_kind` | Should raise RuntimeError for unexpected participant kind. | Calls _make_mock_participant, _make_mock_config, pytest.raises. Verifies behavior via mocks/return value. | Raises for unexpected participant kind | In scope: TestExtractSipInfo. Out of scope: real LLM/network calls (mocked), no explicit assertions. |
| 39 |  | `TestFindSipParticipant.test_returns_sip_info_when_sip_participant_present` | Should return SipParticipantInfo when SIP participant exists in room. | Calls _make_mock_participant, MagicMock, find_sip_participant. Asserts 5 conditions on result. | Returns sip info when sip participant present | In scope: TestFindSipParticipant. Out of scope: real LLM/network calls (mocked). |
| 40 |  | `TestFindSipParticipant.test_returns_none_when_no_sip_participant` | Should return None when no SIP participant in room. | Calls _make_mock_participant, MagicMock, find_sip_participant. Asserts result is None. | Returns none when no sip participant | In scope: TestFindSipParticipant. Out of scope: real LLM/network calls (mocked). |
| 41 |  | `TestFindSipParticipant.test_returns_none_when_room_empty` | Should return None when room has no remote participants. | Calls MagicMock, find_sip_participant. Asserts result is None. | Returns none when room empty | In scope: TestFindSipParticipant. Out of scope: real LLM/network calls (mocked). |
| 42 |  | `TestFindSipParticipant.test_finds_sip_among_multiple_participants` | Should find SIP participant among multiple participants. | Calls _make_mock_participant, MagicMock, find_sip_participant. Asserts 2 conditions on result. | Finds sip among multiple participants | In scope: TestFindSipParticipant. Out of scope: real LLM/network calls (mocked). |
| 43 |  | `TestSipParticipantInfo.test_creates_with_all_fields` | Should create instance with all fields. | Calls SipParticipantInfo. Asserts 5 conditions on result. | Creates with all fields | In scope: TestSipParticipantInfo. Out of scope: integration with live services. |
| 44 |  | `TestSipParticipantInfo.test_creates_with_none_optional_fields` | Should allow None for twilio_call_sid, participant_identity, and hostname. | Calls SipParticipantInfo. Asserts 3 conditions on result. | Creates with none optional fields | In scope: TestSipParticipantInfo. Out of scope: integration with live services. |

### Suggested Missing Tests
- **Malformed SIP URI** - Verify graceful error rather than crash.
- **SIP transfer status mapping** - Map every SIP response code (200/302/486/487/503).

### Improvement Suggestions / Irrelevant Tests
- **Real SIP fixture** - Helpers tested with synthetic strings; consider a captured trace fixture.

## test_synthetic_tool_items.py

(setup-only file - no test functions)


### Suggested Missing Tests
- **Setup-only file** - No tests detected — synthetic tool items are how the agent injects fake assistant turns; gaps risk silent regressions.

### Improvement Suggestions / Irrelevant Tests
- **Empty file** - No coverage at all.

## test_time_helpers.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 45 |  | `TestGetTimePlaceholders.test_returns_all_required_placeholders` | Should return the_current_time, today, and tomorrow. | Calls get_time_placeholders. Asserts 3 conditions on result. | Returns all required placeholders | In scope: TestGetTimePlaceholders. Out of scope: integration with live services. |
| 46 |  | `TestGetTimePlaceholders.test_formats_in_practice_timezone` | Should use the practice's timezone for all dates. | Calls get_time_placeholders. Asserts est_result['the_current_time'] != pst_result['the_current_time']. | Formats in practice timezone | In scope: TestGetTimePlaceholders. Out of scope: integration with live services. |
| 47 |  | `TestGetTimePlaceholders.test_formats_with_natural_speech` | Should format dates in natural speech format. | Calls get_time_placeholders, re.match. Asserts 3 conditions on result. | Formats with natural speech | In scope: TestGetTimePlaceholders. Out of scope: integration with live services. |
| 48 |  | `TestGetTimePlaceholders.test_tomorrow_is_one_day_after_today` | Tomorrow should be exactly one day after today. | Calls get_time_placeholders, re.search, today_match.group. Asserts 3 conditions on result. | Tomorrow is one day after today | In scope: TestGetTimePlaceholders. Out of scope: integration with live services. |
| 49 |  | `TestGetTimePlaceholders.test_supports_various_timezones` | Should work with various IANA timezones. | Fixtures: timezone. Calls get_time_placeholders. Asserts 3 conditions on result. | Supports various timezones (parametrized) | In scope: TestGetTimePlaceholders. Out of scope: integration with live services. |
| 50 |  | `TestGetTimePlaceholders.test_returns_day_of_week_placeholder` | Should return day_of_week as the current day name. | Calls get_time_placeholders. Asserts 2 conditions on result. | Returns day of week placeholder | In scope: TestGetTimePlaceholders. Out of scope: integration with live services. |
| 51 |  | `TestGetTimePlaceholders.test_ordinal_suffixes_correct` | Ordinal suffixes should be grammatically correct. | Calls get_time_placeholders, re.search, match.group. Asserts 6 conditions on result. | Ordinal suffixes correct | In scope: TestGetTimePlaceholders. Out of scope: integration with live services. |
| 52 |  | `TestSpokenTime.test_spoken_time` | Spoken time | Fixtures: time_str, expected. Calls spoken_time. Asserts spoken_time(time_str) == expected. | Spoken time (parametrized) | In scope: TestSpokenTime. Out of scope: integration with live services. |
| 53 |  | `TestSanitizeTimesForTts.test_sanitize_times` | Sanitize times | Fixtures: text, expected. Calls sanitize_times_for_tts. Asserts sanitize_times_for_tts(text) == expected. | Sanitize times (parametrized) | In scope: TestSanitizeTimesForTts. Out of scope: integration with live services. |

### Suggested Missing Tests
- **Timezone with DST in spring-forward window** - Times that "do not exist" need explicit handling.
- **Locale-aware "tomorrow"** - Different timezone "now" — what does "tomorrow" mean?

### Improvement Suggestions / Irrelevant Tests
- **Frozen time** - Use `freezegun` to stabilize tests instead of mocking `datetime.now`.

## test_user_input.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 54 |  | `TestSanitizeUserInput.test_blank_inputs` | Blank inputs | Fixtures: raw, expected. Calls sanitize_user_input. Asserts sanitize_user_input(raw) == expected. | Blank inputs (parametrized) | In scope: TestSanitizeUserInput. Out of scope: integration with live services. |
| 55 |  | `TestSanitizeUserInput.test_strips_unsafe_characters` | Strips unsafe characters | Fixtures: char. Calls sanitize_user_input. Asserts sanitize_user_input(f'hello{char}world') == 'helloworld'. | Strips unsafe characters (parametrized) | In scope: TestSanitizeUserInput. Out of scope: integration with live services. |
| 56 |  | `TestSanitizeUserInput.test_collapses_whitespace` | Collapses whitespace | Calls sanitize_user_input. Asserts sanitize_user_input(' a b ') == 'a b'. | Collapses whitespace | In scope: TestSanitizeUserInput. Out of scope: integration with live services. |
| 57 |  | `TestSanitizeUserInput.test_preserves_long_input` | Preserves long input | Calls sanitize_user_input, long_input.strip. Asserts sanitize_user_input(long_input) == long_input.strip(). | Preserves long input | In scope: TestSanitizeUserInput. Out of scope: integration with live services. |
| 58 |  | `TestUnsafePhraseRemoval.test_removes_unsafe_phrases` | Removes unsafe phrases | Fixtures: raw, expected. Calls sanitize_user_input. Asserts result == expected. | Removes unsafe phrases (parametrized) | In scope: TestUnsafePhraseRemoval. Out of scope: integration with live services. |
| 59 |  | `TestFixTimeFormat.test_time_leading_zero` | Time leading zero | Fixtures: raw, expected. Calls sanitize_user_input. Asserts sanitize_user_input(raw) == expected. | Time leading zero (parametrized) | In scope: TestFixTimeFormat. Out of scope: integration with live services. |
| 60 |  | `TestFixDateOrdinals.test_date_ordinals` | Date ordinals | Fixtures: raw, expected. Calls sanitize_user_input. Asserts sanitize_user_input(raw) == expected. | Date ordinals (parametrized) | In scope: TestFixDateOrdinals. Out of scope: integration with live services. |
| 61 |  | `TestFixDateOrdinals.test_invalid_month_date_leaves_entire_input_unchanged` | Invalid month date leaves entire input unchanged | Calls sanitize_user_input. Asserts sanitize_user_input(raw) == raw. | Invalid month date leaves entire input unchanged | In scope: TestFixDateOrdinals. Out of scope: integration with live services. |
| 62 |  | `TestOrdinalSuffix.test_suffix` | Suffix | Fixtures: raw, suffix. Calls sanitize_user_input, result.endswith. Asserts result.endswith(suffix). | Suffix (parametrized) | In scope: TestOrdinalSuffix. Out of scope: integration with live services. |

### Suggested Missing Tests
- **Empty input string** - Bare whitespace from ASR — handle without raising.
- **Profanity / wake-word collision** - Sanitization before passing to LLM.

### Improvement Suggestions / Irrelevant Tests
- **Limited language coverage** - Add Spanish input cases.

## test_variation_loader.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 63 |  | `TestLoadVariations.test_loads_valid_file` | Loads valid file | Fixtures: tmp_path. Calls _write_json, load_variations. Asserts 3 conditions on result. | Loads valid file | In scope: TestLoadVariations. Out of scope: integration with live services. |
| 64 |  | `TestLoadVariations.test_missing_turn_raises` | Missing turn raises | Fixtures: tmp_path. Calls _write_json, pytest.raises, load_variations. Verifies behavior via mocks/return value. | Missing turn raises | In scope: TestLoadVariations. Out of scope: real LLM/network calls (mocked), no explicit assertions. |
| 65 |  | `TestLoadVariations.test_file_not_found_raises` | File not found raises | Calls pytest.raises, load_variations. Verifies behavior via mocks/return value. | File not found raises | In scope: TestLoadVariations. Out of scope: real LLM/network calls (mocked), no explicit assertions. |
| 66 |  | `TestLoadVariations.test_empty_variations_returns_empty_list` | Empty variations returns empty list | Fixtures: tmp_path. Calls _write_json, load_variations. Asserts result == []. | Empty variations returns empty list | In scope: TestLoadVariations. Out of scope: integration with live services. |
| 67 |  | `TestLoadVariations.test_missing_variations_key_raises` | Missing variations key raises | Fixtures: tmp_path. Calls _write_json, pytest.raises, load_variations. Verifies behavior via mocks/return value. | Missing variations key raises | In scope: TestLoadVariations. Out of scope: real LLM/network calls (mocked), no explicit assertions. |
| 68 |  | `TestLoadVariations.test_invalid_json_raises` | Invalid json raises | Fixtures: tmp_path. Calls path.write_text, pytest.raises, load_variations. Verifies behavior via mocks/return value. | Invalid json raises | In scope: TestLoadVariations. Out of scope: real LLM/network calls (mocked), no explicit assertions. |
| 69 |  | `TestLoadVariations.test_missing_inputs_key_raises` | Missing inputs key raises | Fixtures: tmp_path. Calls _write_json, pytest.raises, load_variations. Verifies behavior via mocks/return value. | Missing inputs key raises | In scope: TestLoadVariations. Out of scope: real LLM/network calls (mocked), no explicit assertions. |

### Suggested Missing Tests
- **Missing variation file** - Loader should raise a clear error — not return empty silently.
- **Cache invalidation** - After file edit, does loader pick up changes (or is caching by design)?

### Improvement Suggestions / Irrelevant Tests
- **IO mocked entirely** - Loader behavior under real filesystem with permission errors not tested.

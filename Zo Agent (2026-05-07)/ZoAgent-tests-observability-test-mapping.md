# Observability - Unit Test Mapping

> Repo: [Zocdoc/zo-agent](https://github.com/Zocdoc/zo-agent)
> Folder: `tests/observability/`
> Generated: 2026-05-07
> Stack: Python / pytest / LiveKit Agents

## test_call_cost_tracker.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | TestCallCostTracker::test_initial_summary_is_zero | Fresh tracker reports zero tokens, requests, and cost. | Build tracker; read `summary`; assert all zero. | Init state. | In scope: defaults. Out of scope: emission. |
| 2 | | TestCallCostTracker::test_single_record | Single `record()` updates totals (input/output tokens, requests, cost). | Build tracker; record; assert summary fields. | Single record. | In scope: accumulation. Out of scope: estimation. |
| 3 | | TestCallCostTracker::test_multiple_records_accumulate | Multiple `record()` calls accumulate totals. | Record 3; assert sums and request count. | Accumulation. | In scope: accumulation. Out of scope: estimation. |
| 4 | | TestCallCostTracker::test_none_cost_still_counts_request_and_tokens | `cost_usd=None` records still count requests/tokens but don't add to cost. | Record None cost; assert tokens counted, cost stays 0. | None-cost behavior. | In scope: accumulation. Out of scope: estimation. |
| 5 | | TestCallCostTracker::test_mixed_known_and_unknown_costs | Mixed known + unknown costs sum only known. | Record 3 mixed; assert. | Mixed accumulation. | In scope: accumulation. Out of scope: estimation. |
| 6 | | TestCallCostTracker::test_record_stores_line_items_with_metadata | Per-record metadata (model, agent, cached, ttft, duration) stored as `LLMRequestRecord`. | Record with all fields; assert. | Line item shape. | In scope: line item. Out of scope: serialization. |
| 7 | | TestCallCostTracker::test_record_defaults_optional_metadata | Optional metadata defaults: model None, agent_name "unknown", cached/ttft/duration zero. | Record minimal; assert. | Defaults. | In scope: defaults. Out of scope: line item. |
| 8 | | TestProcessMetricsEvent::test_records_llm_metrics | `process_metrics_event` ingests `LLMMetrics` and records tokens. | Build LLM event; process; assert summary. | Bridge happy. | In scope: bridge. Out of scope: LiveKit event shape. |
| 9 | | TestProcessMetricsEvent::test_ignores_non_llm_metrics | STT events ignored (no record). | Build STT event; process; assert summary unchanged. | Bridge filter. | In scope: filter. Out of scope: STT. |
| 10 | | TestProcessMetricsEvent::test_extracts_model_name | Model name extracted from event metadata onto record. | Build with model; process; assert `r.model`. | Extraction. | In scope: extraction. Out of scope: pricing. |
| 11 | | TestProcessMetricsEvent::test_handles_missing_metadata | Missing metadata -> `model=None`, `cost_usd=None`. | Build with `model_name=None`; process; assert. | Defensive. | In scope: defensive. Out of scope: estimation. |
| 12 | | TestProcessMetricsEvent::test_estimates_cost_for_known_model | Known model produces correct USD cost from token counts. | Build with gemini-2.5-flash 1M+1M tokens; process; assert `cost_usd ~= 2.80`. | Cost estimation. | In scope: estimation. Out of scope: pricing source. |
| 13 | | TestProcessMetricsEvent::test_returns_none_cost_for_unknown_model | Unknown model -> `cost_usd=None`. | Build with unknown model; process; assert. | Defensive. | In scope: defensive. Out of scope: pricing. |
| 14 | | TestProcessMetricsEvent::test_converts_timing_to_milliseconds | Seconds converted to ms (ttft, duration). | Build with `ttft=0.25, duration=1.8`; process; assert ms values. | Unit conversion. | In scope: unit. Out of scope: source units. |
| 15 | | TestProcessMetricsEvent::test_records_cached_tokens | `prompt_cached_tokens` mapped to `cached_tokens`. | Build with cached=50; process; assert. | Mapping. | In scope: mapping. Out of scope: caching. |
| 16 | | TestProcessMetricsEvent::test_records_agent_name | `process_metrics_event(ev, agent_name=...)` stores agent name. | Process with agent_name; assert. | Mapping. | In scope: mapping. Out of scope: agent identity. |
| 17 | | TestProcessMetricsEvent::test_defaults_agent_name_to_unknown | Without agent_name -> "unknown". | Process without; assert. | Defaults. | In scope: default. Out of scope: agent identity. |
| 18 | | TestProcessMetricsEvent::test_multiple_events_accumulate | Multiple events -> 2 records with distinct agent names. | Process 2; assert. | Accumulation. | In scope: accumulation. Out of scope: per-agent rollup. |

### Suggested Missing Tests
- **Cost budget breaches** - `record()` should emit a metric/event when call exceeds a configured budget; no test today.
- **Concurrent record() from multiple coroutines** - assert thread/async safety.
- **PHI scrubbing in line items** - ensure no DOB/name/phone is stored in `LLMRequestRecord` (e.g., agent_name should not be a patient identifier).
- **Negative tokens / negative cost** - defensively reject or clamp.
- **Floating-point sum precision** - many small `cost_usd` values; verify summary cost stays consistent.
- **Pricing version drift** - if `model_pricing` changes mid-call, summary should still be deterministic.

### Improvement Suggestions / Irrelevant Tests
- **`_make_llm_event` and `_make_stt_event`** - extract to shared conftest so other observability tests can reuse.
- **18 tests for one tracker** - well covered; consider splitting into `_record.py` and `_process_metrics_event.py` if file grows.

## test_cost.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | TestEstimateCost::test_known_model | `estimate_cost` produces correct USD totals for 6 (model, in, out) cases including pro/flash/flash-lite/zero-token. | Parametrized 6; assert via `pytest.approx`. | Pricing math. | In scope: math. Out of scope: pricing source. |
| 2 | | TestEstimateCost::test_unknown_model_returns_none | Unknown model returns None. | Call with `unknown/model`; assert None. | Defensive. | In scope: defensive. Out of scope: pricing. |
| 3 | | TestEstimateCost::test_model_pricing_has_required_keys | Every entry in `model_pricing` has `input_per_1m` and `output_per_1m`. | Iterate dict; assert keys. | Schema invariant. | In scope: schema. Out of scope: prices. |

### Suggested Missing Tests
- **Cached-token pricing** - many providers price cached input lower; verify estimator accounts for this (or document that it doesn't).
- **Pricing drift** - assert prices match a sourced reference (e.g., a JSON snapshot that auditors can review).
- **Negative tokens** - defensive guard.
- **Very large counts** (1B tokens) - precision and overflow.
- **Per-model deprecation** - removing a model should not break tests for older line items.

### Improvement Suggestions / Irrelevant Tests
- **Tiny file (53 lines)** - acceptable, but consider including a `currency` test (assume USD only) to lock the contract.
- **Hard-coded prices in test names** ("2.5-pro-both" etc.) become outdated if pricing shifts; consider sourcing prices via a fixture.

## test_event_bus.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | TestEventBus::test_subscribe_and_publish | Event published is delivered to subscribed handler. | Subscribe handler; publish event; await asyncio.Event with timeout; assert handler ran with event. | Pub/sub happy. | In scope: dispatch. Out of scope: handler logic. |
| 2 | | TestEventBus::test_multiple_handlers_for_same_event | Multiple handlers all receive same event. | Subscribe 2; publish; sleep 0.1s; assert both ran. | Fan-out. | In scope: fan-out. Out of scope: order. |
| 3 | | TestEventBus::test_handler_error_isolation | Exception in one handler does not block others. | Subscribe failing + successful; publish; assert successful still ran. | Error isolation. | In scope: error containment. Out of scope: error reporting. |
| 4 | | TestEventBus::test_no_handlers_registered | Publishing with no subscribers does not raise. | Publish CallEndedEvent without subscribers; assert no exception. | Edge. | In scope: edge. Out of scope: dropped events. |

### Suggested Missing Tests
- **Event ordering guarantees** - publish A, B, C; assert handlers see them in order (especially within same event type).
- **Dropped events on overflow** - if bus is bounded, simulate backpressure; assert metric/log emitted, not silently dropped.
- **Handler timeout** - long-running handler should not block other handlers (test concurrency model).
- **Drain semantics** - after `bus.drain()`, assert no in-flight handlers remain.
- **Unsubscribe** - currently no API tested; if it exists, cover.
- **Subscribe to base class** - if event hierarchy used, verify subclass events delivered to base subscribers (or not, per design).
- **PHI in events** - assert `to_dict()` of an event with patient data scrubs PHI before any logger writes (cross-cutting with `test_events.py`).
- **`asyncio.sleep(0.1)` is flaky** - replace with deterministic event-completion synchronization in `test_multiple_handlers_for_same_event` and `test_handler_error_isolation` (mark `@pytest.mark.flaky` if not refactored).

### Improvement Suggestions / Irrelevant Tests
- **`asyncio.sleep(0.1)`** appears 3x as a synchronization primitive; this is the #1 flake risk in the file. Use `asyncio.Event` or `bus.drain()` consistently (the first test does this correctly).
- **No assertion that handler ran on a separate task** - if the bus is supposed to dispatch async, add a test that asserts publish returns before handler completes.

## test_events.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | TestEventSerialization::test_call_started_event_serialization | `CallStartedEvent.to_dict()` produces expected ISO timestamp and field shape. | Build event; call to_dict; assert keys/values. | Serialization. | In scope: shape. Out of scope: bus dispatch. |
| 2 | | TestEventSerialization::test_turn_complete_event_with_nulls | `TurnCompleteEvent` with all metric nulls serializes them as None. | Build minimal; to_dict; assert nulls. | Null safety. | In scope: shape. Out of scope: metric source. |
| 3 | | TestEventSerialization::test_turn_complete_event_with_cost_fields | `TurnCompleteEvent` cost fields (model, cached_tokens, tokens_per_second, estimated_cost_usd) serialize. | Build full; to_dict; assert. | Shape (cost). | In scope: shape. Out of scope: cost calc. |
| 4 | | TestEventSerialization::test_call_ended_event_with_cost_totals | `CallEndedEvent` cost totals serialize. | Build with totals; to_dict; assert. | Shape (totals). | In scope: shape. Out of scope: rollup logic. |
| 5 | | TestEventSerialization::test_call_ended_event_with_line_items | `CallEndedEvent.llm_request_records` serializes per-request items. | Build with 2 records; to_dict; assert list shape. | Shape (line items). | In scope: shape. Out of scope: tracker. |
| 6 | | TestEventImmutability::test_events_are_frozen | Events are frozen dataclasses (cannot mutate after creation). | Build event; assign field; expect AttributeError. | Frozen invariant. | In scope: frozen. Out of scope: copy semantics. |

### Suggested Missing Tests
- **PHI scrubbing on serialization** - if `to_dict()` is the canonical log payload, assert it scrubs/redacts patient_id, phone, DOB before logging.
- **Round-trip** - `to_dict()` -> JSON -> back; assert event reconstructable for replay.
- **Schema versioning** - events should carry `schema_version`; assert presence (or document absence).
- **Timestamp timezone** - assert `to_dict()` always emits TZ-aware ISO 8601 (`+00:00`); a naive datetime should error.
- **Equality / hash for deduplication** - two events with same fields should/shouldn't be equal per design.
- **`TurnCompleteEvent` for user vs assistant turn** - assert speaker_role serialization.

### Improvement Suggestions / Irrelevant Tests
- **`from_phone_number=None` test path** - could be promoted to a parametrized test of optional fields.
- **Manual datetime construction** is verbose; consider a helper for events.

## test_utils.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | TestTurnCounter::test_turn_counter_starts_at_zero | New `TurnCounter` initializes to 0. | Build; assert. | Init. | In scope: init. Out of scope: persistence. |
| 2 | | TestTurnCounter::test_turn_counter_increments | `increment()` returns post-increment value (1, 2, 3). | Increment 3x; assert returns and `count`. | Increment. | In scope: counter. Out of scope: thread safety. |
| 3 | | TestTurnCounter::test_count_property_does_not_increment | Reading `count` is idempotent. | Increment 1; read count twice; assert both 1. | Read idempotency. | In scope: counter. Out of scope: persistence. |

### Suggested Missing Tests
- **Concurrent increments** - race between two coroutines calling `increment()`; verify atomicity.
- **`reset()` if it exists** - cover.
- **Negative increment** - if `decrement()` exists, cover; otherwise document non-existence.

### Improvement Suggestions / Irrelevant Tests
- **Smallest test file (29 lines)** - intentionally minimal, OK as is. Add concurrency test if `TurnCounter` is shared across handlers.

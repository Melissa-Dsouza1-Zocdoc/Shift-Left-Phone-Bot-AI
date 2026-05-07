# tests/benchmarks/ - Benchmark Test Mapping

> Repo: [Zocdoc/zo-agent](https://github.com/Zocdoc/zo-agent)
> Folder: `tests/benchmarks/`
> Generated: 2026-05-07
> Stack: Python / pytest / LiveKit Agents
>
> These are latency benchmarks rather than correctness tests. They measure per-turn wall-clock latency of `session.run()` against the real Gemini Vertex AI LLM (`gemini-2.5-flash`) with `thinking_budget=0` vs default thinking enabled. Tool calls hit fixture/mock data via `_AuthAgent.with_mock_patients(...)` and `_ManageAppointmentAgent.with_*_goal(...)`, but the LLM and the LiveKit agent stack are real, so this is closer to a load-test / regression-tracking harness than a pure unit-level benchmark.

## conftest.py
(setup file - shared fixtures for benchmark runs)
Registers the `benchmark` pytest marker, builds a parameterized `benchmark_llm` fixture that produces two `livekit.plugins.google.LLM` instances (one with `ThinkingConfig(thinking_budget=0)`, one with thinking on) targeting Vertex AI project `phoneai-dev` / `us-central1`, and exposes a session-scoped `combined_collector` `MetricsCollector` that calls `write_report(...)` on teardown so both parameterizations land in a single side-by-side report.

## metrics.py / report.py / runner.py
(helper modules - not tests)

- **metrics.py** — defines the `TurnMetric` dataclass (scenario, agent, turn index/name, config label, rep index, `latency_ms`) and `MetricsCollector` with `add()`, `filter()` and `latencies()` helpers. No percentile math here, just storage.
- **report.py** — computes summary stats (n / min / p50 / p75 / p90 / p95 / max / mean ± stddev) from collected latencies via `numpy`, then renders an executive summary, aggregate table, per-scenario breakdown, per-agent breakdown, and raw data table. `write_report()` prints to stderr and writes `benchmark-results/latency-report-YYYY-MM-DD.md` locally.
- **runner.py** — `run_benchmark_scenario()` mirrors the eval-suite's cumulative runner but replaces expectation validation with `time.monotonic()` deltas around each `session.run()` / `session.start()` call. After each turn it polls `session._activity._scheduling_paused` (`_wait_for_activity_ready`, 5s timeout) before recording the metric.

## test_latency_benchmark.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | `TestLatencyBenchmark::test_benchmark[thinking_off]` | Per-turn wall-clock latency of each agent's `session.run()` when Gemini thinking is disabled (`thinkingBudget=0`). | (a) Warm-up: run scenario 1 segment 1 with a discarded `MetricsCollector` to absorb cold-start cost; (b) Loop 5 scenarios x `NUM_REPS=5` reps x N agent segments, each turn wrapped in `asyncio.wait_for(..., timeout=120s)`; (c) On each `session.run()`, record `latency_ms` into the session-scoped `combined_collector`; (d) Timeouts/exceptions are logged but do not fail the test. No assertion on latency thresholds — pass = "ran without crashing". | Captures latency samples for the "thinking off" arm of the A/B; report is generated only at session teardown after both arms run. | In scope: real Vertex Gemini call latency, real LiveKit `AgentSession` overhead, mocked patient/appointment tool data, multi-segment handoff flows (greeting -> location -> provider -> visit reason; auth -> manage appointment; auth disambiguation; FAQ + transfer). Out of scope: TTS/STT/EOU latency, network jitter from real telephony, barge-in, p99, regression thresholds, CI persistence. |
| 2 | | `TestLatencyBenchmark::test_benchmark[thinking_on]` | Same five scenarios but with Gemini thinking left at default (`thinking_config=None`), so the LLM may spend tokens on a "thinking" phase before each response. | Identical control flow to row 1; the parameterized `benchmark_llm` fixture swaps in an LLM without `ThinkingConfig(thinking_budget=0)`. Records into the same session-scoped `combined_collector` so `report.py` can diff thinking-on vs thinking-off in the executive summary. | Captures the "thinking on" arm; combined teardown writes `benchmark-results/latency-report-<date>.md` with p50/p75/p90/p95 deltas and per-scenario / per-agent tables. | In scope: same as row 1 but with thinking enabled — measures the latency cost (and variance) added by Gemini's thinking phase across realistic agent transitions. Out of scope: token-cost tracking, quality/correctness comparison between modes, cache-hit behavior, p99, automated regression gating. |

### Suggested Missing Tests
- **Tool-call round-trip latency** — runner times the entire `session.run()` but does not isolate the tool-call hop. A separate benchmark instrumenting LiveKit's `function_tool` invocations (or per-event timing via `session.on("function_tools_executed")`) would catch regressions in mocked tool plumbing without LLM noise.
- **Time-to-first-token (TTFT) / time-to-first-audio** — `latency_ms` is the full turn duration. For a voice agent, perceived latency is dominated by TTFT and time-to-first-audio-frame; LiveKit exposes `LLMMetrics.ttft` and `TTSMetrics.ttfb` that should be captured alongside total latency.
- **End-of-utterance / end-of-turn detection latency** — VAD/EOU thresholds dominate barge-in feel; no scenario currently exercises STT+EOU on real audio.
- **Barge-in response time** — measure how quickly the agent yields when the user interrupts mid-response. Critical for a voice product, not currently covered.
- **Language switch latency** — if the agent supports Spanish/English (typical for Zocdoc patient flows), measure the cost of mid-call language changes.
- **p99 measurement** — `report.py` only computes up to p95. With `NUM_REPS=5` x 5 scenarios = ~25 samples per arm, p99 is statistically meaningless today; bumping reps and adding p99 would catch tail latency that 1-in-100 callers actually feel.
- **Cold-start vs warm latency split** — only one warm-up turn (scenario 1 segment 1) is discarded. Cold-start metrics for every agent class would help track first-call-after-deploy regressions.
- **Concurrency benchmark** — current harness runs scenarios serially. A concurrency test (e.g., 10 parallel `AgentSession`s) would surface contention/throughput limits that single-stream benchmarks miss.
- **Per-segment scheduling-paused wait time** — `_wait_for_activity_ready` is included in latency only up to the `session.run()` boundary; the post-turn settle (~50ms + poll) is not tracked, but it can hide real handoff cost.

### Improvement Suggestions / Irrelevant Tests
- **No regression thresholds / pass-fail gate** — the test only fails on uncaught exceptions; timeouts and per-turn errors are logged and swallowed (`logger.error(...)`). There is no `assert p95 < threshold_ms` or comparison against a baseline file. Per OPS-001 / OPS-010, latency SLOs should be encoded so CI can fail on regression rather than relying on a human reading the markdown report.
- **Real Vertex AI dependency makes this a load test, not a benchmark** — `_create_llm` calls `google.LLM(vertexai=True, project="phoneai-dev", ...)`. Results vary with Vertex region load, model rollout, network RTT, and quota. Add a mock-LLM mode (configurable latency distribution) so engineers can run the harness offline and isolate framework overhead from model latency.
- **Results not persisted to a tracking dashboard** — `write_report` writes to a local `benchmark-results/` directory only. There is no upload to DataDog, S3, BigQuery, or a longitudinal dashboard, so trend-over-time analysis requires manual diffing of dated markdown files. Recommend emitting per-turn metrics as DataDog distributions or pushing the report JSON to S3 keyed by git SHA.
- **Sample size is small** — 5 reps x 5 scenarios = ~125 turns per arm. p90/p95 from ~25 per-scenario samples have wide confidence intervals; bump `NUM_REPS` or downweight per-scenario tables when n is small.
- **Warm-up only covers one segment** — only `SCENARIOS[0].segments[0]` (Greeting) is warmed up. Scenarios 2-4 use `_AuthAgent` and `_ManageAppointmentAgent` whose first invocation may pay cold-start cost (model warm-up, Vertex routing) that is then attributed to the measured run. Either warm up one segment per agent class, or discard the first rep per (scenario, segment).
- **Hardcoded `TURN_TIMEOUT = 120` seconds** — 2 minutes per turn is generous; if a turn truly takes 120s the run is silently dropped from the dataset (only a log line). Long timeouts should themselves count as a failure mode in the report (e.g., a "timeouts" row per config).
- **Brittle private-attribute polling** — `_wait_for_activity_ready` reads `session._activity._scheduling_paused`. This will break silently across LiveKit Agents upgrades. The comment "Keep in sync with src/evaluations/runner.py" acknowledges duplication; extract to a shared helper.
- **GreetingAgent omitted from cancel/reschedule flows** — comment at scenario 2 notes "GreetingAgent omitted - goal detection triggers FlowController handoffs that hang in benchmarks." Worth fixing the underlying hang so the benchmark covers the realistic full-call latency path; otherwise scenarios 2/3 understate real call duration.
- **Scenario 5 `_greeting_faq_transfer_turns` exercises FAQ retrieval** — if FAQ uses a separate retrieval path (RAG / vector search), its latency is bundled into the same `latency_ms`. Consider a per-tool latency breakdown so the FAQ retrieval cost is visible.
- **No correctness sanity check** — `runner.py` ignores `turn.expects`. A turn that returns garbage (e.g., LLM refuses, tool errors) still records a "fast" latency. At minimum, a smoke check on output non-emptiness would prevent silently benchmarking a broken agent.
- **Single LLM provider/model under test** — only `gemini-2.5-flash` on Vertex. If the agent stack supports multiple models (Gemini Pro, OpenAI, etc.) the same harness should be parameterized by model so model-swap decisions are data-driven.
- **No GPU/CPU/memory profiling** — wall-clock only. For a voice agent running in ECS, host CPU saturation directly maps to perceived latency; a complementary resource-usage capture (per OPS-005) would make scaling decisions easier.

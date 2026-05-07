# ZoTools.IntegrationTests - Integration Test Mapping

> Repo: [Zocdoc/zo-tools](https://github.com/Zocdoc/zo-tools)  
> Folder: `tests/ZoTools.IntegrationTests/`  
> Generated: 2026-05-07

## ExampleTests.cs

(placeholder file - contains a single trivial assertion `(1+1).Should().Be(2)` named `TokenEmptyTest` in class `TokenTest`. No real integration coverage.)

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|-------------------|-----------|---------------|-------|---------|-------|
| 1 | No - delete or replace | TokenEmptyTest | Nothing meaningful; asserts `1 + 1 == 2` | No setup -> trivial arithmetic -> assert | Smoke placeholder, not an integration test | In scope: nothing real. Out of scope: any actual ZoTools integration behavior |

### Suggested Missing Tests

This project (`ZoTools.IntegrationTests`, the non-Web flavor) is empty in practice. Per Zocdoc TEST-001 / TEST-002 / TEST-003, an integration test project at this layer should cover cross-component flows that do not require the full web pipeline. Candidates:

- **End-to-end agent tool flows against fakes** - Wire `ManageAppointmentAgentTools`, `GreetingAgentTools`, etc. to fake downstream clients (Interop, Cistern, MarketplaceApi) and verify orchestration across multiple tools.
- **InteropPlatformApi client integration** - Real HTTP client (or in-process fake host) round-tripping for the calls ZoTools makes (patient search, appointment fetch, reschedule, cancel) to lock the contract.
- **Audio/transcription pipeline** - Initialize call -> stream audio -> emit transcript events, exercising real serialization and timing.
- **CSAT tool flow** - Full submit-feedback path with a fake upstream service, including failure / retry behavior.
- **Cron / worker entrypoints** - If ZoTools has scheduled jobs, run them end-to-end against fakes per PROC-002 and TEST-003.
- **Logging / metrics enrichment** - Verify `CallIdLogEnrichmentMiddleware` and `HttpLoggingMiddleware` produce the right structured fields when exercised through a realistic request path (currently only unit-tested).

### Improvement Suggestions / Irrelevant Tests

- **Placeholder is misleading** - `TokenEmptyTest` gives a green CI signal with zero coverage. Either delete it once a real test is added, or replace it with one bootstrap test that verifies the test host wires DI / appsettings correctly (so the rest of the suite has a real foundation).
- **Project purpose unclear vs. `ZoTools.Web.IntegrationTests`** - Today both projects exist but only the Web one has tests. Decide explicitly: does this project own non-HTTP integration (e.g., worker / library / agent tool flows), or should it be removed? Document the split in the project README per PROC-004 / PROC-005.
- **Use fakes, not mocks** - When tests are added here, prefer Zocdoc fakes (TEST-002) over Moq for downstream services. The Web project already mixes both; this project should be cleaner from the start.
- **Localstack assets are checked in** - `localstack/` and `appsettings.IntegrationTests.json` exist but no test consumes them. Either start using them or remove to avoid drift.

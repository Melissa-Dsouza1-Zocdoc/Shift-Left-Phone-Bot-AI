# ZO Tools - Test Mapping Index

> Test mapping for the [Zocdoc/zo-tools](https://github.com/Zocdoc/zo-tools) repository (the "Zo voice agent" tools server).
> Generated: 2026-05-07
> Format follows the [provider-calendar test mapping](https://github.com/Mervis-Mascarenhas-Zocdoc/Shift-Left-Appointment-Management/blob/main/2026-05-06/provider-calendar-test-mapping.md) template.

## How to read these files

Each mapping file contains, per spec file (`.cs`):

- A numbered table of every test method with `Test Name`, `What It Tests`, `Steps`, `Summary`, `Scope`
- A **Suggested Missing Tests** section listing concrete tests the team should consider adding
- An **Improvement Suggestions / Irrelevant Tests** section flagging redundancy, weak assertions, or tests worth deleting

The `Do we need these?` column is intentionally left blank — it is a triage column for the team to fill during review.

## Files

### Unit tests (`tests/ZoTools.UnitTests/`)
| Folder | File | Notes |
|--------|------|-------|
| Caching | [ZoTools-UnitTests-Caching-test-mapping.md](ZoTools-UnitTests-Caching-test-mapping.md) | Cache entry, headers, results, repository caches |
| Helpers | [ZoTools-UnitTests-Helpers-test-mapping.md](ZoTools-UnitTests-Helpers-test-mapping.md) | Appointment modification, location naming, TTS time formatter, validation |
| Repository | [ZoTools-UnitTests-Repository-test-mapping.md](ZoTools-UnitTests-Repository-test-mapping.md) | ZoCallSession DDB repository |
| Service - Booking | [ZoTools-UnitTests-Service-Booking-test-mapping.md](ZoTools-UnitTests-Service-Booking-test-mapping.md) | Appointment, availability, booking, interop, reschedule, isNewPatient |
| Service - Insurance | [ZoTools-UnitTests-Service-Insurance-test-mapping.md](ZoTools-UnitTests-Service-Insurance-test-mapping.md) | Insurance acceptance, carrier GQL, catalog, search |
| Service - Other | [ZoTools-UnitTests-Service-Other-test-mapping.md](ZoTools-UnitTests-Service-Other-test-mapping.md) | Ab, Audit, Csat, Sms, Twilio, Phone, NonPhiGql, Provider/Location search, Validation, Timezone, etc. |

### Web unit tests (`tests/ZoTools.Web.UnitTests/`)
| Scope | File | Notes |
|-------|------|-------|
| AgentTools impls | [ZoTools-Web-UnitTests-AgentTools-test-mapping.md](ZoTools-Web-UnitTests-AgentTools-test-mapping.md) | SelectLocation, SelectProvider, SelectTimeslot, SelectVisitReason, ManageAppointment, Greeting impls |
| Converters | [ZoTools-Web-UnitTests-Converters-test-mapping.md](ZoTools-Web-UnitTests-Converters-test-mapping.md) | ToolParametersJsonConverter |
| Providers | [ZoTools-Web-UnitTests-Providers-test-mapping.md](ZoTools-Web-UnitTests-Providers-test-mapping.md) | AgentToolsRegistry, AuthenticatePatient, Greeting, ManageAppointment, SelectLocation, ToolDefinitionBuilder, Unified providers |
| Other | [ZoTools-Web-UnitTests-Other-test-mapping.md](ZoTools-Web-UnitTests-Other-test-mapping.md) | Ab/Appointment/Audio/CallCompleted/PatientInfo impls, middleware, schema/serialization |

### Web API tests (`tests/ZoTools.Web.ApiTests/`)
| Theme | File | Notes |
|-------|------|-------|
| Admin / Tools surface | [ZoTools-Web-ApiTests-Admin-test-mapping.md](ZoTools-Web-ApiTests-Admin-test-mapping.md) | Ab, AdminSeed, Debug, Tools, ToolsSchema |
| AgentTools endpoints | [ZoTools-Web-ApiTests-AgentTools-test-mapping.md](ZoTools-Web-ApiTests-AgentTools-test-mapping.md) | SelectLocation/Provider/Timeslot/VisitReason, Insurance, Validation, PracticeFaq |
| Booking + Patient | [ZoTools-Web-ApiTests-Booking-test-mapping.md](ZoTools-Web-ApiTests-Booking-test-mapping.md) | Booking, Appointment, InsertPatient, UpdatePatient, ValidatePatient, PatientSearch, PracticeInfo |
| Telephony | [ZoTools-Web-ApiTests-Telephony-test-mapping.md](ZoTools-Web-ApiTests-Telephony-test-mapping.md) | Audio, Sms, InitializeCall, CallCompleted, CsatTool |

### Integration tests
| Project | File | Notes |
|---------|------|-------|
| ZoTools.IntegrationTests | [ZoTools-IntegrationTests-test-mapping.md](ZoTools-IntegrationTests-test-mapping.md) | Currently placeholder only |
| ZoTools.Web.IntegrationTests | [ZoTools-Web-IntegrationTests-test-mapping.md](ZoTools-Web-IntegrationTests-test-mapping.md) | LockedActionService + ZoCallSessionRepository against LocalStack DDB |

## Cross-cutting findings

The following gaps surfaced repeatedly across folders. Treat these as systemic rather than per-spec issues.

1. **Audit logging (AUTH-005) is unverified across every mutation endpoint.** No test in the API or service layer asserts that audit events are emitted for booking, cancel, reschedule, send-sms, set-sms-consent, csat-submit, insert-patient, update-patient, or admin seeding.
2. **Idempotency / replay semantics are undefined and untested for all mutating POSTs.** Voice-agent retries are likely; without idempotency keys or replay tests, double-actions are a real risk.
3. **Response-shape contracts use brittle field probes or `BeEquivalentTo + ExcludingMissingMembers`.** Verify snapshots (CS-006) are not in use, so contract drift slips through.
4. **PHI scrubbing in `HttpLoggingMiddleware` is unverified.** The middleware test file has only one test — no coverage for header/body redaction, status, or exception-from-next.
5. **Cancellation-token forwarding is universally unverified across async tools.** None of the `*Impl` tests assert that an inbound `CancellationToken` reaches downstream calls.
6. **Mocks where fakes belong (TEST-002 violation).** `IMetricRecorder`, `IAuditLoggerService`, and similar collaborators are mocked rather than replaced with fakes that record interactions.
7. **LLM prompt/description contracts use `Contain(...)` string assertions.** Cosmetic prompt tweaks break tests without behavior changing — Verify snapshots are the right fit.
8. **No 5xx/downstream-failure paths in API tests.** Tests only cover happy paths and 400-class validation; nothing exercises Monolith API timeouts or 5xx responses.
9. **Auth/permission negative tests missing in repository integration tests.** `ZoCallSessionRepository` and `LockedActionService` have no tests for unauthorized callers, permission denials, or audit failures.

## Output location note

These files were generated under sandbox restrictions in `/tmp/ZO Tools/`. To move them into the Shift-Left repo, run from outside the sandbox:

```bash
cp -R "/tmp/ZO Tools" ~/Downloads/Shift-Left-Appointment-Management/
```

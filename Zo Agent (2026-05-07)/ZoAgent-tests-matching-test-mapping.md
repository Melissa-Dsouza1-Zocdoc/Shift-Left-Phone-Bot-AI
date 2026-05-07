# Matching - Unit Test Mapping

> Repo: [Zocdoc/zo-agent](https://github.com/Zocdoc/zo-agent)
> Folder: `tests/matching/`
> Generated: 2026-05-07
> Stack: Python / pytest / LiveKit Agents

## test_patient_matcher.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | TestFilterByPhone::test_filter_by_phone | `filter_by_phone` matches exact, multi-phone-list, and no-match cases. | Parametrized over (phone, expected_id) for 3 cases; build candidates; assert filter result. | Phone filter. | In scope: matcher logic. Out of scope: phone normalization. |
| 2 | | TestFilterByPhone::test_empty_candidates_returns_empty | Empty candidate list -> empty result. | Call with `[]`; assert empty. | Empty edge. | In scope: matcher. Out of scope: input validation. |
| 3 | | TestFilterByFirstName::test_filter_by_first_name | First-name filter is case-insensitive and whitespace-tolerant; non-match returns empty. | Parametrized 5 cases (exact, uppercase, lowercase, whitespace, no_match); assert. | Name filter. | In scope: matcher. Out of scope: locale folding. |
| 4 | | TestFilterByLastName::test_filter_by_last_name | Last-name filter mirrors first-name behavior. | Parametrized 5 cases; assert. | Name filter. | In scope: matcher. Out of scope: locale folding. |
| 5 | | TestGetMatchResult::test_zero_candidates_returns_zero_matches | Zero candidates -> `MatchStatus.ZERO_MATCHES`, count=0, no patient. | Call with `[]`; assert. | Match status. | In scope: status. Out of scope: orchestration. |
| 6 | | TestGetMatchResult::test_single_candidate_returns_success | Single candidate -> `MatchStatus.SUCCESS` with patient. | Call with one; assert. | Match status. | In scope: status. Out of scope: orchestration. |
| 7 | | TestGetMatchResult::test_multiple_candidates_returns_multiple_matches | Multiple candidates -> `MatchStatus.MULTIPLE_MATCHES`, count=2, no single patient. | Call with two; assert. | Match status. | In scope: status. Out of scope: orchestration. |

### Suggested Missing Tests
- **Deduplication across DOB+phone+name** - PatientMatcher has individual filters but no test combines them; verify a multi-step match flow (e.g., phone narrows to two, then last name narrows to one).
- **Twins same DOB, same last name, different first names** - assert filter chain disambiguates rather than collapsing.
- **Phone with formatting variations** (e.g., `+1-555-123-4567` vs candidate stored as `5551234567`) - ensure normalization is consistent.
- **Confidence threshold tuning** - if/when fuzzy matching is added (typos in name); currently no fuzzy logic, document or add.
- **Unicode names** - `Núñez`, `O'Connor`, `Müller`; lower-case folding may not normalize accents.
- **Hyphenated names** - "Garcia-Lopez" vs "Garcia Lopez" (a real ambiguity in healthcare data).
- **Candidate with empty/whitespace name field** - ensure filter doesn't crash or false-match.
- **`location_ids` constraint** - matcher may need a filter-by-location method; add when relevant.
- **`date_of_birth` filter** - currently there is no DOB filter test; PatientMatcher likely has one (or should).
- **Stable sort for `MULTIPLE_MATCHES`** - ensure deterministic ordering for downstream UX.

### Improvement Suggestions / Irrelevant Tests
- **Module is small (183 lines, 7 tests)** but covers an authentication-critical component; this is the largest under-tested risk surface in the repo from a QA standpoint.
- **`make_patient` factory** is duplicated in `tests/tasks/test_authenticate_patient_task.py` (`_make_task` neighborhood) - move to shared `tests/conftest.py` or a `factories.py`.
- **No `__init__.py` test** - confirmed only `test_patient_matcher.py` exists in folder; add tests for any other `src/matching/` modules (e.g., normalization helpers).
- **`get_match_result` test** depends on `count` field semantics; document that `count` is 0 for zero/single match (only meaningful for multi).
- **Add property-based tests** (Hypothesis) for `filter_by_phone` with random valid/invalid E.164 inputs.
- **Add a flake guard** - mark hot tests with `@pytest.mark.flaky(reruns=2)` if any fuzzy matching is introduced.

# Per-Task Flow - Evaluation Test Mapping

> Repo: [Zocdoc/zo-agent](https://github.com/Zocdoc/zo-agent)
> Folder: `tests/evaluations/`
> Generated: 2026-05-07
> Stack: Python / pytest / LiveKit eval framework
>
> These are LLM flow evaluations that simulate user turns and assert agent behavior. Treat per-row "Steps" as the conversation script + expected agent actions.

Covers the small-task collection agents (DOB, name, phone, gender) plus CSAT. Each is a focused micro-flow exercising a single field or close-out interaction.

## test_collect_dob_flow.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | test_dob_format[parametrized] | Various DOB phrasings parse to (month,day,year) | Parametrized over format strings ("March 15, 1985", "3/15/85", "3-15-1985", etc); expect submit_date_of_birth with correct ints. | Format coverage | In scope: format. Out of scope: timezone. |
| 2 | | test_single_correction | Caller corrects DOB; agent re-submits | "March 15" -> "actually March 16"; expect new submit. | Single correction | In scope: correction. Out of scope: multi. |
| 3 | | test_multiple_corrections | Multiple corrections handled | Two corrections; final submit correct. | Multi correction | In scope: multi. Out of scope: budget. |
| 4 | | test_smart_correction | Partial correction (only year changes) preserved | Change only year; agent re-submits with prior month/day. | Smart correction | In scope: partial. Out of scope: typo correction. |
| 5 | | test_immediate_transfer_request | "Talk to person" mid-DOB -> transfer | Transfer ask. | Transfer | In scope: transfer. Out of scope: confirmation. |
| 6 | | test_transfer_after_frustration | Frustrated phrasing -> transfer | Frustration phrasing; transfer. | Frustration | In scope: transfer. Out of scope: language. |
| 7 | | test_dob_reconfirm_only_discusses_dob | Reconfirm stays on DOB topic | When asked to reconfirm DOB with appointment context, agent doesn't drift. | Topic discipline | In scope: focus. Out of scope: barge-in. |
| 8 | | test_ai_disclosure_response | "Are you AI" mid-DOB -> disclose | AI Q. | Disclosure | In scope: disclosure. Out of scope: full text. |

### Suggested Missing Tests
- **Future DOB rejection** — "March 15, 2099" should error; not tested.
- **Year-only or month-only ASR fragments** — partial DOB not tested.
- **Two-digit vs four-digit year ambiguity** — "85" -> 1985 vs 2085 not asserted.
- **Cultural date ordering** — DD/MM/YYYY vs MM/DD/YYYY ambiguity not asserted.

## test_collect_name_flow.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | test_first_name_format[parametrized] | First-name phrasings parse correctly | Parametrized inputs ("My name is Joe", "Joe", "It's Joe"); expect submit_first_name. | Format coverage | In scope: format. Out of scope: spelling. |
| 2 | | test_first_name_correction | Correct first name | "Joe" -> "actually Joseph"; re-submit. | Correction | In scope: correction. Out of scope: spelling. |
| 3 | | test_first_name_transfer | Transfer mid-first-name -> transfer | Transfer ask. | Transfer | In scope: transfer. Out of scope: language. |
| 4 | | test_last_name_format[parametrized] | Last-name phrasings parse | Parametrized over phrasings; expect submit_last_name. | Format coverage | In scope: format. Out of scope: hyphenation. |
| 5 | | test_last_name_correction | Correct last name | Correction; re-submit. | Correction | In scope: correction. Out of scope: spelling. |
| 6 | | test_last_name_transfer | Transfer mid-last-name | Transfer ask. | Transfer | In scope: transfer. Out of scope: language. |
| 7 | | test_ai_disclosure_response | AI Q -> disclose | Disclosure. | Disclosure | In scope: disclosure. Out of scope: full text. |

### Suggested Missing Tests
- **Spelling clarification** — "Smith" vs "Smyth" (no spelling-out test for legacy collect_name; unified has one).
- **Hyphenated/multi-part names** — "Mary-Jane Smith-Jones" not tested.
- **Non-ASCII / international names** — accents, non-Latin scripts not covered.
- **Name with title/suffix** — "Jr.", "Sr.", "Dr." not asserted.

## test_collect_patient_gender_flow.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | test_happy_path[parametrized] | Various gender phrasings map to canonical value | Parametrized inputs ("male", "I'm a man", "F"); expect submit_gender. | Format coverage | In scope: gender phrasings (self). Out of scope: pronouns. |
| 2 | | test_transfer_on_request | Caller asks human -> transfer | Transfer ask. | Transfer | In scope: transfer. Out of scope: language. |
| 3 | | test_other_phrasing | "Other" / non-binary phrasings handled | Non-binary phrasings; submit canonical. | Non-binary | In scope: non-binary. Out of scope: full taxonomy. |
| 4 | | test_clarification_then_valid | Vague answer -> agent clarifies, then submits | Vague then valid; submit. | Clarify | In scope: clarify. Out of scope: budget. |
| 5 | | test_unmappable_triggers_transfer | Truly unmappable -> transfer | Refusal/unmappable; transfer. | Unmappable | In scope: transfer. Out of scope: privacy. |

### Suggested Missing Tests
- **"Prefer not to say"** — opt-out of gender; behavior not specified.
- **Gender for someone-else booking flow** — only "self" path tested; "for my daughter" gender collection not covered here.
- **Pronoun vs sex/gender distinction** — not asserted.

## test_collect_phone_flow.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | test_phone_format[parametrized] | Phone phrasings parse to E.164 | Parametrized formats ("(212) 555-1234", "212-555-1234", "two one two..."); expect submit_phone. | Format coverage | In scope: format. Out of scope: international. |
| 2 | | test_confirm_caller_phone | Agent suggests caller's incoming phone, user confirms | Suggestion + confirm; submit. | Suggestion confirm | In scope: suggestion. Out of scope: rejection. |
| 3 | | test_reject_caller_phone_provide_new | Reject suggestion, give different phone | "No, use this one"; submit new. | Reject + new | In scope: rejection. Out of scope: format error. |
| 4 | | test_single_correction | Phone correction handled | Correction; re-submit. | Correction | In scope: correction. Out of scope: multi. |
| 5 | | test_incomplete_phone_asks_for_full_number | < 10 digits -> agent re-asks | Partial; re-prompt. | Incomplete | In scope: re-prompt. Out of scope: format. |
| 6 | | test_incomplete_phone_only_area_code | Area code only -> re-prompt | "212"; re-prompt. | Area code only | In scope: partial. Out of scope: digits-only. |
| 7 | | test_immediate_transfer_request | Transfer mid-phone | Transfer ask. | Transfer | In scope: transfer. Out of scope: language. |
| 8 | | test_no_phone_transfer | "I don't have a phone" -> transfer | No-phone declaration; transfer. | No-phone | In scope: transfer. Out of scope: alt contact. |
| 9 | | test_ai_disclosure_response | AI Q -> disclose | Disclosure. | Disclosure | In scope: disclosure. Out of scope: full text. |

### Suggested Missing Tests
- **International phone numbers** — +44, +1 prefixes, country code digit parsing.
- **Extension numbers** — "555-1234 ext 567" handling.
- **VoIP / Google Voice numbers** — passes formatting but business rule may differ.
- **Caller rejects phone but provides invalid format** — only happy rejection tested.

## test_csat_flow.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | test_full_flow | Full CSAT happy path: consent -> score -> feedback -> close | Consent, score 4, feedback "great", close. | End-to-end CSAT | In scope: full. Out of scope: i18n. |
| 2 | | test_high_score_with_feedback | Score 5 with positive feedback | Score 5 + free text; submit. | High score | In scope: score 5. Out of scope: low. |
| 3 | | test_decline_consent | Caller declines CSAT -> close politely | "No, I don't want"; close. | Decline | In scope: decline. Out of scope: forced. |
| 4 | | test_polite_decline | Polite decline still respected | Polite decline; close. | Polite decline | In scope: polite. Out of scope: rude. |
| 5 | | test_no_feedback | Score given, no free text -> submit anyway | Score 4, "no feedback"; submit. | No feedback | In scope: score-only. Out of scope: empty. |
| 6 | | test_vague_then_valid_score | Vague score ("good") -> clarify -> valid number | "Good" -> "1-5" -> "5"; submit. | Clarify score | In scope: clarify. Out of scope: budget. |
| 7 | | test_transfer_at_gate | Transfer request at CSAT consent gate | Transfer ask at gate; honor. | Transfer at CSAT | In scope: transfer. Out of scope: post-CSAT. |

### Suggested Missing Tests
- **Score 0 / negative** — only positive scores tested.
- **Score above 5** — "10 out of 10"; not asserted.
- **Profane / abusive feedback** — should still be captured + scrubbed; not asserted.
- **PHI in free-text feedback** — caller may state PHI in feedback; no scrubbing test.
- **Consent timeout / silence** — caller silent at consent gate; no test.

## Cross-cutting Per-Task Gaps

- **AI disclosure consistent** — DOB, name, phone all have it. Gender and CSAT do NOT have AI disclosure tests. Add for parity.
- **Transfer paths consistent** — present everywhere; good.
- **Correction paths consistent on DOB/Name/Phone** — Gender lacks a "correction" test.
- **PHI scrubbing untested across the board** — every collection task captures PHI; none asserts log scrubbing.
- **Determinism for parametrized format tests** — many `test_X_format[parametrized]` tests; without a seeded LLM these are likely flake sources.
- **Field-level negative paths** — "do NOT call submit_X if user says X" never asserted.

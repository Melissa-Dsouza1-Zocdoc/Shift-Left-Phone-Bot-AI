# Unified Appointment Flows - Evaluation Test Mapping

> Repo: [Zocdoc/zo-agent](https://github.com/Zocdoc/zo-agent)
> Folder: `tests/evaluations/`
> Generated: 2026-05-07
> Stack: Python / pytest / LiveKit eval framework
>
> These are LLM flow evaluations that simulate user turns and assert agent behavior. Treat per-row "Steps" as the conversation script + expected agent actions.

Covers all `test_unified_*` flows. The unified agents (`UnifiedManageAppointmentAgent`, `UnifiedNewAppointmentAgent`) are the consolidated successors to the per-agent flows in `test_manage_appointment_flow.py`. Tests here represent the production routing path. Files are grouped: blocked, cancel, closest_alternatives, confirm, hybrid_reschedule, location_selection, new_appointment_*, provider_search, reschedule, reschedule_directives.

## test_unified_appointment_blocked_flow.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | test_reschedule_blocked_transfers_with_appointment_id | Reschedule-blocked flag -> transfer carrying appointment_id | User asks reschedule; mock blocked; expect transfer_to_human with appointment_id arg. | Blocked reschedule ID-pass | In scope: blocked reschedule. Out of scope: cancel-blocked. |
| 2 | | test_reschedule_allowed_when_only_cancel_blocked | Cancel-blocked but reschedule allowed -> reschedule proceeds | Only cancel blocked; reschedule succeeds. | Independent flags | In scope: independent flags. Out of scope: both blocked. |
| 3 | | test_cancel_blocked_transfers_with_appointment_id | Cancel-blocked -> transfer with appointment_id | User cancels; mock blocked; transfer with ID. | Blocked cancel ID-pass | In scope: blocked cancel. Out of scope: reschedule-blocked. |
| 4 | | test_cancel_allowed_when_only_reschedule_blocked | Reschedule-blocked but cancel allowed -> cancel proceeds | Only reschedule blocked; cancel succeeds. | Independent flags | In scope: independent flags. Out of scope: both blocked. |

### Suggested Missing Tests
- **Both reschedule + cancel blocked** — agent must transfer regardless of intent.
- **Blocked + caller insists** — caller refuses transfer; agent loop behavior.
- **Blocked reason text** — does the transfer carry the reason for routing? Not asserted.

## test_unified_cancel_flow.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | test_cancel_single_appointment | Single appt cancel: greeting -> auth (DOB) -> confirm identity -> cancel_appointment tool | DOB "November 3rd, 1982" -> search_patients with date args -> "Yes that's me" -> get_appointments -> "I'd like to cancel" -> cancel_appointment. | Single cancel happy path | In scope: single. Out of scope: blocked. |
| 2 | | test_cancel_multiple_appointments_uses_preview | Multi appts: agent asks which one before cancel | Multi-appt fixture; user asks to cancel one; agent asks "which" referencing providers/times; user selects "Dr. Patel"; expect cancel_appointment. | Multi cancel with preview | In scope: multi disambig. Out of scope: nominal. |
| 3 | | test_cancel_blocked_transfers_immediately | Blocked cancel -> immediate transfer (no question) | Blocked fixture; expect transfer immediately. | Blocked cancel | In scope: blocked. Out of scope: appointment_id pass-through (covered in blocked file). |

### Suggested Missing Tests
- **Cancel after caller initially asks for confirm** — switch intent.
- **Cancel within 24h of appt** — cancellation window business rule not asserted.
- **Cancel a series / recurring appt** — not tested.

## test_unified_closest_alternatives_flow.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | test_specific_time_offers_closest_alternatives | Specific time has no slot -> agent offers closest available | Caller asks "Tuesday 3pm"; no slot; agent offers nearby slots. | Closest alternatives | In scope: time-specific. Out of scope: date range. |
| 2 | | test_empty_search_widens_range | Empty result -> widen the range automatically | Empty search; agent broadens (e.g., adds days). | Auto-widen | In scope: widen. Out of scope: caller approval. |
| 3 | | test_time_preference_no_match_broadens_search | "Morning" with no match -> broadens to afternoon | Pref returns nothing; agent breaks pref and shows others. | Pref relaxation | In scope: pref relax. Out of scope: pref preserve. |

### Suggested Missing Tests
- **Caller refuses widened slots** — agent loop behavior unclear.
- **All days fully booked** — terminal "no availability" path; covered partially in reschedule file.

## test_unified_confirm_flow.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | test_get_appointments_called_after_search | After search_patients, get_appointments fires with location_ids | DOB -> search -> get_appointments call shape. | Tool sequencing | In scope: order + args. Out of scope: payload. |
| 2 | | test_no_match_confirms_dob_before_alternate_phone_then_transfers | Zero patient match -> reconfirm DOB -> ask alternate phone -> transfer | Zero match path with DOB reconfirm + alt phone before transfer. | Zero-match auth ladder | In scope: ladder. Out of scope: name fallback. |
| 3 | | test_no_match_without_tts_speak_does_not_combine_dob_and_phone | When TTS speak unavailable, agent doesn't bundle DOB+phone in one message | Specialized agent without speak; assert messages stay separated. | TTS-aware messaging | In scope: TTS variation. Out of scope: voice latency. |
| 4 | | test_multiple_appointments_with_unknown_provider | Multi appts where one has unknown provider; agent describes appropriately | Multi-appt with one provider unknown; agent verbalizes by time/location. | Unknown-provider fallback | In scope: fallback labeling. Out of scope: silent error. |

### Suggested Missing Tests
- **Confirm after partial auth (only DOB known)** — not tested.
- **Confirm during business-hours edge** — appt later today vs tomorrow phrasing.

## test_unified_hybrid_reschedule_flow.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | test_virtual_selection | Hybrid reschedule: caller picks virtual -> reschedule virtual | Hybrid agent; "virtual"; expect virtual reschedule. | Virtual choice | In scope: virtual. Out of scope: in-person. |
| 2 | | test_in_person_selection | Hybrid reschedule: caller picks in-person -> reschedule in-person | "In-person"; expect physical reschedule. | In-person choice | In scope: physical. Out of scope: virtual. |
| 3 | | test_ambiguous_response_transfers | Ambiguous reply at hybrid prompt -> transfer | Vague response; transfer. | Ambiguity transfer | In scope: ambiguity. Out of scope: clarify retry. |
| 4 | | test_update_failure_transfers | Tool failure on update -> transfer | Mock update failure; transfer. | Tool failure | In scope: failure path. Out of scope: retry. |
| 5 | | test_physical_only_skips_hybrid_check | Non-hybrid appt skips hybrid prompt | Physical-only appt; flow skips modality Q. | Skip when not hybrid | In scope: skip. Out of scope: virtual-only. |

### Suggested Missing Tests
- **Hybrid + transfer mid-prompt** — caller asks for human at the modality prompt.
- **Hybrid with both unavailable** — both modalities have no slot.

## test_unified_location_selection_flow.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | test_single_location_no_question | Single-location practice doesn't ask | No question asked. | Single skip | In scope: skip. Out of scope: virtual. |
| 2 | | test_single_location_wrong_location_offers_transfer | Caller asks for wrong-location -> transfer offered | Wrong location named; transfer. | Wrong-loc transfer | In scope: transfer. Out of scope: nearby. |
| 3 | | test_multi_location_asks_preference | Multi-location asks caller preference | Asks. | Multi prompt | In scope: prompt. Out of scope: state. |
| 4 | | test_caller_names_location | Caller names location -> resolve directly | Named location; resolve. | Named direct | In scope: name. Out of scope: ZIP. |
| 5 | | test_zip_code_search | ZIP triggers search_locations | ZIP; search. | ZIP path | In scope: ZIP. Out of scope: city. |
| 6 | | test_location_not_found | No match -> agent re-asks | Not found; re-prompt. | Not found | In scope: re-prompt. Out of scope: max retries. |
| 7 | | test_location_confirmation | Selection confirmed before submit | Confirm before submit. | Confirm step | In scope: confirm. Out of scope: revert. |
| 8 | | test_working_location_scoping | Already-scoped working location preserved | Pre-scoped state honored. | State preserve | In scope: scope. Out of scope: invalidate. |
| 9 | | test_unsupported_criteria_redirect | Unsupported criteria (e.g., insurance-only) -> redirect | Redirect to alternative path. | Unsupported | In scope: redirect. Out of scope: full-flow. |
| 10 | | test_in_person_preference_calls_search_locations | "In-person" pref -> physical search | Physical search. | Physical search | In scope: physical. Out of scope: virtual. |
| 11 | | test_provider_single_location_inference | Provider + single location -> location inferred | No location asked. | Inference | In scope: infer. Out of scope: multi. |
| 12 | | test_provider_multi_location_ask | Provider with multi-location -> ask | Asks location. | Provider ask | In scope: ask. Out of scope: which. |
| 13 | | test_virtual_preference_asked | Virtual preference question asked | Asks virtual y/n. | Virtual prompt | In scope: prompt. Out of scope: which state. |
| 14 | | test_default_to_in_person | No pref -> default in-person | Defaults. | Default | In scope: default. Out of scope: virtual default. |
| 15 | | test_virtual_single_state_asks_state | Virtual single-state still confirms state | Confirms state. | State confirm | In scope: state. Out of scope: ZIP. |
| 16 | | test_virtual_multi_state_asks_state | Virtual multi-state asks state | Asks. | Multi-state ask | In scope: prompt. Out of scope: city. |
| 17 | | test_virtual_asks_provider_preference_after_state | Virtual + state -> ask provider preference | Sequenced. | Sequenced ask | In scope: order. Out of scope: skip. |
| 18 | | test_virtual_any_provider_searches_all_locations | Virtual + "any provider" -> search all | All-locations search. | All-loc search | In scope: any. Out of scope: filter. |
| 19 | | test_virtual_provider_not_in_state_transfers | Virtual provider not licensed in state -> transfer | Licensure miss; transfer. | Licensure transfer | In scope: licensure. Out of scope: alt provider. |
| 20 | | test_virtual_no_providers_in_state_transfers | Virtual no providers in state -> transfer | No provider; transfer. | No-provider transfer | In scope: no provider. Out of scope: physical. |
| 21 | | test_virtual_many_locations_single_state_flow | Virtual with many locations, single state | Single-state path with many locations. | Many-loc | In scope: scale. Out of scope: pagination. |
| 22 | | test_virtual_5_or_fewer_searches_all | Virtual w/ <=5 locations searches all | Threshold logic. | Threshold | In scope: threshold. Out of scope: 6+. |
| 23 | | test_virtual_only_skips_modality_question | Virtual-only practice skips modality Q | Skips. | Skip modality | In scope: skip. Out of scope: in-person. |

### Suggested Missing Tests
- **Caller in different state than searchable** — TX caller, NY-only practice, no virtual.
- **Address vs ZIP precedence** — address provided when ZIP works; precedence not asserted.
- **Caller refuses to give state for virtual** — flow halt behavior.

### Improvement Suggestions / Irrelevant Tests
- **23 tests, 1 file, 1151 lines** — split into virtual vs physical sub-files; current file is hard to review.
- **Heavy reliance on `eval_llm_no_thinking` fixture** — implies non-thinking model used to reduce flake; document this convention.

## test_unified_new_appointment_auth_flow.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | test_new_patient_transfers | After zero-match DOB, "I'm new" -> transfer for all unified-agent practices | Shared `ZERO_MATCH_DOB_CONFIRMED` 3-turn prologue (DOB asked -> "March 15, 1985" -> confirm); user says "first time"; expect transfer_to_human. | New patient transfer | In scope: new path. Out of scope: existing. |
| 2 | | test_existing_patient_asks_for_phone | Zero-match + "existing patient" -> agent asks phone | Same prologue; "I've been there before"; expect message asking for phone. | Existing escalation | In scope: existing. Out of scope: alt name. |

### Suggested Missing Tests
- **"Not sure if I'm new"** — caller uncertain; clarification path not asserted.
- **Existing patient with wrong phone** — phone given but no match; not tested.

## test_unified_new_appointment_gender_flow.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | test_agent_calls_collect_patient_gender_for_demographics | Unified agent invokes gender collection sub-task when needed | Demographics phase; agent calls collect_patient_gender. | Sub-task wiring | In scope: wiring. Out of scope: gender values. |
| 2 | | test_agent_handles_gender_collection_transfer | Transfer during gender collection bubbles up | Sub-task transfers; main agent honors. | Transfer bubble | In scope: bubble. Out of scope: state. |

### Suggested Missing Tests
- **Gender for "for my daughter"** — third-party demographics not covered.
- **Skipping gender if already known** — pre-filled state path not asserted.

## test_unified_new_appointment_insurance_flow.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | test_aetna_open_access_plus_happy_path | Insurance "Aetna Open Access Plus" parsed and submitted | Caller names; expect submit_insurance with parsed plan. | Insurance happy path | In scope: parse. Out of scope: card scan. |
| 2 | | test_insurance_not_accepted_calls_transfer_to_human | Unaccepted insurance -> transfer | Out-of-network; transfer. | Out-of-network | In scope: OON transfer. Out of scope: pricing. |
| 3 | | test_bcbs_directive_triggers_state_question | BCBS requires state-specific plan -> agent asks state | "BCBS"; agent asks state. | BCBS state ask | In scope: BCBS. Out of scope: other multi-state. |

### Suggested Missing Tests
- **Self-pay path** — caller says "I'll pay cash"; not tested.
- **Insurance with Medicare/Medicaid** — government plans likely have specific rules; not asserted.
- **Plan typo / fuzzy match** — "Etna" instead of "Aetna"; not tested.
- **Multiple insurance cards (primary + secondary)** — not covered.

## test_unified_new_appointment_name_spelling.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | test_obvious_name_inserts_patient_immediately | Common unambiguous name -> insert without spelling confirmation | "John"; insert direct. | Common name skip | In scope: common. Out of scope: rare. |
| 2 | | test_ambiguous_name_confirms_spelling_before_insert | Ambiguous name -> spelling confirmation first | "Catherine" / "Katherine"; agent asks spelling. | Spelling confirm | In scope: ambiguous. Out of scope: ASR confidence. |

### Suggested Missing Tests
- **Three-way ambiguity** — Sean / Shawn / Shaun; behavior unclear.
- **Hyphenated last names** — "Smith-Jones" parsing.
- **Apostrophe names** — "O'Brien"; not asserted.
- **Non-ASCII names** — "Núñez", "François"; not tested.

## test_unified_new_appointment_timeslot_flow.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | test_new_patient_next_week_morning | New patient, "next week morning" -> search with right filters | Date+time pref; expect search args. | New patient slot search | In scope: new. Out of scope: existing. |
| 2 | | test_existing_patient_passes_patient_type | Existing patient passes patient_type=existing in search | Search args include patient_type. | Patient-type passthrough | In scope: existing. Out of scope: new. |
| 3 | | test_soonest_appointment | "Soonest" -> next-available fallback | Open-ended request; fallback. | Soonest fallback | In scope: soonest. Out of scope: tomorrow. |
| 4 | | test_corrected_date_used_in_search | Date corrected by caller; search uses new date | Original "Tuesday" -> "actually Wednesday"; search uses Wednesday. | Date correction | In scope: correction. Out of scope: time correction. |
| 5 | | test_many_slots_are_summarized_not_enumerated | Many slots returned -> agent summarizes (doesn't read all 20) | 20+ slots; agent groups/summarizes. | Slot summarization | In scope: summarization. Out of scope: pagination. |
| 6 | | test_no_availability_does_not_ask_for_further_dates | No availability -> agent does NOT loop asking new dates | Empty result; agent escalates rather than re-asks. | Loop avoidance | In scope: loop avoidance. Out of scope: transfer. |

### Suggested Missing Tests
- **Date in past** — "Last Tuesday"; rejection behavior not asserted.
- **Date too far out** — "next year"; threshold not asserted.
- **Conflicting prefs** — "morning but after 11am"; behavior unclear.
- **Caller picks slot then changes mind** — selection revert.

## test_unified_new_appointment_visit_reason_flow.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | test_excluded_visit_reason_triggers_transfer | Excluded reason (per practice config) -> transfer | Exclusion list hit; transfer. | Excluded transfer | In scope: excluded. Out of scope: similar reason. |
| 2 | | test_multiple_complaints_book_generic_and_note_others | Multiple symptoms -> book generic visit + note others as text | Symptoms A, B, C; book generic; A/B/C captured as note. | Multi-symptom note | In scope: multi-complaint. Out of scope: triage. |

### Suggested Missing Tests
- **Symptom severity triage** — emergency phrasing should fast-transfer; not asserted.
- **Self-described code (e.g., "G47.31")** — not tested.
- **Visit reason in caller's first language** — not covered.

## test_unified_provider_search_flow.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | test_caller_names_provider_triggers_search_providers | Caller names provider -> search_providers fires | "Dr Smith"; expect search. | Name search | In scope: name. Out of scope: NPI. |
| 2 | | test_offers_previous_provider_from_appointment_history | Existing patient with appt history -> agent offers prior provider | History present; agent offers prior. | History offer | In scope: history. Out of scope: stale. |
| 3 | | test_compatible_search_mode_passes_filters_and_location | Compatible search mode passes filters + location to search | Search args include filter+location. | Filter passthrough | In scope: filter. Out of scope: insurance. |
| 4 | | test_blocked_provider_age_restriction_is_not_booked | Provider with age restriction -> not booked | Pediatric provider for adult; skipped. | Age restriction | In scope: age. Out of scope: gender. |
| 5 | | test_blocked_provider_does_not_perform_procedure | Provider doesn't perform requested procedure -> not booked | Procedure miss; skipped. | Procedure restriction | In scope: procedure. Out of scope: insurance. |
| 6 | | test_excluded_provider_triggers_immediate_transfer | Excluded provider per config -> immediate transfer | Excluded; transfer. | Excluded transfer | In scope: excluded. Out of scope: alt provider. |

### Suggested Missing Tests
- **Provider on leave / vacation** — temporal availability not asserted.
- **Provider gender preference** — caller asks "female provider"; not tested.
- **Provider with multiple specialties** — disambiguation not asserted.

## test_unified_reschedule_directives_flow.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | test_timeslot_taken_error_prompts_new_selection | Slot taken error -> agent re-prompts (doesn't claim success) | Reschedule selects slot; tool returns "taken"; agent says try again. | Stale-slot recovery | In scope: race. Out of scope: max retries. |
| 2 | | test_transfer_directive_does_not_claim_success | Transfer directive from tool -> agent transfers (no fake success) | Tool returns transfer directive; agent transfers; doesn't say "rescheduled". | No false-success | In scope: directive. Out of scope: details. |

### Suggested Missing Tests
- **Other tool error directives** — what if tool returns "needs_auth" or "policy_block"? Coverage unclear.
- **Multiple sequential errors** — race -> transfer -> race; not asserted.

## test_unified_reschedule_flow.py

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | | test_asks_when_to_reschedule_before_searching | Reschedule asks "when" before calling search | Initial reschedule turn -> agent asks for date pref before any search tool. | Pre-search prompt | In scope: prompt order. Out of scope: search args. |
| 2 | | test_uses_get_next_available_when_caller_has_no_preference | "Anytime" / no pref -> get_next_available used | "Whenever"; expect get_next_available. | No-pref path | In scope: no-pref. Out of scope: morning. |
| 3 | | test_morning_preference_uses_time_filter | "Morning" -> search args include morning time filter | "Morning"; search has time filter. | Time filter | In scope: morning. Out of scope: afternoon. |
| 4 | | test_no_availability_never_reuses_original_slot | No availability anywhere -> agent does NOT propose original slot | Empty + original-slot leak guard. | Original-slot guard | In scope: guard. Out of scope: transfer. |
| 5 | | test_no_afternoon_slots_triggers_follow_up_search | No afternoon -> agent does follow-up morning search | Pref + empty -> next search broadens. | Follow-up search | In scope: follow-up. Out of scope: pref preserve. |

### Suggested Missing Tests
- **Reschedule across providers** — user wants different provider on reschedule; not tested.
- **Reschedule virtual to in-person (or vice versa)** — modality switch on reschedule; only hybrid tested.
- **Reschedule with insurance change** — not covered.
- **Cross-week reschedule** — original Mon, new Sun; week boundary handling.

## Cross-cutting Unified Gaps

- **Coverage parity (new vs reschedule)** — `test_unified_new_appointment_*` covers auth, gender, insurance, name spelling, timeslot, visit reason. The reschedule path covers timeslot + directives but lacks parallel files for: insurance-change-on-reschedule, gender-collection-on-reschedule, name-spelling-on-reschedule. RECOMMEND parity audit.
- **Adversarial inputs** — none of the unified flows test prompt injection, role manipulation, or off-topic dumps.
- **State setup duplication** — `_TestX_Agent` factories in `fixtures/test_agents.py` already abstract this, but each file re-declares its own subclass (`_TestCancelAgent`, `_TestUnifiedConfirmAgent`, `_TestRescheduleAgent`, `_TestNewAppointmentAgent`, etc.). Consider a shared parametrized fixture.
- **Negative-path tool assertions** — only `test_transfer_directive_does_not_claim_success` and `test_no_availability_never_reuses_original_slot` assert "must NOT". Most tests assert only positive.
- **Latency budget** — long flows (location_selection 23 turns, timeslot 6 across 1000 lines) have no time budget; CI cost can balloon.
- **PHI in fixtures + transcripts** — DOBs ("November 3rd, 1982"), names ("Dr. Patel"), phones ("+15551234567") are throughout. None of the eval transcript persistence is verified to scrub.
- **Deterministic LLM** — most files use `eval_llm` / `eval_llm_no_thinking`; the latter implies known flake reduction. The `_no_thinking` convention should be documented and the regular `eval_llm` re-evaluated for flake risk.
- **Hybrid coverage gap** — hybrid_reschedule covers reschedule; no hybrid_cancel or hybrid_new_appointment.

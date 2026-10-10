# SmartBook four-week implementation plan review

Reviewed on October 10, 2026 against `SmartBook_PRD_v2.docx`,
`SmartBook_Technical_Design_and_Project_Plan_v2.docx`, and the repository README.
Section references below refer to the four-week plan unless another document is named.

The plan is a useful reduced delivery proposal, but it needs the clarifications below
before becoming an implementation baseline. It correctly keeps AI outside booking
correctness, preserves fixed slots, seat capacity, UTC timestamps and integer VND,
and specifies measurable concurrency and parser checks. The original DOCX is
preserved unchanged. Adding it to Git does not approve its proposed scope changes.

## Findings to resolve before implementation

| Priority | Reference | Finding and required clarification |
| --- | --- | --- |
| High | Sections 1, 9; PRD sections 15, 19; technical design section 21 | Team sign-off is still blank. Direct confirmation, omitted holds/payment, manual schedules, reduced cancellation and ranking all change the approved baseline. Record the approved deviations in both baseline Change Logs and update affected requirements, tests and demo gates when the team approves them. The document's change-control sentence cites PRD section 15 for the Change Log; the actual Change Log is section 19. |
| High | Sections 3, 7 | The idempotency lookup precedes the user-row lock. Two simultaneous requests can both miss that lookup. Recheck `(user_id, endpoint, key)` and the request hash after acquiring the user lock, before capacity/overlap checks; enforce a matching database uniqueness constraint and store the response in the booking transaction. Test concurrent same-key requests, changed-body reuse, and retry after a committed response is lost. Otherwise a retry can receive CONFLICT instead of the original success. |
| High | Sections 2, 4, 5, 9 | Provider publication is a P0 outcome, but no FE backlog item explicitly owns the provider form. The cut-order mitigation retains only an API and seeds, which no longer satisfies the promised provider UI flow. Assign the minimal form to Phat with an estimate and browser acceptance check, or explicitly approve an API-only provider demo as a scope change. |
| Medium | Sections 1, 5 | Weekly allocations exceed the stated 15-hour budget. Phat: 12/16/18/14; Bao: 12/16/18/14; Huy: 14/20/16/10 hours. Each sums to 60, and the team total is correctly 180, but Week 2 requires 52 team hours and Week 3 requires 52 against a 45-hour weekly budget. Rebalance or explicitly agree to peak weeks. Include review, integration, documentation and demo preparation in those estimates. |
| Medium | Sections 4, 5, 9 | The roadmap/backlog schedules CI and Compose in Week 4, while the risk mitigation says Compose in Week 1 and a clean launch rehearsal in Week 2. Move a minimal API/DB Compose launch and CI into BE-01; add frontend integration by Week 2. Leave Week 4 for regression and final packaging, with the work included in the estimates. |
| Medium | Sections 6, 7 | The provider-create contract and manual-slot model omit resource-overlap rules and mutation behavior. Define `starts_at < ends_at`, valid price/capacity bounds, and whether a resource may publish overlapping slots. Independent slot capacity checks cannot prevent double-selling one physical court through overlapping slots. Define permitted changes when bookings exist and make ownership and capacity validation transactional. |
| Medium | Sections 6, 7 | User-then-slot locking is a sensible proposed safeguard for simultaneous same-user bookings across different slots, but cancellation details are missing. Apply the same lock order to every capacity-changing operation, re-read state under lock, and define repeated cancellation and cancellation after slot start/end. Test cancellation racing with booking and duplicate cancellation. The existing technical design section 7 locks only the slot, so its self-overlap query alone does not serialize cross-slot requests by one user; section 18's single-lock claim must also be revised if this proposal is approved. |
| Medium | Sections 6, 7, 8 | Freeze the remaining API behavior: creation versus replay HTTP status, validation/auth/not-found errors, pagination and range limits, parser timeout, and server authority for the clock/timezone. Retain the baseline three-second parser timeout and login/parser rate limits and restricted CORS from technical design section 14. A client-supplied `nowInstant` should not silently override the production clock. |
| Medium | Sections 7, 8 | Ranking weights sum correctly to 1.0, but factor normalization, zero-budget behavior, tie-breaking and the alternative search window are unspecified. Clarify which constraints are hard filters and which may be relaxed for alternatives. In particular, an exact time filter can eliminate the documented '60 minutes later' suggestion. Exclude the customer's own overlapping bookings and compare total booking price. Specify deterministic fixture expectations. |
| Medium | Sections 8, 10; PRD sections 11, 12 | The reduced gates omit baseline performance/usability measurements and lower the 200-attempt metric to 50 requests repeated three times. Explicitly retain, defer or replace these targets in the approved deviation record. The referenced feasibility study and latest chat agreement are absent from this repository, so their claims cannot be independently checked here. |

## Checks completed

- Read the plan's body paragraphs and table text, and compared relevant baseline
  requirements, concurrency design, release gates and Change Logs.
- Confirmed October 12–November 8 spans four Monday-to-Sunday weeks.
- Checked all three 60-hour totals, the 180-hour team total and weekly allocations.
- Checked ranking weights: 0.55 + 0.30 + 0.15 = 1.0.
- Checked the parser threshold: at least 26/30 is the minimum integer pass count
  meeting 85 percent; it is approximately 86.7 percent.
- Confirmed this checkout contains planning documents, with no application code,
  migrations, OpenAPI contract or test suite to validate the proposed behavior.

This is a content review. DOCX page layout was not rendered or visually verified:
the documents skill's bundled dependency runtime and LibreOffice renderer were
not available in this Windows session. No application, security or concurrency
test results are claimed. The source DOCX was copied without edits and its SHA-256
hash checked against the supplied Downloads file before commit.

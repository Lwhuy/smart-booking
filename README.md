# SmartBook

Planning documents for SmartBook, a booking platform for sports facilities and local
activities. Student project — 3 third-year students, 10 weeks.

Stack: Java (Spring Boot) · Python (FastAPI) · PostgreSQL · React

## Repository structure

| Path | Responsibility |
| --- | --- |
| `backend-java/` | Authoritative Spring Boot API and booking transactions |
| `frontend/` | React customer and provider experience |
| `ai-service-python/` | FastAPI natural-language search parser and ranking experiments |
| `contracts/` | Shared API schemas and fixtures |
| `deploy/` | Production deployment configuration |
| `scripts/` | Setup, seed, demo, and verification commands |
| `tests/` | Cross-service load and system tests |

See [`docs/PROJECT_STRUCTURE.md`](docs/PROJECT_STRUCTURE.md) for the complete
folder map and ownership boundaries.

## Documents

| Document | Purpose |
| --- | --- |
| [`SmartBook_PRD_v2.docx`](SmartBook_PRD_v2.docx) | Product requirements. Numbered requirements (FR-n.m), business rules (BR-01…BR-16), resolved product decisions (D-01…D-08), edge cases (EC-01…EC-10), metrics with targets, and a traceability matrix. |
| [`SmartBook_Technical_Design_and_Project_Plan_v2.docx`](SmartBook_Technical_Design_and_Project_Plan_v2.docx) | Architecture, database schema, concurrency design, API, testing strategy, team split and the 10-week plan. |
| [`SmartBook_4_Week_Implementation_Plan.docx`](SmartBook_4_Week_Implementation_Plan.docx) | Proposed four-week delivery scope for October 12–November 8, 2026. Pending team sign-off; does not replace the approved PRD v2. |
| [`Four-week plan review`](docs/4-week-plan-review.md) | Content review against PRD v2 and technical design v2, with required clarifications before implementation. |
| `SmartBook_PRD_v1.docx` | Superseded — kept for reference. |
| `SmartBook_Technical_Design_and_Project_Plan_v1.docx` | Superseded — kept for reference. |

The PRD governs *whether*; the technical design governs *how*. Where they disagree, the PRD
wins, and the disagreement is a bug in one of them.

## Key decisions (PRD §15)

- **D-03/D-04** — The bookable unit is a fixed-length generated **slot** with a seat capacity.
  A booking consumes `party_size` seats of exactly one slot.
- **D-05** — Payment is **mocked**: a payment row plus an idempotent callback driving
  `PENDING_PAYMENT → CONFIRMED`. No real money, no card data.
- **D-07** — All instants stored **UTC**; display and relative-date parsing use the venue
  timezone (`Asia/Ho_Chi_Minh` in v1).
- **D-08** — Prices are **integer VND** with an explicit `PER_PERSON` / `PER_BOOKING` unit.

## The correctness problem

The centre of the project is the last-seat race. A booking transaction takes a row lock on the
slot (`SELECT … FOR UPDATE` at READ COMMITTED), recomputes effective capacity, sums the seats
held by active bookings, and only then inserts. See §7 of the technical design.

Each change to either document gets a Change Log entry (PRD §19, TDD §21).

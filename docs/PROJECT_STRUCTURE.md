# Project structure

This scaffold follows the SmartBook PRD v2 and technical design while using the
same planning-first layout as the reference e-commerce repository. Empty folders
contain `.gitkeep` until their first real file is added.

```text
.
├── .github/workflows/             CI workflows
├── backend-java/                  Spring Boot system of record
│   └── src/
│       ├── main/java/com/smartbook/
│       │   ├── auth/
│       │   ├── booking/
│       │   ├── catalog/
│       │   ├── notification/
│       │   ├── payment/
│       │   ├── recommendation/
│       │   ├── review/
│       │   ├── schedule/
│       │   └── search/
│       ├── main/resources/db/migration/
│       └── test/java/com/smartbook/
├── contracts/                     Shared schemas and fixtures
├── deploy/                        Production deployment files
├── docs/                          Architecture and delivery evidence
├── frontend/                      React application
│   ├── e2e/
│   └── src/features/
├── ai-service-python/             FastAPI search parser
│   ├── app/
│   └── tests/
├── scripts/                       Delivery and verification commands
└── tests/load/                    Cross-service load tests
```

The Java backend remains authoritative for availability, price, capacity, and
booking writes. The Python service only parses search intent and helps rank
candidates; SmartBook must still work when it is unavailable.

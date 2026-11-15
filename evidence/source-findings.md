# Source Code and Documentation Findings

- docs/status-lifecycle.md: Import Completed means workbook read and invoices validated; ASP submission continues in background. Invoice statuses include Parsed, Validated, Submitted, Accepted, Rejected and Failed. Accepted requires an ASP reference.
- services/api/internal/submit/submit.go: Status set Submitted before ASP request; handles 200, 422 and selected 5xx responses; no explicit 429 handling identified.
- services/asp-mock/main.go: senderBurst=8; senderRatePerSec=2.0.
- services/api/main.go: Submitter configured with 4 workers.
- services/api/internal/pint/pint.go: Net/VAT rounded to 2 decimals; gross calculated from raw quantity × unit price × (1+VAT), which can create a mismatch.
- services/api/internal/store/store.go: Duplicate lookup excludes only Rejected.
- docs/template-field-guide.md: Dates should be YYYY-MM-DD text or genuine Excel date cells.
- Runtime verification: Docker image downloads did not complete; no live database, Grafana or container-log evidence collected.

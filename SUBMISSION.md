# Submission – K. Logeswari

- Date: 14 November 2026
- Repository link: To be added
- Total time spent: Not accurately recorded

Date: 14 November 2026

Note: All customer replies below are proposed drafts, not messages actually sent.

## 1. Oasis Builders

**Acknowledgement:** Thank you for reporting this urgent issue. Please do not retry the complete workbook. We will reconcile the invoice statuses and ASP references and confirm which invoices were successfully submitted.

**Priority:** P1 provisional.

**Justification:** Multiple upload attempts, duplicate errors and uncertainty about invoice delivery create month-end business risk.

**Investigation:** The import lifecycle and duplicate validation logic were reviewed. Runtime database and ASP records still need checking.

**Root cause:** Not confirmed. Duplicate validation does not prove an invoice reached the ASP.

**Workaround:** Hold further uploads until invoice-level reconciliation is complete.

## 2. Marina Bay – API Restart

**Acknowledgement:** We acknowledge the repeated API restarts and intermittent 502 errors. We are checking service health and invoices stuck in Validated status.

**Priority:** P1.

**Justification:** Production API instability affects multiple customers and invoice submission.

**Investigation:** The alert reports three restarts within 15 minutes. Docker configuration shows a 128 MB API memory limit. Container logs were not available to confirm the restart cause.

**Root cause:** Not confirmed. Memory pressure is a hypothesis, not a proven cause.

**Workaround:** Avoid repeated uploads. Reconcile existing invoices before retrying.

## 3. Al Noor Fuel – BR-CO-15

**Acknowledgement:** Thank you for sharing the workbook. We reviewed the net, VAT and gross calculation logic and identified a possible rounding inconsistency.

**Priority:** P2.

**Justification:** Multiple invoices in one customer batch are rejected while others are accepted. The issue also affects VAT audit preparation.

**Investigation log:**
* Reviewed the workbook and template rules.
* Inspected services/api/internal/pint/pint.go.
* Compared rounded net plus VAT with calculated gross.
* Reproduced differences in invoices 0042, 0044 and 0046.

**Root cause:** Gross is calculated from raw quantity and unit price, while net and VAT are rounded separately. This can cause BR-CO-15 mismatches.

**Evidence:** evidence/al-noor-calculation.md and evidence/source-findings.md.

**Customer reply:** The calculation review identified a rounding inconsistency. Please do not change quantities or prices manually. We are escalating the calculation defect and will confirm affected invoices before resubmission.

**Workaround:** Regenerate and validate only confirmed rejected invoices after the calculation is corrected.

**Prevention:** Apply consistent rounding and add automated BR-CO-15 tests.

## 4. Bluepeak ERP – Sahara Medical

**Acknowledgement:** HTTP 201 confirms that the portal accepted the import request, but it does not prove ASP delivery. Please do not resend the batch until reconciliation is complete.

**Priority:** P1 provisional.

**Justification:** A 40-invoice batch has a reported delivery gap and several invoices have no ASP reference.

**Investigation log:**
* Reviewed the ASP API documentation.
* Inspected services/api/internal/submit/submit.go.
* Reviewed the ASP rate limiter in services/asp-mock/main.go.
* Found an unhandled HTTP 429 response path.

**Root cause:** The code sets invoice status to Submitted before sending. It does not explicitly handle HTTP 429. The ASP mock has a burst limit of 8 and rate of 2 requests per second. This is a confirmed code defect, but its occurrence in this particular batch is not yet verified.

**Evidence:** evidence/source-findings.md.

**Customer reply:** We identified a possible rate-limit handling gap. We are checking portal statuses against ASP records. Please hold the batch and do not resend yet.

**Workaround:** Retry only invoices confirmed absent from ASP records.

**Prevention:** Handle 429, honour Retry-After, implement backoff and monitor Submitted invoices without ASP references.

## 5. Ghaf Tree Café

**Acknowledgement:** Thank you for sharing the workbook. The date may be correct, but its format does not match the upload requirement.

**Priority:** P3.

**Justification:** One customer is affected by a documented input-format issue with a simple workaround.

**Investigation log:** Reviewed docs/template-field-guide.md. The required date format is YYYY-MM-DD or a genuine Excel date cell. The reported text value 13/11/2026 does not match the required format.

**Root cause:** Unsupported date representation in the uploaded workbook.

**Customer reply:** Please change the text date to 2026-11-13 or use a genuine Excel date cell, then upload a corrected copy.

**Workaround:** Correct the date format and validate the workbook before uploading again.

**Prevention:** Provide a validated template and clearer date-format instructions.

## Overall Prioritisation

Marina Bay, Oasis and Bluepeak were treated as provisional P1 because of possible production or batch-level impact. Al Noor was P2 because a subset of invoices was rejected. Ghaf was P3 because it is a single-customer formatting issue.

## What I would do differently

I would verify the local environment earlier and collect database, API and ASP evidence in parallel.

## What I learned

I learned the difference between import completion and ASP submission, invoice lifecycle statuses, ASP rate limiting, and rounding requirements in e-invoicing. I would study Go worker queues, PostgreSQL queries, Grafana and PINT AE validation further.

## Time Log

14 November 2026 – Repository setup, documentation and source-code review, workbook calculations and report preparation. Exact hours not recorded.

## AI Usage Disclosure

I used ChatGPT to help interpret the supplied documentation and source code, calculate sample totals and draft customer replies. I checked findings against the repository and workbook. Docker image downloads did not complete, so live database, Grafana and container-log verification could not be performed. Unverified runtime findings are clearly identified.

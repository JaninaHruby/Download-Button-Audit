# SaaS Release Impact Analysis — Download Button Implementation Audit

## Background

A SaaS company was facing a high volume of support tickets, driving up operational costs. Analysis of the incoming requests revealed that a large portion were general product inquiries. To address this, the company published a detailed product manual as a PDF download on their website as part of **Release v1.2 (PDF flow update)**.

## Objective

Determine whether the introduction of the PDF download actually led to a reduction in support requests — and investigate any unintended side effects, such as new backend errors introduced by the release.

## Dataset

To ensure an unbiased Exploratory Data Analysis, I created a synthetic dataset using Python's Faker module with AI support. The data structure was specifically designed to reflect real-world business scenarios of a SaaS company, including realistic relationships between users, sessions, tickets, and error logs.

The dataset consists of five tables:

| Table | Description |
|---|---|
| `deployment_history_logs` | Release timeline with version numbers and deployment dates |
| `raw_crm_customer_data` | Customer profiles including browser, device, and OS |
| `sys_backend_error_logs` | Backend error logs with timestamps, messages, and endpoints |
| `web_tracking_event_log` | Session tracking with user IDs, duration, and PDF download clicks |
| `zendesk_tickets_export` | Support tickets with categories, priorities, and timestamps |

## Approach

### Part 1: Exploratory Data Analysis (Python)

Initial data inspection using Pandas to understand the structure of each table. Looking at the backend error logs, one entry immediately stood out: a `500 Internal Server Error` on `/api/pdf/download` — the very endpoint introduced in v1.2. This raised the question of whether these errors were isolated incidents or a systemic pattern. The web tracking table provided the missing link: it connects `session_id` to `user_id`, making it possible to trace errors back to specific users and their device configurations. This prompted the move to SQL for structured cross-table analysis.

### Part 2: SQL Analysis (Databricks)

Starting with the most obvious question — did ticket volume actually drop after the release? The answer was no. That result alone justified looking further: if the feature did not reduce overall tickets, did it at least affect the right category? Filtering for product inquiries specifically showed a 14% decrease, which confirmed the PDF content was reaching users as intended. But the simultaneous rise in bug reports pointed to a technical problem introduced by the release itself. Narrowing down to the backend error logs confirmed that all errors originated from a single endpoint. The final step — a JOIN across CRM, tracking, and error data — revealed that no specific browser, device, or OS combination was responsible, ruling out a client-side issue and pointing directly to a backend root cause.

## Key Findings

| Metric | Before Release | After Release | Change |
|---|---|---|---|
| Total support tickets | 1,399 | 1,601 | +14.4% |
| Product inquiries | 769 | 661 | **-14.0%** |
| Bug reports | 157 | 257 | +63.7% |
| Backend errors | 3,084 | 3,590 | +16.4% |

- **Product inquiries decreased by 14%**, suggesting the PDF manual is having its intended effect.
- However, **bug reports increased by 64%** and **backend errors rose by 16%** after the release.
- All 6,674 backend errors are `Internal Server Error` on a single endpoint: `/api/pdf/download`.
- **Note on data quality:** The fact that PDF download errors appear *before* the download button was even released is a limitation of the synthetic data generated with Faker. In a real-world scenario, these errors could only occur after the feature was deployed. This is an important reminder that synthetic datasets require careful validation against the business logic they are meant to represent.

## Recommendation

The `/api/pdf/download` endpoint requires immediate investigation. The error distribution across all browser, device, and OS combinations rules out a client-side compatibility issue — no specific configuration is responsible, which points directly to the server-side implementation. Likely candidates are file generation under load, missing timeout handling, or insufficient error recovery logic.

The 14% drop in product inquiries shows the PDF content itself is working as intended. The feature has merit — but it cannot deliver its full potential while the backend is unstable. A targeted hotfix, scoped specifically to the download endpoint, should be prioritized before the next release cycle.

## Project Status

- [x] Create synthetic dataset (Python / Faker)
- [x] Initial data inspection (Pandas)
- [x] Set up database environment (PostgreSQL / DBeaver / Databricks)
- [x] Import CSV data into SQL tables
- [x] Analyze support ticket trends (pre- vs. post-release)
- [x] Analyze backend error trends (total errors, error categories, affected endpoints)
- [x] Investigate error distribution by browser, device, and OS (JOIN analysis)
- [x] Final summary and business impact report

## Tools Used

Python (Pandas, Faker) · SQL · PostgreSQL · DBeaver · Databricks Community Edition

## File Structure

```
├── README.md
├── SaaS_Release_Impact_Analysis.ipynb    # Combined notebook (Python EDA + SQL)
├── deployment_history_logs.csv
├── raw_crm_customer_data.csv
├── sys_backend_error_logs.csv
├── web_tracking_event_log.csv
└── zendesk_tickets_export.csv
```

# SaaS Release Impact Analysis — Download Button Implementation Audit

## Background

A SaaS company was facing a high volume of support tickets, driving up operational costs. Analysis of the incoming requests revealed that a large portion were general product inquiries. To address this, the company published a detailed product manual as a PDF download on their website as part of **Release v1.2 (PDF flow update)**.

## Objective

Determine whether the introduction of the PDF download actually led to a reduction in support requests — and investigate any unintended side effects, such as new backend errors introduced by the release.

## Dataset

To ensure an unbiased Exploratory Data Analysis, I created a synthetic dataset using Python's Faker module. The data structure was specifically designed to reflect real-world business scenarios of a SaaS company, including realistic relationships between users, sessions, tickets, and error logs.

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

Initial data inspection using Pandas to understand the structure of each table, identify relationships between them (e.g. linking `session_id` and `user_id` across tables), and formulate key questions for the SQL analysis.

### Part 2: SQL Analysis (Databricks)

Structured queries to answer the core business questions, comparing metrics before and after the deployment of Release v1.2.

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

## Project Status

- [x] Create synthetic dataset (Python / Faker)
- [x] Initial data inspection (Pandas)
- [x] Set up database environment (PostgreSQL / DBeaver / Databricks)
- [x] Import CSV data into SQL tables
- [x] Analyze support ticket trends (pre- vs. post-release)
- [x] Analyze backend error trends
- [x] Identify affected endpoints
- [ ] Investigate error correlation with browser/OS
- [ ] Narrow down exact timing of error occurrences
- [ ] Perform relational analysis (JOINs) to link users and tickets
- [ ] Final summary and business impact report

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

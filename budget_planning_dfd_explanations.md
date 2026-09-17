# Data Flow Diagram — Budget Planning Application
### Supporting Explanations (for report / viva use)

Open `budget_planning_dfd.drawio` in [draw.io](https://app.diagrams.net) — it contains all 8 diagrams below as separate tabs, in Gane–Sarson notation (rectangle = external entity, rounded rectangle = process, open-ended rectangle = data store). Export each tab as PNG/SVG and place it above the matching section below in your report.

---

## 1. DFD Level 0 — Context Diagram

**Purpose:** Shows the Budget Planning Application as a single process interacting with its only external entity, the **User**.

- **Process 0.0 – Budget Planning Application:** represents the entire system as a black box — registration/login, income and expense tracking, budgeting, analytics, and alerts are not yet broken out.
- **External Entity – User:** the sole actor; sends financial data and requests, and receives status, reports, and alerts back.
- **Data flow in:** registration/login details, income and expense entries, budget and category details, and report requests.
- **Data flow out:** login/registration status, dashboard information, budget status, expense/income confirmations, reports and analytics, and budget alerts.

This diagram establishes the system boundary before any internal decomposition.

---

## 2. DFD Level 1 — Complete System

**Purpose:** Decomposes Process 0.0 into its six major functional processes and introduces the four data stores.

| Process | What it does | Data store |
|---|---|---|
| 1.0 User Authentication | Registers new users, validates and stores credentials, logs users in, and authorizes sessions | D1 User Data |
| 2.0 Income Management | Adds, edits, deletes, and retrieves income entries after validation | D2 Income Data |
| 3.0 Expense Management | Adds, edits, deletes, retrieves, and categorizes expense entries | D3 Expense Data |
| 4.0 Budget Management | Creates, updates, deletes, and tracks category-wise budgets | D4 Budget Data |
| 5.0 Analytics and Reports | Reads income, expense, and budget data to compute summaries, spending analysis, and charts | Reads D2, D3, D4 |
| 6.0 Notification Management | Compares expenses against budgets to detect overspending and generate alerts | Reads D3, D4 |

**Data stores:**
- **D1 User Data** — user profile and authentication credentials.
- **D2 Income Data** — all recorded income transactions per user.
- **D3 Expense Data** — all recorded expense transactions, with category tags, per user.
- **D4 Budget Data** — budget limits set overall and per category.

**How data flows:** the User supplies details to Processes 1.0–4.0, which validate and persist them in their respective stores and return confirmations/history. Processes 5.0 and 6.0 don't take direct user input for their core function — they continuously read the stores to produce dashboards/reports (5.0) and threshold-based alerts (6.0) back to the User. This satisfies balancing with Level 0: every input/output category from the context diagram reappears here, split across the six processes.

---

## 3. DFD Level 2 — Process 1.0 User Authentication

Splits into two flows that share the D1 User Data store:

- **Registration path (1.1 → 1.2 → 1.3):** the user's registration details are validated (e.g., checking for duplicate accounts, required fields); valid data is stored as a new record in D1, and a confirmation goes back to the user, while invalid data returns a validation error.
- **Login path (1.4 → 1.5 → 1.6):** login credentials are checked against stored credentials in D1; a match creates an authenticated session (1.6), otherwise a login error is returned.

D1 User Data is written to during registration and read from during login credential validation.

---

## 4. DFD Level 2 — Process 2.0 Income Management

Four parallel sub-flows around D2 Income Data:

- **Add (2.1 → 2.2 → 2.3):** income details are validated, then stored as a new record; confirmation or a validation error is returned.
- **Update (2.4):** reads the existing record from D2, writes the updated version back, confirms to the user.
- **Delete (2.5):** removes a record from D2 and confirms.
- **Retrieve (2.6):** reads stored records from D2 and returns income history to the user.

---

## 5. DFD Level 2 — Process 3.0 Expense Management

Mirrors Income Management but adds a categorization step:

- **Add (3.1 → 3.2 → 3.3 → 3.4):** expense details are validated, assigned a category, then stored in D3; confirmation or error returned.
- **Update (3.5)** and **Delete (3.6):** modify or remove existing D3 records, with confirmations.
- **Retrieve (3.7):** reads D3 and returns expense history.

---

## 6. DFD Level 2 — Process 4.0 Budget Management

- **Create (4.1 → 4.2 → 4.3 → 4.4):** budget details are validated, category-wise limits are set, and the record is stored in D4; a creation confirmation or validation error is returned.
- **Update (4.5):** reads and rewrites an existing D4 record, with confirmation.
- **Usage tracking (4.6 → 4.7 → 4.8):** retrieves stored budget data from D4, calculates how much of the budget has been used, and displays the current budget status to the user.

---

## 7. DFD Level 2 — Process 5.0 Analytics and Reports

- **Retrieval (5.1, 5.2, 5.3):** independently pull income, expense, and budget records from D2, D3, and D4 respectively, triggered by the user's report/analytics request.
- **Calculation (5.4, 5.5, 5.6):** 5.4 combines income and expense data into an overall financial summary; 5.5 breaks expense data down by category; 5.6 compares budget data against expenses to compute what remains.
- **Output generation (5.7, 5.8):** the calculated results feed into chart generation and report generation respectively.
- **5.9 Display Analytics:** consolidates charts and reports into the dashboard/report view returned to the user.

---

## 8. DFD Level 2 — Process 6.0 Notification Management

- **6.1 / 6.2:** retrieve current budget and expense records from D4 and D3.
- **6.3 Compare Expenses with Budget:** checks actual spending against the allocated budget.
- **6.4 Detect Budget Threshold:** flags when spending crosses a warning or limit threshold.
- **6.5 Generate Alert → 6.6 Send Notification:** builds and delivers the alert (budget alert / overspending notification) to the user.

This process only reads from D3 and D4 — it does not write to any store, consistent with its role as a monitoring/alerting function.

---

## Notes on notation and balancing

- **External Entity** = rectangle, **Process** = rounded rectangle (numbered per DFD convention: `n.0` at Level 1, `n.m` at Level 2), **Data Store** = open-ended rectangle.
- Every specific data-flow label avoids generic terms like "data" or "request" in favor of concrete names (e.g. *Validated Income Data*, *Budget Alert*).
- Inputs/outputs are preserved across levels: what enters/leaves Process 2.0 at Level 1 (income details, update/delete requests, confirmations, history) is exactly what its Level 2 sub-processes collectively consume and produce — the same balancing rule applies to Processes 1.0, 3.0, 4.0, 5.0, and 6.0.

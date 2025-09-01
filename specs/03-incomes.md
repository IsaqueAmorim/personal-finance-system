# 03 - Incomes Module

## General Description

The Incomes module allows users to register, track, and manage their income sources, including **recurring** (salary, pensions) and **one-time** (freelance, refunds, sales) incomes.

It integrates with **Open Finance** for automatic imports and reconciliations, while supporting **manual inputs**, **categorization**, **reporting**, and **receipt attachments**.

---

## Entities (TypeScript)

### Income

```ts
export class Income {
  id: string; // UUID
  userId: string;
  sharedAccountId?: string;
  description: string;
  amount: number; // stored in cents
  categoryId: string;
  type: 'ONE_TIME' | 'RECURRING';
  status: 'PENDING' | 'RECEIVED' | 'CANCELLED';
  dueDate?: Date;
  receivedAt?: Date;
  recurrenceRule?: string; // RRULE string format (e.g. FREQ=MONTHLY)
  source: 'MANUAL' | 'OPEN_FINANCE' | 'IMPORT';
  attachmentUrl?: string;
  createdAt: Date;
  updatedAt: Date;
}
```

### IncomeCategory

```ts
export class IncomeCategory {
  id: string; // UUID
  userId: string;
  name: string;
  description?: string;
  color: string; // UI representation
  createdAt: Date;
  updatedAt: Date;
}
```

---

## Features

### Income Management

- Create **manual incomes** with description, amount, category, and due date.
- Manage **recurring incomes** via RRULE expressions (monthly salary, etc.).
- Upload **attachments** (e.g., invoices, receipts).
- Track history of received and pending incomes.
- Mark incomes as `RECEIVED`, `PENDING`, or `CANCELLED`.

### Categories

- Predefined categories: Salary, Bonus, Freelance, Refund, Others.
- User-defined categories with custom colors.
- Categories used for dashboard aggregations.

### Automatic Imports

- Import from **Open Finance** (connected accounts).
- Automatic reconciliation against manual entries with similarity scoring.
- Flag potential duplicates for user review.

### Analytics & Dashboard

- Monthly/annual income summaries.
- Breakdown by categories (pie charts).
- Trends over time (line charts).
- Export incomes as CSV/Excel/PDF.

---

## Business Rules

- Required fields: `description`, `amount`, `categoryId`.
- Only `PENDING` incomes can be marked as `RECEIVED`.
- Recurring incomes auto-generate future events until stopped.
- Imported incomes must include reference to source institution.
- Amounts are stored in **cents** to avoid floating-point inconsistencies.
- Incomes linked to shared accounts must keep **member attribution**.

---

## API Endpoints

### Incomes

- `POST /api/incomes` → Create new income.
- `GET /api/incomes` → List all incomes (filters: date, status, category).
- `GET /api/incomes/:id` → Get details of specific income.
- `PUT /api/incomes/:id` → Update income details.
- `DELETE /api/incomes/:id` → Delete income.
- `POST /api/incomes/:id/mark-received` → Mark income as received.
- `POST /api/incomes/:id/cancel` → Cancel income.

### Categories

- `POST /api/income-categories` → Create income category.
- `GET /api/income-categories` → List categories.
- `PUT /api/income-categories/:id` → Update category.
- `DELETE /api/income-categories/:id` → Delete category.

### Imports & Reconciliation

- `POST /api/incomes/import` → Upload & process import file (CSV/OFX).
- `POST /api/incomes/reconcile` → Reconcile imported with manual income.
- `GET /api/incomes/open-finance-sync` → Trigger sync with bank accounts.

---

## Integration Points

- **Dashboard Module** → Provides income totals and trend visualizations.
- **Expenses Module** → Used for net cash flow calculations.
- **Savings Goals** → Percentage of income auto-allocated to goals.
- **Open Finance Module** → Auto-import bank credits as incomes.
- **Shared Accounts** → Collaborative income tracking per member.
- **Notifications Module** → Reminders for pending incomes and alerts when credits arrive.

---

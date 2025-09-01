# 11 - Use Cases Module

## General Description

This module contains **example user flows** that demonstrate how different modules of the system interact in real-world scenarios.

Use cases illustrate **end-to-end processes**, combining APIs, background jobs, notifications, and reconciliation to show the system’s value to the user.

---

## Use Case 1: Register Recurring Expense

### Scenario

A user wants to register their **monthly rent** payment as a recurring expense.

### Flow

1. User navigates to **"Add Expense"** form.
2. Selects type → **Recurring**.
3. Enters details:
   - description: `"Monthly Rent"`
   - amount: `R$ 1.500,00`
   - category: `"Housing & Utilities"`
   - dueDate: every **5th of the month**
   - paymentMethod: `BANK_TRANSFER`
4. Configures recurrence:
   - frequency: `MONTHLY`
   - startDate: current month
   - endDate: `null` (ongoing)
5. Submits.
6. System validates and stores record in `expenses` table.
7. A background job generates the next 12 months of expense entries.
8. Notifications module schedules reminders 3 days before due date.

### Expected Outcome

- User has a recurring expense automatically tracked.
- System ensures reminders and correct status (`PENDING` → `PAID` or `OVERDUE`).

---

## Use Case 2: Automatic Bill Due Notification

### Scenario

User has upcoming bills and wants to be notified before the due date.

### Flow

1. Cron job runs daily at `09:00`.
2. System queries expenses with `dueDate <= next 3 days` and `status = PENDING`.
3. For each expense:
   - Check notification preferences.
   - Create `Notification` record in DB.
   - Push event to **Redis queue**.
4. Workers send alerts (Email/WhatsApp).
5. System updates notification `status = SENT`.
6. Failed notifications retried (max 3 times, exponential backoff).

### Expected Outcome

- User receives reminders via configured channels.
- Reduced chance of overdue expenses.

---

## Use Case 3: Open Finance Transaction Reconciliation

### Scenario

A user connects their **bank account** and system auto-reconciles bank transactions.

### Flow

1. User initiates bank sync via OAuth flow.
2. System fetches 90 days of transactions.
3. For each imported transaction:
   - Match with existing expenses/incomes (`description + amount + date`).
   - If confidence score > 85% → auto-link.
   - If 60–85% → flagged for manual review.
   - If < 60% → created as new transaction.
4. System displays reconciliation dashboard:
   - Auto-matched → user confirms.
   - Ambiguous → user resolves.

### Expected Outcome

- Transactions automatically enriched with categories.
- Manual bookkeeping reduced drastically.

---

## Use Case 4: Shared Account Expense Collaboration

### Scenario

A couple uses a **shared account** to manage household expenses.

### Flow

1. User A creates shared account "Household Expenses".
2. User B receives invitation and joins.
3. User B logs grocery purchase → `expense.split = 50/50`.
4. Shared account dashboard shows:
   - User A spent R$ 1.200 (60%).
   - User B spent R$ 800 (40%).
5. System suggests rebalancing for fairness.

### Expected Outcome

- Transparent expense sharing.
- Reduced conflict in shared finances.

---

## Use Case 5: Savings Goal Achievement

### Scenario

A user wants to save for a **vacation trip**.

### Flow

1. Creates `Goal`:
   - name: `"Europe Trip"`
   - targetAmount: `R$ 15.000,00`
   - targetDate: `Dec/2024`.
2. Configures Auto Savings:
   - monthlyAmount: R$ 1.250.
   - dayOfMonth: 5.
3. System executes scheduled transfers monthly.
4. Extra deposits added (bonuses, refunds).
5. Notifications at milestones: 25%, 50%, 75%, 100%.
6. Goal status changes to **COMPLETED**.

### Expected Outcome

- User progressively saves and reaches financial target by planned date.

---

## Key Observations

- Each use case leverages **multiple modules** (Expenses, Incomes, Goals, Open Finance, Shared Accounts, Notifications).
- Background jobs and **Redis-based queues** are key for async flows.
- **Audit logs** track every critical step for accountability.

---

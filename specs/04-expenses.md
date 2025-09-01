# 04 - Expenses Module

## General Description

The Expenses module allows users to track, manage, and analyze their spending.  
It supports **one-time** and **recurring expenses**, budgeting by category, automatic reminders, and receipt attachments for better accountability.

This module integrates tightly with the **Dashboard**, **Notifications**, and **Open Finance** for seamless financial control.

---

## Entities

```typescript
export interface Expense {
  id: string; // UUID
  description: string;
  amount: number; // in cents, for accuracy
  categoryId: string; // FK to ExpenseCategory
  type: 'ONE_TIME' | 'RECURRING';
  dueDate: Date;
  paidDate?: Date;
  status: 'PENDING' | 'PAID' | 'OVERDUE' | 'CANCELLED';
  paymentMethod:
    | 'CASH'
    | 'CREDIT_CARD'
    | 'DEBIT_CARD'
    | 'PIX'
    | 'BANK_TRANSFER'
    | 'BOLETO';
  notes?: string;
  receiptUrl?: string; // stored in cloud storage
  userId: string;
  sharedAccountId?: string;
  createdAt: Date;
  updatedAt: Date;
}

export interface ExpenseCategory {
  id: string; // UUID
  name: string;
  description?: string;
  color: string; // hex format
  icon?: string;
  monthlyBudget?: number; // in cents
  isActive: boolean;
  userId: string;
  createdAt: Date;
}
```

## Features

### One-Time Expenses

- Quick entry form with validation
- Attach receipts via upload (PDF, JPEG, PNG)
- OCR (optical character recognition) for automatic metadata extraction
- Support for multiple payment methods
- Expense splitting (for shared accounts)

### Recurring Expenses

- Flexible scheduling: Monthly, Quarterly, Semi-Annual, Annual
- Ability to handle variable amounts (e.g., water, electricity bills)
- Automatic reminders before due dates
- Pause/Resume recurring series
- Automatic generation of future installments

### Budgets per Category

- Monthly budget allocation per category
- Tracking overspending (alerts at 80%, 100%, 120%)
- Budget rollover (carry-over surplus or deficit to next month)
- Budget reporting and comparison with actual expenses

### Receipts Attachment

- Upload receipt images or PDFs
- OCR extraction for merchant name, date, amount
- Secure cloud storage with expiring signed URLs for retrieval
- Linking receipts to expenses for auditing

### Categorization Rules

- Default categories:
  - Food & Dining
  - Transportation
  - Housing & Utilities
  - Health
  - Education
  - Entertainment
  - Shopping
  - Insurance
  - Others
- Custom categories supported
- Color and icon customization
- Auto-categorization of expenses via **bank transactions (Open Finance)**

---

## Business Rules

- Pending expenses should automatically be marked **OVERDUE** after dueDate passes.
- Recurring expenses generate their next occurrence **one cycle in advance**.
- Deleting an expense with a receipt should also remove the file from cloud storage.
- Shared account expenses must store **attribution** (who created, who paid).

---

## API Endpoints

### Expenses

- `POST /api/expenses` → Create new expense
- `GET /api/expenses` → List expenses (filters: status, type, date range, categoryId, payment method)
- `GET /api/expenses/:id` → Retrieve specific expense
- `PUT /api/expenses/:id` → Update expense
- `DELETE /api/expenses/:id` → Delete expense
- `POST /api/expenses/:id/pay` → Mark as paid (requires paidDate & paymentMethod)
- `POST /api/expenses/:id/receipt` → Upload receipt

### Categories

- `POST /api/expense-categories` → Create category
- `GET /api/expense-categories` → List categories
- `PUT /api/expense-categories/:id` → Update category
- `PUT /api/expense-categories/:id/budget` → Configure monthly budget
- `GET /api/expense-categories/:id/budget-status` → Retrieve budget status
- `DELETE /api/expense-categories/:id` → Delete category

---

## Integration Points

- **Dashboard** → Provides expense distribution by category, monthly totals, and trends
- **Notifications** → Sends alerts for upcoming due dates and overspending
- **Open Finance** → Bank transactions can be auto-imported and linked to expenses
- **Shared Accounts** → Enables multiple users to contribute or be attributed in expenses

---

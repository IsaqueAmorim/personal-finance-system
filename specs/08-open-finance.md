# 08 - Open Finance Module

## General Description

The Open Finance module integrates the system with Brazilian financial institutions, enabling automatic **bank account synchronization**, **transaction import**, and **categorization**.

It provides secure OAuth-based flows with banks, reconciliation of imported transactions, and AI-driven **auto-categorization rules**.

---

## Entities (TypeScript)

### BankAccount

```ts
export class BankAccount {
  id: string; // UUID
  userId: string;
  institutionName: string;
  accountType: 'CHECKING' | 'SAVINGS' | 'INVESTMENT';
  accountNumber: string; // encrypted
  agency: string;
  currentBalance?: number; // in cents
  lastSyncAt?: Date;
  isActive: boolean;
  openFinanceId: string; // external system ID
  accessToken: string; // encrypted
  refreshToken: string; // encrypted
  tokenExpiresAt: Date;
  createdAt: Date;
  updatedAt: Date;
}
```

### BankTransaction

```ts
export class BankTransaction {
  id: string; // UUID
  bankAccountId: string;
  externalId: string; // bank’s transaction ID
  description: string;
  amount: number; // in cents
  type: 'CREDIT' | 'DEBIT';
  transactionDate: Date;
  balanceAfter?: number; // in cents
  category?: string; // auto-categorized
  expenseId?: string; // linked expense
  incomeId?: string; // linked income
  isReconciled: boolean;
  createdAt: Date;
}
```

---

## Features

### Bank Connection

- OAuth2 flow to securely connect bank accounts.
- Support for multiple accounts per user.
- Secure storage of tokens (AES-256 encryption).
- Automatic token refresh.
- Monitoring of connection status.

### Automatic Transaction Import

- Daily scheduled sync jobs.
- Fetches last X days of transactions on each sync.
- Detects duplicates to avoid double entries.
- Real-time balance update for linked accounts.

### Smart Categorization

- Initial categorization using **Open Finance data fields**.
- Machine learning algorithm to classify expenses/incomes.
- Supports user-defined rules (e.g., "Uber → Transportation").
- Learns from user corrections.
- Assigns confidence score to each automatic categorization.

### Reconciliation Flows

- Automatic matching of bank transactions with existing manual entries:
  - Match on description + amount + date.
  - Fuzzy match with similarity scores.
- Manual reconciliation for ambiguous cases.
- Discrepancy reporting (differences flagged to user).
- Bulk reconciliation options for faster matching.

---

## Business Rules

- Only accounts with valid OAuth tokens can sync.
- Duplicates are detected using `(externalId + bankAccountId)`.
- Transactions older than 12 months are archived, not deleted.
- A bank account must be **active** to accept reconciliation.
- User consent must be explicit before connecting accounts.

---

## API Endpoints

### Institutions

- `GET /api/open-finance/institutions` → List supported institutions.

### Accounts

- `POST /api/open-finance/connect` → Start OAuth connection with a bank.
- `GET /api/open-finance/accounts` → List connected accounts.
- `DELETE /api/open-finance/accounts/:id` → Disconnect account.

### Transactions

- `POST /api/open-finance/sync` → Trigger sync for specific account.
- `GET /api/open-finance/transactions` → List synchronized transactions.
- `POST /api/open-finance/reconcile` → Reconcile a bank transaction with manual entry.
- `PUT /api/open-finance/categorize` → Update or correct transaction category.

---

## Integration Points

- **Expenses Module** → Bank DEBIT transactions imported and reconciled as expenses.
- **Incomes Module** → Bank CREDIT transactions matched as incomes.
- **Dashboard Module** → Feeds account balances and transaction history.
- **Notifications Module** → Alerts for failed syncs or unexpected activity.
- **Shared Accounts** → Support linking team/group bank accounts.

---

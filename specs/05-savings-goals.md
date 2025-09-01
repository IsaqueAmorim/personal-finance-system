# 05 - Savings Goals Module

## General Description

The Savings Goals module helps users define, track and achieve financial objectives — such as building an emergency fund, saving for a trip, or purchasing an asset.

It offers **visual progress bars**, **automatic savings scheduling**, and **goal simulations** to encourage financial discipline.

---

## Entities (TypeScript)

### Goal

```ts
export class Goal {
  id: string; // UUID
  name: string;
  description?: string;
  targetAmount: number; // in cents
  currentAmount: number; // in cents
  targetDate: Date;
  category: 'EMERGENCY' | 'TRAVEL' | 'PURCHASE' | 'INVESTMENT' | 'OTHER';
  priority: 'LOW' | 'MEDIUM' | 'HIGH';
  status: 'ACTIVE' | 'PAUSED' | 'COMPLETED' | 'CANCELLED';
  userId: string;
  sharedAccountId?: string;
  createdAt: Date;
  updatedAt: Date;
}
```

### GoalTransaction

```ts
export class GoalTransaction {
  id: string; // UUID
  goalId: string;
  type: 'DEPOSIT' | 'WITHDRAWAL';
  amount: number; // in cents
  description?: string;
  transactionDate: Date;
  userId: string;
  createdAt: Date;
}
```

### GoalAutoSavingsConfig

```ts
export class GoalAutoSavingsConfig {
  id: string; // UUID
  goalId: string;
  isEnabled: boolean;
  monthlyAmount: number; // cents
  dayOfMonth: number; // 1-31
  sourceAccountId?: string;
  createdAt: Date;
  updatedAt: Date;
}
```

---

## Features

### Goal Management

- Create and edit savings goals with details (name, target amount, target date, category, priority).
- Track progress with **visual progress bars**.
- Calculate **percentage completion** automatically.
- Estimate **time to goal** based on transaction history.
- Pause or resume goals anytime.

### Goal Transactions

- Register deposits and withdrawals.
- Each transaction updates the **currentAmount**.
- Transactions can be linked with **bank imports** (Open Finance).

### Automatic Savings

- Schedule recurring monthly deposits.
- Round-up option (e.g. R$ 9,75 → deposit R$ 0,25).
- Percentage-based savings from income (e.g. 10% of salary).
- Enable/disable anytime.

### Savings Simulator

- Calculate required monthly savings to reach a target by set date.
- Run “what-if” scenarios: vary amount vs duration.
- Include optional **inflation and interest projections**.
- Save or export simulation results.

---

## Business Rules

- `status` transitions automatically:
  - **COMPLETED** when `currentAmount >= targetAmount`.
  - **PAUSED** if auto-savings disabled but not cancelled.
  - **CANCELLED** only by explicit user action.
- `currentAmount` must never go negative.
- Only one **active GoalAutoSavingsConfig** can exist per goal at a time.

---

## API Endpoints

### Goals

- `POST /api/goals` → Create new goal
- `GET /api/goals` → List goals (filters: status, category, priority)
- `GET /api/goals/:id` → Get goal details
- `PUT /api/goals/:id` → Update goal
- `DELETE /api/goals/:id` → Delete goal
- `POST /api/goals/:id/pause` → Pause goal
- `POST /api/goals/:id/resume` → Resume goal

### Goal Transactions

- `POST /api/goals/:id/transactions` → Add transaction (deposit/withdrawal)
- `GET /api/goals/:id/transactions` → List transactions
- `DELETE /api/goals/:id/transactions/:transactionId` → Delete transaction

### Automatic Savings

- `PUT /api/goals/:id/auto-savings` → Configure or update automatic savings
- `POST /api/goals/:id/auto-savings/disable` → Disable automatic savings

### Simulator

- `GET /api/goals/:id/simulator?amount=...&date=...` → Run simulation

---

## Integration Points

- **Dashboard** → Displays progress, status, and milestones of each goal.
- **Notifications** → Triggers alerts at 25%, 50%, 75%, and 100% progress.
- **Expenses/Income** → Supports round-up and percentage-based savings rules.
- **Shared Accounts** → Group goals with tracking per member.

---

# 07 - Dashboard Module

## General Description

The Dashboard module provides users with a centralized view of their financial health.  
It aggregates data from **incomes**, **expenses**, **savings goals**, and **budgets** into summaries, charts, and KPIs.

The dashboard is optimized for **performance**, **filtering**, and **exporting reports** (PDF/Excel).

---

## Entities (TypeScript DTOs)

### DashboardSummaryDto

```ts
export class DashboardSummaryDto {
  totalIncome: number; // in cents
  totalExpenses: number; // in cents
  netCashFlow: number; // in cents
  savingsRate: number; // percentage
  budgetUtilization: number; // percentage total
  upcomingBills: number; // count of expenses due in next 7 days
  activeGoals: number; // number of active savings goals
}
```

### ExpenseByCategoryDto

```ts
export class ExpenseByCategoryDto {
  categoryId: string;
  categoryName: string;
  color: string;
  total: number; // in cents
  percentage: number; // portion of all expenses
}
```

### GoalProgressDto

```ts
export class GoalProgressDto {
  goalId: string;
  goalName: string;
  targetAmount: number; // in cents
  currentAmount: number; // in cents
  progressPercentage: number;
  targetDate: Date;
}
```

### CashFlowDto

```ts
export class CashFlowDto {
  month: string; // YYYY-MM
  income: number; // in cents
  expenses: number; // in cents
  net: number; // in cents
}
```

---

## Features

### Financial KPIs

- **Total Incomes vs Expenses** (for selected period).
- **Net Cash Flow** (income - expenses).
- **Savings Rate** (% of saved vs earned).
- **Budget Utilization** (global + per category).
- **Upcoming Bills** (count + amount).
- **Goals Progress Summary**.

### Charts & Visualizations

- **Pie Chart** → Expenses by Category (current month).
- **Line Chart** → Incomes vs Expenses over time.
- **Bar Chart** → Month-by-Month comparison.
- **Progress Bars** → Savings goals progress.
- **Gauge Chart** → Budget utilization.

### Advanced Analytics

- **Heatmap** → Daily spending patterns.
- **Waterfall Chart** → Cash flow breakdown.
- **Trend Analysis** → Seasonal spending patterns.

### Filtering Options

- Time range: Current Month, Last 30 days, Last 3/6/12 months, Custom.
- Category filter (include/exclude categories).
- Account filter (personal vs shared).
- Amount range filters.
- Status filters (`paid/unpaid`, `received/pending`).

### Export Options

- Export summary report as **PDF**.
- Export raw data as **Excel** or **CSV**.
- Automated scheduled monthly reports via email.
- Export simulation charts for external analysis.

---

## Business Rules

- Dashboard data must be **cached in Redis** for fast retrieval.
- Reports should be pre-calculated daily by background jobs.
- All values displayed in **user’s preferred currency**.
- Reports with >12 months must be **paginated or aggregated** to optimize performance.

---

## API Endpoints

### Dashboard

- `GET /api/dashboard/overview` → Summary KPIs + quick stats.
- `GET /api/dashboard/expenses-by-category?from&to` → Aggregated expenses.
- `GET /api/dashboard/income-vs-expenses?from&to` → Income vs expense evolution.
- `GET /api/dashboard/cashflow?from&to` → Net cashflow history.
- `GET /api/dashboard/goals-progress` → Progress of active goals.
- `GET /api/dashboard/upcoming-bills` → Bills due next 7 (or X) days.

### Reports

- `POST /api/reports/generate` → Generate a custom report (select KPIs, categories, date range).
- `GET /api/reports/export/:format` → Export (PDF/Excel/CSV).
- `POST /api/reports/schedule` → Schedule monthly reports delivery.

---

## Integration Points

- **Incomes Module** → Provides income data grouped per category.
- **Expenses Module** → Provides categorized spending & budgets.
- **Savings Goals Module** → Provides progress updates.
- **Notifications Module** → Can send scheduled monthly reports.
- **Open Finance Module** → Feeds real transactions from banks.

---

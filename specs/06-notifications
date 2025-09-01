# 06 - Notifications Module

## General Description

The Notifications module allows the system to send alerts and reminders to users about upcoming bills, exceeded budgets, savings milestones, and account activities.

It supports **Email** and **WhatsApp** channels, background jobs, retry mechanisms, and granular notification settings.

---

## Entities (TypeScript)

### Notification

```ts
export class Notification {
  id: string; // UUID
  userId: string;
  type: 'EMAIL' | 'WHATSAPP';
  category:
    | 'BILL_DUE'
    | 'INCOME_DUE'
    | 'BUDGET_EXCEEDED'
    | 'GOAL_MILESTONE'
    | 'MONTHLY_SUMMARY'
    | 'SECURITY_ALERT';
  title: string;
  message: string;
  scheduledAt: Date;
  sentAt?: Date;
  status: 'PENDING' | 'SENT' | 'FAILED' | 'CANCELLED';
  attempts: number;
  metadata: Record<string, any>; // dynamic for templates
  createdAt: Date;
  updatedAt: Date;
}
```

### NotificationSettings

```ts
export class NotificationSettings {
  id: string; // UUID
  userId: string;
  emailEnabled: boolean;
  whatsappEnabled: boolean;
  whatsappNumber?: string;

  billReminders: {
    enabled: boolean;
    daysInAdvance: number; // e.g. 3
    timeOfDay: string; // HH:mm format
  };

  incomeReminders: {
    enabled: boolean;
    daysInAdvance: number;
    timeOfDay: string;
  };

  budgetAlerts: {
    enabled: boolean;
    thresholdPercentage: number; // e.g. 80
  };

  monthlySummary: {
    enabled: boolean;
    dayOfMonth: number;
    timeOfDay: string;
  };

  createdAt: Date;
  updatedAt: Date;
}
```

---

## Features

### Email Notifications

- Responsive **HTML templates** with placeholders.
- Integration with SMTP providers (SendGrid, SES, etc.).
- Personalization using `Notification.metadata`.
- Tracking (sent, opened, clicked) when supported.
- Unsubscribe links for compliance.

### WhatsApp Notifications

- Powered by **WhatsApp Business API**.
- Phone verification required (OTP).
- Pre-approved message templates.
- Opt-in / Opt-out support.
- Delivery status reports.

### Background Jobs

- Queue system using **Redis**.
- Scheduled consumers to process pending notifications.
- Retry policy with exponential backoff (`1min → 5min → 15min`).
- Dead-letter queue for failed events.
- Logging and monitoring.

### Notification Types

- Bill reminders (3 / 1 day before due date).
- Income reminders (expected payment dates).
- Budget exceeded alerts (≥80%, 100%, 120%).
- Savings goal milestones (25%, 50%, 75%, 100%).
- Monthly summary generation.
- Security alerts (login from new device, 2FA disabled).

---

## Business Rules

- User must **opt-in** for WhatsApp with verified number.
- Email notifications cannot be disabled for **security alerts**.
- Monthly summaries are only generated if user had at least 1 transaction.
- Failed notifications are retried 3 times max.
- Notifications linked to recurring expenses/incomes are scheduled by cron job every midnight.

---

## API Endpoints

### Notification Settings

- `GET /api/notifications/settings` → Get user notification preferences.
- `PUT /api/notifications/settings` → Update preferences.

### Notifications

- `GET /api/notifications/history` → Get delivery history with filters.
- `POST /api/notifications/test-email` → Send a test email to current user.
- `POST /api/notifications/test-whatsapp` → Send a test WhatsApp message to current user.
- `POST /api/notifications/verify-whatsapp` → Verify WhatsApp number with OTP.
- `POST /api/notifications/unsubscribe` → Unsubscribe user from chosen category.

---

## Integration Points

- **Expenses Module** → Generates bill due notifications.
- **Incomes Module** → Triggers income received/due alerts.
- **Savings Goals** → Triggers milestone notifications.
- **Dashboard** → Triggers monthly financial summary notifications.
- **Authentication** → Security alerts for login and 2FA changes.

---

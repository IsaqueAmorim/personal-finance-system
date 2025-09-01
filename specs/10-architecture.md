# 10 - Architecture Module

## General Description
The **Architecture Module** defines the project’s technical foundation, including its **folder structure**, **naming conventions**, design patterns for **DTOs**, **global error handling**, **validation**, and **security** measures (LGPD/GDPR compliance).

It ensures the system remains **scalable**, **consistent**, and **maintainable** across all modules.

---

## Folder Structure (NestJS Recommended)

```text
src/
├── app.module.ts
├── main.ts
├── common/
│   ├── decorators/
│   ├── filters/
│   ├── guards/
│   ├── interceptors/
│   ├── pipes/
│   └── utils/
├── config/
│   ├── database.config.ts
│   ├── keycloak.config.ts
│   ├── redis.config.ts
│   └── notification.config.ts
├── modules/
│   ├── auth/
│   ├── incomes/
│   ├── expenses/
│   ├── goals/
│   ├── notifications/
│   ├── dashboard/
│   ├── open-finance/
│   └── shared-accounts/
└── database/
    ├── migrations/
    ├── seeds/
    └── entities/
```

---

## Naming Conventions

### Code
- **Classes / Interfaces**: `PascalCase` → `ExpenseCategory`  
- **Methods / Functions**: `camelCase` → `createExpense()`  
- **Variables**: `camelCase` → `monthlyBudget`  
- **Constants**: `UPPER_SNAKE_CASE` → `MAX_RETRY_ATTEMPTS`  
- **Database Columns**: `snake_case` → `created_at`  
- **API Endpoints**: `kebab-case` → `/expense-categories`  

### Files
- `.entity.ts` → Database entity classes.  
- `.repository.ts` → Data access layer.  
- `.controller.ts` → Request handling.  
- `.service.ts` → Business logic.  
- `.dto.ts` → Data Transfer Objects.  

---

## Data Transfer Objects (DTOs)

### Example (Expense Creation DTO with Validation)

```ts
import { IsNotEmpty, IsPositive, IsEnum, IsUUID, Length } from 'class-validator';
import { Transform } from 'class-transformer';

export class CreateExpenseDto {
  @IsNotEmpty({ message: 'Description is required' })
  @Length(3, 255)
  description: string;

  @IsPositive({ message: 'Amount must be positive' })
  @Transform(({ value }) => Math.round(value * 100)) // store in cents
  amount: number;

  @IsUUID('4', { message: 'Invalid category ID' })
  categoryId: string;

  @IsEnum(['ONE_TIME', 'RECURRING'])
  type: 'ONE_TIME' | 'RECURRING';
}
```

---

## Global Error Handling

- Define **custom exception filters** to map technical errors to user-friendly responses.  
- Standard error response object:

```ts
export class ApiErrorResponse {
  statusCode: number;
  message: string;
  error: string;
  timestamp: Date;
  path: string;
}
```

- Unified JSON format improves client integration.  
- Integration with external logging services (e.g., Sentry).  

---

## Security Guidelines

### Data Protection
- Data encrypted at rest (AES-256).  
- Communication enforced over HTTPS (TLS 1.3).  
- Sensitive data (tokens, credentials) stored in **secrets manager**.  
- SQL injection prevented via ORM (TypeORM/Prisma).  
- XSS prevention using proper output encoding.  

### LGPD/GDPR Compliance
- **Consent management** → users explicitly approve data use.  
- **Data minimization** → only store essential user data.  
- **Right to be forgotten** → delete user & anonymize references.  
- **Right to access/export data** → provide JSON/CSV exports.  
- **Processing audit trails** → all sensitive operations logged.  

---

## Performance Optimizations
- Caching using Redis for dashboard & reports.  
- Database indexes on heavily queried columns.  
- Pagination + filtering for large datasets.  
- Background jobs for heavy processes (notifications, imports).  
- Static assets (receipts) served via CDN.  

---

## Monitoring
- **Application Metrics** → Prometheus + Grafana.  
- **Structured Logging** → Winston.  
- **Health Checks** → `/health` endpoint.  
- **Error Tracking** → Sentry (alerts).  
- **Audit Logs** → stored per critical user operation.  

---

## Scalability Considerations
- Stateless NestJS services for horizontal scaling.  
- Support for Kubernetes Pods/Containers.  
- Database read replicas.  
- Redis clustering for high availability.  
- Event-driven architecture (future).  

---
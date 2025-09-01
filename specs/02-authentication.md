# Authentication & Authorization

This document describes the architecture for authentication and authorization in the Personal Finance Management System. The solution is based on **Keycloak** for Identity and Access Management (IAM), fully integrated with **NestJS**.

## Key Objectives

- Centralized Identity Management via Keycloak
- Support for OAuth2 and OpenID Connect (OIDC) flows
- Secure JWT token-based access control across microservices
- Role-Based Access Control (RBAC) and fine-grained permissions
- Support for future Multi-Factor Authentication (MFA)

## Architecture Overview

### 1. Keycloak Server

- Manages users, roles, groups, and sessions
- Provides login, registration, password reset, MFA, and social login options
- Issues access tokens (JWT), refresh tokens, and ID tokens
- Handles user profile management and password policies

### 2. NestJS Backend

- Uses Keycloak NestJS adapter (e.g., `nest-keycloak-connect`) for resource authorization
- Verifies JWT tokens via Keycloak public keys
- Defines API access policies based on roles (`@Roles()` decorators)
- Implements custom guards for fine-grained permissions

### 3. Frontend Integration

- Redirects users to Keycloak login pages
- Stores and uses access/refresh tokens for API requests
- Handles token refresh automatically

## Data Entities

Although Keycloak handles most identity details, our system maps essential user data into our database for application-specific needs.

```typescript
// User entity stored in our database (subset from Keycloak)
export interface User {
  id: string; // Maps to Keycloak's userId (UUID)
  keycloakId: string; // Keycloak internal user ID
  email: string;
  name: string;
  phone?: string;
  timezone: string; // Default: "America/Sao_Paulo"
  locale: string; // Default: "pt-BR"
  currency: string; // Default: "BRL"
  firstDayOfWeek: number; // 0-6 (Sunday-Saturday)
  profilePicture?: string;
  emailVerified: boolean;
  phoneVerified: boolean;
  isActive: boolean;
  lastLoginAt?: Date;
  createdAt: Date;
  updatedAt: Date;
}

// Session audit (for security monitoring, not replacing Keycloak session)
export interface SessionAudit {
  id: string;
  userId: string;
  sessionId: string; // Keycloak session ID
  ipAddress: string;
  userAgent: string;
  loginAt: Date;
  logoutAt?: Date;
  isActive: boolean;
}

// User preferences for the application
export interface UserPreferences {
  userId: string;
  dashboardLayout: 'COMPACT' | 'DETAILED';
  defaultDateRange: 'CURRENT_MONTH' | 'LAST_30_DAYS' | 'LAST_3_MONTHS';
  notificationSettings: {
    emailEnabled: boolean;
    whatsappEnabled: boolean;
    pushEnabled: boolean;
  };
  createdAt: Date;
  updatedAt: Date;
}
```

## Authentication Flows

### 1. User Registration

1. User accesses registration page
2. Redirected to Keycloak registration form
3. User fills required information (name, email, password)
4. Keycloak validates and creates user account
5. Email verification sent (if enabled)
6. Upon verification, user profile is synced to our database
7. User is redirected back to application with valid tokens

### 2. Login (Authorization Code Flow)

1. User clicks "Login" button
2. Redirected to Keycloak login page (`/auth/realms/{realm}/protocol/openid-connect/auth`)
3. User enters credentials
4. Keycloak validates credentials and issues authorization code
5. Backend exchanges code for tokens (`access_token`, `refresh_token`, `id_token`)
6. User profile is synced/updated in our database
7. User is redirected to dashboard with valid session

### 3. Token Management

- **Access Token:** Short-lived (15 minutes), contains user roles and permissions
- **Refresh Token:** Longer-lived (30 days), used to obtain new access tokens
- **ID Token:** Contains user profile information
- **Automatic Refresh:** Frontend handles token refresh transparently

### 4. Logout

1. Frontend calls logout endpoint
2. Backend invalidates session in our database
3. Redirect to Keycloak logout endpoint
4. Keycloak invalidates all tokens and sessions
5. User redirected to login page

## Authorization & Roles

### Default Roles in Keycloak

```typescript
enum UserRole {
  USER = 'USER', // Default role for personal accounts
  ADMIN = 'ADMIN', // System administrator
  SHARED_ACCOUNT_ADMIN = 'SHARED_ACCOUNT_ADMIN', // Admin of shared account
  SHARED_ACCOUNT_MEMBER = 'SHARED_ACCOUNT_MEMBER', // Member of shared account
  SHARED_ACCOUNT_VIEWER = 'SHARED_ACCOUNT_VIEWER', // Read-only access to shared account
}
```

### Permission Matrix

| Action                         | USER | ADMIN | SHARED_ADMIN | SHARED_MEMBER | SHARED_VIEWER |
| ------------------------------ | :--: | :---: | :----------: | :-----------: | :-----------: |
| Manage own incomes/expenses    |  ✅  |  ✅   |      ✅      |      ✅       |      ❌       |
| View shared account data       |  ❌  |  ✅   |      ✅      |      ✅       |      ✅       |
| Manage shared incomes/expenses |  ❌  |  ✅   |      ✅      |     ✅\*      |      ❌       |
| Invite/remove members          |  ❌  |  ✅   |      ✅      |      ❌       |      ❌       |
| System administration          |  ❌  |  ✅   |      ❌      |      ❌       |      ❌       |

\*Subject to specific permissions configured by admin

## NestJS Integration

### 1. Keycloak Configuration

```typescript
// keycloak.config.ts
export const keycloakConfig = {
  authServerUrl: process.env.KEYCLOAK_AUTH_SERVER_URL,
  realm: process.env.KEYCLOAK_REALM,
  clientId: process.env.KEYCLOAK_CLIENT_ID,
  secret: process.env.KEYCLOAK_CLIENT_SECRET,
  cookieKey: 'KEYCLOAK_JWT',
  logLevels: ['verbose'],
  useNestLogger: true,
};
```

### 2. Guards and Decorators

```typescript
// Example usage in controllers
@Controller('expenses')
@UseGuards(AuthGuard, ResourceGuard)
export class ExpensesController {
  @Get()
  @Roles('USER', 'SHARED_ACCOUNT_MEMBER')
  async findAll(@AuthenticatedUser() user: KeycloakUser) {
    return this.expensesService.findByUser(user.sub);
  }

  @Post()
  @Roles('USER', 'SHARED_ACCOUNT_ADMIN', 'SHARED_ACCOUNT_MEMBER')
  @Permissions('expenses:create')
  async create(@Body() createExpenseDto: CreateExpenseDto) {
    // Implementation
  }
}
```

### 3. User Synchronization Service

```typescript
@Injectable()
export class UserSyncService {
  async syncUserFromKeycloak(keycloakUser: KeycloakUser): Promise<User> {
    // Check if user exists in our database
    // If not, create new user record
    // Update user information if changed
    // Return user entity
  }

  async handleUserLogin(
    keycloakUser: KeycloakUser,
    sessionInfo: any,
  ): Promise<void> {
    // Sync user data
    // Create session audit record
    // Update last login timestamp
  }
}
```

## API Endpoints

Since authentication is handled by Keycloak, our backend primarily provides user profile and session management endpoints.

### User Profile Management

- `GET    /api/auth/profile` — Get current user profile
- `PUT    /api/auth/profile` — Update user profile
- `POST   /api/auth/profile/picture` — Upload profile picture
- `DELETE /api/auth/profile/picture` — Remove profile picture

### User Preferences

- `GET    /api/auth/preferences` — Get user preferences
- `PUT    /api/auth/preferences` — Update user preferences

### Session Management

- `GET    /api/auth/sessions` — List active sessions
- `DELETE /api/auth/sessions/:id` — Terminate specific session
- `DELETE /api/auth/sessions` — Terminate all sessions (except current)

### Account Management

- `POST   /api/auth/deactivate` — Deactivate account
- `POST   /api/auth/delete` — Request account deletion (LGPD compliance)

## Security Features

### 1. Token Validation

- JWT Signature Verification: Using Keycloak's public keys (JWKS endpoint)
- Token Expiration: Automatic validation of token expiry
- Scope Validation: Ensure tokens have required scopes for specific operations

### 2. Session Security

- Session Timeout: Configurable idle timeout
- Concurrent Session Limits: Limit number of active sessions per user
- Device Tracking: Track and display active devices/sessions to users

### 3. Password Policies (Keycloak)

- Minimum 8 characters
- At least one uppercase, lowercase, number, and special character
- Password history (prevent reuse of last 5 passwords)
- Account lockout after 5 failed attempts

### 4. Multi-Factor Authentication (Future)

- SMS-based OTP
- TOTP (Google Authenticator, Authy)
- Email-based verification for sensitive operations

## Environment Configuration

```env
# Environment variables for Keycloak integration
KEYCLOAK_AUTH_SERVER_URL=https://keycloak.yourdomain.com/auth
KEYCLOAK_REALM=finance-app
KEYCLOAK_CLIENT_ID=finance-backend
KEYCLOAK_CLIENT_SECRET=your-client-secret

# Database connection for user sync
DATABASE_URL=postgresql://user:password@localhost:5432/finance_db

# Redis for caching user sessions and permissions
REDIS_URL=redis://localhost:6379
```

## User Lifecycle Management

### 1. User Registration

Keycloak handles registration, but we sync essential data.

```typescript
@Injectable()
export class AuthService {
  async handleNewUser(keycloakUser: KeycloakUser): Promise<User> {
    const user = await this.userRepository.create({
      id: keycloakUser.sub,
      keycloakId: keycloakUser.sub,
      email: keycloakUser.email,
      name: keycloakUser.name,
      timezone: 'America/Sao_Paulo',
      locale: 'pt-BR',
      currency: 'BRL',
      isActive: true,
    });

    // Create default categories for the user
    await this.createDefaultCategories(user.id);

    return user;
  }
}
```

### 2. User Profile Updates

- Changes in Keycloak are synced to our database via webhooks or periodic sync
- Application-specific preferences are managed in our database

### 3. Account Deactivation/Deletion

- **Deactivation:** User account is disabled but data is preserved
- **Deletion:** Full data removal in compliance with LGPD (Right to be Forgotten)

## Error Handling

### Common Authentication Errors

```typescript
export enum AuthErrorCode {
  INVALID_TOKEN = 'INVALID_TOKEN',
  EXPIRED_TOKEN = 'EXPIRED_TOKEN',
  INSUFFICIENT_PERMISSIONS = 'INSUFFICIENT_PERMISSIONS',
  USER_NOT_FOUND = 'USER_NOT_FOUND',
  ACCOUNT_DEACTIVATED = 'ACCOUNT_DEACTIVATED',
  SESSION_EXPIRED = 'SESSION_EXPIRED',
}

// Global exception filter for auth errors
@Catch(UnauthorizedException)
export class AuthExceptionFilter implements ExceptionFilter {
  catch(exception: UnauthorizedException, host: ArgumentsHost) {
    const response = host.switchToHttp().getResponse();

    response.status(401).json({
      statusCode: 401,
      message: 'Authentication required',
      error: 'Unauthorized',
      timestamp: new Date().toISOString(),
    });
  }
}
```

## Testing Strategy

### 1. Unit Tests

- Mock Keycloak responses for service testing
- Test user synchronization logic
- Validate permission checking logic

### 2. Integration Tests

- Test complete authentication flows
- Verify token validation
- Test role-based access control

### 3. E2E Tests

- Full login/logout flows
- Permission enforcement across different user roles
- Session management scenarios

## Monitoring & Auditing

### 1. Authentication Events

- Login attempts (successful/failed)
- Token refresh events
- Permission denied events
- Session terminations

### 2. Security Metrics

- Failed login attempts per IP/user
- Token validation failures
- Unusual access patterns

### 3. Compliance Logging

- User data access logs (LGPD compliance)
- Data modification audit trail
- Account deletion requests and processing

---

This authentication module provides a robust foundation for secure user management while leveraging Keycloak's enterprise-grade identity management capabilities.

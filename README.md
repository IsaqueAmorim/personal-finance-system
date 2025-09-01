# 💰 Personal Finance Management System

## 📌 Overview

O **Personal Finance Management System** é uma plataforma desenvolvida em **NestJS**, integrada com **Open Finance**, que auxilia usuários a gerenciar suas finanças pessoais e/ou compartilhadas.

Principais recursos:

- Registro e controle de **incomes (receitas)** e **expenses (despesas)**.
- Gerenciamento de **savings goals** com contribuições manuais e automáticas.
- Integração com **Open Finance** para sincronização bancária e reconciliação de transações.
- Suporte a **shared accounts** (famílias, casais, times).
- **Dashboard** completo com métricas, gráficos e análises.
- Sistema de **notifications** via Email e WhatsApp.
- **Segurança e conformidade** com LGPD/GDPR.

---

## 📂 Project Documentation

A documentação completa está dividida em módulos. Cada arquivo `.md` cobre uma área específica do sistema:

1. [01 - Overview](01-overview.md) → Visão geral do sistema.
2. [02 - Authentication](02-authentication.md) → Autenticação e Keycloak.
3. [03 - Incomes](03-incomes.md) → Registro e gestão de receitas.
4. [04 - Expenses](04-expenses.md) → Registro e gestão de despesas.
5. [05 - Savings Goals](05-savings-goals.md) → Metas de poupança.
6. [06 - Notifications](06-notifications.md) → Sistema de notificações (Email/WhatsApp).
7. [07 - Dashboard](07-dashboard.md) → Relatórios e indicadores financeiros.
8. [08 - Open Finance](08-open-finance.md) → Integração bancária e reconciliação.
9. [09 - Shared Accounts](09-shared-accounts.md) → Contas compartilhadas e colaboração.
10. [10 - Architecture](10-architecture.md) → Estrutura, DTOs, padrões e segurança.
11. [11 - Use Cases](11-use-cases.md) → Cenários de uso reais.
12. [12 - Requirements](12-requirements.md) → Requisitos não funcionais.

---

## 🏗️ Tech Stack

- **Backend** → [NestJS](https://nestjs.com/) (Node.js, TypeScript)
- **Database** → PostgreSQL (com TypeORM)
- **Authentication** → Keycloak (OIDC, RBAC, MFA)
- **Caching / Queue** → Redis
- **Notifications** → Email (SMTP), WhatsApp Business API
- **Open Finance** → Integração via OAuth2 APIs de bancos
- **Infrastructure** → Docker + Kubernetes (scalable, microservices-ready)
- **Monitoring** → Prometheus + Grafana + Sentry

---

## 🔐 Security & Compliance

- Autenticação de usuários com **Keycloak**.
- Senhas e tokens armazenados com **hashing e criptografia**.
- **TLS 1.3** obrigatório para todo tráfego de rede.
- **LGPD/GDPR**: direito de acesso, exportação e exclusão de dados.
- **Auditoria e rastreabilidade** de operações críticas.

---

## 📊 Modules Overview

| Module              | Description                                    |
| ------------------- | ---------------------------------------------- |
| **Authentication**  | Login, MFA e RBAC com Keycloak.                |
| **Incomes**         | Registro de receitas (únicas e recorrentes).   |
| **Expenses**        | Registro de despesas (únicas e recorrentes).   |
| **Savings Goals**   | Criação e acompanhamento de metas financeiras. |
| **Notifications**   | Email/WhatsApp para lembretes e alertas.       |
| **Dashboard**       | KPIs, relatórios, exportação (PDF/Excel).      |
| **Open Finance**    | Conexão com bancos e reconciliação.            |
| **Shared Accounts** | Contas colaborativas entre múltiplos usuários. |
| **Architecture**    | Estrutura de pastas, padrões, segurança.       |
| **Use Cases**       | Exemplos de fluxos reais do sistema.           |
| **Requirements**    | Requisitos NFR, escalabilidade, segurança.     |

---

## 🚦 Development Roadmap

1. **Phase 1 – MVP**
   - Auth (Keycloak), CRUD de Incomes/Expenses, Categorias, Dashboard básico, Email notifications.
2. **Phase 2 – Enhanced**
   - Recorrências, Savings Goals, WhatsApp notifications, Advanced Analytics.
3. **Phase 3 – Integrations**
   - Open Finance API, Auto Categorization IA, Reconciliação automática.
4. **Phase 4 – Collaboration**
   - Shared Accounts, Logs de auditoria, Exportações completas, Mobile app.

---

## 🧪 Testing & Monitoring

- **Unit Tests**: Jest (meta ≥ 80%).
- **Integration Tests**: todas APIs.
- **E2E Tests**: flows críticos (Ex: reconciliação bancária).
- **Monitoring**: Prometheus & Grafana.
- **Error Tracking**: Sentry.

---

## 🚀 Getting Started (Dev)

### Requirements

- Node.js ≥ 18
- PostgreSQL ≥ 15
- Redis ≥ 6
- Keycloak ≥ 20
- Docker + Docker Compose

### Setup

```bash
# Clone project
git clone https://github.com/IsaqueAmorim/personal-finance-system.git
cd personal-finance-system

# Install dependencies
npm install

# Start services
docker-compose up -d

# Run application
npm run start:dev
```

---

## 📜 License

Este projeto é licenciado sob a **MIT License** – veja [LICENSE](LICENSE) para detalhes.

---

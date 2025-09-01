# Project Overview

This document outlines the specifications for a personal finance management system. The system aims to provide users with a comprehensive and intuitive platform to track, manage, and plan their financial activities. Developed with a focus on the Brazilian market, it will offer advanced features for financial control, budgeting, and goal-setting.

## Key Objectives

**Empower Users:** Provide tools for users to gain full control and understanding of their personal finances.

**Automation:** Minimize manual data entry through recurring transactions and Open Finance integration.

**Insightful Analytics:** Offer clear visualizations and reports to help users make informed financial decisions.

**Security & Privacy:** Ensure the highest standards of data security and compliance with privacy regulations (e.g., LGPD).

**User-Friendly:** Deliver an intuitive and responsive user experience across devices.

## Target Audience

Individuals seeking better control over their personal finances.

Couples or families looking to manage shared expenses and incomes.

Users interested in setting and achieving financial goals (e.g., saving for a trip, down payment).

## Core Functionalities

**Income Management:** Track one-time and recurring incomes with categorization.

**Expense Management:** Record one-time and recurring expenses, categorize them, and attach receipts.

**Savings Goals:** Set financial goals and track progress with automated savings options.

**Notifications:** Receive alerts via email and WhatsApp for upcoming bills, budget overruns, and goal progress.

**Comprehensive Dashboard:** Visualize financial health with detailed charts and reports (expenses by category, income/expense evolution).

**Open Finance Integration:** Connect to bank accounts for automatic transaction import and categorization.

**Authentication:** Secure user registration, login, and profile management.

**Shared Accounts:** Manage finances collaboratively with other users, with granular permission control.

## Technology Stack

**Backend Framework:** NestJS (Node.js + TypeScript)

**Database:** PostgreSQL

**ORM:** TypeORM or Prisma (to be decided during initial setup)

**Authentication:** JWT (JSON Web Tokens) + Passport.js

**Caching/Queue:** Redis (for session management, background jobs, and caching)

**Notifications:** SMTP service (e.g., SendGrid, AWS SES) for email, WhatsApp Business API (e.g., Twilio) for WhatsApp.

**Documentation:** Swagger/OpenAPI for API documentation.

**Testing:** Jest (Unit/Integration), Supertest (E2E API), Cypress (E2E Frontend - if applicable).

**Language:** English (for code, documentation, and API naming conventions).

## Naming Conventions

**Entities/Classes:** PascalCase (e.g., IncomeCategory)

**Properties/Database Columns:** snake_case (e.g., due_date)

**API Endpoints:** kebab-case (e.g., /income-categories)

**Methods/Functions:** camelCase (e.g., createExpense())

This overview serves as the foundational document for the project, setting the stage for more detailed specifications in subsequent files.

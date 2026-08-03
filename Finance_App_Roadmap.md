# Personal Finance App Roadmap

## Philosophy

This is not a roadmap for learning programming fundamentals.

You already have years of professional experience as a developer. The goal is to learn:

- Kotlin idioms and ecosystem
- Modern Android development
- Spring Boot and the Spring ecosystem
- Mobile architecture
- Synchronization strategies
- Security concepts
- Product design and software architecture

The objective is simple:

> Replace the Notepad as quickly as possible.

---

# Phase 0 — Environment Preparation

## Install

- Git
- Android Studio
- JDK
- PostgreSQL
- Bruno or Postman
- IntelliJ IDEA Community Edition (optional)

## Configure

- Git
- SSH keys
- Android SDK
- USB debugging
- Gradle
- Device deployment

## Project structure

```text
FinanceApp/
├── finance-android/
├── finance-backend/
└── finance-docs/
```

---

# Phase 1 — Learn Kotlin

Focus on the things that make Kotlin different from Java.

Topics:

- Null safety
- Data classes
- Extension functions
- Coroutines
- Flow and StateFlow
- Sealed classes
- Higher-order functions
- Lambdas
- Scope functions
- Delegation

Do not spend much time on:

- Variables
- Loops
- Functions
- Classes
- Interfaces
- Inheritance
- Generics

You already know those concepts.

---

# Phase 2 — Learn Modern Android Development

Study:

- Android architecture
- Activities
- Lifecycle
- Jetpack Compose
- Navigation
- State management
- ViewModels
- Repositories
- Material Design
- Dependency injection

---

# Phase 3 — Music Player Application

Goal:

Build a complete application before starting the finance project.

Features:

- Splash screen
- Music library
- Search
- Album artwork
- Playback controls
- Notifications
- Background playback
- Settings
- Dark mode

Concepts:

- Navigation
- State management
- Permissions
- Services
- Project organization
- Reusable components

---

# Phase 4 — Android Architecture

Study:

- MVVM
- Repository pattern
- Clean Architecture
- Room
- Retrofit
- WorkManager
- Coroutines
- Flow
- Dependency injection
- Lifecycle-aware components

---

# Phase 5 — Finance Application Planning

Before writing code:

- Requirements
- Database model
- Navigation flow
- Wireframes
- Package organization
- Architecture

Use specification-driven development.

Review:

- Requirements
- Design
- Tasks

---

# Phase 6 — Finance Application MVP (Version 0.1)

Goal:

Stop using the Notepad.

## Income

- Salary
- Gifts
- Dividends
- Other income sources

## Expenses

- Create expenses
- Edit expenses
- Delete expenses
- Search
- Categories
- Payment methods

## Calculations

- Monthly income
- Monthly expenses
- Remaining balance

## Storage

- Room
- Offline-only operation

Definition of done:

Manage finances for an entire month without opening the Notepad.

---

# Phase 7 — Quality-of-Life Improvements (Version 0.2)

Features:

- Recurring expenses
- Scheduled expenses
- Notifications
- Multiple cards
- Due dates
- Closing dates
- Expected expenses
- Better filtering
- Better search
- Icons

---

# Phase 8 — Charts (Version 0.3)

Implement:

- Expense evolution
- Category distribution
- Income versus expenses
- Pie charts
- Line charts
- Bar charts

---

# Phase 9 — Investments (Version 0.4)

Implement:

- Portfolios
- Wallets
- Objectives
- Institutions
- Stocks
- REITs
- Treasury bonds
- CDBs
- Emergency funds

---

# Phase 10 — Investment Analytics (Version 0.5)

Implement:

- Asset allocation charts
- Dividend history
- Net-worth evolution
- Compound-interest calculator
- Fixed-income calculator

---

# Phase 11 — Spring Boot

Study:

- Controllers
- Services
- Repositories
- Spring Data JPA
- Hibernate
- Validation
- Exception handling
- Dependency injection
- Flyway
- PostgreSQL integration

---

# Phase 12 — Synchronization Layer

Architecture:

```text
Android Application
        │
        ▼
      Room
        │
        ▼
Synchronization
        │
        ▼
Spring Boot
        │
        ▼
PostgreSQL
```

Study:

- Offline-first architecture
- Retry strategies
- Caching
- Synchronization queues
- Conflict resolution
- WorkManager

---

# Phase 13 — Security

Study:

- HTTPS
- Authentication
- Authorization
- JWT
- Spring Security
- BCrypt
- Encryption
- Android Keystore
- Rate limiting
- Validation
- CORS
- CSRF

---

# Phase 14 — Market Integration

Implement:

- Stock prices
- CDI
- IPCA
- IFIX
- Ibovespa
- Historical prices
- Dividend data

---

# Phase 15 — React Administrative Interface

Purpose:

Learning only.

Features:

- Manage categories
- Manage institutions
- Manage icons
- Administrative tools

---

# AI Roadmap

For each feature:

1. Write the specification.
2. Review the requirements.
3. Review the design.
4. Ask questions.
5. Generate code.
6. Review code.
7. Test.
8. Refactor.

Rule:

Understand first. Generate later.

---

# Learning Roadmap

Kotlin

↓

Android ecosystem

↓

Jetpack Compose

↓

Architecture

↓

Room

↓

Music player

↓

Finance MVP

↓

Charts

↓

Investments

↓

Spring Boot

↓

Synchronization

↓

Security

↓

Market APIs

↓

React

---

# Final Objective

Success is not version 2.0.

Success is replacing the Notepad with software that you designed, built, and use every day.

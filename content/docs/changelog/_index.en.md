---
title: "Roadmap and Changelog"
linkTitle: "Changelog"
weight: 50
description: "Development roadmap and changelog for the RailTech B2B platform."
categories: ["Changelog"]
tags: ["roadmap", "releases", "sprints"]
---

## Context

The platform is in active development. After a comprehensive security and architecture audit, all critical vulnerabilities have been fixed. Further development follows the Security-First principle: security and monitoring are implemented before the first public release.

---

## Sprint 0: Security (Completed)

**Goal:** Fix critical vulnerabilities, prepare the platform for safe testing.

### Completed

- [x] Fixed resource access isolation (IDOR protection)
- [x] Implemented database-level background task ownership model
- [x] Implemented Finite State Machine (FSM) for deal stage transitions
- [x] Standardized permission checking system
- [x] Added role-based activity log filtering
- [x] Strengthened deal-level WebSocket chat access control
- [x] Added uploaded file format validation
- [x] Implemented security headers: CSP, X-Frame-Options, HSTS preparation
- [x] Configured request rate limiting
- [x] Separated caching and task queue services
- [x] Added immutable audit log for financial operations
- [x] Optimized database indexes for key queries
- [x] Added client-side form validation
- [x] WebSocket authentication via first message (not via URL)

---

## Sprint 1: Deal Core (In Development)

**Goal:** Full deal cycle: client receives a quote, approves or rejects it.

- PDF quote export
- Quote sending to client with notification
- Client quote approval / rejection
- Error monitoring integration

---

## Sprint 2: Real-time Notifications

**Goal:** Push notifications via WebSocket instead of periodic polling.

- WebSocket channel for system notifications
- UI notification integration (counter, sound alerts)
- Transactional email notifications

---

## Sprint 3: Financial Settings and Currencies

**Goal:** Configurable financial parameters, multi-currency support.

- Platform settings model (VAT rate, margin parameters)
- Automatic currency rate synchronization with the Central Bank of Uzbekistan
- Financial parameter validation at save level

---

## Sprint 4: Document Management and Catalog

**Goal:** Deal document management, improved catalog.

- Deal document upload and download
- Full-text catalog search
- Product image gallery

---

## Sprint 5: Testing and Quality

**Goal:** Test coverage, automated quality control.

- Unit tests: authorization, finance, RBAC, FSM
- UI component tests
- E2E tests for the full deal cycle

---

## Sprint 6: Production Readiness

**Goal:** SSL, monitoring, deployment.

- SSL/HTTPS (Let's Encrypt)
- Performance monitoring
- Automatic API documentation (OpenAPI 3.0)
- Production deployment with Docker Compose

---

## Sprint 7: Advanced Features

**Goal:** Business features for scaling.

- Deal templates
- Supplier efficiency dashboard
- Multilingual UI (Russian, Uzbek, English)

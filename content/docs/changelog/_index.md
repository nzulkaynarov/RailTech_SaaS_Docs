---
title: "Roadmap & Changelog"
linkTitle: "Changelog"
weight: 50
description: "Дорожная карта разработки и история изменений платформы RailTech B2B."
categories: ["Changelog"]
tags: ["roadmap", "релизы", "спринты"]
---

## Контекст

Проект реализован на ~60%. После полного аудита безопасности и архитектуры (2026-03-20) были выявлены и исправлены критические уязвимости. Данный Roadmap учитывает необходимость Security-First подхода: мониторинг и безопасность -- до первого деплоя, а не после.

**Этапность:** Локальное тестирование с удаленной БД (Neon Cloud) -> Staging на VPS -> Production.

---

## Sprint 0: Security Hardening (ВЫПОЛНЕН 2026-03-20)

**Цель:** Устранить критические уязвимости, подготовить к безопасному локальному тестированию.

### Выполнено:
- [x] IDOR fix: Excel import + task status (sourcing/views.py)
- [x] ImportTask model -- persistent task ownership (заменяет volatile cache)
- [x] Deal stage FSM -- explicit transition matrix
- [x] Стандартизация auth checks (`is_manager_or_admin` вместо `is_staff`)
- [x] ActivityLog RBAC -- фильтрация по user
- [x] WebSocket access control -- проверка deal-level доступа
- [x] XLSX magic bytes validation -- защита от malicious uploads
- [x] Security headers: CSP, X-Frame-Options DENY, HSTS-ready
- [x] Nginx rate limiting: api_general, api_auth, ws_connect zones
- [x] Split Redis: channels+cache / celery broker (2 инстанса)
- [x] Celery multi-queue: default+pdf / imports (2 worker'а)
- [x] QuoteItemHistory -- immutable audit trail для финансов
- [x] DB indexes: Deal.stage, Deal.manager, Deal.company, Product.domain_type
- [x] Catalog signals: M2M validation + cache invalidation
- [x] KYC form Zod validation (frontend)
- [x] WebSocket auth: token через first message (не query string)
- [x] services.py fix: aggregate на QuerySet, не list

---

## Sprint 1: Ядро сделок (Недели 1-2)

**Цель:** Клиент получает КП, одобряет -> менеджер отправляет.

### 1.1 Quote PDF Export
- Celery-таск `generate_quote_pdf` через WeasyPrint
- `GET /api/v1/crm/deals/{id}/export-pdf/` -> PDF blob
- HTML-шаблон КП: `crm/templates/quote_pdf.html`

### 1.2 Send Quote to Client
- `POST /api/v1/crm/deals/{id}/send-quote/`
- Quote status -> SENT, Deal stage -> QUOTE_SENT (FSM validated)
- Notification + email с PDF (Celery task)
- Frontend: кнопка «Отправить КП» (manager/admin only)

### 1.3 Approve/Reject Quote
- `POST /api/v1/crm/deals/{id}/approve-quote/`
- `POST /api/v1/crm/deals/{id}/reject-quote/`
- Клиент видит Action Banner с кнопками
- Approve -> Deal.stage = CONTRACT_SIGNED, Quote.approved_at = now()

### 1.4 Sentry Integration (Sprint 0.5)
- Backend: `sentry-sdk[django]` + DSN config
- Frontend: `@sentry/react` + ErrorBoundary integration
- Мониторинг нужен с первого деплоя

---

## Sprint 2: Real-time Notifications (Недели 3-4)

**Цель:** Push-уведомления через WebSocket, отказ от polling.

### 2.1 NotificationConsumer
- WebSocket consumer: `ws/notifications/`
- JWT auth через first message (как в ChatConsumer)
- Signal triggers: Deal/Quote/KYC status changes

### 2.2 Frontend WebSocket Integration
- `notificationStore` (Zustand) с WebSocket state
- Badge count в header, sound alerts
- Exponential backoff reconnect

### 2.3 Email Notifications
- Celery tasks для transactional emails
- Templates: quote sent, quote approved, KYC status change

---

## Sprint 3: Financial Settings & Currency (Недели 5-6)

**Цель:** Настраиваемые финансовые параметры, мульти-валюта.

- PlatformSetting Model (VAT rate, default margin, min margin constraint)
- Exchange Rate Sync (Celery Beat: daily sync с ЦБ Узбекистана)
- Margin Validation (min/max constraints на уровне QuoteItem.save())

---

## Sprint 4: Documents & Advanced Catalog (Недели 7-8)

**Цель:** Управление документами, продвинутый каталог.

- Deal Document Management (Upload/download через Attachment model)
- Advanced Catalog Search (PostgreSQL FTS, фасетная фильтрация)
- Product image gallery

---

## Sprint 5: Testing & Quality (Недели 9-10)

**Цель:** 60% coverage, CI green.

- Backend Unit Tests (Auth, Financial, RBAC, FSM)
- Frontend Component Tests (Vitest + React Testing Library)
- E2E Tests (Playwright: full deal lifecycle)

---

## Sprint 6: Production Readiness (Недели 11-12)

**Цель:** SSL, мониторинг, деплой на VPS.

- SSL/HTTPS (Let's Encrypt + Certbot)
- Monitoring (Prometheus + Grafana)
- API Documentation (drf-spectacular, OpenAPI 3.0)
- VPS Deployment (Docker Compose production profile)

---

## Sprint 7: Advanced Features (Недели 13-14)

**Цель:** Бизнес-фичи для масштабирования.

- Deal Templates
- Supplier Performance Dashboard
- Multi-language (i18next: ru, uz, en)

---

## Deployment Pipeline

```
Local Dev (текущий этап)
  | .env + remote Neon DB
  | docker-compose up (без local-db profile)
  | Тестирование всех user journeys
  v
Staging VPS
  | docker-compose up + SSL
  | E2E тесты (Playwright)
  | Security scan (OWASP ZAP)
  v
Production
  | DNS switch
  | Monitoring (Sentry + Prometheus)
  | Backup verification
```

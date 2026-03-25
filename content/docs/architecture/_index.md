---
title: "Архитектура"
linkTitle: "Архитектура"
weight: 20
description: "Системная архитектура, бизнес-логика, технические спецификации и интерфейсы платформы RailTech B2B."
categories: ["Архитектура"]
tags: ["архитектура", "Django", "React", "PostgreSQL", "безопасность"]
---

Раздел содержит полную архитектурную документацию платформы RailTech B2B Ecosystem.

Платформа построена на 4-слойной архитектуре: Landing Page, B2B-каталог, Кабинет клиента и CRM/Backoffice. Backend реализован на Django + DRF + Channels, Frontend на React + TypeScript + Zustand. База данных -- PostgreSQL с расширениями pg_trgm и JSONB. Фоновые задачи -- Celery + Redis.

- **[Обзор системы](system-overview/)** -- Master System Document с описанием 4-слойной топологии
- **[Бизнес-логика](business-logic/)** -- Процессы KYC, Smart Match, финансовая модель, чат и уведомления
- **[Техническая спецификация](technical-spec/)** -- Стек, архитектура БД, RBAC, алгоритмы, инфраструктура
- **[Архитектура интерфейсов](interface-layers/)** -- UI/UX архитектура 4 слоев системы
- **[UX/UI Brief](ux-ui-brief/)** -- Дизайн-система, визуальные требования к компонентам
- **[Аудит безопасности](security-audit/)** -- Результаты аудита 2026-03-20

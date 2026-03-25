---
title: "Architecture"
linkTitle: "Architecture"
weight: 20
description: "Platform architecture, business logic, technical specification and security."
categories: ["Architecture"]
tags: ["architecture", "Django", "React", "PostgreSQL", "security"]
cascade:
  - type: "docs"
    _target:
      path: "/**"
---

This section provides a comprehensive technical overview of the RailTech B2B Ecosystem platform architecture.

The platform is built on a 4-layer architecture: Landing Page, B2B Catalog, Client Cabinet and CRM/Backoffice. The backend is implemented with Django + DRF + Channels, and the frontend with React + TypeScript + Zustand. The database is PostgreSQL with pg_trgm and JSONB extensions. Background tasks are handled by Celery + Redis.

- **[System Overview](system-overview/)** -- Master System Document describing the 4-layer topology
- **[Business Logic](business-logic/)** -- KYC, Smart Match, Landed Cost financial model, chat and notifications
- **[Technical Specification](technical-spec/)** -- Tech stack, database architecture, RBAC, algorithms, infrastructure
- **[Interface Architecture](interface-layers/)** -- UI/UX architecture of 4 system layers
- **[UX/UI Brief](ux-ui-brief/)** -- Design system, visual requirements for components
- **[Security Audit](security-audit/)** -- Security audit results and protective measures

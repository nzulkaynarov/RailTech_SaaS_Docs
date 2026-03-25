---
title: "Финансовый пайплайн"
linkTitle: "Финансовый пайплайн"
weight: 20
description: "Расчет Landed Cost, маржирование и ценообразование для коммерческих предложений."
categories: ["База знаний"]
tags: ["финансы", "Landed Cost", "НДС"]
---

{{< callout type="warning" >}}
**Критическое правило:** Все финансовые вычисления выполняются исключительно на сервере (Django backend). Frontend никогда не рассчитывает цены, НДС, маржу или Landed Cost. Нарушение этого правила -- критическая уязвимость.
{{< /callout >}}

Расчет сквозной себестоимости реализован на бэкенде в слое `crm.services`. Все финансовые вычисления выполняются исключительно на сервере -- frontend никогда не рассчитывает цены.

Поля `landed_cost` и итоговые цены хранятся в физических колонках БД (`DecimalField`) для поддержки агрегации.

---

## Формула для физических товаров (RAILWAY_MACHINERY, GENERAL_GOODS)

```
FCA Base Price (Закупка на заводе)
  + Logistics DAP (Логистика до Ташкента)
  + Customs Duty (Таможенная пошлина)
  = Landed Cost (Себестоимость)
    x (1 + Margin %)
    = Final Price (excl. VAT)
      x 1.12
      = Total Price (incl. 12% Uzbekistan VAT)
```

### Пошагово

| Шаг | Поле | Формула |
|-----|------|---------|
| 1 | `fca_base_price` | Чистая стоимость на заводе-изготовителе |
| 2 | `logistics_dap` | Затраты на логистику, страхование до DAP Ташкент |
| 3 | `customs_duty` | Таможенная пошлина |
| 4 | `landed_cost` | `fca_base_price + logistics_dap + customs_duty` |
| 5 | `final_price_excl_vat` | `landed_cost * (1 + margin_percent / 100)` |
| 6 | `total_price_incl_vat` | `final_price_excl_vat * 1.12` |

---

## Формула для услуг (SERVICES)

Для домена SERVICES логистика и таможня **игнорируются**:

```
Base Rate (Ставка подрядчика)
  x (1 + Margin %)
  = Final Price (excl. VAT)
    x 1.12
    = Total Price (incl. 12% Uzbekistan VAT)
```

---

## Технические детали

### Хранение
- Все денежные и процентные поля используют `DecimalField` (не `FloatField`)
- Поля `landed_cost`, `final_price_excl_vat`, `total_price_incl_vat` -- физические колонки, вычисляемые через Service Layer перед сохранением
- Это обеспечивает точность агрегации на уровне БД (`SUM`, `AVG`)

### Аудит
- Модель `QuoteItemHistory` хранит неизменяемый аудит-журнал всех изменений финансовых данных
- Каждое изменение цены или маржи фиксируется для последующего анализа

### Ставка НДС

{{< callout type="info" >}}
НДС Узбекистана: **12%** (фиксированная ставка). В будущем планируется вынесение в `PlatformSetting` для настройки через админку (Sprint 3).
{{< /callout >}}

### Quote Builder UI
- Внутренняя зона (себестоимость): колонки FCA, DAP, Таможня -- серый фон
- Внешняя зона (цена клиента): Маржа, Цена, Сумма -- белый фон
- Totalling Panel: чистый профит компании (зеленый) + итого с НДС для клиента

### Документооборот
- PDF коммерческих предложений генерируется через `WeasyPrint`
- Endpoint: `GET /api/v1/crm/deals/{id}/export_pdf/`

# Lead qualification examples

## Example 1 — price question from a product listing

Context:
- Avito listing: `NVIDIA RTX A5000 24GB`
- Category known from listing: `Видеокарты`
- Customer: `Здравствуйте, сколько сейчас стоит?`

Expected behavior:

```json
{
  "status": "qualifying",
  "intent": "price",
  "facts": {
    "category": "Видеокарты",
    "model": "RTX A5000 24GB",
    "quantity": null,
    "city": null,
    "payment": null,
    "expectation": null,
    "budget_rub": null,
    "phone": null
  },
  "crm_patch": {
    "Категория": "Видеокарты",
    "Модель": "RTX A5000 24GB"
  },
  "missing": ["phone", "quantity", "payment", "city", "expectation"],
  "next_step": {
    "action": "ask_phone",
    "field": "phone",
    "question": "Цены актуальные, отправьте номер телефона."
  },
  "handoff": {
    "required": false,
    "reason": null
  },
  "flags": {
    "price_question": true,
    "discount_request": false,
    "urgent_delivery": false,
    "compatibility_uncertain": false,
    "strong_buying_intent": false
  }
}
```

## Example 2 — customer gives several fields at once

Context:
- Listing: `NVIDIA H200 141GB`
- Customer: `Нужно 2 штуки с НДС, Москва. Телефон +7 999 123-45-67`

Expected behavior:

```json
{
  "status": "qualified",
  "intent": "buy",
  "facts": {
    "category": "Видеокарты",
    "model": "NVIDIA H200 141GB",
    "quantity": 2,
    "city": "Москва",
    "payment": "С НДС",
    "expectation": null,
    "budget_rub": null,
    "phone": "+79991234567"
  },
  "crm_patch": {
    "Категория": "Видеокарты",
    "Модель": "NVIDIA H200 141GB",
    "Кол-во": 2,
    "Город": "Москва",
    "Оплата": "С НДС",
    "Телефон": "+79991234567"
  },
  "missing": ["expectation"],
  "next_step": {
    "action": "none",
    "field": null,
    "question": null
  },
  "handoff": {
    "required": false,
    "reason": null
  },
  "flags": {
    "price_question": false,
    "discount_request": false,
    "urgent_delivery": false,
    "compatibility_uncertain": false,
    "strong_buying_intent": true
  }
}
```

## Example 3 — ambiguous payment

Customer: `Берём 4, оплата безнал.`

Rule:
- extract `quantity = 4`;
- do not map plain `безнал` to a specific payment type;
- leave `payment = null`;
- ask whether payment is with VAT, without VAT, or to an individual entrepreneur when payment detail is the best next question.

## Example 4 — discount request

Customer: `Если возьму 8 штук, какую скидку дадите?`

Rule:
- extract quantity if product is already known;
- set `discount_request = true`;
- if phone is absent, request it if appropriate;
- set `handoff.required = true` because individual discount terms require a human decision;
- never invent or promise a discount.

## Example 5 — urgent delivery

Customer: `Нужно в Казань максимум через 10 дней, успеете?`

Rule:
- extract city `Казань`;
- set `expectation` to the explicit deadline in a concise normalized form;
- set `urgent_delivery = true`;
- do not promise the deadline;
- hand off if no verified delivery data is available.

## Example 6 — model conflict

Listing: `RTX A5000 24GB`
Customer: `А A6000 есть?`

Rule:
- do not keep `RTX A5000 24GB` as the requested model;
- record the customer's requested model only if it is clear enough (`A6000` as a candidate/request);
- request product resolution when exact generation or memory configuration matters;
- never silently patch an uncertain exact model into CRM.

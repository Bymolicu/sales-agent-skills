# Manager handoff examples

## Example 1 — phone received

Input:
- Product: NVIDIA H200 141GB
- Quantity: 2
- City: Москва
- Payment: С НДС
- Phone: +7 999 123-45-67
- Responsible manager is known

Expected:

```json
{
  "decision": "handoff",
  "handoff": {
    "required": true,
    "target": "responsible_manager",
    "urgency": "normal",
    "reason_code": "phone_received",
    "reason": "Клиент сообщил телефон и проявляет коммерческий интерес"
  },
  "ai_control": {
    "reply_allowed": true,
    "disable_auto_reply": true,
    "do_not_reply_again": true
  },
  "task": {
    "create": true,
    "due_in_minutes": 15,
    "title": "Связаться с клиентом — NVIDIA H200 141GB",
    "note": "2 шт.; Москва; С НДС; телефон +79991234567"
  },
  "crm_safe_updates": {
    "Телефон": "+79991234567",
    "Модель": "NVIDIA H200 141GB",
    "Кол-во": 2,
    "Город": "Москва",
    "Оплата": "С НДС"
  },
  "deduplication": {
    "handoff_already_exists": false,
    "create_new_task": true
  }
}
```

## Example 2 — discount request

Customer:
`Возьму 8 штук. Дадите скидку 10%?`

Expected behavior:
- target = `Александр`;
- due_in_minutes = 0;
- do not approve 10%;
- disable AI after handoff.

## Example 3 — exact delivery deadline

Customer:
`Нужно в Казань максимум через 7 дней. Гарантируете?`

Expected behavior:
- target = responsible manager or queue;
- reason_code = `nonstandard_delivery_deadline`;
- due_in_minutes = 0;
- do not guarantee 7 days.

## Example 4 — complaint

Customer:
`Вы меня обманули, хочу возврат и буду жаловаться.`

Expected behavior:
- immediate human handoff;
- reason_code = `complaint_or_conflict`;
- no AI dispute;
- disable auto replies.

## Example 5 — simple question, no handoff

Customer:
`Какая память у A5000?`

No phone and no escalation trigger.

Expected behavior:
- decision = `continue_ai`;
- no task;
- AI remains enabled.

## Example 6 — duplicate webhook after handoff

Input:
- handoff_already_created = true
- auto_reply_disabled = true

Expected behavior:
- decision = `stop_ai`;
- task.create = false;
- create_new_task = false;
- reply_allowed = false.

## Example 7 — asks for a manager

Customer:
`Позовите менеджера, хочу обсудить заказ.`

Expected behavior:
- immediate handoff;
- target = responsible manager if known, else `sales_manager_queue`;
- do not continue qualification before transfer.

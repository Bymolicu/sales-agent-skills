# Sales Rules Examples

## 1. Price question, no phone

Customer:
`Сколько стоит H200?`

Expected:

```json
{
  "decision": "allow",
  "rule": "price",
  "reason": "В Avito цену не сообщать; телефон ещё неизвестен.",
  "safe_response_hint": "Цены актуальные, отправьте номер телефона.",
  "required_verification": null,
  "handoff": {
    "required": false,
    "reason": null,
    "target": null
  },
  "crm_constraints": {
    "allowed": [],
    "blocked": ["Цена"]
  }
}
```

## 2. Price question, phone already known

Context:
`phone = +79991234567`

Customer:
`Какая цена?`

Expected behavior:
- не повторять запрос телефона;
- не сообщать цену;
- `decision = handoff`.

## 3. Discount request

Customer:
`Если возьму 8 штук, какую скидку дадите?`

Expected behavior:
- не называть скидку;
- `decision = handoff`;
- `handoff.target = Александр`;
- если телефона нет, `safe_response_hint` может попросить номер.

## 4. Unknown availability

Customer:
`A6000 есть в наличии?`

No current stock source is present.

Expected behavior:
- не говорить `да` или `нет`;
- `decision = verify`;
- `required_verification = наличие A6000`.

## 5. Confirmed availability

Context contains a current authoritative stock record for the exact model.

Expected behavior:
- `decision = allow`;
- не добавлять количество/склад, если они не подтверждены.

## 6. Delivery exact deadline

Customer:
`Мне нужно в Казань точно до пятницы. Успеете?`

Expected behavior:
- не обещать;
- `decision = handoff`;
- причина: точный нестандартный дедлайн.

## 7. Delivery range is explicitly confirmed

Context:
`Актуальный срок для этой позиции: 10–14 дней.`

Customer:
`Сколько доставка?`

Expected behavior:
- разрешить сообщить ориентир `10–14 дней`;
- не использовать слова `гарантированно` или `точно`.

## 8. Ambiguous bank transfer

Customer:
`Оплата безнал.`

Expected behavior:
- не выбирать между `Безнал без НДС`, `С НДС`, `Безнал ИП`;
- `decision = verify`;
- запросить уточнение формы оплаты.

## 9. USDT spelling

Customer:
`Оплата USDT.`

Expected canonical form:
`US DT`

Do not invent exchange-rate or commission rules.

## 10. Compatibility uncertainty

Customer:
`7955WX точно заведётся на этой плате?`

No authoritative compatibility data is provided.

Expected behavior:
- не отвечать `да`;
- `decision = verify` or `handoff` depending on available product tools.

## 11. Unsupported strong claim

Draft reply:
`У нас гарантированно лучшая цена на рынке.`

No approved campaign or evidence is present.

Expected behavior:
- `decision = block`;
- rule = `claim`.

## 12. Phone already provided

Customer already sent a phone number earlier.
Draft reply asks: `Отправьте номер телефона.`

Expected behavior:
- `decision = block`;
- reason: телефон уже известен, повторный запрос запрещён.

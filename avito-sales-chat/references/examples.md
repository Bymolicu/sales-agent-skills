# Avito Sales Chat Examples

## 1. Price question, no phone

Customer:
`Здравствуйте. Сколько стоит H200 141GB?`

Qualification:
- price question = true
- phone = null
- next question = `Цены актуальные, отправьте номер телефона.`

Expected:

```json
{
  "action": "reply",
  "message": "Цены актуальные, отправьте номер телефона.",
  "handoff": {
    "required": false,
    "reason": null
  }
}
```

## 2. Price question, phone already known

Customer:
`А сколько сейчас стоит?`

Context:
- phone = `+79991234567`

Expected:

```json
{
  "action": "reply_and_handoff",
  "message": "Номер вижу. Передам менеджеру запрос по стоимости.",
  "handoff": {
    "required": true,
    "reason": "Клиент запросил стоимость, телефон уже известен"
  }
}
```

## 3. Confirmed availability

Customer:
`H200 есть в наличии?`

Context:
- availability = confirmed in stock

Expected message:
`Да, H200 есть в наличии.`

Do not add a price.

## 4. Availability unknown

Customer:
`A6000 есть?`

Context:
- availability is unknown
- phone is unknown

Expected message:
`Наличие нужно уточнить. Отправьте номер телефона, менеджер свяжется с вами.`

Never claim the item is in stock.

## 5. Discount request

Customer:
`Если возьму 8 штук, скидку сделаете?`

Phone unknown.

Expected:

```json
{
  "action": "reply_and_handoff",
  "message": "По скидке нужно согласование. Отправьте номер телефона.",
  "handoff": {
    "required": true,
    "reason": "Запрос скидки или индивидуальных условий"
  }
}
```

Do not invent a discount amount.

## 6. Delivery deadline not confirmed

Customer:
`В Казань за 7 дней успеете?`

No confirmed delivery deadline is available.

Expected message:
`Точный срок нужно согласовать. Отправьте номер телефона.`

Recommend handoff.

## 7. Payment options

Customer:
`Какие варианты оплаты есть?`

Expected message:
`Можно наличными, безналом без НДС, с НДС, безналом на ИП или US DT.`

Always spell `US DT` with a space.

## 8. Ambiguous payment

Customer:
`Оплата будет безнал.`

Qualification indicates payment type is ambiguous.

Expected message:
`Безнал нужен с НДС, без НДС или на ИП?`

Ask only one question.

## 9. Technical compatibility uncertain

Customer:
`Эта память точно подойдёт к моей плате?`

Motherboard model is unknown.

Expected message:
`Напишите точную модель материнской платы.`

Do not guess compatibility.

## 10. Complaint

Customer:
`Мне пришла не та комплектация, разберитесь.`

Expected:

```json
{
  "action": "reply_and_handoff",
  "message": "Понял. Передам вопрос менеджеру, чтобы проверили комплектацию по заказу.",
  "handoff": {
    "required": true,
    "reason": "Претензия по комплектации"
  }
}
```

Do not admit fault without verified facts.

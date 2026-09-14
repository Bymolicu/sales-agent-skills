# Product resolver examples

## Example 1 — RTX A5000 alias

Input:

`Нужна A5000, две штуки.`

If catalog/context confirms NVIDIA RTX A5000 24GB:

```json
{
  "resolution": "normalized",
  "category": "Видеокарты",
  "raw_mention": "A5000",
  "resolved_model": "RTX A5000 24GB",
  "brand": "NVIDIA",
  "confidence": 0.94,
  "candidates": [],
  "needs_catalog_lookup": false,
  "needs_clarification": false,
  "clarification_question": null,
  "crm_patch": {
    "Категория": "Видеокарты",
    "Модель": "RTX A5000 24GB"
  },
  "evidence": [
    "A5000 однозначно относится к NVIDIA RTX A5000 в текущем контексте"
  ]
}
```

## Example 2 — H200

Input:

`Есть H200 141?`

Expected canonical model:

```json
{
  "resolution": "normalized",
  "category": "Видеокарты",
  "raw_mention": "H200 141",
  "resolved_model": "NVIDIA H200 141GB",
  "brand": "NVIDIA",
  "confidence": 0.95,
  "candidates": [],
  "needs_catalog_lookup": false,
  "needs_clarification": false,
  "clarification_question": null,
  "crm_patch": {
    "Категория": "Видеокарты",
    "Модель": "NVIDIA H200 141GB"
  },
  "evidence": [
    "Явно указано H200 141"
  ]
}
```

## Example 3 — do not invent VRAM

Input:

`Нужна 3080 Ti.`

If no laptop/desktop context or catalog entry confirms VRAM:

```json
{
  "resolution": "normalized",
  "category": "Видеокарты",
  "raw_mention": "3080 Ti",
  "resolved_model": "RTX 3080 Ti",
  "brand": "NVIDIA",
  "confidence": 0.88,
  "candidates": [],
  "needs_catalog_lookup": true,
  "needs_clarification": false,
  "clarification_question": null,
  "crm_patch": {
    "Категория": "Видеокарты",
    "Модель": "RTX 3080 Ti"
  },
  "evidence": [
    "Объём памяти не указан"
  ]
}
```

Do not change it to `RTX 3080 Ti 16GB` or another VRAM value without evidence.

## Example 4 — client switches model

Listing:

`RTX A5000 24GB`

Customer:

`А A6000 есть?`

Expected behavior:

- do not keep A5000 as the active model;
- resolve the current request to A6000 only as far as safely possible;
- do not invent a memory size unless confirmed by catalog/context.

Example result:

```json
{
  "resolution": "normalized",
  "category": "Видеокарты",
  "raw_mention": "A6000",
  "resolved_model": "RTX A6000",
  "brand": "NVIDIA",
  "confidence": 0.88,
  "candidates": [],
  "needs_catalog_lookup": true,
  "needs_clarification": false,
  "clarification_question": null,
  "crm_patch": {
    "Категория": "Видеокарты",
    "Модель": "RTX A6000"
  },
  "evidence": [
    "Клиент явно переключился с модели объявления на A6000"
  ]
}
```

## Example 5 — ambiguous request

Input:

`A5000 или A6000 интересует.`

Expected:

```json
{
  "resolution": "ambiguous",
  "category": "Видеокарты",
  "raw_mention": "A5000 или A6000",
  "resolved_model": null,
  "brand": "NVIDIA",
  "confidence": 0.7,
  "candidates": ["RTX A5000", "RTX A6000"],
  "needs_catalog_lookup": false,
  "needs_clarification": true,
  "clarification_question": "Какая модель нужна: RTX A5000 или RTX A6000?",
  "crm_patch": {
    "Категория": "Видеокарты"
  },
  "evidence": [
    "Клиент указал две разные модели"
  ]
}
```

## Example 6 — Threadripper PRO

Input:

`7955wx нужен.`

Expected:

```json
{
  "resolution": "normalized",
  "category": "Процессоры",
  "raw_mention": "7955wx",
  "resolved_model": "Threadripper PRO 7955WX",
  "brand": "AMD",
  "confidence": 0.92,
  "candidates": [],
  "needs_catalog_lookup": false,
  "needs_clarification": false,
  "clarification_question": null,
  "crm_patch": {
    "Категория": "Процессоры",
    "Модель": "Threadripper PRO 7955WX"
  },
  "evidence": [
    "7955WX однозначно относится к AMD Threadripper PRO в текущем контексте"
  ]
}
```

## Example 7 — memory module part number

Input:

`Нужен M321R4GA3EB2-CCP.`

If there is no catalog row loaded:

- preserve the exact part number;
- categorize as `Оперативная память` only if context confirms it;
- do not invent capacity or speed from memory unless verified by catalog/reference.

## Example 8 — bare A100 is ambiguous

Input:

`Нормализуй модель товара: A100`

Expected:

```json
{
  "resolution": "ambiguous",
  "category": "Видеокарты",
  "raw_mention": "A100",
  "resolved_model": null,
  "brand": "NVIDIA",
  "confidence": 0.8,
  "candidates": ["NVIDIA A100 40GB", "NVIDIA A100 80GB"],
  "needs_catalog_lookup": false,
  "needs_clarification": true,
  "clarification_question": "Какой объём памяти нужен: 40 или 80 ГБ?",
  "crm_patch": {
    "Категория": "Видеокарты"
  },
  "evidence": [
    "У A100 есть варианты 40GB и 80GB; объём памяти не указан"
  ]
}
```

Do not return `NVIDIA A100` as the final CRM model when the customer only wrote `A100`.

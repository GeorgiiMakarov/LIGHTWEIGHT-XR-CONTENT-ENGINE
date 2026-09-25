# Ad Template Pack v1 (DRAFT)

Персонализированная реклама для XR: рекламодатель заранее описывает шаблон
(`schemas/template-pack.schema.json`), пользователь даёт согласие
(`schemas/consent-receipt.schema.json`), персонализация компонуется **только
на устройстве**, показ доказывается двумя потоками.

## Pipeline

```
advertiser -> template pack -> consent -> on-device compose -> XR delivery
-> verified impression / presence
```

1. **Template pack.** Креатив + слоты персонализации (`character_anchor`,
   `name_token`, `text_token`), обязательный fallback, disclosure
   («реклама»), политика верификации. Рекламодатель, студия или генератор
   (класса Higgsfield) выпускает пак по открытой схеме; платформа валидирует
   его (`tools/validate_template_pack.py`).
2. **Consent.** Двухосевой: временной (`single_request | campaign_window |
   standing`) × целевой (`global | advertiser | campaign`). Классы раздельно:
   `profile_token` (текст/имя) и `likeness_binding` (облик персонажа).
   Подписанное устройство хранит квитанцию; события `ConsentGranted /
   ConsentRevoked` — состояние ядра решений, не чекбокс UI.
3. **On-device compose.** Сырое лицо/биометрия не входит в пак, квитанцию
   и аудит. Медиа инстанса живёт TTL и удаляется; в логе остаются только хеши.
4. **Delivery.** Трейлеры — те же слоты, не отдельный продукт. Уровень 1:
   только текстовые токены (без биометрии вообще). Уровень 2: композиция
   персонажа по Character Pack (референс, не сырое лицо).
5. **Proof.** Dual-stream: A — intent рекламодателя (campaign, template,
   manifest hash, запрошенное время); B — факт устройства (что показано,
   персонализировано ли, receipt ID, хеш инстанса, evidence).

## Инварианты

- **P1:** нет fallback → пак отклоняется (проверяется в CI).
- **P2:** нет валидного consent → только fallback, `personalized=false`.
- **P3:** в логах хеши, никогда — персонализированное медиа.

## Canonical hash

`manifest_sha256` считается по каноническому JSON манифеста **без самого
поля** `manifest_sha256`:

```python
json.dumps(obj, sort_keys=True, separators=(",", ":"),
           ensure_ascii=False).encode("utf-8")  # -> sha256 hex
```

Пример с реальным хешем: `examples/sample_template_pack.json`
(level 1, только текстовые токены). Квитанция и события:
`examples/sample_consent_receipt.json`,
`examples/sample_consent_granted.json`, `examples/sample_consent_revoked.json`.

## Проверки

- `tools/validate_template_pack.py` — schema + P1 + level/slots
  (level 1: только `name_token`/`text_token`) + `manifest_sha256`.
- `tools/test_template_pack.py` — негативная матрица (9 проб).
- e2e (`tools/e2e_stack_check.py`): consent missing → fallback only;
  consent ok → personalized flag в evidence (18/18).

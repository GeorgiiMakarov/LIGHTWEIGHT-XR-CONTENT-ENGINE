# Lightweight XR Content Engine (Android XR Edition)

## **B2B SDK для интерактивных 3D-слоёв поверх видео:** pre-baked XR-состояния, переключаемые жестами руки, с привязкой к таймингу контента. Паттерн — динамическая вставка рекламы в онлайн-видео (VAST/VMAP), перенесённая в пространственный XR.


## Статус

**Phase 1** инженерный скелет (pre-compile). Unity 6 / OpenXR: свитчер слоёв, классификаторы жестов поверх сырых поз 26 суставов (OpenXR), парсер JSON-манифеста, head-locked плейсмент.

Скелет на целевом железе ещё не компилировался и не тестировался. Первая компиляция, отладка и сбор датасета жестов запланированы как milestone 1 совместно с инженерным партнёром. Пороги классификаторов - заглушки под тюнинг на реальном датасете.

Проверено без железа: схемы и спецификации, валидаторы паков, сквозной e2e-прогон пак → события → decision core → Defense-Dossier (14/14 зелёных).

**CI:** `.github/workflows/ci.yml` гоняет на каждый пуш валидатор паков, матрицу проб, референсный пайплайн, e2e-прогон 14/14 и C#-эмулятор; прикладывает verification-артефакт. **Бенчмарки:** `docs/benchmarks.md` (замеры без железа, не гарантии для устройства).


## Состав репозитория

**docs/** архитектурный документ rev3: два сценария синхронизации (A — UGC/локальное ACR через AudioPlaybackCapture, B — DRM cue API для партнёров), словарь жестов v1, фазы развития; xr-session-protocol-v1.md протокол взаимодействия смартфона и XR smart glass; character-pack-spec-v1.md спека контент-паков персонажей для роботов QAZBOT

**unity/** исходники скелета Phase 1 (C#): XREngine, LayerStack, LayerSwitcher, классификаторы Swipe / PalmHold, пример манифеста

**schemas/** xr-layer-manifest.schema.json (v1, контракт манифеста слоёв, Spatial VAST/VMAP), xr-preset-manifest.schema.json (v2, тайминговые пресеты), gesture-stream.schema.json (контракт синтетического потока жестов), xr-event.schema.json (доменные события для биллинга/аудита), character-pack.schema.json (Character Pack v1, контракт контент-пака персонажа для роботов QAZBOT)

**unity/LayerSwitcherEmulator/** headless-тестбенч: чистый C#, replay синтетического потока 26 суставов из JSON через ту же математику жестов, PASS/FAIL-отчёт и замер бюджета 100 мс. Работает без Unity и железа (dotnet run)

**tools/** generate_gesture_stream.py (детерминированный генератор синтетики, seed 7), check_pipeline.py (референсная проверка пайплайна), validate_character_pack.py (валидатор паков), test_character_pack.py (матрица позитивных/негативных проб), e2e_stack_check.py (сквозной прогон: пак → XR-события → decision core → Defense-Dossier, 14/14)

**examples/** presets/ (пример таймингового пресета), events/ (примеры доменных событий), sample_character_pack.json (референсный пак персонажа «Ару»)


## Architecture: Host-Periphery Topology

**schemas/xr-preset-manifest.schema.json** пресет как единица контента с собственным таймингом (до 15 с); телефон владеет часами, жест только сменяет или закрывает.

**docs/xr-session-protocol-v1.md** протокол «телефон ↔ очки»: manifest_push → pre-warm handshake (state ready) → preset_start; поза 10 Гц без ACK, дискретные жесты с ACK.

**schemas/xr-event.schema.json** доменные события для decision-intelligence-core: PresetServed, ImpressionValidated, GestureInteraction, PresetClosed; evidence обязателен (I3), event_id = idempotency_key (I6).


## Дорожная карта

**Phase 2** Сценарий A: Android Service (AudioPlaybackCapture-приоритет, аудиофингерпринтинг), consent UX, префетч слоёв, тюнинг жестов

**Phase 2.5** (roadmap) On-device SLM intent layer: резидентная в NPU-памяти SLM класса 3B вместо эвристических порогов жестов (фьюжн поз руки + аудиоконтекст + таймлайн); gated on железо с ≥12 ГБ unified memory

**Phase 3** Сценарий B: cue-контракт POST /v1/xr-cue для DRM-партнёров (BD-трек), бэкенд, Co-Viewing Sync.

**Showtime Audit** — Merkle-якорение биллинговых записей работает (синтетика); продакшн-подписи (ЭЦП НУЦ РК; интеграция RFC 3161 TSA в аудит — следующий шаг).



## Контакт

Георгий Макаров автор и драйвер проекта. По вопросам партнёрства: 

https://www.linkedin.com/in/georgii-makarov

crporativefun@gmail.com

https://signal.me/#eu/1vhnqxY7atTKFAOymSbaPMEiSAC31MY2cD3pOuQr82ZdbpPCTtBLK5UddmrC06N_

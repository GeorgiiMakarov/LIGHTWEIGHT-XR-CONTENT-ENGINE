# Lightweight XR Content Engine (Android XR Edition)

## **B2B SDK для интерактивных 3D-слоёв поверх видео:** pre-baked XR-состояния, переключаемые жестами руки, с привязкой к таймингу контента. Паттерн — динамическая вставка рекламы в онлайн-видео (VAST/VMAP), перенесённая в пространственный XR.


## Статус

**Phase 1** — инженерный скелет (pre-compile). Unity 6 / OpenXR: свитчер слоёв, классификаторы жестов поверх сырых поз 26 суставов (OpenXR), парсер JSON-манифеста, head-locked плейсмент.

Скелет на целевом железе ещё не компилировался и не тестировался — первая компиляция, отладка и сбор датасета жестов запланированы как milestone 1 совместно с инженерным партнёром. Пороги классификаторов — заглушки под тюнинг на реальном датасете.


## Состав репозитория

**docs/** — архитектурный документ rev3: два сценария синхронизации (A — UGC/локальное ACR через AudioPlaybackCapture, B — DRM cue API для партнёров), словарь жестов v1, фазы развития

**unity/** — исходники скелета Phase 1 (C#): XREngine, LayerStack, LayerSwitcher, классификаторы Swipe / PalmHold, пример манифеста

**schemas/** - xr-layer-manifest.schema.json (контракт манифеста слоёв, Spatial VAST/VMAP), gesture-stream.schema.json (контракт синтетического потока жестов)

**unity/LayerSwitcherEmulator/** - headless-тестбенч: чистый C#, replay синтетического потока 26 суставов из JSON через ту же математику жестов, PASS/FAIL-отчёт и замер бюджета 100 мс. Работает без Unity и железа (dotnet run)

**tools/** - generate_gesture_stream.py (детерминированный генератор синтетики, seed 7), check_pipeline.py  (референсная проверка пайплайна)


## Architecture: Host-Periphery Topology

**docs/xr-session-protocol-v1.md**
протокол взаимодействия смартфона и XR smart glass.

**schemas/xr-preset-manifest.schema.json**
Схема манифеста.


## Releases — v0.1-phase1: zip-архив скелета для быстрой передачи


## Дорожная карта

**Phase 2** — Сценарий A: Android Service (AudioPlaybackCapture-приоритет, аудиофингерпринтинг), consent UX, префетч слоёв, тюнинг жестов

**Phase 2.5** (roadmap) — On-device SLM intent layer: резидентная в NPU-памяти SLM класса 3B вместо эвристических порогов жестов (фьюжн поз руки + аудиоконтекст + таймлайн); gated on железо с ≥12 ГБ unified memory

**Phase 3** — Сценарий B: cue-контракт POST /v1/xr-cue для DRM-партнёров (BD-трек), бэкенд, Co-Viewing Sync.


## Контакт
Георгий Макаров — автор и драйвер проекта. По вопросам партнёрства: 

https://www.linkedin.com/in/georgii-makarov

crporativefun@gmail.com

https://signal.me/#eu/1vhnqxY7atTKFAOymSbaPMEiSAC31MY2cD3pOuQr82ZdbpPCTtBLK5UddmrC06N_

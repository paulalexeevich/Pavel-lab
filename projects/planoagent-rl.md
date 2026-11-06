---
slug: planoagent-rl
name: PlanoAgent-RL
status: active
type: ML / RL research (planogram generation)
stack:
  - Python
  - Stable-Baselines3 (MaskablePPO)
  - FastAPI (api/)
  - Streamlit (ui/)
  - Supabase (data source)
infra:
  - GitHub (paulalexeevich/PlanoAgent-RL)
repo: paulalexeevich/PlanoAgent-RL
url:
added: 2026-11-06
updated: 2026-11-06
---

# PlanoAgent-RL

> Планограмма-генератор для категории Hair Care (P&G): гибрид детерминированного
> бин-паккинга и RL, дообучаемый на текстовом фидбэке мерчандайзера в реальном
> времени. Началось как чистая RL-политика; по ходу экспериментов выяснилось,
> что RL плохо держит визуальную связность блоков бренда/сегмента на нескольких
> полках подряд — детерминированный алгоритм решает это лучше, RL лучше решает
> размещение фейсингов и дообучение на фидбэке. Отсюда двухслойная архитектура.

## What It Does

- **Слой 1 — macro-раскладка**: `planoagent/packing/tree_packer.py` — детерминированный
  рекурсивный бин-паккинг (Категория → Сегмент → Бренд → Товар), по убыванию объёма.
- **Слой 2 — RL-агент перестановки**: `planoagent/env/rearrange_env.py::RearrangeFacingEnv`
  (MaskablePPO) берёт раскладку tree packer'а и доучивает её, двигая фейсинги между
  слотами, чтобы исправить несвязность. Обучен на 300k шагов.
- **Исходная RL-политика размещения**: `planoagent/env/planogram_env.py::PlanogramEnv` —
  совместное действие (SKU, полка) с нуля; альтернативный beam-search декодер.
- **Контур фидбэка**: `planoagent/feedback/parser.py` парсит текст мерчандайзера в
  `FeedbackDelta` через LLM, `planoagent/feedback/finetune.py` делает burst-дообучение.
- **API + UI**: FastAPI (`api/`) поверх генерации/фидбэка/истории сессии; Streamlit
  (`ui/`) — инспектор раскладки, отчёт по комплаенсу, мониторинг обучения.

## Stack & Infra

- **Code**: Python (RL env/policy, tree packer, FastAPI, Streamlit)
- **Deploy**: не задеплоено — локальный/исследовательский запуск
- **Repo**: [PlanoAgent-RL](https://github.com/paulalexeevich/PlanoAgent-RL)
- **Live**: not deployed yet

## Status

**ACTIVE** — исследование гипотез в разгаре. 2026-11-03: начальный коммит с чистой
RL-архитектурой, затем переписан README под двухслойную схему. 2026-11-05: гипотеза
"чистый RL" дополнена/скорректирована — разменяли часть hard-constraint'ов
(brand/segment override) на soft reward termы, добавлены graph-contiguity rewards и
lookahead search для rearrange-агента, двухстадийный pipeline (tree_packer + rearrange)
подключён в `/generate`.

## Key Decisions

- Гибрид вместо чистого RL: детерминированный tree packer лучше держит визуальную
  связность блока бренда/сегмента на нескольких полках, RL лучше решает размещение
  фейсингов и дообучение на живом фидбэке мерчандайзера (чего CP "из коробки" не умеет)
- Hard-constraint'ы по brand/segment override демонтированы в soft reward termы
  (2026-11-05) — see commit `167d2f3`
- `PLANOAGENT_DATA_MODE` поддерживает `db`/`cache`/`synthetic`/`auto` fallback-цепочку,
  чтобы работать без доступа к Supabase (synthetic данные явно помечены `_synthetic: true`)

## Sessions

| Date | What happened |
|------|---------------|
| 2026-11-03 | Initial commit: PlanoAgent-RL planogram generator; README обновлён под двухслойную архитектуру (tree packer + RL rearrange) |
| 2026-11-05 | Демонтированы hard-constraints brand/segment override → soft rewards; добавлены graph-contiguity rewards и lookahead search в rearrange-агента; two-stage pipeline (tree_packer+rearrange) подключён в `/generate`; убраны устаревшие тесты hard-constraint'ов |

## Links

- GitHub: https://github.com/paulalexeevich/PlanoAgent-RL
- Live: —
- Handoff: —

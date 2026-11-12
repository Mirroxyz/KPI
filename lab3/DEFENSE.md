# DEFENSE — Завдання 3: Sequence Diagram

## 1. Що я просив у AI

Я попросив деталізувати два Gherkin-сценарії UC `Reserve Book` з Lab 2 у
Mermaid sequence-діаграмах, узгоджених із ER-моделлю Lab 1. Вхідні критерії,
межі й out-of-scope зафіксовані мною в [`spec.md`](./spec.md); повний слід —
у [`ai/`](./ai/).

## 2. Топ-3 розбіжності, які я знайшов і виправив

| # | Що було не так проти `spec.md` | Мій слід | Виправлення |
|---|---|---|---|
| 1 | AI-вихід не містив явної примітки, що persistence відбувається лише після session, member і book checks. | [`ai/02-review-feedback.md`](./ai/02-review-feedback.md) · критерій у `spec.md` | [`066b9d6`](https://github.com/Mirroxyz/KPI/commit/066b9d6) |
| 2 | У штрафній гілці AI-вихід не містив явної гарантії відсутності викликів `BookRepository` і `ReservationRepository`. | [`ai/02-review-feedback.md`](./ai/02-review-feedback.md) · критерій у `spec.md` | [`066b9d6`](https://github.com/Mirroxyz/KPI/commit/066b9d6) |
| 3 | AI-вихід розміщував аудит в окремому `AUDIT.md`, хоча вхідний контракт вимагав розділ аудиту в `spec.md`. | [`ai/02-review-feedback.md`](./ai/02-review-feedback.md) · критерій у `spec.md` | [`c9bf873`](https://github.com/Mirroxyz/KPI/commit/c9bf873) |

## 3. Ключове рішення

Залишити `FINE` станом, який повертає `MemberRepository`, а не додавати нову
ER-сутність у Lab 3. Це зберігає межі вхідної моделі; альтернативи й наслідки
описані в [`ADR-0001`](./ADR/0001-reserve-book-sequence-boundary.md).

## 4. Перевірка

- `spec.md` містить два сценарії, критерії прийняття та трасування до Lab 2.
- Учасники перевірені проти `lab1/er-model.mmd`: `MEMBER`, `BOOK`,
  `RESERVATION`; зайві `BOOK_COPY` і `LOAN` виключені в розділі
  `Аудит та трасування` у `spec.md`.
- Рендер відтворено командами Mermaid CLI з `spec.md`; обидві команди
  завершилися успішно й оновили SVG у [`lab3/`](.).
- `git diff --check` пройшов без помилок; історія містить базовий,
  specification/trace, diagram-fix і defense-кроки.


# DEFENSE — Завдання 3: Sequence Diagram

## 1. Що я просив у AI

Я попросив деталізувати два Gherkin-сценарії UC `Reserve Book` з Lab 2 у
Mermaid sequence-діаграмах, узгоджених із ER-моделлю Lab 1. Вхідні критерії,
межі й out-of-scope зафіксовані мною в [`spec.md`](./spec.md); повний слід —
у [`ai/`](./ai/).

## 2. Топ-3 розбіжності, які я знайшов і виправив

| # | Що було не так проти `spec.md` | Мій слід | Виправлення |
|---|---|---|---|
| 1 | Початковий spec описував сценарії, але не фіксував повні критерії, out-of-scope і команду рендера. | [`ai/02-review-feedback.md`](./ai/02-review-feedback.md) | [`1775d43`](https://github.com/Mirroxyz/KPI/commit/1775d43) |
| 2 | Діаграми не показували явно, що запис `RESERVATION` відбувається лише після перевірок, а негативна гілка не викликає repository. | [`ai/02-review-feedback.md`](./ai/02-review-feedback.md) | [`78dca1f`](https://github.com/Mirroxyz/KPI/commit/78dca1f) |
| 3 | Один журнал prompt history не був повним слідом у `ai/`, а defense не давав рев'юеру короткої точки входу з SHA. | [`ai/01-request.md`](./ai/01-request.md), [`ai/02-review-feedback.md`](./ai/02-review-feedback.md) | [`1775d43`](https://github.com/Mirroxyz/KPI/commit/1775d43) |

## 3. Ключове рішення

Залишити `FINE` станом, який повертає `MemberRepository`, а не додавати нову
ER-сутність у Lab 3. Це зберігає межі вхідної моделі; альтернативи й наслідки
описані в [`ADR-0001`](./ADR/0001-reserve-book-sequence-boundary.md).

## 4. Перевірка

- `spec.md` містить два сценарії, критерії прийняття та трасування до Lab 2.
- Учасники перевірені проти `lab1/er-model.mmd`: `MEMBER`, `BOOK`,
  `RESERVATION`; зайві `BOOK_COPY` і `LOAN` виключені в
  [`AUDIT.md`](./AUDIT.md).
- Рендер відтворено командами Mermaid CLI з `spec.md`; обидві команди
  завершилися успішно й оновили SVG у [`lab3/`](.).
- `git diff --check` пройшов без помилок; історія містить базовий,
  specification/trace, diagram-fix і defense-кроки.


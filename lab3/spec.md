# Специфікація Lab 3 — Sequence Diagram

## Намір і межі

Потрібно деталізувати **UC `Reserve Book`** з Lab 2. У scope лише два його
Gherkin-сценарії: успішне бронювання та відмова через несплачений штраф.
Пошук, видача, повернення, оплата штрафу й виконання бронювання — out of scope.

## Вхідні сценарії

1. **`Member reserves an available book` (Lab 2, сценарій 3):** за
   авторизованої сесії та доступної `BOOK` система створює активний
   `RESERVATION` для `Member` і `BOOK`.
2. **`Member with an unpaid fine cannot reserve a book` (Lab 2, сценарій 4):**
   за авторизованої сесії та несплаченого штрафу система відхиляє бронювання й
   показує суму заборгованості.

## Критерії прийняття

- Є окрема Mermaid sequence-діаграма й SVG-рендер для кожного сценарію.
- Учасники та повідомлення відображають тільки сценарії: `Member`, boundary
  UI, `AuthService`, `ReservationService`, `MemberRepository`, `BookRepository`
  і `ReservationRepository`.
- У кожній діаграмі є `alt`, виклики з реалістичними назвами, return-повідомлення
  та activation bars. `UI` і `ReservationService` активні до й після `alt`;
  у гілці штрафу не викликається `createActiveReservation()`.
- `MEMBER`, `BOOK` і `RESERVATION` узгоджені з ER-моделлю Lab 1:
  `createActiveReservation(memberId, bookId)` створює активний запис із двома
  FK. `FINE` не додається, бо його немає в ER-моделі.
- `spec.md`, `DEFENSE.md` і `AUDIT.md` мають бути стислими; повні дослівні
  промпти та студентський фідбек зберігаються в `ai/`.
- Рендер відтворюється командою:
  `npx --yes @mermaid-js/mermaid-cli -i lab3\reserve-success.mmd -o lab3\reserve-success.svg`
  та аналогічною командою для другого сценарію.

## Докази й рішення

- Зв'язок вимога → вихід → перевірка фіксується в [AUDIT.md](./AUDIT.md).
- Точка входу для рев'ю з SHA комітів — [DEFENSE.md](./DEFENSE.md).
- Ключове рішення про межі та відсутність `FINE` описане в
  [ADR/0001-reserve-book-sequence-boundary.md](./ADR/0001-reserve-book-sequence-boundary.md).


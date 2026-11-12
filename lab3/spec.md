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
  та activation bars. `UI` і `ReservationService` активні до й після `alt`.
- У позитивній гілці `createActiveReservation()` викликається тільки після
  успішних `validateSession()`, `getMemberStatus()` і
  `findAvailableBook()`. Mermaid-код має містити явну примітку про цю
  persistence-інваріанту.
- У гілці `outstanding fine exists` не викликаються ні `BookRepository`, ні
  `ReservationRepository`; Mermaid-код має містити явну примітку про відсутність
  persistence-виклику.
- `MEMBER`, `BOOK` і `RESERVATION` узгоджені з ER-моделлю Lab 1:
  `createActiveReservation(memberId, bookId)` створює активний запис із двома
  FK. `FINE` не додається, бо його немає в ER-моделі.
- `spec.md` і `DEFENSE.md` мають бути стислими; повні дослівні
  промпти та студентський фідбек зберігаються в `ai/`.
- Рендер відтворюється командою:
  `npx --yes @mermaid-js/mermaid-cli -i lab3\reserve-success.mmd -o lab3\reserve-success.svg`
  та аналогічною командою для другого сценарію.

## Докази й рішення

- Зв'язок вимога → вихід → перевірка зафіксовано нижче в розділі «Аудит та трасування».
- Точка входу для рев'ю з SHA комітів — [DEFENSE.md](./DEFENSE.md).
- Ключове рішення про межі та відсутність `FINE` описане в
  [ADR/0001-reserve-book-sequence-boundary.md](./ADR/0001-reserve-book-sequence-boundary.md).

## Аудит та трасування

| Перевірка | Доказ |
|---|---|
| `Member` / `MemberRepository` відповідають `MEMBER` | `memberId` проходить до `createActiveReservation()` |
| `BookRepository` відповідає `BOOK` | `findAvailableBook(bookId)` повертає доступний `BOOK` |
| `ReservationRepository` відповідає `RESERVATION` | створюється `status = active` з `memberId` і `bookId` |
| Сценарій 3 | доступна `BOOK` → `createActiveReservation()` → підтвердження |
| Сценарій 4 | `outstandingFine` → `rejectReservation()` → сума показана читачу |

`AuthService` і `ReservationService` є компонентами поведінкової моделі, а не
новими таблицями. `BOOK_COPY`, `LOAN`, `AUTHOR`, `CATEGORY` та `EmailService`
поза межами обраного UC.


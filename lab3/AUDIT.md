# Аудит Lab 3

## Трасування до ER-моделі й Gherkin

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

## Топ-3 розбіжності

1. **Неповний вхід:** первинний spec не містив повного acceptance-контракту.
   Критерії, межі й команда рендера додані до `spec.md` у коміті
   [`1775d43`](https://github.com/Mirroxyz/KPI/commit/1775d43).
2. **Неявна гарантія запису:** діаграми не підкреслювали момент persistence.
   Додано примітку після `createActiveReservation()` та примітку про відсутність
   виклику repository у штрафній гілці в коміті
   [`78dca1f`](https://github.com/Mirroxyz/KPI/commit/78dca1f).
3. **Відсутній структурований AI-слід:** prompt history був лише одним
   підсумковим файлом. Додано дослівний запит і студентський review feedback у
   [`ai/`](./ai), а ADR і defense прив'язали їх до комітів у `1775d43`.

## Перевірка activation bars

`Reservation UI` і `ReservationService` активуються до `alt` і деактивуються
після нього. `MemberRepository`, `BookRepository` та
`ReservationRepository` мають вкладені activation bars лише на час власних
викликів. У негативній гілці `reserve-unpaid-fine.mmd` немає виклику
`createActiveReservation()`.


# Лабораторна робота №3 - Sequence Diagram

## Обраний Use Case

**UC: Reserve Book** з Лабораторної роботи №2.

Актор `Member` створює бронювання бібліографічної сутності `BOOK` у вже
авторизованій сесії. Система перевіряє право на бронювання, зокрема відсутність
несплаченого штрафу, і створює `RESERVATION`, пов'язану з `MEMBER` та `BOOK`.

## Учасники

| Учасник | Роль у взаємодії | Відповідність попереднім роботам |
|---|---|---|
| `Member` | ініціює бронювання | актор `Member`, сутність `MEMBER` |
| `Reservation UI` | приймає запит і показує результат | компонент межі системи |
| `AuthService` | перевіряє авторизовану сесію | include `Authenticate` |
| `ReservationService` | координує перевірки й створення бронювання | реалізує UC `Reserve Book` |
| `MemberRepository` | читає стан читача і штраф | `MEMBER` |
| `BookRepository` | перевіряє наявність та ідентифікує книжку | `BOOK` |
| `ReservationRepository` | зберігає активне бронювання | `RESERVATION` |

`Fine` не є сутністю ER-моделі Лабораторної роботи №1. Тому його стан читається
через `MemberRepository` як поточний атрибут/результат політики доступу, а не
додається як новий учасник або таблиця.

## Сценарій 3 - успішне бронювання

Джерело: Gherkin-сценарій `Member reserves an available book` з Лабораторної
роботи №2.

1. `Member` надсилає `placeReservation(bookId)`.
2. UI передає запит до `ReservationService`.
3. Сервіс перевіряє авторизовану сесію через `AuthService`.
4. Сервіс читає стан `Member` і перевіряє відсутність несплаченого штрафу.
5. Сервіс читає `BOOK` і перевіряє доступність для бронювання.
6. За успішної перевірки створюється активний `RESERVATION` для `Member` і `BOOK`.
7. Система повертає підтвердження, а UI показує ідентифікатор бронювання.

## Сценарій 4 - блокування через несплачений штраф

Джерело: Gherkin-сценарій `Member with an unpaid fine cannot reserve a book` з
Лабораторної роботи №2.

1. `Member` надсилає спробу `placeReservation(bookId)`.
2. UI передає запит до `ReservationService`.
3. Сервіс перевіряє авторизовану сесію.
4. Сервіс читає стан `Member` і виявляє несплачений штраф.
5. Система не викликає створення `RESERVATION`, відхиляє запит і повертає суму
   заборгованості.
6. UI показує повідомлення про відмову.

## Трасування

| Крок sequence-діаграми | Gherkin | Вимога |
|---|---|---|
| `submitReservation()` / `placeReservation()` | When | REQ-02, REQ-03 |
| `validateSession()` | Given authorized session | REQ-01 |
| `getMemberStatus()` | Given unpaid fine | REQ-03 |
| `findAvailableBook()` | Given selected BOOK is available | REQ-02 |
| `createActiveReservation()` | Then active reservation is created | REQ-02 |
| `rejectReservation()` і `displayOutstandingFine()` | Then rejected + amount displayed | REQ-03 |

# Лабораторна робота №2 - Структуровані вимоги

## Межі системи

Система міської бібліотеки охоплює пошук книжок, бронювання, автентифікацію, видачу та повернення фізичних примірників, розрахунок штрафів і надсилання сповіщень. Закупівля нових книжок, бухгалтерський облік і побудова статистичних звітів не входять до поточного обсягу.

## Актори

- `User` - узагальнений користувач системи.
- `Member` - читач, який шукає, бронює, отримує та повертає книжки.
- `Librarian` - бібліотекар, який реєструє видачу, повернення та виконання бронювань.
- `EmailService` - зовнішня система, що доставляє email-сповіщення.

Узагальнення акторів: `Member` і `Librarian` успадковують спільну поведінку `User`.

## EARS-вимоги

- **REQ-01:** КОЛИ `Member` вводить коректні облікові дані, СИСТЕМА ПОВИННА автентифікувати `Member` і розпочати авторизовану сесію.
- **REQ-02:** КОЛИ автентифікований `Member` бронює доступну `BOOK`, СИСТЕМА ПОВИННА створити активне бронювання та пов'язати його з цим `Member` і `BOOK`.
- **REQ-03:** ПОКИ `Member` має несплачений штраф, СИСТЕМА ПОВИННА забороняти цьому `Member` створювати нове бронювання та показувати суму заборгованості.
- **REQ-04:** КОЛИ `Librarian` реєструє повернення `BOOK_COPY` після завершення строку, СИСТЕМА ПОВИННА розрахувати штраф за тривалістю прострочення та пов'язати його з відповідним `LOAN`.
- **REQ-05:** КОЛИ бронювання набуває статусу виконаного, СИСТЕМА ПОВИННА надіслати `Member` email-сповіщення через `EmailService` з назвою книжки та кінцевим строком отримання.
- **REQ-06:** ПОКИ `BOOK_COPY` має активний `LOAN`, СИСТЕМА ПОВИННА забороняти іншому `Member` отримувати цей примірник.
- **REQ-07:** ПОКИ настає кінець місяця, СИСТЕМА ПОВИННА сформувати для бібліотекаря статистичний звіт про всі видачі.

## User Stories

### US-01 - Автентифікація читача

Як `Member`, я хочу увійти за допомогою своїх облікових даних, щоб користуватися захищеними сервісами бібліотеки.

### US-02 - Бронювання доступної книжки

Як `Member`, я хочу забронювати доступну книжку, щоб забрати її пізніше.

### US-03 - Видача та повернення примірника

Як `Librarian`, я хочу реєструвати видачу та повернення фізичного примірника, щоб облік фонду й історія видач залишалися точними.

### US-04 - Обробка прострочення

Як `Member`, я хочу бачити штраф за несвоєчасне повернення, щоб розуміти, яку суму потрібно сплатити перед новим бронюванням.

### US-05 - Сповіщення про готове бронювання

Як `Member`, я хочу отримувати email, коли заброньована книжка готова, щоб знати, коли та де її забрати.

## Gherkin-сценарії

### Сценарій 1 - Успішне бронювання

```gherkin
Feature: Book reservation

  Scenario: Member reserves an available book
    Given a Member has an authorized session
    And the selected BOOK is available for reservation
    When the Member places a reservation
    Then the system creates an active reservation for that Member and BOOK
```

### Сценарій 2 - Блокування бронювання через штраф

```gherkin
Feature: Reservation eligibility

  Scenario: Member with an unpaid fine cannot reserve a book
    Given a Member has an authorized session
    And the Member has an unpaid fine
    When the Member tries to reserve an available BOOK
    Then the system rejects the reservation
    And the system displays the outstanding fine amount
```

### Сценарій 3 - Повернення із простроченням

```gherkin
Feature: Loan return

  Scenario: Librarian records a late return
    Given a BOOK_COPY has an active LOAN past its due date
    When the Librarian records the return
    Then the system marks the LOAN as returned
    And the system calculates and attaches a fine to the LOAN
```

### Сценарій 4 - Сповіщення про виконане бронювання

```gherkin
Feature: Reservation notification

  Scenario: Email is sent when a reservation is fulfilled
    Given a Member has an active reservation for a BOOK
    When the Librarian marks the reservation as fulfilled
    Then the system sends an email through EmailService
    And the email contains the book title and collection deadline
```

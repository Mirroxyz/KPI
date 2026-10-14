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

- **REQ-01:** КОЛИ `User` вводить коректні облікові дані, СИСТЕМА ПОВИННА автентифікувати `User` і розпочати авторизовану сесію.
- **REQ-02:** КОЛИ автентифікований `Member` бронює доступну `BOOK`, СИСТЕМА ПОВИННА створити активне бронювання та пов'язати його з цим `Member` і `BOOK`.
- **REQ-03:** ПОКИ `Member` має несплачений штраф, СИСТЕМА ПОВИННА забороняти цьому `Member` створювати нове бронювання та показувати суму заборгованості.
- **REQ-04:** КОЛИ `Librarian` реєструє повернення `BOOK_COPY` після завершення строку, СИСТЕМА ПОВИННА розрахувати штраф за тривалістю прострочення та пов'язати його з відповідним `LOAN`.
- **REQ-05:** КОЛИ бронювання набуває статусу виконаного, СИСТЕМА ПОВИННА надіслати `Member` email-сповіщення через `EmailService` з назвою книжки та кінцевим строком отримання.
- **REQ-06:** ПОКИ `BOOK_COPY` має активний `LOAN`, СИСТЕМА ПОВИННА забороняти `Librarian` реєструвати нову видачу цього примірника.
- **REQ-07:** ПОКИ настає кінець місяця, СИСТЕМА ПОВИННА сформувати для бібліотекаря статистичний звіт про всі видачі.
- **REQ-08:** КОЛИ `User` вводить критерій пошуку, СИСТЕМА ПОВИННА показати відповідні доступні записи каталогу `BOOK`.

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

### US-06 - Пошук у каталозі

Як `User`, я хочу шукати книжки за критеріями, щоб знаходити потрібні записи каталогу.

### US-07 - Оплата штрафу

Як `Member`, я хочу сплатити нарахований штраф, щоб відновити можливість користуватися бронюванням.

## Gherkin-сценарії

### Сценарій 1 - Автентифікація користувача

```gherkin
Feature: Authentication

  Scenario: User starts an authorized session
    Given a registered User has valid credentials
    When the User submits the credentials
    Then the system authenticates the User
    And the system starts an authorized session
```

### Сценарій 2 - Пошук у каталозі

```gherkin
Feature: Catalog search

  Scenario: User searches the catalog
    Given the catalog contains books matching a search criterion
    When the User enters the search criterion
    Then the system displays matching BOOK records
    And the results identify available copies when present
```

### Сценарій 3 - Успішне бронювання

```gherkin
Feature: Book reservation

  Scenario: Member reserves an available book
    Given a Member has an authorized session
    And the selected BOOK is available for reservation
    When the Member places a reservation
    Then the system creates an active reservation for that Member and BOOK
```

### Сценарій 4 - Блокування бронювання через штраф

```gherkin
Feature: Reservation eligibility

  Scenario: Member with an unpaid fine cannot reserve a book
    Given a Member has an authorized session
    And the Member has an unpaid fine
    When the Member tries to reserve an available BOOK
    Then the system rejects the reservation
    And the system displays the outstanding fine amount
```

### Сценарій 5 - Повернення без прострочення

```gherkin
Feature: Loan return

  Scenario: Librarian records an on-time return
    Given a BOOK_COPY has an active LOAN before its due date
    When the Librarian records the return
    Then the system marks the LOAN as returned
    And the system does not create a fine
```

### Сценарій 6 - Блокування повторної видачі

```gherkin
Feature: Loan eligibility

  Scenario: Librarian cannot issue a copy with an active loan
    Given a BOOK_COPY has an active LOAN
    When the Librarian tries to register a new loan for that BOOK_COPY
    Then the system rejects the new loan
    And the system keeps the active LOAN unchanged
```

### Сценарій 7 - Повернення із простроченням

```gherkin
Feature: Loan return

  Scenario: Librarian records a late return
    Given a BOOK_COPY has an active LOAN past its due date
    When the Librarian records the return
    Then the system marks the LOAN as returned
    And the system calculates a fine of 50 UAH per overdue day
    And the system attaches the calculated fine to the LOAN
```

### Сценарій 8 - Оплата штрафу

```gherkin
Feature: Fine payment

  Scenario: Member pays an outstanding fine
    Given a Member has an outstanding fine
    When the Member pays the fine
    Then the system marks the fine as paid
    And the system allows the Member to create a reservation
```

### Сценарій 9 - Сповіщення про виконане бронювання

```gherkin
Feature: Reservation notification

  Scenario: Email is sent when a reservation is fulfilled
    Given a Member has an active reservation for a BOOK
    When the Librarian marks the reservation as fulfilled
    Then the system sends an email through EmailService
    And the email contains the book title and collection deadline
```

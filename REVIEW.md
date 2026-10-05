
## Ресурсы и URL

- [x] Path — существительные, множественное число, kebab-case: `/books`, `/readers`, `/loans`.
- [x] Глагольные URL только осознанные: `POST /loans/{id}/return`.
- [x] id в path (`/books/{id}`), фильтры в query (`?author=`, `?status=overdue`).
- [x] Версия в `servers`: `https://api.library.example.com/v1`.

## Методы и коды

- [x] POST создания → 201 + Location (`POST /books`, `POST /readers`, `POST /loans`).
- [x] DELETE → 204 без тела.
- [x] Пустой список → 200 + `data: []`, не 404.
- [x] Бизнес-конфликт → 409 (`available_copies = 0`, лимит выдач).
- [x] Валидация → 422 (`due_date` в прошлом, пустой title).

## Схемы

- [x] У каждого request body — отдельная схема: `CreateBookRequest`, `UpdateBookRequest`, `CreateReaderRequest`, `UpdateReaderRequest`, `CreateLoanRequest`.
- [x] У каждого успешного ответа — схема: `Book`, `Reader`, `Loan`, `BookList`, `ReaderList`, `LoanList`.
- [x] `required`, `enum`, `format`, `nullable`, `minimum`, `minLength` описаны явно.
- [x] `additionalProperties: false` в request-схемах — серверные поля не принимаем.
- [x] `available_copies` помечено `readOnly: true`.
- [x] `returned_at` помечено `nullable: true`.

## Ошибки

- [x] Единый `ErrorResponse` со всеми кодами: `INVALID_REQUEST`, `NOT_FOUND`, `VALIDATION_ERROR`, `CONFLICT`, `BUSINESS_RULE_VIOLATION`, `IDEMPOTENCY_MISMATCH`.
- [x] Для 422 есть example с массивом `details`.
- [x] `request_id` в каждой ошибке — required.
- [x] 400 и 422 разделены: 400 — битый запрос, 422 — семантика.

## Списки

- [x] Фильтры: `author`, `available` (книги), `reader_id`, `book_id`, `status` (выдачи).
- [x] `status=overdue` описан в параметре и в description операции.
- [x] Сортировка `sort=-field` — общий параметр.
- [x] Пагинация `offset`/`limit`, `limit` имеет `default: 20` и `maximum: 100`.
- [x] Обёртка `{ data, pagination }` на всех коллекциях.

## Документация

- [x] `tags` — Books, Readers, Loans.
- [x] У ключевых операций — `summary` и `description`.
- [x] Минимум 3 examples: успех (`GET /books`, `POST /loans`),
      ошибка валидации (`POST /books`, 422), бизнес-конфликт (`POST /loans`, 409).
- [x] Общие схемы подключены через `$ref`, без копипасты.
- [x] Файл валидируется без ошибок (проверено через `npx @redocly/cli lint`).

## Бизнес-правила

- [x] Выдача только при `available_copies > 0` → 409 `BUSINESS_RULE_VIOLATION`.
- [x] Не более 5 активных выдач → 409 `BUSINESS_RULE_VIOLATION`.
- [x] Возврат через `POST /loans/{id}/return`, не DELETE.
- [x] `GET /loans?status=overdue` описан.
- [x] Удаление книги/читателя с активными выдачами → 409.
- [x] `due_date` в будущем → 422.
- [x] `available_copies` вычисляется сервером (`readOnly`).

## Идемпотентность

- [x] `Idempotency-Key` required на `POST /loans`.
- [x] Описан replay при повторе с тем же телом.
- [x] Описан 409 `IDEMPOTENCY_MISMATCH` при другом теле.
- [x] Описаны конкурентные повторы.
- [x] Указан TTL — 24 часа.
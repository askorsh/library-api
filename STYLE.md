# STYLE.md — правила API «Библиотека»

1. **URL — существительные во множественном числе, kebab-case.**
   `/books`, `/readers`, `/loans`. Действия вне CRUD — осознанные
   глаголы только там, где нельзя обойтись: `/loans/{id}/return`.

2. **Идентификатор ресурса — в path, фильтры — в query.**
   `GET /books/15` — конкретная книга. `GET /books?author=Булгаков` —
   фильтр по коллекции.

3. **Версия API — в servers как префикс `/v1`.**
   Все endpoint публикуются под `https://api.library.example.com/v1`.

4. **JSON — snake_case, URL — kebab-case.**
   `total_copies`, `available_copies`, `due_date` в телах;
   `/order-items` в URL.

5. **Один формат ошибки на все endpoint — `ErrorResponse`.**
   Поля: `error.code`, `error.message`, `error.details[]`, `error.request_id`.
   Один `$ref` на схему, никаких копипаст.

6. **HTTP-коды отражают смысл.**
   201 на создание, 204 на удаление, 404 — только для конкретного
   ресурса, пустой список — 200 + `[]`, валидация — 422,
   бизнес-конфликт — 409.

7. **Пагинация — `offset`/`limit` с дефолтами и максимумом.**
   `limit` по умолчанию 20, максимум 100. Метаданные — в объекте
   `pagination` внутри ответа.

8. **Списки возвращаются в обёртке `{ data, pagination }`.**
   «Голый» массив запрещён — он не оставляет места для метаданных.

9. **Request и response — разные схемы.**
   `CreateBookRequest` для POST, `UpdateBookRequest` для PATCH,
   `Book` — только в ответах. Серверные поля (`id`, `available_copies`)
   в request не принимаем: `additionalProperties: false`.

10. **Идемпотентность опасных POST — через `Idempotency-Key`.**
    Обязателен на `POST /loans`. TTL 24 часа, повтор с тем же
    телом — replay, другое тело — 409 `IDEMPOTENCY_MISMATCH`.
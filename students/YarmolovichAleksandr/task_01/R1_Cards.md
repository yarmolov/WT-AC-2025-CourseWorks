# Вариант 8 — Карточки (обязательные поля и валидации)

1.Карточка пользователя (User)

- id: UUID, автогенерация
- username: string, не пустое, мин. 3 символа, только буквы, цифры, ошибка: «Введите корректное имя пользователя.»
- email: string, формат email, уникальное ошибка: «Введите правильный email.»
- password_hash: строка (хранится хэш)
- role: enum [user, moderator, admin]

2.Карточка категории (Category)

- id: UUID
- name: string, не пустое → «Введите название категории.»
- parent_id: reference Category.id (опционально)

3.Карточка объявления (Ad)

- id: UUID
- author_id: reference User.id, обязательное → «Укажите автора объявления.»
- category_id: eference Category.id, обязательное
- title: string не пустое → «Введите заголовок.», максимум 120 символов
- description: string, минимум 10 символов
- price: number, ≥ 0, ошибка: «Цена должна быть положительным числом.»
- location: string (опционально)
- created_at: datetime
- status: enum [active, paused, archived, banned], default: active

4.Карточка медиа (Media)

- id: UUID
- ad_id: reference Ad.id, обязательное
- url: string, обязательное → «Ошибка загрузки изображения.»
- type: enum [image, video] (опционально)

5.Карточка чата (Conversation)

- id: UUID
- ad_id: reference Ad.id
- user1_id: reference User.id (владелец)
- user2_id: reference User.id (покупатель)
- ограничение: user1_id ≠ user2_id

6.Карточка сообщения (Message)

- id: UUID
- conversation_id: reference Conversation.id
- author_id: reference User.id
- text: string, не пустое, макс. 2000 символов
- created_at: datetime

7.Карточка жалобы (Report)

id: UUID
ad_id: reference Ad.id
reporter_id: reference User.id
reason: string, не пустое, макс. 500 символов
status: enum [new, reviewing, resolved]
created_at: datetime

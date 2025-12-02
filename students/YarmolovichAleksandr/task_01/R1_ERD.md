# Вариант 8 — ERD (диаграмма сущностей) — Объявки «Бери, пока горячее»

Файл содержит: 1) mermaid-диаграмму ERD; 2) ASCII-эскиз; 3) минимальный SQL DDL-скетч для создания таблиц.

## Mermaid ERD

```mermaid
erDiagram
    USER ||--o{ AD : owns
    CATEGORY ||--o{ AD : contains
    AD ||--o{ MEDIA : has
    AD ||--o{ REPORT : receives
    USER ||--o{ CONVERSATION : participates
    CONVERSATION ||--o{ MESSAGE : includes

    USER {
        uuid id PK
        string username
        string email
        string password_hash
        string role
    }

    CATEGORY {
        uuid id PK
        string name
        uuid parent_id FK
    }

    AD {
        uuid id PK
        uuid author_id FK
        uuid category_id FK
        string title
        string description
        numeric price
        string status
        datetime created_at
    }

    MEDIA {
        uuid id PK
        uuid ad_id FK
        string url
        string type
    }

    CONVERSATION {
        uuid id PK
        uuid ad_id FK
        uuid user1_id FK
        uuid user2_id FK
    }

    MESSAGE {
        uuid id PK
        uuid conversation_id FK
        uuid author_id FK
        string text
        datetime created_at
    }

    REPORT {
        uuid id PK
        uuid ad_id FK
        uuid reporter_id FK
        string reason
        string status
        datetime created_at
    }
```

## ASCII-эскиз

```
User 1---* Ad 1---* Media
             \
              \---* Report

Ad 1---* Conversation *---1 User
Conversation 1---* Message

Category 1---* Ad
```

## Минимальный SQL DDL (пример, PostgreSQL)

```sql
CREATE TABLE users (
    id UUID PRIMARY KEY,
    username TEXT NOT NULL UNIQUE,
    email TEXT NOT NULL UNIQUE,
    password_hash TEXT NOT NULL,
    role TEXT CHECK(role IN ('user','moderator','admin'))
);

CREATE TABLE categories (
    id UUID PRIMARY KEY,
    name TEXT NOT NULL,
    parent_id UUID REFERENCES categories(id)
);

CREATE TABLE ads (
    id UUID PRIMARY KEY,
    author_id UUID REFERENCES users(id),
    category_id UUID REFERENCES categories(id),
    title TEXT NOT NULL,
    description TEXT,
    price NUMERIC CHECK(price >= 0),
    status TEXT NOT NULL,
    created_at TIMESTAMP DEFAULT now()
);

CREATE TABLE media (
    id UUID PRIMARY KEY,
    ad_id UUID REFERENCES ads(id) ON DELETE CASCADE,
    url TEXT NOT NULL,
    type TEXT CHECK(type IN ('image','video'))
);

CREATE TABLE conversations (
    id UUID PRIMARY KEY,
    ad_id UUID REFERENCES ads(id),
    user1_id UUID REFERENCES users(id),
    user2_id UUID REFERENCES users(id)
);

CREATE TABLE messages (
    id UUID PRIMARY KEY,
    conversation_id UUID REFERENCES conversations(id) ON DELETE CASCADE,
    author_id UUID REFERENCES users(id),
    text TEXT NOT NULL,
    created_at TIMESTAMP DEFAULT now()
);

CREATE TABLE reports (
    id UUID PRIMARY KEY,
    ad_id UUID REFERENCES ads(id),
    reporter_id UUID REFERENCES users(id),
    reason TEXT NOT NULL,
    status TEXT,
    created_at TIMESTAMP DEFAULT now()
);
```

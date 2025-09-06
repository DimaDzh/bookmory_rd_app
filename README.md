# Bookmory - Book Management System

Веб-застосунок для управління персональною бібліотекою книг з можливістю пошуку, відстеження прогресу читання та організації книжкових колекцій.

## 🏗️ Архітектура

- **Frontend**: Next.js 15 з React 19, TypeScript, Tailwind CSS
- **Backend**: NestJS з TypeScript
- **База даних**: PostgreSQL з Prisma ORM
- **Кешування**: Redis
- **Балансування навантаження**: Nginx
- **Інтернаціоналізація**: Підтримка англійської та української мов

## 🚀 Локальний запуск

### Передумови

- Node.js 18+ та pnpm
- PostgreSQL 15+
- Redis 7+

## 📦 Структура проекту

```
├── client/              # Next.js фронтенд
│   ├── src/
│   │   ├── app/        # App Router сторінки
│   │   ├── components/ # React компоненти
│   │   ├── contexts/   # React контексти
│   │   ├── lib/        # Утиліти та API клієнт
│   │   └── types/      # TypeScript типи
│   └── dictionaries/   # Файли локалізації
├── server/             # NestJS бекенд
│   ├── src/
│   │   ├── auth/       # Модуль аутентифікації
│   │   ├── books/      # Модуль книг
│   │   ├── users/      # Модуль користувачів
│   │   └── user-books/ # Модуль користувацьких книг
│   └── prisma/         # Схема та міграції БД
└── load-balancer/      # Nginx конфігурація
```

## 🐳 Запуск через Docker Compose

```bash
git clone --recurse-submodules git@github.com:DimaDzh/bookmory_rd_app.git

cd bookmory_rd_app
```

### Продакшн збірка

```bash
# Запуск всіх сервісів
docker-compose -f docker-compose.prod.yml up -d

# Перегляд логів
docker-compose -f docker-compose.prod.yml logs -f

# Зупинка сервісів
docker-compose -f docker-compose.prod.yml down
```

Після запуску:

- Застосунок: http://localhost:8080
- API: http://localhost:8080/api

### Команди для роботи з контейнерами

```bash
# Пересбірка образів
docker-compose -f docker-compose.prod.yml build --no-cache

# Запуск міграцій в контейнері
docker-compose -f docker-compose.prod.yml exec server npx prisma migrate deploy

# Перегляд бази даних
docker-compose -f docker-compose.prod.yml exec db psql -U bookmory -d bookmory_db
```

## 🔧 Змінні середовища

### Backend

| Змінна           | Опис                          | За замовчуванням |
| ---------------- | ----------------------------- | ---------------- |
| `NODE_ENV`       | Режим роботи                  | `development`    |
| `PORT`           | Порт для сервера              | `3030`           |
| `DATABASE_URL`   | URL підключення до PostgreSQL | -                |
| `JWT_SECRET`     | Секретний ключ для JWT        | -                |
| `JWT_EXPIRES_IN` | Час дії токена                | `7d`             |
| `REDIS_URL`      | URL підключення до Redis      | -                |

### Frontend

| Змінна                | Опис           | За замовчуванням            |
| --------------------- | -------------- | --------------------------- |
| `NEXT_PUBLIC_API_URL` | URL бекенд API | `http://localhost:3030/api` |
| `NODE_ENV`            | Режим роботи   | `development`               |

## 📚 API Ендпоінти

### Аутентифікація

```
POST /api/auth/register     - Реєстрація користувача
POST /api/auth/login        - Вхід в систему
GET  /api/auth/profile      - Отримання профілю
GET  /api/auth/validate     - Валідація токена
```

### Користувачі

```
POST   /api/users           - Створення користувача (адмін)
GET    /api/users           - Список користувачів (адмін)
GET    /api/users/me        - Профіль поточного користувача
GET    /api/users/:id       - Користувач за ID
PATCH  /api/users/me        - Оновлення власного профілю
PATCH  /api/users/:id       - Оновлення користувача (адмін)
DELETE /api/users/:id       - Видалення користувача (адмін)
```

### Книги

```
GET /api/books/search       - Пошук книг через Google Books API
GET /api/books/:id          - Деталі книги за ID
```

### Користувацькі книги

```
POST   /api/user-books              - Додання книги до бібліотеки
GET    /api/user-books              - Список книг користувача
GET    /api/user-books/:bookId      - Деталі книги користувача
PATCH  /api/user-books/:bookId/progress  - Оновлення прогресу
DELETE /api/user-books/:bookId      - Видалення з бібліотеки
```

### Параметри запитів

#### GET /api/books/search

```
?q=<пошуковий запит>&limit=<кількість>&page=<сторінка>
```

#### GET /api/user-books

```
?status=<статус>&favorite=<true/false>&limit=<кількість>&page=<сторінка>
```

### Статуси книг

- `WANT_TO_READ` - Хочу прочитати
- `READING` - Читаю зараз
- `FINISHED` - Прочитано
- `PAUSED` - Відкладено
- `DNF` - Не дочитав

## 🔐 Аутентифікація

Система використовує JWT токени для аутентифікації. Токен передається через HTTP-заголовки:

```
Authorization: Bearer <jwt-token>
```

## 🛠️ Розробка

### Корисні команди

```bash
# Генерація Prisma клієнта
cd server && npx prisma generate

# Створення міграції
cd server && npx prisma migrate dev --name <назва-міграції>

# Перегляд бази даних
cd server && npx prisma studio

# Лінтинг
cd client && pnpm run lint
cd server && pnpm run lint

# Тестування
cd server && pnpm run test
```

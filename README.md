# Yeep - Библиотека для работы с PostgreSQL

Yeep - это простая и мощная библиотека для работы с PostgreSQL в Python, предоставляющая удобный интерфейс для выполнения CRUD операций, сложных запросов и управления схемой базы данных.

## Особенности

- 🚀 Простой и интуитивно понятный API
- 📦 Автоматическое создание таблиц на основе схемы
- 🔄 Поддержка транзакций
- 🔍 Гибкие условия поиска и фильтрации
- 🔗 Поддержка JOIN запросов
- 📊 Агрегатные функции
- 📈 Массовые операции вставки и обновления
- 🛡️ Защита от SQL-инъекций

## Установка

```bash
pip install yeep
```

## Быстрый старт

```python
from yeep import PostgresDB, Table, Column, ColumnType

# Подключение к базе данных
db = PostgresDB(
    database="your_db",
    user="your_user",
    password="your_password",
    host="localhost",
    port=5432
)

# Определение схемы таблицы
users_schema = [
    Column("id", ColumnType.SERIAL, primary_key=True),
    Column("name", ColumnType.VARCHAR, length=100, nullable=False),
    Column("email", ColumnType.VARCHAR, length=100, nullable=False, unique=True),
    Column("created_at", ColumnType.TIMESTAMP, default="CURRENT_TIMESTAMP")
]

# Создание таблицы
with db:
    users = Table(db, "users", users_schema)
    
    # Вставка данных
    new_user = users.insert({
        "name": "Иван Иванов",
        "email": "ivan@example.com"
    })
```

## Основные операции

### Создание записей

```python
# Одиночная вставка
user = users.insert({
    "name": "Иван Иванов",
    "email": "ivan@example.com"
})

# Массовая вставка
new_users = users.insert_many([
    {"name": "Петр Петров", "email": "petr@example.com"},
    {"name": "Анна Сидорова", "email": "anna@example.com"}
])
```

### Поиск записей

```python
from yeep import Operator, Condition

# Простой поиск
user = users.find_one({"email": "ivan@example.com"})

# Сложные условия
adult_users = users.find_many([
    Condition("age", Operator.GTE, 18),
    Condition("status", Operator.EQ, "active")
])

# Поиск с JOIN
user_orders = users.find_with_join(
    joins=[
        ("orders", "orders.user_id = users.id", JoinType.LEFT, "o"),
        ("products", "o.product_id = products.id", JoinType.INNER, "p")
    ],
    columns=["users.name", "p.name as product_name", "o.order_date"]
)
```

### Обновление записей

```python
# Обновление по условию
updated_users = users.update(
    {"status": "inactive"},
    {"last_login": None}
)

# Массовое обновление
users.update_many([
    {"id": 1, "points": 100},
    {"id": 2, "points": 150}
], key_column="id")
```

### Удаление записей

```python
deleted_users = users.delete({"status": "inactive"})
```

### Транзакции

```python
with users.transaction():
    user = users.insert({
        "name": "Новый пользователь",
        "email": "new@example.com"
    })
    
    orders.insert({
        "user_id": user["id"],
        "product_id": 1,
        "total_amount": 999.99
    })
```

### Агрегатные функции

```python
stats = orders.aggregate(
    aggregations=[
        ("SUM", "total_amount", "total_sales"),
        ("COUNT", "id", "order_count"),
        ("AVG", "total_amount", "avg_order")
    ],
    group_by=["user_id", "status"]
)
```

## Типы данных

Библиотека поддерживает следующие типы данных PostgreSQL:

- INTEGER
- BIGINT
- SERIAL
- BIGSERIAL
- TEXT
- VARCHAR
- BOOLEAN
- TIMESTAMP
- DATE
- NUMERIC
- JSONB
- UUID

## Операторы сравнения

- EQ (=)
- NEQ (!=)
- GT (>)
- GTE (>=)
- LT (<)
- LTE (<=)
- LIKE
- ILIKE
- IN
- NOT_IN
- BETWEEN
- IS_NULL
- IS_NOT_NULL

## Типы JOIN

- INNER JOIN
- LEFT JOIN
- RIGHT JOIN
- FULL JOIN

## Лицензия

MIT License

## Автор

[Tima Yeep]

## Вклад в проект

Мы приветствуем ваш вклад в развитие библиотеки! Пожалуйста, создавайте issue или pull request на GitHub. 
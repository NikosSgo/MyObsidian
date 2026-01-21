Alembic — это инструмент для управления миграциями баз данных в Python-проектах, работающих с SQLAlchemy. Он позволяет версионировать схему БД и применять изменения структуры базы данных контролируемым образом.


**Инициализация:**

```bash
alembic init alembic
```

**Cоздание миграции:**

```bash
# Пустая миграция
alembic revision -m "create users table"

# Автогенерация на основе моделей SQLAlchemy
alembic revision --autogenerate -m "add email column"
```

**Применение миграций:**

```bash
# Применить все миграции
alembic upgrade head

# Откатить одну миграцию назад
alembic downgrade -1

# Применить до конкретной ревизии
alembic upgrade abc123
```


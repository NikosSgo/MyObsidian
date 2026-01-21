SQLAlchemy Core — это нижний уровень библиотеки, предназначенный для **прямой работы с базой данных через SQL-подобные выражения**, без привязки к объектам Python или ORM. Оно обеспечивает полный контроль над схемой, запросами и транзакциями.

**Модули:**
- `sqlalchemy` — основной пакет.
- `sqlalchemy.sql` — выражения SQL, функции, операторы.
- `sqlalchemy.schema` — таблицы, колонки, индексы, constraints.
- `sqlalchemy.types` — типы данных (`Integer`, `String`, `Boolean` и т.д.).
**Основные классы:**
- `Engine` — объект, управляющий соединением и пулом.
- `Connection` — конкретное соединение с БД.
- `MetaData` — контейнер для всех таблиц схемы.
- `Table` — описание таблицы.
- `Column` — описание колонки.
- `ForeignKey`, `PrimaryKeyConstraint`, `UniqueConstraint`, `Index` — ограничения и индексы.
- `select()`, `insert()`, `update()`, `delete()` — генерация SQL-запросов.
- `func` — SQL-функции (`func.count()`, `func.now()` и т.д.).
- `text()` — для raw SQL.
- `bindparam()` — параметризованные запросы.
**Функции:**
- `create_engine(url, **kwargs)` — создание Engine.
- `Table(...)` — создание таблицы в Python.
- `select(...)` — SELECT выражения.
- `insert(...).values(...)` — вставка данных. 
- `update(...).where(...).values(...)` — обновление.
- `delete(...).where(...)` — удаление.
- `MetaData.create_all(engine)` — создание таблиц в БД.    

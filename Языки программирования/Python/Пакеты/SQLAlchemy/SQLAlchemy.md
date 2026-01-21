1. **Core (sqlalchemy core)** — работа с SQL на низком уровне
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

---

2. **ORM (sqlalchemy.orm)** — объектно-реляционное отображение

	**Модули:**
	- `sqlalchemy.orm` — основной модуль ORM.
	- `sqlalchemy.orm.session` — работа с сессиями.
	- `sqlalchemy.orm.mapper` — маппинг объектов на таблицы.
	- `sqlalchemy.orm.relationships` — связи между моделями.

	**Основные классы:**
	- `Session` — unit-of-work и управление транзакциями.
	- `sessionmaker` — фабрика для сессий.
	- `declarative_base` — базовый класс для декларативного определения моделей.
	- `relationship` — связи между моделями (`one-to-many`, `many-to-many`).
	- `scoped_session` — потокобезопасная сессия.
	- `Mapper` — объект, который связывает класс с таблицей.
	- `Query` — построение запросов ORM (deprecated в SQLAlchemy 2.0, заменено на `select`).
	- `InstrumentedAttribute` — описывает колонку класса с маппингом.

	**Функции и утилиты:**
	- `declarative_base()` — создание базового класса для моделей.
	- `relationship()` — описывает связи между объектами.
	- `session.add()`, `session.add_all()` — добавление объектов.
	- `session.commit()`, `session.rollback()` — завершение транзакции.
	- `session.flush()` — синхронизация состояния объектов с БД.
	- `session.query()` — построение ORM-запросов.
	- `joinedload()`, `subqueryload()`, `selectinload()` — стратегии загрузки связей.

---

3. **Типы данных (sqlalchemy.types)**
	- `Integer`, `BigInteger`, `SmallInteger`
	- `String`, `Text`, `Unicode`
	- `Boolean`
	- `Date`, `DateTime`, `Time`
	- `Float`, `Numeric`, `DECIMAL`
	- `JSON`, `JSONB` (Postgres)
	- `Enum`, `Interval`, `LargeBinary`
    

---

### 4. **Модули утилит**

- `sqlalchemy.ext` — расширения:
    
    - `declarative` — декларативные модели ORM.
        
    - `automap` — автоматический маппинг таблиц.
        
    - `hybrid` — hybrid attributes.
        
    - `asyncio` — поддержка async/await.
        
- `sqlalchemy.exc` — ошибки и исключения.
    
- `sqlalchemy.event` — события Engine, Session и ORM.
    

---

### 5. **Alembic (миграции)**

Хотя Alembic — отдельный пакет, тесно интегрируется с SQLAlchemy.

- `op.create_table()`, `op.add_column()`, `op.drop_column()` — операции миграции.
    
- `context.get_bind()` — получение Engine в миграции.
    
- Миграции хранятся как Python-скрипты с версиями.
    

---

Если хочешь, я могу нарисовать **схему модулей и классов SQLAlchemy в виде дерева**, чтобы было понятно, как Core, ORM, типы и сессии связаны между собой. Это сильно помогает при изучении.

Хочешь, чтобы я это сделал?
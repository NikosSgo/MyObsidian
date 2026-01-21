 **ORM (sqlalchemy.orm)** — объектно-реляционное отображение

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

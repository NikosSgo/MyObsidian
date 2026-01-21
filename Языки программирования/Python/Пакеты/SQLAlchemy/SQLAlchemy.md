**SQLAlchemy** — это [[Python]]-библиотека для работы с базами данных, объединяющая **низкоуровневый SQL-Toolkit (Core)** и **ORM (Object Relational Mapper)**. Core позволяет строить SQL-запросы и схемы таблиц программно, а ORM связывает Python-объекты с таблицами через Data Mapper. Библиотека даёт полный контроль над транзакциями, связями, загрузкой данных и хорошо интегрируется с миграциями через Alembic.

![[SQLAlchemy Core]]

---

![[SQLAlchemy ORM]]

---

![[SQLAlchemy Types]]

---

4. **Модули утилит**
	- `sqlalchemy.ext` — расширения:
	    - `declarative` — декларативные модели ORM.
	    - `automap` — автоматический маппинг таблиц.
	    - `hybrid` — hybrid attributes.
	    - `asyncio` — поддержка async/await.
	- `sqlalchemy.exc` — ошибки и исключения.
	- `sqlalchemy.event` — события Engine, Session и ORM.

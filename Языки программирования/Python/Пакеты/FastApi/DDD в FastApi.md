Структура FastApi проекта с DDD:

**src**:
- domain
	- entities
	- value_objects
	- aggregates
	- repositories (abstract)
	- events
	- exceptions
- application
- infrastructure
	- persistance
		- models
			- mixin
	- repositories (implementation)
- presentation
	- api

---

В DDD репозиторий — это:
> абстракция коллекции агрегатов, а не DAO и не ORM-обёртка.

Он существует для того, чтобы:
- доменная модель **не знала**, где и как она хранится;
- application слой работал с агрегатами, а не с таблицами;
- инфраструктура могла меняться (PostgreSQL → MongoDB → API) без изменения домена.


DirectorWorker выполняет задания от DirectorEngine.

Существуют следующие сущности:
- Проекты
	- Версия проекта
		- Файлы проекта
			- Состояние файла проекта
		- Патчи
			- Операции над файлами, которые меняют состояния файла 

Проект всегда имеет хотя бы одну версию, применение патча переводит систему к следующей версии. Патч содержит операции над состояниями файла.

Что делать, если операция должна объединять файлы? Состояние ссылается сразу на два файла или порождает новый файл на который может в дальнейшем ссылаться?

С чем операция может работать:
- С одним файлом. Тогда она переводит из одного состояние в другое файл
- С двумя файлами. Тогда она должна переводить состояния двух файлов. А если мы хотим дальше работать с объединённым как с целым, то как поступать? Создавать объединённое состояние или пораждать новый файл.... я не понимаю



Patch
```sql
id (PK)
project_id (FK → Project.id)
created_at
updated_at
```

PatchOperation
```sql
id (PK)
patch_id (FK → ProjectPatch.id)
type ENUM('cut_clip', 'glue_clips', …)
payload JSONB
created_at
updated_at
```

PatchOperationResource
```sql
id (PK)
operation_id (FK → PatchOperation.id)
file_id (FK → ProjectFile.id, nullable)  -- output файл, создаваемый операцией
resource_type ENUM('file', 'temp_clip', …)
status ENUM('pending', 'ready', 'failed')
created_at
updated_at
```


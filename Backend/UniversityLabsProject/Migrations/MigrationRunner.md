`MigrationRunner` - класс, который выполняет миграции.
Он принимает в конструкторе одно поле - это подключение к базе данных и следующие методы:
- private IServiceProvider CreateServices
- private void UpdateDatabase(IMigrationRunner runner)
- private void Migrate

Метод `CreateServices()`
- Настраивает [[CSharp.Net DI]]-контейнер 
- Регистрирует FluentMigrator с поддержкой PostgreSQL
- Указывает, что миграции нужно искать в текущей сборке (где находится `MigratorRunner`)
- Добавляет консольное логирование для вывода прогресса миграций
- Регистрирует кастомную реализацию таблицы версий [[VersionTable]]
- Возвращает настроенный [[IServiceProvider]]

Метод `Migrate()`
- Публичный метод, который запускает весь процесс миграции
- Создает DI-контейнер через `CreateServices()`
- Создает scope для правильного управления временем жизни scoped-сервисов
- Получает `IMigrationRunner` из контейнера и передает его в `UpdateDatabase()`

Метод `UpdateDatabase()`**
- Вызывает `MigrateUp()` для применения всех непримененных миграций    
- Для PostgreSQL дополнительно вызывает `ReloadTypes()` для обновления информации о типах
- Это важно при использовании пользовательских типов (enum, composite types)

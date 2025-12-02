**FluentMigrator** — это библиотека для управления миграциями базы данных в .NET. Она позволяет писать миграции на C# с использованием Fluent API, обеспечивая полный контроль над структурой БД и SQL. Подходит для проектов, где не используют Entity Framework Core, нужна поддержка разных СУБД (SQL Server, PostgreSQL, MySQL, Oracle, SQLite и др.) или важна высокая производительность.

### Что можно делать
- Создавать/изменять/удалять таблицы, столбцы, индексы, ограничения.
- Выполнять произвольный SQL или вставлять данные.
- Поддерживать миграции для нескольких БД в одном проекте.

### Как подключить
```bash
dotnet add package FluentMigrator
dotnet add package FluentMigrator.Runner
dotnet add package FluentMigrator.Runner.<YourDB> # например, .SqlServer или .Postgres
```

### Пример миграции
```csharp
[Migration(202504021234)]
public class AddUsersTable : Migration
{
    public override void Up()
    {
        Create.Table("Users")
            .WithColumn("Id").AsGuid().PrimaryKey()
            .WithColumn("Email").AsString(255).Unique()
            .WithColumn("CreatedAt").AsDateTime2().WithDefault(SystemMethods.CurrentDateTime);
    }

    public override void Down()
    {
        Delete.Table("Users");
    }
}
```

### Запуск миграций
1. **В коде**: Настройка через `IServiceCollection` и вызов `runner.MigrateUp()`.
2. **Через CLI**:
   ```bash
   dotnet tool install -g FluentMigrator.DotNet.Cli
   dotnet-fm migrate up -p <dbtype> -c "<connection-string>" -a "<path-to-dll>"
   ```
3. **В CI/CD**: Используй `dotnet-fm` в скриптах или Docker.
**Npgsql** — это официальный и самый популярный **.NET-драйвер для PostgreSQL**.  
Он разрабатывается командой PostgreSQL + сообществом уже больше 20 лет и с 2017 года является полностью открытым проектом под эгидой .NET Foundation.

### Зачем использовать именно Npgsql в 2025 году

- Полная поддержка всех современных возможностей PostgreSQL 13–17 (JSONB, Range, Hstore, PostGIS, TimescaleDB, Citus и т.д.).
- Поддержка .NET 6, 7, 8, 9 (и preview .NET 10).
- Отличная интеграция с Entity Framework Core (Npgsql.EntityFrameworkCore.PostgreSQL).
- Поддержка новых фич: Server-side connection pooling, Prepared statements, Logical replication, Physical replication, SCRAM-SHA-256, GSSAPI и т.д.
- Лучшая производительность среди всех .NET-драйверов для Postgres.

### Как подключить (2025)

```bash
# Базовый ADO.NET драйвер
dotnet add package Npgsql

# Для Entity Framework Core (самый частый случай)
dotnet add package Npgsql.EntityFrameworkCore.PostgreSQL

# Если нужны расширения (PostGIS, pg_crypto и т.д.)
dotnet add package Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite   # PostGIS
dotnet add package Npgsql.Json.NET                                      # System.Text.Json или Newtonsoft
```

### Примеры использования

#### 1. Простое подключение (ADO.NET)
```csharp
await using var conn = new NpgsqlConnection("Host=localhost;Database=mydb;Username=postgres;Password=123");
await conn.OpenAsync();

await using var cmd = new NpgsqlCommand("SELECT version()", conn);
var version = await cmd.ExecuteScalarAsync();
Console.WriteLine(version);
```

#### 2. Entity Framework Core (Program.cs в .NET 8+)
```csharp
builder.Services.AddDbContext<AppDbContext>(options =>
    options.UseNpgsql(builder.Configuration.GetConnectionString("Postgres"),
        npgsqlOptions => npgsqlOptions
            .MigrationsHistoryTable("__EFMigrationsHistory", "public")
            .UseNetTopologySuite()           // если нужен PostGIS
            .UseAdminDatabase("postgres"))); // для миграций
```

#### 3. Работа с JSONB
```csharp
// POCO
public class User
{
    public int Id { get; set; }
    public JsonDocument Metadata { get; set; }  // .NET 8+ предпочитает JsonDocument
}

// В OnModelCreating
modelBuilder.Entity<User>()
    .Property(u => u.Metadata)
    .HasColumnType("jsonb");
```

#### 4. Постоянные подготовленные запросы (огромный прирост производительности)
```csharp
// В UseNpgsql():
npgsqlOptions.EnablePreparedStatements(true);  // с Npgsql 8.0+ по умолчанию включено
```

### Полезные фичи 2024–2025 годов

| Фича                            | Как включить                                                  | Зачем нужна                              |
|---------------------------------|---------------------------------------------------------------|------------------------------------------|
| Connection Pooling (встроенный) | По умолчанию включён                                          | До 100k RPS на одном сервере             |
| Server-side pooling (pgbouncer) | `ServerCompatibilityMode=NoTypeLoading` + `Pooling=false`     | Для сотен тысяч соединений               |
| Batch-запросы                   | `NpgsqlBatch` вместо `NpgsqlCommand`                          | В 3–10 раз быстрее вставки/обновления    |
| Logical replication             | `new NpgsqlConnection(...).Open(); conn.StartReplication(...)` | CDC, кэши-инвалидация и т.д.             |
| Read-write splitting            | `Target Session Attributes=read-write,read-only` в строке     | Для реплик на чтение                     |
| Notifications (LISTEN/NOTIFY)   | `conn.Notification += ...`                                    | Pub/Sub без внешних брокеров             |

### Рекомендуемые строки подключения (2025)

```text
# Обычная
Host=pg-host;Database=mydb;Username=user;Password=pass;Port=5432

# С высокой производительностью
Host=pg-host;Database=mydb;Username=user;Password=pass;
Pooling=true;MaxPoolSize=100;Keepalive=60;PreparedStatements=true

# Для серверов с репликами
Host=primary,standby1,standby2;Database=mydb;Username=user;Password=pass;
Target Session Attributes=prefer-standby

# Для Yandex Cloud / Supabase / Neon
Host=xxx.supabase.co;Port=5432;Database=postgres;Username=postgres;Password=xxx;
SslMode=Require;Trust Server Certificate=true
```
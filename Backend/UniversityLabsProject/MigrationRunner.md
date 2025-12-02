MigrationRunner - класс, который выполняет миграции.
Он принимает в конструкторе одно поле - это подключение к базе данных и следующие методы:
- private IServiceProvider CreateServices
- private void UpdateDatabase(IMigrationRunner runner)
- private void Migrate

CreateServices
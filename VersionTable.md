VersionTable - содержит метаданные таблицы версий для [[FluentMigrator]]. По умолчанию [[FluentMigrator]] хранит информацию о том, какие миграции уже применены, в таблице VersionInfo. Но ты часто нужно переопределить название таблицы, схемы, колонок и поведение — именно для этого и служит интерфейс IVersionTableMetaData. В ней мы меняем названия и поля в таблице в формат snake_case и убираем primary key.

``` C#
using FluentMigrator.Runner.VersionTableInfo;  
public class VersionTable : IVersionTableMetaData  
{  
    public bool OwnsSchema => true;  
  
    public string SchemaName => "public";  
  
    public string TableName => "version_info";  
  
    public string ColumnName => "version";  
  
    public string DescriptionColumnName => "description";  
  
    public string AppliedOnColumnName => "applied_on";  
  
    public bool CreateWithPrimaryKey { get; } = false;  
  
    public string UniqueIndexName => "uc_version";  
}
```

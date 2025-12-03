Этот проект представляет собой WebApi приложение и содержит следующие элементы:
- [[BLL]]
- [[DAL]]
- [[Contollers]]
- [[Validators]]
- Program.cs
- Dockerfile
- Два конфигурационных файла: обычный и development 

В Program.cs происходит следующее:
- Создаём WebApplicationBuilder из AspNetCore
- Dapper передаётся настройка, которая позволяет мапить snake_case поля из таблиц в PascalCase классов
- Добавляется конфигурация подключения к базе данных
- Регистрируются следующие сервисы: UnitOfWork, OrderRepository, OrderItemRepository, OrderService, ValidatorVactory
- Передаём настройку с помощью, которой добавляются все валидаторы из сборки
- Добавляем все контроллеры
- Добавляем swagger
- Создаём инстанс приложения
- Добавляем MiddleWare для Swagger, SwaggerUi и всех контроллеров
- Запускаем проект [[Migrations]]
- И наконец запускаем само приложение


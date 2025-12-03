DI в .NET реализован через **Microsoft.Extensions.DependencyInjection** - легковесный, высокопроизводительный контейнер, который является частью .NET Core/5+. 
Это не полноценный IoC-контейнер в духе Autofac/Ninject, а минималистичная реализация паттерна Service Locator с поддержкой конструкторного инъектирования.


Ключевые типы и их ответственность 
- [[IServiceCollection]] - реестр зависимостей
- [[IServiceDescriptor]] - атомарная единица регистрации
- [[IServiceProvider]] - резолвер зависимостей
- IServiceScopeFactory - фабрика для создания областей
- IServiceScope - изолированный контекст для Scoped сервисов

Структура DI .NEt
Microsoft.Extensions.DependencyInjection
├── Abstractions/          ← Интерфейсы и абстракции
│   ├── IServiceProvider
│   ├── IServiceScope
│   └── ServiceLifetime
├── ServiceCollection.cs   ← Реализация IServiceCollection
├── ServiceProvider.cs     ← Реализация IServiceProvider (дефолтная)
├── ServiceProviderOptions.cs
└── ServiceLookup/         ← Внутренняя логика резолва

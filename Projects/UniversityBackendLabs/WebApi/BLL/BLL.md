Бизнес слой [[WebApi]] содержит бизнес модели и сервисы.

Имеются следующие модели:
- OrderUnit и OrderItemUnit, которые просто по началу являются копиями DTO из [[Models]] 
- QueryOrderItemsModel, который содержит следующие поля:
	- ids
	- CustomersIds
	- Page
	- PageSize
	- IncludeOrderItems

Также имеются следующие сервисы:
- [[OrderService]] - сервис заказов
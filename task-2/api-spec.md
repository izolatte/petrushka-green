## Запрос
`GET /api/v1/partner-stores`


## Описание
Получение списка партнёрских магазинов для экрана "Партнёры"


## Query параметры
- cityId (string/int) — чтобы показать партнёров в городе пользователя

или

- lat, lng - если у нас геопоиск/зоны доставки

## Пример запроса
``` 
GET /api/v1/partner-stores?cityId=spb HTTP/1.1
Host: api.petrushka-green.ru
Accept: application/json
Accept-Language: ru-RU
Authorization: Bearer <token> (если нужна)
``` 

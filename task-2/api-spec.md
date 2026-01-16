## Запрос
`GET /api/v1/partner-stores`


## Описание
Получение списка партнёрских магазинов для экрана "Партнёры"


## Query параметры
- cityId (string/int) — чтобы показать партнёров в городе пользователя

или

- lat, lng - если у нас геопоиск/зоны доставки

Параметры cityId и lat/lng взаимно исключающие (используется один из вариантов)

## Пример запроса
``` 
GET /api/v1/partner-stores?cityId=spb HTTP/1.1
Host: api.petrushka-green.ru
Accept: application/json
Accept-Language: ru-RU
Authorization: Bearer <token> (если нужна)
``` 
## Примечания
API возвращает список партнёрских магазинов с названием, логотипом, информацией о доставке и ссылкой для перехода на внешний ресурс. Поле delivery.type определяет шаблон отображения карточки. На фронте можно собирать текст из кусочков `delivery.title + delivery.dayLabel + delivery.slot.from + delivery.slot.to`  Они нужны для возможной сортировки и поиска.

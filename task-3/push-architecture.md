# Задание 3: Архитектура PUSH-уведомлений (верхний уровень)

## Пояснения
### Mobile App
- получает pushToken (FCM для Android / APNs для iOS)
- отправляет токен на бэк
- получает уведомления от провайдеров

### Device Token Store
Хранит токены устройств. Один пользователь может иметь несколько устройств, следовательно несколько токенов.

### Domain Services (Cart / Orders / Marketing)
Источник событий, от которых могут отправляться пуши.
Примеры событий:
- OrderCanceled (заказ отменен)
- OrderStatusChanged (статус заказа поменялся)
- CartAbandoned (корзина долгое время пустует)
- MarketingCampaignStarted (рекламная кампания)

### Event Bus
Шина событий между сервисами. Нужна, чтобы:
- не делать синхронные вызовы между сервисами
- проще масштабировать
- легче добавлять новые типы пушей без переписывания логики

### Scheduler (отложенные пуши)
Нужен для сценариев, где пуш приходит через время.
Например в случае с пустующей корзиной.

### Notification Service
Оркестратор пушей:
- принимает событие
- решает отправлять пуш или нет (например, учитывает настройки пользователя/категории пушей)
- формирует текст уведомления (может использовать шаблоны/локализацию)
- формирует задачу на отправку и кладёт её в очередь

### Send Queue
Очередь задач на отправку пушей.
Нужна для:
- устойчивости если много событий, чтобы не падать от нагрузки
- если провайдер (FCM/APNs) временно недоступен, чтобы повторить отправку позже

### Push Sender
Фактическая отправка уведомлений в FCM / APNs.
Также может:
- делать retry (повторные попытки)
- помечать токены невалидными (например, если провайдер говорит “token expired”)

```mermaid
flowchart LR
  App["Mobile App<br/>(iOS / Android)"]

  TokenStore["Device Token Store<br/>(хранение токенов устройств)"]

  Services["Domain Services<br/>(Cart / Orders / Marketing)"]
  Bus["Event Bus<br/>(Kafka / RabbitMQ)"]
  Scheduler["Scheduler<br/>(отложенные события)"]

  Notif["Notification Service<br/>(правила + текст пуша)"]
  Queue["Send Queue<br/>(очередь отправки)"]
  Sender["Push Sender"]
  Providers["PUSH Providers<br/>(FCM / APNs)"]

  App -->|"регистрация pushToken"| TokenStore

  Services -->|"события"| Bus
  Bus --> Notif
  Bus --> Scheduler
  Scheduler -->|"событие в нужный момент"| Notif

  Notif --> TokenStore
  Notif -->|"задача на отправку"| Queue
  Queue --> Sender
  Sender --> Providers
  Providers --> App


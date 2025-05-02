## NotificationsService


### Пример структуры, которое не обязывает вас в точности следовать ей.

```C#
/NotificationsService
│
├── API/                         // Веб-слой (REST API)
│   ├── Controllers/             // HTTP endpoints (например, для отправки/получения уведомлений)
│   └── Filters/                 // Кастомные фильтры (логирование, валидация и др.)
│
├── Application/                 // Бизнес-логика и интерфейсы
│   ├── Interfaces/              // Контракты для сервисов/репозиториев
│   ├── Services/                // Реализация логики отправки, хранения и получения уведомлений
│   └── DTOs/                    // DTO-объекты
│       ├── NotificationDto.cs
│       └── CreateNotificationDto.cs
│
├── Models/                      // Модели сущностей
│   └── Notification.cs
│
├── Infrastructure/             // Работа с БД и внешними сервисами
│   ├── Persistence/
│   │   ├── DbContexts/          // DbConnectionFactory или MySqlConnectionManager
│   │   └── Repositories/        // Репозиторий для Notification
│   ├── ExternalServices/        // gRPC-клиенты к другим микросервисам (например, UserService)
│   └── Configurations/          // Логирование, подключение к БД
│
├── Mappers/                     // Маппинг между DTO <-> Model
│   └── NotificationMapper.cs
│
├── Grpc/                        // gRPC сервер и клиент
│   ├── Protos/                  // .proto файлы
│   └── Services/                // Реализация gRPC
│
├── Database/                     // SQL скрипты (init.sql)
│   └── init.sql
│
├── Dockerfile
├── docker-compose.override.yml
├── NotificationsService.csproj
├── appsettings.json
└── Program.cs
```

### Пример модели Notification.cs

```C#
public class Notification
{
    public int Id { get; set; }
    public string Type { get; set; } = string.Empty; // info, warning, alert и т.п.
    public string Message { get; set; } = string.Empty;
    public string RecipientId { get; set; } = string.Empty; // userId
    public DateTime CreatedAt { get; set; } = DateTime.UtcNow;
    public bool IsRead { get; set; } = false;
}
```

## NotificationsService: детальная архитектура
### Основная задача: Управление и доставка уведомлений пользователям через разные каналы (email, push, SMS, in-app, Telegram и др.).

## 1. Основные функции
### 1.1. Типы уведомлений
#### Системные уведомления (регистрация, смена пароля, верификация).
#### Транзакционные (платежи, заказы, статусы документов).
#### Маркетинговые (акции, новости, персональные предложения).
#### Чат-бот (уведомления о новых сообщениях, событиях).
#### Административные (уведомления для админов о критических событиях).

### 1.2. Каналы доставки

#### Email (SMTP, SendGrid, Mailgun).
#### Push-уведомления (Firebase Cloud Messaging, Apple Push Notification Service).
#### SMS (Twilio, SMS.ru).
#### In-App (WebSocket/SignalR для реального времени).
#### Telegram/Bot (через Telegram Bot API).
#### Вебхуки (интеграция с внешними системами).


## 2. Внутренняя архитектура
### 2.1. Основные компоненты
    Компонент	                        Описание

#### API Gateway	↔	 REST/gRPC endpoints для приема запросов.
#### Notification Dispatcher	↔        Маршрутизация уведомлений по каналам.
#### Templates Engine	    ↔	    Генерация контента (шаблоны для email/SMS).
#### Delivery Providers	    ↔	    Адаптеры для внешних сервисов (SendGrid, Twilio и т.д.).
#### Retry & Dead Letter       ↔	      Queue	Повторная отправка при ошибках.
#### User Preferences	   ↔	     Настройки пользователей (каналы, время получения).


### 2.2. База данных (MySQL)
```SQL
Таблицы:
Notifications (id, type, user_id, status, created_at).
Templates (id, content, channel, language).
UserNotificationSettings (user_id, email_enabled, push_enabled и т.д.).
NotificationLogs (для аудита и аналитики).
```

### 3. Интеграция с другими сервисами

#### AuthService → верификация пользователя перед отправкой.
#### UserService → получение данных пользователя (email, phone).
#### ProductService → уведомления о заказах/статусах.
#### ChatBotService → отправка в Telegram/другие мессенджеры.
#### DocumentService → уведомления о готовности документов.


### 4. Технологии и инструменты
#### gRPC для быстрой коммуникации между сервисами.
#### Hangfire/Quartz для отложенных/периодических уведомлений.
#### RabbitMQ/Kafka для асинхронной обработки очередей.
#### Serilog + ELK для логирования.
#### Polly для retry-логики при ошибках доставки.

### 5. Примеры сценариев
Регистрация пользователя:
AuthService отправляет событие → NotificationsService генерирует email с подтверждением.

#### Заказ товара:
#### ProductService запрашивает уведомление → отправка SMS и email.

#### Чат-бот:
#### ChatBotService триггерит push-уведомление через FCM.

### 6. Дополнительные фичи
#### A/B-тестирование шаблонов уведомлений.
#### Персонализация (подстановка имени, истории заказов).
#### График отправки (не беспокоить ночью).
#### Веб-интерфейс для ручной отправки (админка).



## InvestorChatService


### 📁 Структура (пример):
```C#
/InvestorChatService
│
├── API/
│   ├── Controllers/
│   ├── Middleware/
│   └── Filters/
│
├── Application/
│   ├── Interfaces/
│   ├── Services/
│   └── DTOs/
│       ├── ChatMessageDto.cs
│       ├── CreateMessageDto.cs
│       └── ChatRoomDto.cs
│
├── Models/
│   ├── ChatMessage.cs
│   ├── ChatRoom.cs
│   └── ChatParticipant.cs
│
├── Infrastructure/
│   ├── Persistence/
│   │   ├── DbContexts/
│   │   └── Repositories/
│   ├── ExternalServices/
│   └── Configurations/
│
├── SignalR/
│   ├── Hubs/
│   │   └── ChatHub.cs
│   └── Events/
│
├── Database/
│   └── init.sql
│
│
├── Grpc/
│   ├── Services/
│   └── Protos/
│
├── Mappers/
├── Database/
├── Dockerfile
├── InvestorChatService.csproj
├── appsettings.json
└── Program.cs
```

### Технологии:
#### Backend: C#, .NET 8
#### Database: MySQL (отдельная БД на микросервис)
#### Communication:
#### REST (для внешнего взаимодействия)
#### gRPC (для связи с другими микросервисами, например UserService)
#### Real-time: SignalR (для чата в реальном времени)
#### Security: JWT + [Authorize]
#### Logging: Serilog
#### Validation: FluentValidation
#### Mapping: AutoMapper или ручные мапперы
#### Hosting: Docker + Docker Compose


### Модели:
```C#
public class ChatRoom
{
    public Guid Id { get; set; }
    public string Title { get; set; } = string.Empty;
    public List<ChatParticipant> Participants { get; set; } = new();
    public List<ChatMessage> Messages { get; set; } = new();
}

public class ChatParticipant
{
    public Guid Id { get; set; }
    public Guid UserId { get; set; } // Ссылка на UserService
    public Guid ChatRoomId { get; set; }
}

public class ChatMessage
{
    public Guid Id { get; set; }
    public Guid ChatRoomId { get; set; }
    public Guid SenderUserId { get; set; }
    public string Content { get; set; } = string.Empty;
    public DateTime SentAt { get; set; } = DateTime.UtcNow;
}
```

### Дополнительно:
#### Реализация SignalR Hub для отправки/получения сообщений в реальном времени.
#### gRPC-запрос к UserService по UserId, чтобы подтягивать имя/аватар.
#### ChatRoom может быть один-на-один (private) или групповой.
#### WebSocket можно fallback'ом заменить через SignalR + REST.

### Примерный use case:
#### Пользователь авторизуется через AuthService.
#### Его JWT проверяется через [Authorize].
#### Он отправляет сообщение → попадает в SignalR Hub → сохраняется в БД через Repository.
#### Через SignalR сообщение рассылается остальным участникам

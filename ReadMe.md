## Первый пример базовой структуры микросервисов на ADO.NET (C#)

```C#
/StartupHub
│
├── /services
│   ├── /UserService
│   │   ├── Controllers
│   │   │   └── UserController.cs
│   │   ├── DAL
│   │   │   ├── DbConnectionFactory.cs
│   │   │   └── UserRepository.cs
│   │   ├── BLL
│   │   │   └── UserService.cs
│   │   ├── Models
│   │   │   └── User.cs
│   │   ├── Program.cs
│   │   └── appsettings.json
│   │
│   ├── /ProductService
│   │   ├── Controllers
│   │   │   └── StartupController.cs
│   │   ├── DAL
│   │   │   └── StartupRepository.cs
│   │   ├── BLL
│   │   │   └── StartupService.cs
│   │   ├── Models
│   │   │   └── Startup.cs
│   │   └── Program.cs
│   │
│   ├── /MediaService
│   │   ├── Controllers
│   │   │   └── MediaController.cs
│   │   ├── DAL
│   │   │   └── MediaRepository.cs
│   │   ├── BLL
│   │   │   └── MediaService.cs
│   │   ├── Models
│   │   │   └── Media.cs
│   │   └── Program.cs
│   │
│   ├── /ChatBotService
│   │   ├── Controllers
│   │   │   └── ChatController.cs
│   │   ├── DAL
│   │   │   └── ChatRepository.cs
│   │   ├── BLL
│   │   │   └── ChatService.cs
│   │   ├── Models
│   │   │   └── ChatMessage.cs
│   │   └── Program.cs
│   │
│   ├── /DocumentService
│       ├── Controllers
│       │   └── DocumentController.cs
│       ├── DAL
│       │   └── DocumentRepository.cs
│       ├── BLL
│       │   └── DocumentService.cs
│       ├── Models
│       │   └── Document.cs
│       └── Program.cs
│
├── /shared
│   ├── DTOs
│   │   └── SharedModels.cs
│   ├── Interfaces
│   │   └── IRepository.cs
│   └── Utils
│       └── Extensions.cs
│
└── docker-compose.yml

// Каждый сервис использует чистый ADO.NET внутри репозиториев и передает данные в BLL.
// BLL реализует бизнес-логику, а контроллеры работают с REST API.
```
### Микросервисы должны быть изолированы, чтобы над ними могли работать разные команды независимо. Но папка shared здесь используется не для общего доступа к бизнес-логике или данным, а исключительно для вспомогательных вещей, которые можно переиспользовать без нарушения изоляции:

* DTOs: контракты обмена между сервисами — могут быть продублированы по месту или переиспользованы при согласованной версии.

* Interfaces: базовые абстракции вроде IRepository, если стилистика кода должна быть одинаковой.

* Utils: мелкие расширения или общие утилиты, не завязанные на конкретный сервис.

## Второй пример базовой структуры микросервисов где микросервисы полностью независимые на ADO.NET (C#)

```C#

/StartupHub
│
├── /UserService
│   ├── Controllers/
│   ├── DAL/
│   ├── BLL/
│   ├── Models/
│   ├── DTOs/
│   ├── Program.cs
│   ├── appsettings.json
│   └── UserService.csproj
│
├── /ProductService
│   ├── Controllers/
│   ├── DAL/
│   ├── BLL/
│   ├── Models/
│   ├── DTOs/
│   ├── Program.cs
│   ├── appsettings.json
│   └── ProductService.csproj
│
├── /MediaService
├── /ChatBotService
├── /DocumentService
│
└── docker-compose.yml

//Cтандартную структурf и именования проекта, которая будет более отражать практики и паттерны, применяемые в крупных проектах:

/StartupHub
│
├── /UserService
│   ├── /API                // Контроллеры и точки входа
│   ├── /Application        // Слой приложения, включает сервисы, хендлеры команд/запросов
│   ├── /Domain
│   │   ├── /Entities      // Сущности (модели)
│   │   ├── /ValueObjects  // Значимые объекты
│   │   └── /Aggregates    // Агрегаты (если применимо)
│   ├── /Infrastructure    // Реализация доступа к данным и других технических деталей
│   │   ├── /Persistence   // DAL, доступ к данным
│   │   ├── /Repositories  // Репозитории
│   │   └── /Migrations    // Миграции (если используется база данных)
│   ├── /DTOs              // Data Transfer Objects
│   ├── /Extensions        // Расширения для различных сущностей и функционала
│   ├── Program.cs
│   ├── appsettings.json
│   └── UserService.csproj

```

## Пример базовой структуры с моделями

```C#

/StartupHub
│
├── /services
│   ├── /UserService
│   │   ├── Models
│   │   │   ├── User.cs
│   │   │   ├── Team.cs
│   │   │   └── Note.cs
│   │   
│   ├── /ProductService
│   │   ├── Models
│   │   │   ├── Startup.cs
│   │   │   ├── Industry.cs
│   │   │   ├── BusinessModel.cs
│   │   │   ├── Geography.cs
│   │   │   ├── FundingRequest.cs
│   │   │   └── StartupTrack.cs
│   │   
│   ├── /MediaService
│   │   ├── Models
│   │   │   └── MediaFile.cs
│   │
│   ├── /ChatBotService
│   │   ├── Models
│   │   │   └── ChatQuery.cs
│   │
│   ├── /DocumentService
│   │   ├── Models
│   │   │   ├── Document.cs
│   │   │   └── NDAAgreement.cs
│
// MODELS

// UserService.Models
public class User
{
    public int Id { get; set; }
    public string Username { get; set; }
    public string Email { get; set; }
    public string Role { get; set; } // investor, startupOwner
    public List<Team>? TeamMembers { get; set; }
}

public class Team
{
    public int Id { get; set; }
    public int UserId { get; set; }
    public string Name { get; set; }
    public string Role { get; set; }
}

public class Note
{
    public int Id { get; set; }
    public int UserId { get; set; }
    public string Content { get; set; }
    public string? Transcription { get; set; }
    public DateTime CreatedAt { get; set; }
}

// ProductService.Models
public class Startup
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Description { get; set; }
    public string Stage { get; set; } // idea, prototype, MVP, scaling
    public string PhotoUrl { get; set; }
    public int IndustryId { get; set; }
    public int BusinessModelId { get; set; }
    public int GeographyId { get; set; }
    public decimal MarketVolume { get; set; }
    public int TeamRating { get; set; }
    public FundingRequest Funding { get; set; }
}

public class FundingRequest
{
    public int Id { get; set; }
    public int StartupId { get; set; }
    public decimal RequestedAmount { get; set; }
    public string Details { get; set; }
}

public class Industry
{
    public int Id { get; set; }
    public string Name { get; set; } // AI & ML, FinTech, etc.
}

public class BusinessModel
{
    public int Id { get; set; }
    public string Name { get; set; } // B2B, B2C, SaaS, etc.
}

public class Geography
{
    public int Id { get; set; }
    public string Region { get; set; }
}

public class StartupTrack
{
    public int Id { get; set; }
    public int UserId { get; set; }
    public int IndustryId { get; set; }
}

// MediaService.Models
public class MediaFile
{
    public int Id { get; set; }
    public string FileName { get; set; }
    public string Url { get; set; }
    public string FileType { get; set; } // jpg, mp4, etc.
    public int StartupId { get; set; }
}

// ChatBotService.Models
public class ChatQuery
{
    public int Id { get; set; }
    public int UserId { get; set; }
    public string Query { get; set; }
    public string? Response { get; set; }
    public DateTime AskedAt { get; set; }
}

// DocumentService.Models
public class Document
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Url { get; set; }
    public int StartupId { get; set; }
    public bool IsPrivate { get; set; }
}

public class NDAAgreement
{
    public int Id { get; set; }
    public int UserId { get; set; }
    public int StartupId { get; set; }
    public DateTime SignedAt { get; set; }
    public string SignatureCode { get; set; }
}


````
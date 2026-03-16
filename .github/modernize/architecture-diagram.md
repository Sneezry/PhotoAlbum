# Architecture Diagram

PhotoAlbum is an ASP.NET Core 9.0 Razor Pages application that provides photo gallery management with local file storage and SQL Server persistence.

## Application Architecture

```mermaid
flowchart TD
    subgraph Client["Client Layer"]
        Browser["Web Browser"]
    end
    subgraph App["Application Layer - ASP.NET Core 9.0"]
        Pages["Razor Pages"]
        Middleware["Middleware Pipeline"]
        Service["PhotoService"]
        ImageSharp["SixLabors.ImageSharp"]
    end
    subgraph Data["Data Layer"]
        EF["Entity Framework Core 9.0"]
        DB[("SQL Server LocalDB")]
        FS[("Local File System\nwwwroot/uploads")]
    end

    Browser -->|"HTTP/HTTPS requests"| Middleware
    Middleware -->|"routes"| Pages
    Pages -->|"delegates"| Service
    Service -->|"image processing"| ImageSharp
    Service -->|"CRUD operations"| EF
    EF -->|"SQL queries"| DB
    Service -->|"file read/write"| FS
```

## Component Relationships

```mermaid
flowchart LR
    subgraph Presentation["Presentation"]
        IndexPage["IndexPage"]
        DetailPage["DetailPage"]
        PhotoFilePage["PhotoFilePage"]
    end
    subgraph Business["Business Logic"]
        IPhotoSvc["IPhotoService"]
        PhotoSvc["PhotoService"]
    end
    subgraph DataAccess["Data Access"]
        DbCtx["PhotoAlbumContext"]
        PhotoEntity["Photo"]
        UploadResult["UploadResult"]
    end
    subgraph Infra["Infrastructure"]
        StaticFiles["StaticFiles Middleware"]
        FormOptions["FormOptions\n10MB limit"]
        Migrations["EF Migrations"]
    end

    IndexPage -->|"upload/list"| IPhotoSvc
    DetailPage -->|"get/delete"| IPhotoSvc
    PhotoFilePage -->|"get by id"| IPhotoSvc
    IPhotoSvc -->|"implemented by"| PhotoSvc
    PhotoSvc -->|"queries"| DbCtx
    PhotoSvc -->|"returns"| UploadResult
    DbCtx -->|"maps"| PhotoEntity
    StaticFiles -.->|"serves assets"| Presentation
    FormOptions -.->|"configures"| IndexPage
    Migrations -.->|"initializes"| DbCtx
```

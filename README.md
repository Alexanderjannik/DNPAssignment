# Forum Application — DNP Assignment

A forum application built incrementally across three layers as part of a Distributed and Network Programming (DNP) course. Each layer introduces a new tier while the core domain logic stays untouched — made possible by a generic `IRepository<T>` interface that all persistence implementations satisfy.

---

## Architecture

The solution is split into focused projects, each with a single responsibility:

```
DNPAssignment/
├── Entities/               # Domain models: User, Post, Comment, PostLike, CommentLike
├── RepositoryContracts/    # IRepository<T> interface — the persistence abstraction
├── ApiContracts/           # Shared DTOs between API and frontend
│
├── InMemoryRepositories/   # Phase 1: in-memory IRepository<T> implementation
├── FileRepository/         # Phase 2: JSON file-backed IRepository<T> implementation
├── EfcRepositories/        # Phase 3: EF Core + SQLite IRepository<T> implementation
│
├── CLI/                    # Console frontend (uses FileRepository)
├── WebAPI/                 # ASP.NET Core REST API (uses EfcRepositories)
└── ForumApp/               # Blazor Server frontend (consumes WebAPI via HTTP)
```

### The Repository Pattern in Practice

All three persistence implementations satisfy the same `IRepository<T>` interface. Swapping from JSON files to EF Core required no changes to the CLI or API logic — only the DI registration changed:

```csharp
// CLI — JSON file persistence
IRepository<Post> postRepository = new FileRepository<Post>("posts");

// WebAPI — EF Core persistence, registered via DI
builder.Services.AddScoped(typeof(IRepository<>), typeof(Repository<>));
```

---

## Stack

| Layer | Technology |
|---|---|
| Domain | C# (.NET 8), XML-documented entities |
| Persistence | In-memory / JSON files / EF Core + SQLite |
| API | ASP.NET Core Web API, custom exception middleware |
| Frontend | Blazor Server, HTTP service layer |
| Auth | Custom `AuthenticationStateProvider` (Blazor) |

---

## Features

- Users, posts, and comments with full CRUD
- Post and comment likes
- Filter posts by title or user
- Custom global exception handling middleware in the API
- Database seeding on startup
- Blazor frontend with server-side auth state
- HTTP service layer (`HttpPostService`, `HttpUserService`, `HttpCommentService`) abstracting all API calls

---

## Running Locally

**Prerequisites:** .NET 8 SDK

```bash
git clone https://github.com/Alexanderjannik/DNPAssignment.git
cd DNPAssignment
```

**Run the API:**
```bash
cd WebAPI
dotnet run
# API available at http://localhost:5264
```

**Run the Blazor frontend:**
```bash
cd ForumApp
dotnet run
```

**Run the CLI (file-backed):**
```bash
cd CLI
dotnet run
```

> **Note:** The SQLite connection string in `Program.cs` contains an absolute path and will need updating to your local environment before running the API.

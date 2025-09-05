## FinanceProject API (ASP.NET Core 9)

Backend REST API for managing stocks, user portfolios, and comments. It integrates with Financial Modeling Prep (FMP) to fetch stock profiles on demand, uses ASP.NET Core Identity with JWT for authentication, and Entity Framework Core (SQL Server) for persistence. Swagger is enabled for easy exploration.

### Contents
- **Tech stack**
- **Architecture**
- **Getting started**
- **Configuration**
- **Database & migrations**
- **Run & explore**
- **API overview**
- **Auth flow**
- **Request examples**
- **Project structure**
- **Troubleshooting**

## Tech stack
- **.NET**: ASP.NET Core 9 (Minimal hosting)
- **Auth**: ASP.NET Core Identity + JWT (Bearer)
- **Data**: Entity Framework Core (SQL Server)
- **Docs**: Swagger / OpenAPI
- **HTTP**: HttpClient for FMP integration

## Architecture
- Layered controllers → repositories → EF Core `ApplicationDBContext`
- Identity-backed `AppUser`; seeded roles: `Admin`, `User`
- JWT tokens issued by `TokenService` using Issuer/Audience/SigningKey from settings
- External stock lookup via `FMPService` when a symbol is missing locally

## Getting started
Prerequisites:
- .NET SDK 9.0+
- SQL Server (LocalDB/Express/Server)

Clone and restore:
```bash
git clone <your-repo-url>
cd FinanceProject/api
dotnet restore
```

## Configuration
Edit `appsettings.json` in `FinanceProject/api`:
- `ConnectionStrings:DefaultConnection`: point to your SQL Server
- `FMPKey`: your Financial Modeling Prep API key
- `JWT:Issuer`, `JWT:Audience`, `JWT:SigningKey`: set appropriate values for your environment

Example (do not use the sample SigningKey in production):
```json
{
  "ConnectionStrings": { "DefaultConnection": "Server=.;Database=finshark;Trusted_Connection=True;Encrypt=False" },
  "FMPKey": "YOUR_FMP_API_KEY",
  "JWT": {
    "Issuer": "http://localhost:5028",
    "Audience": "http://localhost:5028",
    "SigningKey": "replace-with-strong-secret"
  }
}
```

Optional: use `dotnet user-secrets` for local secrets.

## Database & migrations
Apply migrations to create the schema and seed roles (`Admin`, `User`):
```bash
cd FinanceProject/api
dotnet tool install --global dotnet-ef # if not installed
dotnet ef database update
```

## Run & explore
Run the API:
```bash
cd FinanceProject/api
dotnet run
```

Default URLs (see `Properties/launchSettings.json`):
- HTTP: `http://localhost:5028`
- HTTPS: `https://localhost:7213`

Swagger UI: open `http://localhost:5028/swagger`.

## API overview
Base path: `/api`

Accounts
- `POST /api/account/register` → Register new user
- `POST /api/account/login` → Obtain JWT

Stocks
- `GET /api/stock` (auth) → Query with `Symbol`, `CompanyName`, `SortBy`, `IsDescending`, `PageNumber`, `PageSize`
- `GET /api/stock/{id}` → Get by id
- `POST /api/stock` → Create stock
- `PUT /api/stock/{id}` → Update stock
- `DELETE /api/stock/{id}` → Delete stock

Comments
- `GET /api/comment` (auth) → Query with `Symbol`, `IsDescending`
- `GET /api/comment/{id}` → Get by id
- `POST /api/comment/{symbol}` (expects auth) → Create comment for a stock symbol; will fetch from FMP if unknown
- `PUT /api/comment/{id}` → Update comment
- `DELETE /api/comment/{id}` → Delete comment

Portfolio
- `GET /api/portfolio` (auth) → Current user portfolio
- `POST /api/portfolio?symbol=AAPL` (auth) → Add symbol to portfolio (fetches from FMP if missing)
- `DELETE /api/portfolio?symbol=AAPL` (auth) → Remove symbol from portfolio

## Auth flow
1) Register
```http
POST /api/account/register
Content-Type: application/json

{
  "userName": "alice",
  "email": "alice@example.com",
  "password": "StrongP@ssword123!"
}
```
2) Login and copy the `token`
```http
POST /api/account/login
Content-Type: application/json

{
  "userName": "alice",
  "password": "StrongP@ssword123!"
}
```
3) Call authorized endpoints with header:
```
Authorization: Bearer <token>
```

## Request examples
Create stock
```http
POST /api/stock
Content-Type: application/json

{
  "symbol": "AAPL",
  "companyName": "Apple",
  "purchase": 150.00,
  "lastDiv": 0.24,
  "industry": "Tech",
  "marketCap": 2500000000
}
```

Query stocks (authorized)
```http
GET /api/stock?CompanyName=App&SortBy=Symbol&IsDescending=true&PageNumber=1&PageSize=20
Authorization: Bearer <token>
```

Create comment for a symbol (expects auth)
```http
POST /api/comment/AAPL
Authorization: Bearer <token>
Content-Type: application/json

{
  "title": "Solid earnings",
  "content": "Beat expectations this quarter."
}
```

Add to portfolio (authorized)
```http
POST /api/portfolio?symbol=AAPL
Authorization: Bearer <token>
```

## Project structure
```
FinanceProject/
  api/
    Controllers/        # Stocks, Comments, Portfolio, Account
    Data/               # ApplicationDBContext (EF + Identity)
    Dtos/               # Request/response contracts
    Mappers/            # Model ↔ DTO mappers
    Models/             # Domain entities
    Repository/         # Repositories for data access
    Service/            # TokenService, FMPService
    Helpers/            # Query objects
    Properties/         # launchSettings.json
    Program.cs          # Composition root
```

## Troubleshooting
- 401 Unauthorized: Ensure you include `Authorization: Bearer <token>` and your token is not expired.
- Cannot connect to DB: Verify `ConnectionStrings:DefaultConnection` and that SQL Server is running.
- FMP not returning data: Check `FMPKey` and symbol validity; free keys have rate limits.
- CORS blocked: `Program.cs` currently allows any origin for development; adjust for production.
- Weak password errors: Identity password rules require uppercase, lowercase, digit, non-alphanumeric, length ≥ 12.



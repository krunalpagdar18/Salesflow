# SalesFlow

A full-stack, multitenant order management platform for small businesses — built with .NET, React, and PostgreSQL, and benchmarked under real concurrent load on minimal cloud infrastructure.

![.NET](https://img.shields.io/badge/.NET-8-512BD4?logo=dotnet&logoColor=white)
![C#](https://img.shields.io/badge/C%23-12-239120?logo=csharp&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-16-4169E1?logo=postgresql&logoColor=white)
![React](https://img.shields.io/badge/React-19-61DAFB?logo=react&logoColor=white)
![TypeScript](https://img.shields.io/badge/TypeScript-5.9-3178C6?logo=typescript&logoColor=white)
![Tailwind](https://img.shields.io/badge/Tailwind-3.4-38B2AC?logo=tailwindcss&logoColor=white)
![Vercel](https://img.shields.io/badge/Frontend-Vercel-000000?logo=vercel&logoColor=white)
![Oracle Cloud](https://img.shields.io/badge/API-Oracle_Cloud-F80000?logo=oracle&logoColor=white)

---

## What it does

SalesFlow lets multiple independent businesses (tenants) manage their customers, products, and sales orders through a shared platform — with strict data isolation between tenants enforced at the database query level.

Sales teams use the React web portal to create and track orders. Each order captures line items, unit pricing, item-level discounts and taxes, and a gross total — with all financial figures calculated and validated on both client and server.

---

## Architecture

```
┌─────────────────────┐        ┌──────────────────────────────────┐
│   React SPA (Vercel)│ ──────▶│  ASP.NET Core API (Oracle Cloud) │
│   TypeScript + TW   │  HTTPS │  C# · EF Core · PostgreSQL       │
└─────────────────────┘        └──────────────────────────────────┘
```

- **Frontend** deployed on Vercel — API calls are proxied through Vercel's rewrite rules, keeping the backend origin private and avoiding CORS configuration.
- **Backend** deployed on a single Oracle Cloud VM — both the .NET API and PostgreSQL run on the same instance (1 OCPU, 1 GB RAM, always-free tier).

---

## Key Design Points

- **Automatic tenant isolation** — EF Core global query filters are applied at model-build time using C# Expression Trees, so every database query is scoped to the authenticated tenant without any per-query code.
- **Atomic order writes** — Unit of Work pattern ensures order header and all line items are committed in a single transaction; a partial failure rolls back completely.
- **HttpOnly cookie auth** — JWTs are stored only in HttpOnly cookies; the React app never touches a token string. A silent refresh queue in the Axios interceptor handles token expiry transparently across concurrent requests.
- **Load tested** — The Create Order endpoint was benchmarked with k6 at 20 concurrent users (0% HTTP errors, p95 < 156ms); identified and documented the hardware's saturation point at 30 VUs.

---

## Documentation

- 📐 **[Backend Architecture](docs/Backend.md)** — API design, multitenancy implementation, load test results, trade-off log
- 🎨 **[Frontend Architecture](docs/Frontend.md)** — Component system, auth flow, data patterns, deployment

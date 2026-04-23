# Food Ordering System Backend

Backend API for **Food Ordering System**, a multi-provider food marketplace where users can browse meals, place orders, write reviews, and manage their delivery details, while providers and admins handle operations through role-based endpoints.

Live API: https://foodhub-backend-mu.vercel.app/
Demo Client: https://foodhub-frontend-puce.vercel.app/

## Overview

Supports three main roles:

- `USER` can browse meals, create provider applications, manage addresses, place orders, cancel eligible orders, and post reviews.
- `PROVIDER` can manage their own meals and update the status of their incoming orders.
- `ADMIN` can manage categories and update user account status.

The API is organized around domain modules and uses a consistent `route -> controller -> service -> prisma` structure.

## Tech Stack

- Node.js
- Express 5
- TypeScript
- Prisma ORM
- PostgreSQL
- Better Auth
- CORS

## Core Features

- Email/password authentication with Better Auth
- Session-based authorization with role checks
- Public meal browsing with filtering, search, sorting, and pagination
- Provider profile creation and provider storefront support
- Provider meal management
- Order creation with single-provider order validation
- Order lifecycle updates for users and providers
- Review creation for meals
- Address upsert and retrieval
- Admin-only user management
- Prisma-backed relational schema with custom error handling

## Architecture

The project is structured by feature modules inside [`src/modules`](./src/modules):

- `auth`
- `meal`
- `review`
- `category`
- `providerProfile`
- `provider`
- `address`
- `order`
- `admin`

Supporting layers live in:

- [`src/app.ts`](./src/app.ts) - app configuration, middleware, and route mounting
- [`src/server.ts`](./src/server.ts) - server bootstrap and database connection
- [`src/lib`](./src/lib) - Prisma and Better Auth setup
- [`src/middlewares`](./src/middlewares) - auth and error middleware
- [`prisma/schema`](./prisma/schema) - split Prisma schema files by domain

## Data Model Highlights

The database is designed around a marketplace flow:

- `User` with role and status
- `ProviderProfile` linked one-to-one with a user
- `Meal` linked to category and provider
- `Order` and `OrderItem` for purchase flow
- `Review` linked to meals and users
- `Address` for delivery information
- Better Auth tables for sessions, accounts, and verification records

## API Summary

Base URL examples:

- Production: `https://foodhub-backend-mu.vercel.app`
- Local: `http://localhost:5000`
- Auth base path: `/api/auth`

Main route groups:

### Public / mixed access

- `GET /api/meals`
- `GET /api/meals/:mealId`
- `GET /api/meals/:mealId/reviews`
- `GET /api/categories`
- `GET /api/providers`
- `GET /api/providers/:providerId`

### Authenticated user access

- `GET /api/auth/me`
- `POST /api/providers`
- `GET /api/addresses`
- `GET /api/addresses/:id`
- `POST /api/addresses`
- `POST /api/orders`
- `GET /api/orders`
- `GET /api/orders/:orderId`
- `PUT /api/orders/cancel/:orderId`
- `POST /api/meals/:mealId/reviews`

### Provider access

- `GET /api/providers/me`
- `POST /api/provider/meals`
- `PUT /api/provider/meals/:mealId`
- `DELETE /api/provider/meals/:mealId`
- `PUT /api/provider/orders/:orderId`

### Admin access

- `POST /api/categories`
- `PUT /api/categories/:categoryId`
- `DELETE /api/categories/:categoryId`
- `GET /api/admin/users`
- `PATCH /api/admin/users/:userId`

## Local Development

### 1. Install dependencies

```bash
pnpm install
```

### 2. Configure environment variables

Create a `.env` file with values for:

```env
DATABASE_URL=your_postgresql_connection_string
PORT=5000
BETTER_AUTH_SECRET=your_secret
BETTER_AUTH_URL=http://localhost:5000
FRONT_END_URL=http://localhost:3000
```

### 3. Generate Prisma client

```bash
pnpm prisma generate
```

### 4. Run database migrations

```bash
pnpm prisma migrate dev
```

### 5. Start the development server

```bash
pnpm dev
```

## Build

```bash
pnpm build
```

## Scripts

- `pnpm dev` - run the backend in watch mode
- `pnpm build` - generate Prisma client and bundle the project

## Error Handling

The API uses:

- custom `AppError` instances for domain-specific failures
- Prisma error mapping for common database failures
- a centralized Express error handler for consistent JSON responses

---
id: integration
aliases: []
tags: []
---

# Integration Reference

Frontend-backend integration patterns, authentication, and shared types.

## CORS Configuration

Since frontend (port 3000) and backend (port 3001) run separately, configure CORS in Elysia.js:

```typescript
// apps/api/src/index.ts
import { Elysia } from 'elysia';
import { cors } from '@elysiajs/cors';

const app = new Elysia()
  .use(cors({
    origin: ['http://localhost:3000', 'http://localhost:4000'],
    credentials: true,
    methods: ['GET', 'POST', 'PUT', 'DELETE', 'PATCH', 'OPTIONS'],
    allowedHeaders: ['Content-Type', 'Authorization'],
  }))
  .listen(3001);
```

## Authentication with JWT

### Backend: JWT Setup

```typescript
// apps/api/src/lib/auth.ts
import { Elysia, t } from 'elysia';
import jwt from '@elysiajs/jwt';

export const auth = new Elysia()
  .use(jwt({
    secret: process.env.JWT_SECRET || 'your-secret-key',
    exp: 7 * 24 * 60 * 60, // 7 days
  }));

// Login endpoint
export const loginRoute = new Elysia({ prefix: '/auth' })
  .use(auth)
  .post('/login', async ({ body, jwt }) => {
    const { email, password } = body as { email: string; password: string };

    // Verify user (use Prisma)
    const user = await prisma.user.findUnique({ where: { email } });
    if (!user || !await verifyPassword(password, user.password)) {
      throw new Error('Invalid credentials');
    }

    // Generate token
    const token = await jwt.sign({
      id: user.id,
      email: user.email,
      role: user.role,
    });

    return { token, user: { id: user.id, email: user.email, name: user.name } };
  }, {
    body: t.Object({
      email: t.String(),
      password: t.String(),
    }),
  });
```

### Frontend: API Client with Auth

```typescript
// apps/web/src/lib/api.ts
const API_URL = import.meta.env.VITE_API_URL ?? 'http://localhost:3001';

interface ApiOptions extends RequestInit {
  token?: string;
}

export async function api<T>(path: string, options?: ApiOptions): Promise<T> {
  const { token, ...init } = options || {};

  const headers: HeadersInit = {
    'Content-Type': 'application/json',
  };

  if (token) {
    headers['Authorization'] = `Bearer ${token}`;
  }

  const res = await fetch(`${API_URL}${path}`, {
    ...init,
    headers: { ...headers, ...init.headers },
    credentials: 'include',
  });

  if (!res.ok) {
    const error = await res.json().catch(() => ({ message: 'Request failed' }));
    throw new Error(error.message || `API error: ${res.status}`);
  }

  return res.json();
}

// Auth helper hooks
export function getToken(): string | null {
  return localStorage.getItem('token');
}

export function setToken(token: string): void {
  localStorage.setItem('token', token);
}

export function clearToken(): void {
  localStorage.removeItem('token');
}
```

## Shared Types

Share TypeScript types between frontend and backend:

```typescript
// packages/shared/src/index.ts
export interface User {
  id: string;
  email: string;
  name: string | null;
  role: 'admin' | 'user';
  createdAt: Date;
  updatedAt: Date;
}

export type CreateUser = Pick<User, 'email' | 'name' | 'role'>;
export type UpdateUser = Partial<CreateUser>;

export interface ApiResponse<T> {
  data: T;
  message?: string;
}

export interface PaginatedResponse<T> {
  data: T[];
  total: number;
  page: number;
  pageSize: number;
}
```

### Using Shared Types

```typescript
// Frontend - apps/web/src/pages/Home.tsx
import { User } from '@myorg/shared';
import { api, getToken } from '../lib/api';

export function Home() {
  const [users, setUsers] = useState<User[]>([]);

  useEffect(() => {
    api<User[]>('/users', { token: getToken() })
      .then(setUsers)
      .catch(console.error);
  }, []);

  return <DataTable data={users} fields={userFields} />;
}
```

## Database Seeding

Create test accounts and test data:

```typescript
// apps/api/prisma/seed.ts
import { PrismaClient } from '@prisma/client';

const prisma = new PrismaClient();

async function seed() {
  console.log('Seeding database...');

  // Create test users
  await prisma.user.upsert({
    where: { email: 'test@example.com' },
    update: {},
    create: {
      email: 'test@example.com',
      name: 'Test Admin',
      password: await hashPassword('Test123!'),
      role: 'admin',
    },
  });

  await prisma.user.upsert({
    where: { email: 'user@example.com' },
    update: {},
    create: {
      email: 'user@example.com',
      name: 'Test User',
      password: await hashPassword('Test123!'),
      role: 'user',
    },
  });

  console.log('Seeding complete');
}

seed()
  .catch(console.error)
  .finally(() => prisma.$disconnect());
```

```json
// package.json in apps/api
{
  "prisma": {
    "seed": "bun run prisma/seed.ts"
  }
}
```

Run seed:
```bash
bunx prisma db seed
```

## React Router Setup

```typescript
// apps/web/src/App.tsx
import { BrowserRouter, Routes, Route } from 'react-router-dom';
import { AppProvider } from 'anysystem-design';
import { Home } from './pages/Home';
import { Login } from './pages/Login';
import { Dashboard } from './pages/Dashboard';

export function App() {
  return (
    <AppProvider appName="MyApp">
      <BrowserRouter>
        <Routes>
          <Route path="/login" element={<Login />} />
          <Route path="/" element={<Home />} />
          <Route path="/dashboard" element={<Dashboard />} />
        </Routes>
      </BrowserRouter>
    </AppProvider>
  );
}
```

## Error Handling

### Backend Error Handling

```typescript
// apps/api/src/lib/errors.ts
export class AppError extends Error {
  constructor(
    message: string,
    public statusCode: number = 500,
    public code?: string
  ) {
    super(message);
    this.name = 'AppError';
  }
}

// Usage in routes
.get('/:id', async ({ params, error }) => {
  const user = await prisma.user.findUnique({
    where: { id: params.id }
  });

  if (!user) {
    throw error(404, { message: 'User not found', code: 'USER_NOT_FOUND' });
  }

  return user;
})
```

### Frontend Error Handling

```typescript
// apps/web/src/lib/api.ts
export async function api<T>(path: string, options?: ApiOptions): Promise<T> {
  try {
    const res = await fetch(`${API_URL}${path}`, { /* ... */ });

    if (res.status === 401) {
      clearToken();
      window.location.href = '/login';
      throw new Error('Unauthorized');
    }

    if (!res.ok) {
      throw new Error(`API error: ${res.status}`);
    }

    return res.json();
  } catch (err) {
    console.error('API Error:', err);
    throw err;
  }
}
```

## Environment Configuration

```env
# apps/api/.env
DATABASE_URL="postgresql://user:pass@localhost:5432/mydb"
JWT_SECRET="your-super-secret-jwt-key-min-32-chars"
CORS_ORIGIN="http://localhost:3000"
```

```env
# apps/web/.env
VITE_API_URL=http://localhost:3001
```

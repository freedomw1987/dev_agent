---
id: tech-stack
aliases: []
tags: []
---

# Tech Stack Reference

Default technology stack for application development.

## Stack Overview

| Layer | Technology |
|-------|------------|
| **Runtime** | Bun |
| **Language** | TypeScript |
| **Frontend** | React + TailwindCSS + **Anysystem Design** |
| **Backend** | Elysia.js |
| **ORM** | Prisma |
| **Database** | PostgreSQL (default), SQLite (development) |

## Anysystem Design Components

Reference: https://github.com/david-marstree/anysystem

```bash
npm install anysystem-design
```

### Anysystem Import & Setup

```tsx
import { AppProvider, Button, FormControl, FormInput } from "anysystem-design";

export function App() {
  return (
    <AppProvider appName="MyApp">
      <Button variant="primary">Click Me</Button>
    </AppProvider>
  );
}
```

### Form Components

```tsx
import { Formik, Form } from 'formik';
import { FormControl, FormInput, FormSelectbox, Button } from 'anysystem-design';

function MyForm() {
  return (
    <Formik initialValues={{ email: '', name: '' }} onSubmit={(values) => console.log(values)}>
      <Form>
        <FormControl name="email" label="Email" required>
          <FormInput name="email" type="email" />
        </FormControl>
        <FormControl name="name" label="Name">
          <FormInput name="name" />
        </FormControl>
        <Button type="submit" variant="primary">Submit</Button>
      </Form>
    </Formik>
  );
}
```

### Layout Components

```tsx
import { Container, Row, Column, SideMenuLayout, Navbar, NavList } from "anysystem-design";

<SideMenuLayout
  header={<Navbar title="My App" />}
  menu={<NavList items={menuItems} />}
>
  <Container>
    <Row className="justify-between">
      <Column>Content</Column>
    </Row>
  </Container>
</SideMenuLayout>
```

### Available Components

| Category | Components |
|----------|------------|
| **Form** | Input, FormInput, PasswordInput, Selectbox, Checkbox, RadioGroup, DatePicker, AutoComplete |
| **Layout** | Container, Row, Column, SideMenuLayout, EmptyLayout |
| **Navigation** | Navbar, NavList |
| **Data** | DataTable, Text |
| **Interactive** | Button, Modal |

### If Component Not Available

Build custom components following Anysystem patterns:
- Use Tailwind CSS utility classes
- Match the visual style (colors, spacing, typography)
- Follow same interaction patterns

## Project Structure

```
.
├── apps/
│   ├── web/                 # React frontend
│   │   ├── src/
│   │   │   ├── components/
│   │   │   ├── pages/
│   │   │   ├── hooks/
│   │   │   └── lib/
│   │   ├── tailwind.config.js
│   │   └── vite.config.ts
│   └── api/                 # Elysia backend
│       ├── src/
│       │   ├── routes/
│       │   ├── services/
│       │   ├── middleware/
│       │   └── index.ts
│       └── prisma/
│           └── schema.prisma
├── packages/
│   └── shared/              # Shared types/utils
├── infra/                   # AWS CDK
└── package.json             # Workspace root
```

## Bun Commands

```bash
# Install dependencies
bun install

# Run development
bun run dev              # Run all apps
bun run dev:web          # Frontend only
bun run dev:api          # Backend only

# Build
bun run build
bun run build:web
bunrun build:api

# Database
bunx prisma generate      # Generate Prisma client
bunx prisma db push      # Push schema to DB
bunx prisma db migrate   # Run migrations
bunx prisma studio       # Open Prisma Studio

# Testing
bun test
bun run test:e2e         # Playwright tests
```

## Backend: Elysia.js

```typescript
// apps/api/src/index.ts
import { Elysia } from 'elysia';
import { cors } from '@elysiajs/cors';
import { logger } from '@elysiajs/logger';
import { prisma } from './lib/prisma';
import { userRoutes } from './routes/user';

const app = new Elysia()
  .use(cors())
  .use(logger())
  .use(userRoutes)
  .listen(3000);

export type App = typeof app;
```

```typescript
// apps/api/src/routes/user.ts
import { Elysia, t } from 'elysia';
import { prisma } from '../lib/prisma';

export const userRoutes = new Elysia({ prefix: '/users' })
  .get('/', async () => {
    return prisma.user.findMany();
  })
  .get('/:id', async ({ params }) => {
    return prisma.user.findUnique({
      where: { id: params.id }
    });
  })
  .post('/', async ({ body }) => {
    return prisma.user.create({ data: body });
  }, {
    body: t.Object({
      email: t.String(),
      name: t.Optional(t.String())
    })
  });
```

## Prisma Schema

```prisma
// apps/api/prisma/schema.prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

model User {
  id        String   @id @default(uuid())
  email     String   @unique
  name      String?
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}
```

## Frontend: React + TailwindCSS + Anysystem

```tsx
// apps/web/src/pages/Home.tsx
import { useQuery } from '@tanstack/react-query';
import { Container, DataTable, Button, Row, Text } from 'anysystem-design';

export function Home() {
  const { data: users, isLoading } = useQuery({
    queryKey: ['users'],
    queryFn: () => fetch('/api/users').then(res => res.json())
  });

  if (isLoading) return <div>Loading...</div>;

  const fields = [
    { key: 'name', label: 'Name' },
    { key: 'email', label: 'Email' }
  ];

  return (
    <Container>
      <Row className="justify-between items-center mb-4">
        <Text tag="h1">Users</Text>
        <Button variant="primary">Add User</Button>
      </Row>
      <DataTable data={users || []} fields={fields} selectable name="users-table" />
    </Container>
  );
}
```

```typescript
// apps/web/src/lib/api.ts
const API_URL = import.meta.env.VITE_API_URL ?? 'http://localhost:3001';

export async function api<T>(path: string, init?: RequestInit): Promise<T> {
  const res = await fetch(`${API_URL}${path}`, {
    ...init,
    headers: {
      'Content-Type': 'application/json',
      ...init?.headers
    }
  });

  if (!res.ok) throw new Error(`API error: ${res.status}`);
  return res.json();
}
```

## Shared Types

```typescript
// packages/shared/src/index.ts
export interface User {
  id: string;
  email: string;
  name: string | null;
  createdAt: Date;
  updatedAt: Date;
}

export type CreateUser = Pick<User, 'email' | 'name'>;
export type UpdateUser = Partial<CreateUser>;
```

## Key Dependencies

### Frontend (apps/web/package.json)
```json
{
  "dependencies": {
    "react": "^18.3.0",
    "react-dom": "^18.3.0",
    "@tanstack/react-query": "^5.0.0",
    "tailwindcss": "^3.4.0",
    "anysystem-design": "latest",
    "formik": "^2.4.0",
    "yup": "^1.4.0"
  }
}
```

### Backend (apps/api/package.json)
```json
{
  "dependencies": {
    "elysia": "^1.0.0",
    "@elysiajs/cors": "^1.0.0",
    "@elysiajs/logger": "^1.0.0",
    "@prisma/client": "^5.0.0"
  },
  "devDependencies": {
    "prisma": "^5.0.0"
  }
}
```

## Environment Variables

```env
# apps/api/.env
DATABASE_URL="postgresql://user:pass@localhost:5432/mydb"
```

```env
# apps/web/.env
VITE_API_URL=http://localhost:3000
```

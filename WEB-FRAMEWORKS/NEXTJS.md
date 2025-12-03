# Next.js Development - Standard Operating Procedures

**Source:** [github.com/vercel/next.js](https://github.com/vercel/next.js)
**Repository Stars:** 136k
**Tech Stack:** TypeScript (28.3%), JavaScript (58%), Rust (12.4%)
**License:** MIT

## Overview

Next.js is a React framework with:
- Server-side rendering (SSR)
- Static site generation (SSG)
- Incremental static regeneration (ISR)
- API routes for backend
- Built-in optimization
- Vercel deployment integration

## Project Structure

### Standard Directory Layout

```
my-app/
├── public/                  # Static assets (favicon, robots.txt)
├── src/
│   ├── app/                # Next.js 13+ app directory
│   │   ├── layout.tsx      # Root layout
│   │   ├── page.tsx        # Home page
│   │   ├── api/            # API routes
│   │   │   └── hello/
│   │   │       └── route.ts
│   │   ├── dashboard/      # Nested route
│   │   │   └── page.tsx
│   │   └── [...slug]/      # Dynamic routes
│   │       └── page.tsx
│   ├── components/         # Reusable components
│   │   ├── Header.tsx
│   │   ├── Footer.tsx
│   │   └── Navigation.tsx
│   ├── lib/                # Utility functions
│   │   ├── db.ts          # Database connections
│   │   ├── auth.ts        # Authentication utilities
│   │   └── utils.ts       # Helper functions
│   ├── types/              # TypeScript types
│   │   ├── user.ts
│   │   └── api.ts
│   └── styles/             # Global styles
│       └── globals.css
├── tests/                  # Test suites
│   ├── unit/
│   └── integration/
├── .env.local              # Local environment variables
├── .env.example            # Template for .env variables
├── .gitignore
├── next.config.js          # Next.js configuration
├── tsconfig.json           # TypeScript configuration
├── jest.config.js          # Jest testing configuration
├── package.json
└── README.md
```

## Development Setup

### Prerequisites

- Node.js 18.17+
- npm, yarn, pnpm, or bun

### Initial Setup

```bash
# Create new Next.js project
npx create-next-app@latest my-app
cd my-app

# Or clone existing repository
git clone <repo-url>
cd <repo>

# Install dependencies
npm install

# Create .env.local from .env.example
cp .env.example .env.local
# Edit .env.local with local values

# Start development server
npm run dev

# Open http://localhost:3000
```

### Available Scripts

```bash
# Development server (with hot reload)
npm run dev

# Build for production
npm run build

# Start production server
npm start

# Run linting
npm run lint

# Run tests
npm test

# Type checking
npm run type-check
```

## Configuration Files

### next.config.js

```javascript
/** @type {import('next').NextConfig} */
const nextConfig = {
  // Compiler optimizations
  swcMinify: true,

  // Environment variables
  env: {
    API_URL: process.env.API_URL,
  },

  // Image optimization
  images: {
    domains: ['api.example.com'],
    formats: ['image/avif', 'image/webp'],
  },

  // Headers
  async headers() {
    return [
      {
        source: '/api/:path*',
        headers: [
          { key: 'Access-Control-Allow-Credentials', value: 'true' },
          { key: 'Access-Control-Allow-Origin', value: '*' },
        ],
      },
    ];
  },

  // Redirects
  async redirects() {
    return [
      {
        source: '/about',
        destination: '/team',
        permanent: false,
      },
    ];
  },

  // Rewrites
  async rewrites() {
    return {
      beforeFiles: [
        {
          source: '/api/:path*',
          destination: 'http://localhost:3001/api/:path*',
        },
      ],
    };
  },
};

module.exports = nextConfig;
```

### tsconfig.json

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "jsx": "preserve",
    "module": "ESNext",
    "moduleResolution": "bundler",
    "allowImportingTsExtensions": true,
    "resolveJsonModule": true,
    "strict": true,
    "noEmit": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "paths": {
      "@/*": ["./src/*"]
    }
  },
  "include": ["next-env.d.ts", "**/*.ts", "**/*.tsx"],
  "exclude": ["node_modules"]
}
```

## File Structure Conventions

### Component Organization

**Naming Convention:**
- Components: PascalCase (Button.tsx, UserCard.tsx)
- Utilities: camelCase (formatDate.ts, parseUser.ts)
- Styles: Match component name (Button.module.css)

**Component Structure:**
```typescript
// src/components/UserCard.tsx

import { User } from '@/types/user';
import styles from './UserCard.module.css';

interface UserCardProps {
  user: User;
  onSelect?: (user: User) => void;
}

export default function UserCard({ user, onSelect }: UserCardProps) {
  return (
    <div className={styles.card} onClick={() => onSelect?.(user)}>
      <h3>{user.name}</h3>
      <p>{user.email}</p>
    </div>
  );
}
```

### Page Components

**Next.js 13+ App Router:**
```typescript
// src/app/users/[id]/page.tsx

import { notFound } from 'next/navigation';
import UserDetail from '@/components/UserDetail';
import { getUser } from '@/lib/db';

interface UserPageProps {
  params: {
    id: string;
  };
}

export async function generateMetadata({ params }: UserPageProps) {
  const user = await getUser(params.id);
  if (!user) return {};

  return {
    title: user.name,
    description: `Profile of ${user.name}`,
  };
}

export async function generateStaticParams() {
  // Pre-render popular user pages
  const users = await getUser();
  return users.map((user) => ({
    id: user.id.toString(),
  }));
}

export default async function UserPage({ params }: UserPageProps) {
  const user = await getUser(params.id);

  if (!user) {
    notFound();
  }

  return <UserDetail user={user} />;
}
```

### API Routes

```typescript
// src/app/api/users/route.ts

import { NextRequest, NextResponse } from 'next/server';
import { validateAuth } from '@/lib/auth';
import { getUsers, createUser } from '@/lib/db';

export async function GET(request: NextRequest) {
  try {
    const auth = await validateAuth(request);
    if (!auth) {
      return NextResponse.json(
        { error: 'Unauthorized' },
        { status: 401 }
      );
    }

    const users = await getUsers();
    return NextResponse.json(users);
  } catch (error) {
    return NextResponse.json(
      { error: 'Internal server error' },
      { status: 500 }
    );
  }
}

export async function POST(request: NextRequest) {
  try {
    const auth = await validateAuth(request);
    if (!auth) {
      return NextResponse.json(
        { error: 'Unauthorized' },
        { status: 401 }
      );
    }

    const data = await request.json();
    const user = await createUser(data);

    return NextResponse.json(user, { status: 201 });
  } catch (error) {
    return NextResponse.json(
      { error: 'Failed to create user' },
      { status: 400 }
    );
  }
}
```

## Development Workflow

### Feature Development

```bash
# 1. Create feature branch
git checkout -b feature/user-dashboard

# 2. Start development server
npm run dev

# 3. Create new pages/components as needed
# Next.js hot-reloads automatically

# 4. Test locally
npm run test

# 5. Build to check for errors
npm run build

# 6. Run linting
npm run lint

# 7. Commit and push
git add .
git commit -m "feat: add user dashboard"
git push origin feature/user-dashboard

# 8. Create PR on GitHub
```

### Testing Standards

**Test Files Location:** `tests/` or `*.test.ts`

**Example Unit Test:**
```typescript
// tests/unit/utils.test.ts

import { formatDate, parseUser } from '@/lib/utils';

describe('formatDate', () => {
  it('should format date correctly', () => {
    const date = new Date('2024-01-15');
    expect(formatDate(date)).toBe('Jan 15, 2024');
  });
});

describe('parseUser', () => {
  it('should parse user JSON', () => {
    const json = { id: 1, name: 'John', email: 'john@example.com' };
    const user = parseUser(json);
    expect(user.name).toBe('John');
  });
});
```

**Example Integration Test:**
```typescript
// tests/integration/api.test.ts

import { GET } from '@/app/api/users/route';
import { NextRequest } from 'next/server';

describe('GET /api/users', () => {
  it('should return list of users', async () => {
    const request = new NextRequest(
      new URL('http://localhost:3000/api/users')
    );

    const response = await GET(request);
    expect(response.status).toBe(200);

    const data = await response.json();
    expect(Array.isArray(data)).toBe(true);
  });
});
```

## Deployment

### Vercel (Recommended)

```bash
# 1. Push to GitHub
git push origin main

# 2. Go to vercel.com
# 3. Click "New Project"
# 4. Select your GitHub repository
# 5. Vercel auto-detects Next.js
# 6. Configure environment variables
# 7. Click "Deploy"

# Automatic deployments on push to main
```

### Other Platforms

**Docker Deployment:**
```dockerfile
# Dockerfile
FROM node:18-alpine AS base
WORKDIR /app

# Install dependencies
FROM base AS deps
COPY package*.json ./
RUN npm ci

# Build
FROM base AS build
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN npm run build

# Production
FROM base AS production
COPY --from=build /app/.next ./.next
COPY --from=build /app/node_modules ./node_modules
COPY --from=build /app/public ./public
COPY package*.json ./

EXPOSE 3000
CMD ["npm", "start"]
```

### Environment Variables

**Template (.env.example):**
```bash
# API Configuration
API_URL=http://localhost:3001
API_KEY=your_api_key_here

# Database
DATABASE_URL=postgresql://user:pass@localhost:5432/dbname

# Authentication
JWT_SECRET=your_secret_key
NEXTAUTH_SECRET=your_nextauth_secret
NEXTAUTH_URL=http://localhost:3000

# Third-party Services
STRIPE_KEY=sk_test_...
GITHUB_ID=your_github_id
GITHUB_SECRET=your_github_secret
```

## Code Style Guidelines

### ESLint Configuration

```json
{
  "extends": [
    "next/core-web-vitals",
    "plugin:@typescript-eslint/recommended"
  ],
  "rules": {
    "@typescript-eslint/no-unused-vars": "warn",
    "@typescript-eslint/no-explicit-any": "error",
    "react/no-unescaped-entities": "warn"
  }
}
```

### TypeScript Best Practices

- ✅ Enable strict mode
- ✅ Use interfaces for props
- ✅ Type all function parameters and returns
- ✅ Avoid `any` type
- ✅ Use enums for constants
- ✅ Define types in separate `types/` directory

## Performance Optimization

### Core Web Vitals

1. **Largest Contentful Paint (LCP)**
   - Optimize images with `next/image`
   - Code split heavy components
   - Preload critical resources

2. **First Input Delay (FID)**
   - Reduce JavaScript bundle size
   - Use React Server Components
   - Optimize event handlers

3. **Cumulative Layout Shift (CLS)**
   - Reserve space for images/ads
   - Use `next/image` with fixed dimensions
   - Avoid inserting content above existing content

### Image Optimization

```typescript
import Image from 'next/image';

export default function Hero() {
  return (
    <Image
      src="/hero.jpg"
      alt="Hero image"
      width={1200}
      height={600}
      priority // Load immediately (for above-fold)
      quality={75} // Optimize quality
      sizes="(max-width: 768px) 100vw, 50vw"
    />
  );
}
```

## Security Best Practices

### Environment Variables
- Never commit `.env.local`
- Use `.env.example` as template
- Mark secrets with `NEXT_PUBLIC_` if needed in browser

### Authentication
- Use NextAuth.js or similar
- Store session data server-side
- Validate on API routes
- Use httpOnly cookies

### API Routes
```typescript
// Always validate input and authenticate
export async function POST(request: NextRequest) {
  // Validate authentication
  const session = await getServerSession();
  if (!session) return NextResponse.json({ error: 'Unauthorized' }, { status: 401 });

  // Validate input
  const body = await request.json();
  if (!body.email) return NextResponse.json({ error: 'Missing email' }, { status: 400 });

  // Prevent CORS issues
  const headers = {
    'Content-Type': 'application/json',
  };

  // Process and respond
  return NextResponse.json(result, { headers });
}
```

## Troubleshooting

### Common Issues

| Issue | Solution |
|-------|----------|
| Port 3000 already in use | `npm run dev -- -p 3001` |
| Hot reload not working | Clear `.next` folder and restart |
| Import paths not resolving | Check `tsconfig.json` paths config |
| Build failing | Run `npm run lint` and fix errors |
| Slow builds | Check for large dependencies, enable SWC |

## Related Resources

- [Next.js Official Docs](https://nextjs.org/docs)
- [Next.js GitHub Repository](https://github.com/vercel/next.js)
- [Next.js Contributing Guide](https://github.com/vercel/next.js/blob/canary/contributing.md)
- [Vercel Deployment Guide](https://vercel.com/docs/frameworks/nextjs)
- [React Documentation](https://react.dev)

---

**Last Updated:** December 2024
**Version:** 1.0
**Status:** Active

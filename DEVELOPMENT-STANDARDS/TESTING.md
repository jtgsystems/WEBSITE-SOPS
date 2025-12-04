# Testing Standards - Standard Operating Procedure

**Version:** 1.0
**Last Updated:** December 2024
**Applies To:** All Development Projects

---

## Overview

This SOP defines testing standards, methodologies, and best practices for ensuring software quality. Proper testing reduces bugs, improves reliability, and enables confident deployments.

---

## Testing Pyramid

```
                    ┌─────────┐
                    │   E2E   │  Few, slow, expensive
                   ─┴─────────┴─
                  ┌─────────────┐
                  │ Integration │  Some, moderate
                 ─┴─────────────┴─
                ┌─────────────────┐
                │   Unit Tests    │  Many, fast, cheap
                └─────────────────┘
```

### Coverage Targets

| Test Type | Coverage Target | Run Frequency |
|-----------|-----------------|---------------|
| Unit | 80%+ | Every commit |
| Integration | Key paths | Every PR |
| E2E | Critical flows | Daily/Pre-release |

---

## Unit Testing

### Standards

```typescript
// Test file naming
component.ts       → component.test.ts
service.ts         → service.spec.ts

// Test structure (AAA Pattern)
describe('UserService', () => {
  describe('createUser', () => {
    it('should create a user with valid data', () => {
      // Arrange
      const userData = { name: 'John', email: 'john@example.com' };

      // Act
      const result = userService.createUser(userData);

      // Assert
      expect(result).toHaveProperty('id');
      expect(result.name).toBe('John');
    });

    it('should throw error for invalid email', () => {
      // Arrange
      const userData = { name: 'John', email: 'invalid' };

      // Act & Assert
      expect(() => userService.createUser(userData))
        .toThrow('Invalid email format');
    });
  });
});
```

### Best Practices

```markdown
DO:
- Test one behavior per test
- Use descriptive test names
- Keep tests independent
- Mock external dependencies
- Test edge cases and error paths
- Use meaningful assertions

DON'T:
- Test implementation details
- Share state between tests
- Write flaky tests
- Over-mock (test the integration too)
- Ignore test failures
```

### Mocking Guidelines

```typescript
// Good: Mock external services
jest.mock('./emailService', () => ({
  sendEmail: jest.fn().mockResolvedValue({ success: true })
}));

// Good: Mock time-dependent code
jest.useFakeTimers();
jest.setSystemTime(new Date('2024-01-15'));

// Good: Mock API responses
const mockFetch = jest.fn().mockResolvedValue({
  json: () => Promise.resolve({ data: 'test' })
});
global.fetch = mockFetch;
```

---

## Integration Testing

### API Testing

```typescript
// Integration test for REST API
describe('POST /api/users', () => {
  beforeAll(async () => {
    await setupTestDatabase();
  });

  afterAll(async () => {
    await cleanupTestDatabase();
  });

  it('should create user and return 201', async () => {
    const response = await request(app)
      .post('/api/users')
      .send({ name: 'Test User', email: 'test@example.com' })
      .expect(201);

    expect(response.body).toMatchObject({
      id: expect.any(String),
      name: 'Test User',
      email: 'test@example.com'
    });

    // Verify database state
    const user = await db.users.findById(response.body.id);
    expect(user).toBeDefined();
  });

  it('should return 400 for duplicate email', async () => {
    await createUser({ email: 'existing@example.com' });

    const response = await request(app)
      .post('/api/users')
      .send({ name: 'Another', email: 'existing@example.com' })
      .expect(400);

    expect(response.body.error).toBe('Email already exists');
  });
});
```

### Database Testing

```typescript
// Use test containers or in-memory DB
import { PostgreSqlContainer } from '@testcontainers/postgresql';

describe('Database Integration', () => {
  let container;
  let db;

  beforeAll(async () => {
    container = await new PostgreSqlContainer().start();
    db = await connectToDatabase(container.getConnectionUri());
    await runMigrations(db);
  });

  afterAll(async () => {
    await container.stop();
  });

  it('should handle transactions correctly', async () => {
    await db.transaction(async (trx) => {
      await trx.insert('users', { name: 'Test' });
      await trx.insert('profiles', { userId: 1 });
    });

    const user = await db.query('SELECT * FROM users WHERE name = $1', ['Test']);
    expect(user.rows).toHaveLength(1);
  });
});
```

---

## End-to-End Testing

### Playwright Setup

```typescript
// playwright.config.ts
import { defineConfig } from '@playwright/test';

export default defineConfig({
  testDir: './e2e',
  timeout: 30000,
  retries: process.env.CI ? 2 : 0,
  use: {
    baseURL: 'http://localhost:3000',
    screenshot: 'only-on-failure',
    video: 'retain-on-failure',
    trace: 'retain-on-failure',
  },
  projects: [
    { name: 'chromium', use: { browserName: 'chromium' } },
    { name: 'firefox', use: { browserName: 'firefox' } },
    { name: 'webkit', use: { browserName: 'webkit' } },
  ],
});
```

### E2E Test Examples

```typescript
// e2e/user-flow.spec.ts
import { test, expect } from '@playwright/test';

test.describe('User Registration Flow', () => {
  test('should complete full registration', async ({ page }) => {
    // Navigate to registration
    await page.goto('/register');

    // Fill form
    await page.fill('[data-testid="name-input"]', 'John Doe');
    await page.fill('[data-testid="email-input"]', 'john@example.com');
    await page.fill('[data-testid="password-input"]', 'SecurePass123!');

    // Submit
    await page.click('[data-testid="submit-button"]');

    // Verify redirect to dashboard
    await expect(page).toHaveURL('/dashboard');
    await expect(page.locator('[data-testid="welcome-message"]'))
      .toContainText('Welcome, John');
  });

  test('should show validation errors', async ({ page }) => {
    await page.goto('/register');
    await page.click('[data-testid="submit-button"]');

    await expect(page.locator('[data-testid="name-error"]'))
      .toBeVisible();
    await expect(page.locator('[data-testid="email-error"]'))
      .toBeVisible();
  });
});
```

### Visual Regression Testing

```typescript
// Visual comparison tests
test('homepage should match snapshot', async ({ page }) => {
  await page.goto('/');
  await expect(page).toHaveScreenshot('homepage.png', {
    maxDiffPixels: 100
  });
});

test('dark mode should render correctly', async ({ page }) => {
  await page.goto('/');
  await page.click('[data-testid="theme-toggle"]');
  await expect(page).toHaveScreenshot('homepage-dark.png');
});
```

---

## Component Testing

### React Testing Library

```typescript
// Component test example
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { LoginForm } from './LoginForm';

describe('LoginForm', () => {
  const mockOnSubmit = jest.fn();

  beforeEach(() => {
    mockOnSubmit.mockClear();
  });

  it('should render all form fields', () => {
    render(<LoginForm onSubmit={mockOnSubmit} />);

    expect(screen.getByLabelText(/email/i)).toBeInTheDocument();
    expect(screen.getByLabelText(/password/i)).toBeInTheDocument();
    expect(screen.getByRole('button', { name: /sign in/i })).toBeInTheDocument();
  });

  it('should submit form with valid data', async () => {
    const user = userEvent.setup();
    render(<LoginForm onSubmit={mockOnSubmit} />);

    await user.type(screen.getByLabelText(/email/i), 'test@example.com');
    await user.type(screen.getByLabelText(/password/i), 'password123');
    await user.click(screen.getByRole('button', { name: /sign in/i }));

    await waitFor(() => {
      expect(mockOnSubmit).toHaveBeenCalledWith({
        email: 'test@example.com',
        password: 'password123'
      });
    });
  });

  it('should show loading state during submission', async () => {
    mockOnSubmit.mockImplementation(() => new Promise(() => {})); // Never resolves
    const user = userEvent.setup();
    render(<LoginForm onSubmit={mockOnSubmit} />);

    await user.type(screen.getByLabelText(/email/i), 'test@example.com');
    await user.type(screen.getByLabelText(/password/i), 'password123');
    await user.click(screen.getByRole('button', { name: /sign in/i }));

    expect(screen.getByRole('button', { name: /signing in/i })).toBeDisabled();
  });
});
```

---

## Test Data Management

### Fixtures and Factories

```typescript
// factories/user.factory.ts
import { faker } from '@faker-js/faker';

export const createUserData = (overrides = {}) => ({
  id: faker.string.uuid(),
  name: faker.person.fullName(),
  email: faker.internet.email(),
  createdAt: faker.date.past(),
  ...overrides
});

// Usage in tests
const user = createUserData({ name: 'Specific Name' });
```

### Test Database Seeding

```typescript
// test/setup/seed.ts
export async function seedTestData(db) {
  // Create test users
  const users = await db.users.createMany([
    { email: 'admin@test.com', role: 'admin' },
    { email: 'user@test.com', role: 'user' },
  ]);

  // Create related data
  await db.posts.createMany([
    { title: 'Test Post', authorId: users[0].id },
  ]);

  return { users };
}
```

---

## CI/CD Integration

### GitHub Actions

```yaml
# .github/workflows/test.yml
name: Tests

on: [push, pull_request]

jobs:
  unit-tests:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
      - run: npm ci
      - run: npm run test:unit -- --coverage
      - uses: codecov/codecov-action@v4

  integration-tests:
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:15
        env:
          POSTGRES_PASSWORD: test
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
      - run: npm ci
      - run: npm run test:integration
        env:
          DATABASE_URL: postgresql://postgres:test@localhost:5432/test

  e2e-tests:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
      - run: npm ci
      - run: npx playwright install --with-deps
      - run: npm run build
      - run: npm run test:e2e
      - uses: actions/upload-artifact@v4
        if: failure()
        with:
          name: playwright-report
          path: playwright-report/
```

---

## Testing Tools Reference

### Recommended Stack

| Category | Tool | Use Case |
|----------|------|----------|
| Unit Testing | Jest / Vitest | Fast unit tests |
| Component | Testing Library | React/Vue components |
| E2E | Playwright | Cross-browser E2E |
| API | Supertest | HTTP endpoint testing |
| Mocking | MSW | API mocking |
| Coverage | Istanbul/c8 | Code coverage |
| Visual | Percy / Chromatic | Visual regression |

### Configuration Files

```javascript
// jest.config.js
module.exports = {
  preset: 'ts-jest',
  testEnvironment: 'node',
  roots: ['<rootDir>/src'],
  testMatch: ['**/*.test.ts', '**/*.spec.ts'],
  collectCoverageFrom: [
    'src/**/*.ts',
    '!src/**/*.d.ts',
    '!src/**/index.ts'
  ],
  coverageThreshold: {
    global: {
      branches: 80,
      functions: 80,
      lines: 80,
      statements: 80
    }
  }
};
```

---

## Resources

- [Testing Library Docs](https://testing-library.com/docs/)
- [Playwright Docs](https://playwright.dev/docs/intro)
- [Jest Best Practices](https://github.com/goldbergyoni/javascript-testing-best-practices)
- [Testing Trophy](https://kentcdodds.com/blog/the-testing-trophy-and-testing-classifications)

---

**Document Version:** 1.0
**Maintained by:** QA Team
**Review Cycle:** Quarterly

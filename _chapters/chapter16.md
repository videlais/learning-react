---
title: "Testing React Applications"
order: 16
chapter_number: 16
layout: chapter
---

## Objectives

In this chapter, readers will:

- **Write** effective unit tests for React components with Jest
- **Test** user interactions with React Testing Library
- **Implement** end-to-end tests with Playwright
- **Achieve** high code coverage with meaningful tests
- **Follow** testing best practices for maintainable test suites

## Chapter Outline

- [Objectives](#objectives)
- [Chapter Outline](#chapter-outline)
- [Introduction to React Testing](#introduction-to-react-testing)
- [Testing Setup](#testing-setup)
  - [Jest Configuration](#jest-configuration)
    - [Vite Project Setup](#vite-project-setup)
    - [Next.js Project Setup](#nextjs-project-setup)
  - [React Testing Library](#react-testing-library)
  - [TypeScript Support](#typescript-support)
- [Component Testing](#component-testing)
  - [Basic Component Tests](#basic-component-tests)
  - [Testing Props and State](#testing-props-and-state)
  - [Testing Events](#testing-events)
  - [Testing Hooks](#testing-hooks)
- [Advanced Testing Patterns](#advanced-testing-patterns)
  - [Mocking](#mocking)
    - [Mocking Modules](#mocking-modules)
    - [Mocking Fetch](#mocking-fetch)
  - [Async Testing](#async-testing)
  - [Context Testing](#context-testing)
  - [Router Testing](#router-testing)
- [Integration Testing](#integration-testing)
  - [Form Testing](#form-testing)
  - [API Integration](#api-integration)
  - [Authentication Flow](#authentication-flow)
- [End-to-End Testing](#end-to-end-testing)
  - [Playwright Setup](#playwright-setup)
  - [Writing E2E Tests](#writing-e2e-tests)
  - [Visual Regression](#visual-regression)
- [Test Coverage](#test-coverage)
  - [Measuring Coverage](#measuring-coverage)
  - [Coverage Goals](#coverage-goals)
  - [Coverage Reports](#coverage-reports)
- [Best Practices](#best-practices)
  - [Testing Guidelines](#testing-guidelines)
  - [Query Priority](#query-priority)
  - [Test Organization](#test-organization)
- [Summary](#summary)
  - [2025 Testing Standards](#2025-testing-standards)
  - [Next Steps](#next-steps)

## Introduction to React Testing

**2025 Testing Landscape:**

- **Jest**: Industry standard test runner
- **React Testing Library**: User-centric component testing
- **Playwright**: Modern E2E testing (replaces Cypress for many)
- **Vitest**: Fast alternative to Jest (Vite-native)
- **Testing Library User Event**: Realistic user interactions

**Testing Philosophy:**

> "The more your tests resemble the way your software is used, the more confidence they can give you." - Kent C. Dodds

**Test Types:**

1. **Unit Tests**: Individual components and functions
2. **Integration Tests**: Multiple components working together
3. **E2E Tests**: Full user flows in real browser

## Testing Setup

### Jest Configuration

#### Vite Project Setup

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom @testing-library/user-event jsdom
```

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config';
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [react()],
  test: {
    globals: true,
    environment: 'jsdom',
    setupFiles: './src/test/setup.ts',
    css: true,
    coverage: {
      provider: 'v8',
      reporter: ['text', 'json', 'html'],
      exclude: [
        'node_modules/',
        'src/test/',
      ],
    },
  },
});
```

```typescript
// src/test/setup.ts
import { expect, afterEach } from 'vitest';
import { cleanup } from '@testing-library/react';
import * as matchers from '@testing-library/jest-dom/matchers';

expect.extend(matchers);

afterEach(() => {
  cleanup();
});
```

#### Next.js Project Setup

```bash
npm install -D jest jest-environment-jsdom @testing-library/react @testing-library/jest-dom @testing-library/user-event
```

```javascript
// jest.config.js
const nextJest = require('next/jest');

const createJestConfig = nextJest({
  dir: './',
});

const customJestConfig = {
  setupFilesAfterEnv: ['<rootDir>/jest.setup.js'],
  testEnvironment: 'jest-environment-jsdom',
  moduleNameMapper: {
    '^@/(.*)$': '<rootDir>/src/$1',
  },
  collectCoverageFrom: [
    'src/**/*.{js,jsx,ts,tsx}',
    '!src/**/*.d.ts',
    '!src/**/*.stories.{js,jsx,ts,tsx}',
  ],
};

module.exports = createJestConfig(customJestConfig);
```

```typescript
// jest.setup.js
import '@testing-library/jest-dom';
```

### React Testing Library

```typescript
// src/test/utils.tsx
import { render, RenderOptions } from '@testing-library/react';
import { ReactElement } from 'react';

// Custom render function with providers
function customRender(
  ui: ReactElement,
  options?: Omit<RenderOptions, 'wrapper'>
) {
  return render(ui, { ...options });
}

export * from '@testing-library/react';
export { customRender as render };
```

### TypeScript Support

```json
// tsconfig.json
{
  "compilerOptions": {
    "types": ["vitest/globals", "@testing-library/jest-dom"]
  }
}
```

## Component Testing

### Basic Component Tests

```typescript
// src/components/Button.tsx
interface ButtonProps {
  children: React.ReactNode;
  onClick?: () => void;
  variant?: 'primary' | 'secondary';
  disabled?: boolean;
}

export function Button({
  children,
  onClick,
  variant = 'primary',
  disabled = false,
}: ButtonProps) {
  return (
    <button
      onClick={onClick}
      disabled={disabled}
      className={`btn btn-${variant}`}
    >
      {children}
    </button>
  );
}
```

```typescript
// src/components/Button.test.tsx
import { describe, it, expect, vi } from 'vitest';
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { Button } from './Button';

describe('Button', () => {
  it('renders with text', () => {
    render(<Button>Click me</Button>);
    
    expect(screen.getByRole('button', { name: /click me/i })).toBeInTheDocument();
  });

  it('calls onClick when clicked', async () => {
    const handleClick = vi.fn();
    const user = userEvent.setup();
    
    render(<Button onClick={handleClick}>Click me</Button>);
    
    await user.click(screen.getByRole('button'));
    
    expect(handleClick).toHaveBeenCalledTimes(1);
  });

  it('applies correct variant class', () => {
    render(<Button variant="secondary">Secondary</Button>);
    
    const button = screen.getByRole('button');
    expect(button).toHaveClass('btn-secondary');
  });

  it('is disabled when disabled prop is true', () => {
    render(<Button disabled>Disabled</Button>);
    
    expect(screen.getByRole('button')).toBeDisabled();
  });

  it('does not call onClick when disabled', async () => {
    const handleClick = vi.fn();
    const user = userEvent.setup();
    
    render(<Button onClick={handleClick} disabled>Disabled</Button>);
    
    await user.click(screen.getByRole('button'));
    
    expect(handleClick).not.toHaveBeenCalled();
  });
});
```

### Testing Props and State

```typescript
// src/components/Counter.tsx
import { useState } from 'react';

interface CounterProps {
  initialCount?: number;
  max?: number;
}

export function Counter({ initialCount = 0, max = 10 }: CounterProps) {
  const [count, setCount] = useState(initialCount);

  const increment = () => {
    if (count < max) {
      setCount(count + 1);
    }
  };

  const decrement = () => {
    if (count > 0) {
      setCount(count - 1);
    }
  };

  return (
    <div>
      <p>Count: <span data-testid="count">{count}</span></p>
      <button onClick={decrement}>Decrement</button>
      <button onClick={increment}>Increment</button>
    </div>
  );
}
```

```typescript
// src/components/Counter.test.tsx
import { describe, it, expect } from 'vitest';
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { Counter } from './Counter';

describe('Counter', () => {
  it('renders with default count', () => {
    render(<Counter />);
    
    expect(screen.getByTestId('count')).toHaveTextContent('0');
  });

  it('renders with initial count', () => {
    render(<Counter initialCount={5} />);
    
    expect(screen.getByTestId('count')).toHaveTextContent('5');
  });

  it('increments count when increment button clicked', async () => {
    const user = userEvent.setup();
    render(<Counter />);
    
    await user.click(screen.getByText(/increment/i));
    
    expect(screen.getByTestId('count')).toHaveTextContent('1');
  });

  it('decrements count when decrement button clicked', async () => {
    const user = userEvent.setup();
    render(<Counter initialCount={5} />);
    
    await user.click(screen.getByText(/decrement/i));
    
    expect(screen.getByTestId('count')).toHaveTextContent('4');
  });

  it('does not increment beyond max', async () => {
    const user = userEvent.setup();
    render(<Counter initialCount={10} max={10} />);
    
    await user.click(screen.getByText(/increment/i));
    
    expect(screen.getByTestId('count')).toHaveTextContent('10');
  });

  it('does not decrement below zero', async () => {
    const user = userEvent.setup();
    render(<Counter initialCount={0} />);
    
    await user.click(screen.getByText(/decrement/i));
    
    expect(screen.getByTestId('count')).toHaveTextContent('0');
  });
});
```

### Testing Events

```typescript
// src/components/SearchInput.tsx
interface SearchInputProps {
  onSearch: (query: string) => void;
  placeholder?: string;
}

export function SearchInput({ onSearch, placeholder }: SearchInputProps) {
  const [value, setValue] = useState('');

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    onSearch(value);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="text"
        value={value}
        onChange={(e) => setValue(e.target.value)}
        placeholder={placeholder}
        aria-label="Search"
      />
      <button type="submit">Search</button>
    </form>
  );
}
```

```typescript
// src/components/SearchInput.test.tsx
import { describe, it, expect, vi } from 'vitest';
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { SearchInput } from './SearchInput';

describe('SearchInput', () => {
  it('calls onSearch with query when form submitted', async () => {
    const handleSearch = vi.fn();
    const user = userEvent.setup();
    
    render(<SearchInput onSearch={handleSearch} />);
    
    const input = screen.getByRole('textbox', { name: /search/i });
    await user.type(input, 'React testing');
    await user.click(screen.getByRole('button', { name: /search/i }));
    
    expect(handleSearch).toHaveBeenCalledWith('React testing');
  });

  it('updates input value when typing', async () => {
    const handleSearch = vi.fn();
    const user = userEvent.setup();
    
    render(<SearchInput onSearch={handleSearch} />);
    
    const input = screen.getByRole('textbox');
    await user.type(input, 'test query');
    
    expect(input).toHaveValue('test query');
  });

  it('renders with placeholder', () => {
    render(<SearchInput onSearch={vi.fn()} placeholder="Search items..." />);
    
    expect(screen.getByPlaceholderText(/search items/i)).toBeInTheDocument();
  });
});
```

### Testing Hooks

```typescript
// src/hooks/useCounter.ts
import { useState, useCallback } from 'react';

export function useCounter(initialValue = 0) {
  const [count, setCount] = useState(initialValue);

  const increment = useCallback(() => {
    setCount((c) => c + 1);
  }, []);

  const decrement = useCallback(() => {
    setCount((c) => c - 1);
  }, []);

  const reset = useCallback(() => {
    setCount(initialValue);
  }, [initialValue]);

  return { count, increment, decrement, reset };
}
```

```typescript
// src/hooks/useCounter.test.ts
import { renderHook, act } from '@testing-library/react';
import { describe, it, expect } from 'vitest';
import { useCounter } from './useCounter';

describe('useCounter', () => {
  it('initializes with default value', () => {
    const { result } = renderHook(() => useCounter());
    
    expect(result.current.count).toBe(0);
  });

  it('initializes with custom value', () => {
    const { result } = renderHook(() => useCounter(10));
    
    expect(result.current.count).toBe(10);
  });

  it('increments count', () => {
    const { result } = renderHook(() => useCounter());
    
    act(() => {
      result.current.increment();
    });
    
    expect(result.current.count).toBe(1);
  });

  it('decrements count', () => {
    const { result } = renderHook(() => useCounter(5));
    
    act(() => {
      result.current.decrement();
    });
    
    expect(result.current.count).toBe(4);
  });

  it('resets to initial value', () => {
    const { result } = renderHook(() => useCounter(10));
    
    act(() => {
      result.current.increment();
      result.current.increment();
    });
    
    expect(result.current.count).toBe(12);
    
    act(() => {
      result.current.reset();
    });
    
    expect(result.current.count).toBe(10);
  });
});
```

## Advanced Testing Patterns

### Mocking

#### Mocking Modules

```typescript
// src/services/api.ts
export async function fetchUser(id: string) {
  const response = await fetch(`/api/users/${id}`);
  return response.json();
}
```

```typescript
// src/components/UserProfile.test.tsx
import { describe, it, expect, vi, beforeEach } from 'vitest';
import { render, screen, waitFor } from '@testing-library/react';
import { UserProfile } from './UserProfile';
import * as api from '../services/api';

// Mock the entire module
vi.mock('../services/api');

describe('UserProfile', () => {
  beforeEach(() => {
    vi.clearAllMocks();
  });

  it('displays user data when loaded', async () => {
    const mockUser = { id: '1', name: 'John Doe', email: 'john@example.com' };
    
    vi.spyOn(api, 'fetchUser').mockResolvedValue(mockUser);
    
    render(<UserProfile userId="1" />);
    
    await waitFor(() => {
      expect(screen.getByText('John Doe')).toBeInTheDocument();
    });
    
    expect(api.fetchUser).toHaveBeenCalledWith('1');
  });

  it('displays error when fetch fails', async () => {
    vi.spyOn(api, 'fetchUser').mockRejectedValue(new Error('Failed to fetch'));
    
    render(<UserProfile userId="1" />);
    
    await waitFor(() => {
      expect(screen.getByText(/error/i)).toBeInTheDocument();
    });
  });
});
```

#### Mocking Fetch

```typescript
// Global fetch mock
beforeEach(() => {
  global.fetch = vi.fn();
});

afterEach(() => {
  vi.restoreAllMocks();
});

it('fetches and displays data', async () => {
  const mockData = { name: 'Test Item' };
  
  (global.fetch as any).mockResolvedValueOnce({
    ok: true,
    json: async () => mockData,
  });
  
  render(<DataComponent />);
  
  await waitFor(() => {
    expect(screen.getByText('Test Item')).toBeInTheDocument();
  });
  
  expect(fetch).toHaveBeenCalledWith('/api/data');
});
```

### Async Testing

```typescript
// src/components/AsyncData.tsx
import { useState, useEffect } from 'react';

export function AsyncData() {
  const [data, setData] = useState<any>(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    async function fetchData() {
      try {
        const response = await fetch('/api/data');
        if (!response.ok) throw new Error('Failed to fetch');
        const json = await response.json();
        setData(json);
      } catch (err) {
        setError(err instanceof Error ? err.message : 'Unknown error');
      } finally {
        setLoading(false);
      }
    }

    fetchData();
  }, []);

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;
  if (!data) return <div>No data</div>;

  return <div>Data: {data.value}</div>;
}
```

```typescript
// src/components/AsyncData.test.tsx
import { describe, it, expect, vi } from 'vitest';
import { render, screen, waitFor } from '@testing-library/react';
import { AsyncData } from './AsyncData';

describe('AsyncData', () => {
  it('shows loading state initially', () => {
    global.fetch = vi.fn(() => new Promise(() => {}));
    
    render(<AsyncData />);
    
    expect(screen.getByText(/loading/i)).toBeInTheDocument();
  });

  it('displays data after successful fetch', async () => {
    global.fetch = vi.fn().mockResolvedValueOnce({
      ok: true,
      json: async () => ({ value: 'Test Value' }),
    });
    
    render(<AsyncData />);
    
    await waitFor(() => {
      expect(screen.getByText(/data: test value/i)).toBeInTheDocument();
    });
  });

  it('displays error when fetch fails', async () => {
    global.fetch = vi.fn().mockRejectedValueOnce(new Error('Network error'));
    
    render(<AsyncData />);
    
    await waitFor(() => {
      expect(screen.getByText(/error: network error/i)).toBeInTheDocument();
    });
  });

  it('displays error when response is not ok', async () => {
    global.fetch = vi.fn().mockResolvedValueOnce({
      ok: false,
    });
    
    render(<AsyncData />);
    
    await waitFor(() => {
      expect(screen.getByText(/error/i)).toBeInTheDocument();
    });
  });
});
```

### Context Testing

```typescript
// src/test/utils.tsx
import { render, RenderOptions } from '@testing-library/react';
import { ReactElement } from 'react';
import { AuthProvider } from '../contexts/AuthContext';

interface CustomRenderOptions extends RenderOptions {
  user?: any;
}

function customRender(
  ui: ReactElement,
  { user, ...options }: CustomRenderOptions = {}
) {
  const Wrapper = ({ children }: { children: React.ReactNode }) => (
    <AuthProvider initialUser={user}>
      {children}
    </AuthProvider>
  );

  return render(ui, { wrapper: Wrapper, ...options });
}

export * from '@testing-library/react';
export { customRender as render };
```

```typescript
// src/components/ProtectedContent.test.tsx
import { describe, it, expect } from 'vitest';
import { render, screen } from '../test/utils';
import { ProtectedContent } from './ProtectedContent';

describe('ProtectedContent', () => {
  it('shows content when user is authenticated', () => {
    const mockUser = { id: '1', name: 'John' };
    
    render(<ProtectedContent />, { user: mockUser });
    
    expect(screen.getByText(/protected content/i)).toBeInTheDocument();
  });

  it('shows login prompt when user is not authenticated', () => {
    render(<ProtectedContent />, { user: null });
    
    expect(screen.getByText(/please log in/i)).toBeInTheDocument();
  });
});
```

### Router Testing

```typescript
// src/test/utils.tsx (React Router)
import { render, RenderOptions } from '@testing-library/react';
import { BrowserRouter } from 'react-router-dom';
import { ReactElement } from 'react';

function customRender(
  ui: ReactElement,
  { route = '/', ...options }: RenderOptions & { route?: string } = {}
) {
  window.history.pushState({}, 'Test page', route);

  const Wrapper = ({ children }: { children: React.ReactNode }) => (
    <BrowserRouter>{children}</BrowserRouter>
  );

  return render(ui, { wrapper: Wrapper, ...options });
}

export * from '@testing-library/react';
export { customRender as render };
```

```typescript
// src/components/ProductPage.test.tsx
import { describe, it, expect } from 'vitest';
import { render, screen } from '../test/utils';
import { Route, Routes } from 'react-router-dom';
import { ProductPage } from './ProductPage';

describe('ProductPage', () => {
  it('displays product based on URL parameter', () => {
    render(
      <Routes>
        <Route path="/products/:id" element={<ProductPage />} />
      </Routes>,
      { route: '/products/123' }
    );
    
    // Test component behavior with product ID 123
  });
});
```

## Integration Testing

### Form Testing

```typescript
// src/components/LoginForm.test.tsx
import { describe, it, expect, vi } from 'vitest';
import { render, screen, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { LoginForm } from './LoginForm';

describe('LoginForm', () => {
  it('submits form with valid credentials', async () => {
    const handleLogin = vi.fn();
    const user = userEvent.setup();
    
    render(<LoginForm onLogin={handleLogin} />);
    
    await user.type(screen.getByLabelText(/email/i), 'user@example.com');
    await user.type(screen.getByLabelText(/password/i), 'password123');
    await user.click(screen.getByRole('button', { name: /log in/i }));
    
    await waitFor(() => {
      expect(handleLogin).toHaveBeenCalledWith({
        email: 'user@example.com',
        password: 'password123',
      });
    });
  });

  it('shows validation errors for empty fields', async () => {
    const user = userEvent.setup();
    
    render(<LoginForm onLogin={vi.fn()} />);
    
    await user.click(screen.getByRole('button', { name: /log in/i }));
    
    expect(await screen.findByText(/email is required/i)).toBeInTheDocument();
    expect(await screen.findByText(/password is required/i)).toBeInTheDocument();
  });

  it('shows error message for invalid email', async () => {
    const user = userEvent.setup();
    
    render(<LoginForm onLogin={vi.fn()} />);
    
    await user.type(screen.getByLabelText(/email/i), 'invalid-email');
    await user.click(screen.getByRole('button', { name: /log in/i }));
    
    expect(await screen.findByText(/invalid email/i)).toBeInTheDocument();
  });

  it('disables submit button while submitting', async () => {
    const handleLogin = vi.fn(() => new Promise((resolve) => setTimeout(resolve, 100)));
    const user = userEvent.setup();
    
    render(<LoginForm onLogin={handleLogin} />);
    
    await user.type(screen.getByLabelText(/email/i), 'user@example.com');
    await user.type(screen.getByLabelText(/password/i), 'password123');
    
    const submitButton = screen.getByRole('button', { name: /log in/i });
    await user.click(submitButton);
    
    expect(submitButton).toBeDisabled();
    
    await waitFor(() => {
      expect(submitButton).not.toBeDisabled();
    });
  });
});
```

### API Integration

```typescript
// src/components/UserList.test.tsx
import { describe, it, expect, beforeEach } from 'vitest';
import { render, screen, waitFor } from '@testing-library/react';
import { rest } from 'msw';
import { setupServer } from 'msw/node';
import { UserList } from './UserList';

const mockUsers = [
  { id: '1', name: 'John Doe', email: 'john@example.com' },
  { id: '2', name: 'Jane Smith', email: 'jane@example.com' },
];

const server = setupServer(
  rest.get('/api/users', (req, res, ctx) => {
    return res(ctx.json(mockUsers));
  })
);

beforeAll(() => server.listen());
afterEach(() => server.resetHandlers());
afterAll(() => server.close());

describe('UserList', () => {
  it('fetches and displays users', async () => {
    render(<UserList />);
    
    expect(screen.getByText(/loading/i)).toBeInTheDocument();
    
    await waitFor(() => {
      expect(screen.getByText('John Doe')).toBeInTheDocument();
      expect(screen.getByText('Jane Smith')).toBeInTheDocument();
    });
  });

  it('handles fetch error', async () => {
    server.use(
      rest.get('/api/users', (req, res, ctx) => {
        return res(ctx.status(500));
      })
    );
    
    render(<UserList />);
    
    await waitFor(() => {
      expect(screen.getByText(/error/i)).toBeInTheDocument();
    });
  });

  it('handles empty user list', async () => {
    server.use(
      rest.get('/api/users', (req, res, ctx) => {
        return res(ctx.json([]));
      })
    );
    
    render(<UserList />);
    
    await waitFor(() => {
      expect(screen.getByText(/no users found/i)).toBeInTheDocument();
    });
  });
});
```

### Authentication Flow

```typescript
// src/flows/authentication.test.tsx
import { describe, it, expect, vi } from 'vitest';
import { render, screen, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { App } from '../App';

describe('Authentication Flow', () => {
  it('allows user to log in and access protected content', async () => {
    const user = userEvent.setup();
    
    render(<App />);
    
    // Initially not logged in
    expect(screen.getByText(/log in/i)).toBeInTheDocument();
    
    // Navigate to login page
    await user.click(screen.getByRole('link', { name: /log in/i }));
    
    // Fill in login form
    await user.type(screen.getByLabelText(/email/i), 'user@example.com');
    await user.type(screen.getByLabelText(/password/i), 'password123');
    await user.click(screen.getByRole('button', { name: /log in/i }));
    
    // Should be redirected to dashboard
    await waitFor(() => {
      expect(screen.getByText(/dashboard/i)).toBeInTheDocument();
    });
    
    // Can access protected content
    await user.click(screen.getByRole('link', { name: /profile/i }));
    expect(screen.getByText(/profile page/i)).toBeInTheDocument();
    
    // Can log out
    await user.click(screen.getByRole('button', { name: /log out/i }));
    expect(screen.getByText(/log in/i)).toBeInTheDocument();
  });
});
```

## End-to-End Testing

### Playwright Setup

```bash
npm install -D @playwright/test
npx playwright install
```

```typescript
// playwright.config.ts
import { defineConfig, devices } from '@playwright/test';

export default defineConfig({
  testDir: './e2e',
  fullyParallel: true,
  forbidOnly: !!process.env.CI,
  retries: process.env.CI ? 2 : 0,
  workers: process.env.CI ? 1 : undefined,
  reporter: 'html',
  use: {
    baseURL: 'http://localhost:5173',
    trace: 'on-first-retry',
    screenshot: 'only-on-failure',
  },
  projects: [
    {
      name: 'chromium',
      use: { ...devices['Desktop Chrome'] },
    },
    {
      name: 'firefox',
      use: { ...devices['Desktop Firefox'] },
    },
    {
      name: 'webkit',
      use: { ...devices['Desktop Safari'] },
    },
  ],
  webServer: {
    command: 'npm run dev',
    url: 'http://localhost:5173',
    reuseExistingServer: !process.env.CI,
  },
});
```

### Writing E2E Tests

```typescript
// e2e/authentication.spec.ts
import { test, expect } from '@playwright/test';

test.describe('Authentication', () => {
  test('user can log in', async ({ page }) => {
    await page.goto('/');
    
    // Click login link
    await page.getByRole('link', { name: /log in/i }).click();
    await expect(page).toHaveURL('/login');
    
    // Fill in form
    await page.getByLabel(/email/i).fill('user@example.com');
    await page.getByLabel(/password/i).fill('password123');
    await page.getByRole('button', { name: /log in/i }).click();
    
    // Should redirect to dashboard
    await expect(page).toHaveURL('/dashboard');
    await expect(page.getByText(/welcome/i)).toBeVisible();
  });

  test('shows error for invalid credentials', async ({ page }) => {
    await page.goto('/login');
    
    await page.getByLabel(/email/i).fill('wrong@example.com');
    await page.getByLabel(/password/i).fill('wrongpassword');
    await page.getByRole('button', { name: /log in/i }).click();
    
    await expect(page.getByText(/invalid credentials/i)).toBeVisible();
    await expect(page).toHaveURL('/login');
  });

  test('protects routes when not authenticated', async ({ page }) => {
    await page.goto('/dashboard');
    
    // Should redirect to login
    await expect(page).toHaveURL('/login');
  });
});
```

```typescript
// e2e/shopping-cart.spec.ts
import { test, expect } from '@playwright/test';

test.describe('Shopping Cart', () => {
  test('user can add items to cart and checkout', async ({ page }) => {
    await page.goto('/products');
    
    // Add first product to cart
    await page.getByTestId('product-1').getByRole('button', { name: /add to cart/i }).click();
    
    // Verify cart badge updated
    await expect(page.getByTestId('cart-badge')).toHaveText('1');
    
    // Add second product
    await page.getByTestId('product-2').getByRole('button', { name: /add to cart/i }).click();
    await expect(page.getByTestId('cart-badge')).toHaveText('2');
    
    // View cart
    await page.getByRole('link', { name: /cart/i }).click();
    await expect(page).toHaveURL('/cart');
    
    // Verify items in cart
    await expect(page.getByTestId('cart-item')).toHaveCount(2);
    
    // Proceed to checkout
    await page.getByRole('button', { name: /checkout/i }).click();
    await expect(page).toHaveURL('/checkout');
    
    // Fill checkout form
    await page.getByLabel(/name/i).fill('John Doe');
    await page.getByLabel(/email/i).fill('john@example.com');
    await page.getByLabel(/address/i).fill('123 Main St');
    
    // Submit order
    await page.getByRole('button', { name: /place order/i }).click();
    
    // Verify success
    await expect(page.getByText(/order confirmed/i)).toBeVisible();
    await expect(page.getByTestId('cart-badge')).toHaveText('0');
  });
});
```

### Visual Regression

```typescript
// e2e/visual.spec.ts
import { test, expect } from '@playwright/test';

test.describe('Visual Regression', () => {
  test('homepage looks correct', async ({ page }) => {
    await page.goto('/');
    await expect(page).toHaveScreenshot('homepage.png');
  });

  test('product page looks correct', async ({ page }) => {
    await page.goto('/products/123');
    await expect(page).toHaveScreenshot('product-page.png');
  });

  test('mobile homepage looks correct', async ({ page }) => {
    await page.setViewportSize({ width: 375, height: 667 });
    await page.goto('/');
    await expect(page).toHaveScreenshot('homepage-mobile.png');
  });
});
```

## Test Coverage

### Measuring Coverage

```bash
# Vitest
npm run test -- --coverage

# Jest
npm run test -- --coverage
```

```json
// package.json
{
  "scripts": {
    "test": "vitest",
    "test:coverage": "vitest --coverage",
    "test:ui": "vitest --ui"
  }
}
```

### Coverage Goals

**Recommended Coverage Targets:**

- **Statements**: 80%+
- **Branches**: 75%+
- **Functions**: 80%+
- **Lines**: 80%+

**Focus on:**

- Business logic
- User interactions
- Error handling
- Edge cases

**Less critical:**

- UI/styling components
- Third-party integrations
- Configuration files

### Coverage Reports

```typescript
// vitest.config.ts
export default defineConfig({
  test: {
    coverage: {
      provider: 'v8',
      reporter: ['text', 'json', 'html'],
      exclude: [
        'node_modules/',
        'src/test/',
        '**/*.d.ts',
        '**/*.config.*',
        '**/mockData',
      ],
      thresholds: {
        statements: 80,
        branches: 75,
        functions: 80,
        lines: 80,
      },
    },
  },
});
```

## Best Practices

### Testing Guidelines

**✅ Do:**

1. **Test behavior, not implementation**
2. **Use accessible queries** (`getByRole`, `getByLabelText`)
3. **Test user interactions** with `@testing-library/user-event`
4. **Write descriptive test names**
5. **Keep tests focused** - one concept per test
6. **Use proper async/await** for async operations
7. **Mock external dependencies**
8. **Test error states**
9. **Follow AAA pattern** (Arrange, Act, Assert)

**❌ Don't:**

1. **Don't test implementation details**
2. **Don't use `getByTestId` unless necessary**
3. **Don't write brittle selectors**
4. **Don't skip cleanup**
5. **Don't forget accessibility**
6. **Don't have flaky tests**
7. **Don't test third-party libraries**

### Query Priority

```typescript
// ✅ Best: Accessible to everyone
screen.getByRole('button', { name: /submit/i });
screen.getByLabelText(/email/i);
screen.getByPlaceholderText(/search/i);
screen.getByText(/welcome/i);

// ⚠️ Use sparingly
screen.getByDisplayValue('value');
screen.getByAltText('image');
screen.getByTitle('title');

// ❌ Last resort only
screen.getByTestId('submit-button');
```

### Test Organization

```tree
src/
├── components/
│   ├── Button/
│   │   ├── Button.tsx
│   │   ├── Button.test.tsx
│   │   └── index.ts
│   └── Form/
│       ├── Form.tsx
│       ├── Form.test.tsx
│       └── index.ts
├── hooks/
│   ├── useAuth.ts
│   └── useAuth.test.ts
└── test/
    ├── setup.ts
    ├── utils.tsx
    └── mockData/
```

## Summary

### 2025 Testing Standards

**Testing Stack:**

- **Unit/Integration**: Vitest + React Testing Library
- **E2E**: Playwright
- **API Mocking**: MSW (Mock Service Worker)
- **User Interactions**: @testing-library/user-event

**Key Principles:**

1. Test user behavior, not implementation
2. Make tests maintainable and readable
3. Balance unit, integration, and E2E tests
4. Automate testing in CI/CD
5. Maintain good coverage of critical paths

### Next Steps

Congratulations! You've learned React testing. Continue with:

- **Chapter 17**: Deployment and Production

Testing ensures your application works correctly - invest time in good tests!

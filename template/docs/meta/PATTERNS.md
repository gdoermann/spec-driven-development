# Reusable Patterns Library

This document contains tested, reusable patterns extracted from successful implementations. Each pattern includes context, implementation, and usage notes.

## Database Patterns

### Soft Delete Pattern
**When to Use**: When you need to maintain data history and support recovery

```sql
-- Add to any table requiring soft delete
ALTER TABLE table_name ADD COLUMN deleted_at TIMESTAMP;
ALTER TABLE table_name ADD COLUMN deleted_by UUID REFERENCES users(id);

-- Create view for active records
CREATE VIEW active_table_name AS
SELECT * FROM table_name WHERE deleted_at IS NULL;

-- Soft delete function
CREATE FUNCTION soft_delete_table_name(record_id UUID, user_id UUID)
RETURNS VOID AS $$
BEGIN
    UPDATE table_name 
    SET deleted_at = NOW(), deleted_by = user_id
    WHERE id = record_id;
END;
$$ LANGUAGE plpgsql;
```

### Audit Trail Pattern
**When to Use**: When you need to track all changes to critical data

```sql
-- Audit table
CREATE TABLE table_name_audit (
    audit_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    record_id UUID NOT NULL,
    action VARCHAR(10) NOT NULL CHECK (action IN ('INSERT', 'UPDATE', 'DELETE')),
    changed_by UUID REFERENCES users(id),
    changed_at TIMESTAMP NOT NULL DEFAULT NOW(),
    old_data JSONB,
    new_data JSONB
);

-- Trigger function
CREATE FUNCTION audit_table_name_changes() RETURNS TRIGGER AS $$
BEGIN
    IF TG_OP = 'DELETE' THEN
        INSERT INTO table_name_audit(record_id, action, old_data)
        VALUES (OLD.id, TG_OP, to_jsonb(OLD));
        RETURN OLD;
    ELSIF TG_OP = 'UPDATE' THEN
        INSERT INTO table_name_audit(record_id, action, old_data, new_data)
        VALUES (NEW.id, TG_OP, to_jsonb(OLD), to_jsonb(NEW));
        RETURN NEW;
    ELSIF TG_OP = 'INSERT' THEN
        INSERT INTO table_name_audit(record_id, action, new_data)
        VALUES (NEW.id, TG_OP, to_jsonb(NEW));
        RETURN NEW;
    END IF;
END;
$$ LANGUAGE plpgsql;
```

## API Patterns

### Standard API Response Format
**When to Use**: For all API endpoints to ensure consistency

```typescript
interface ApiResponse<T> {
    success: boolean;
    data?: T;
    error?: {
        code: string;
        message: string;
        details?: any;
    };
    meta?: {
        timestamp: string;
        version: string;
        requestId: string;
    };
}

// Success response helper
export const successResponse = <T>(data: T, meta?: any): ApiResponse<T> => ({
    success: true,
    data,
    meta: {
        timestamp: new Date().toISOString(),
        version: process.env.API_VERSION || '1.0.0',
        requestId: context.requestId,
        ...meta
    }
});

// Error response helper
export const errorResponse = (
    code: string, 
    message: string, 
    details?: any
): ApiResponse<never> => ({
    success: false,
    error: { code, message, details },
    meta: {
        timestamp: new Date().toISOString(),
        version: process.env.API_VERSION || '1.0.0',
        requestId: context.requestId
    }
});
```

### Input Validation Pattern
**When to Use**: For all API endpoints accepting user input

```typescript
import { z } from 'zod';

// Define schema
const CreateUserSchema = z.object({
    email: z.string().email(),
    name: z.string().min(2).max(100),
    role: z.enum(['admin', 'user', 'guest']),
    metadata: z.record(z.unknown()).optional()
});

// Validation middleware
export const validateInput = <T>(schema: z.ZodSchema<T>) => {
    return async (req: Request, res: Response, next: NextFunction) => {
        try {
            const validated = await schema.parseAsync(req.body);
            req.validatedData = validated;
            next();
        } catch (error) {
            if (error instanceof z.ZodError) {
                return res.status(400).json(
                    errorResponse(
                        'VALIDATION_ERROR',
                        'Invalid input data',
                        error.errors
                    )
                );
            }
            next(error);
        }
    };
};

// Usage in route
router.post('/users', 
    validateInput(CreateUserSchema),
    async (req, res) => {
        const data = req.validatedData; // Type-safe!
        // Implementation
    }
);
```

### Pagination Pattern
**When to Use**: For any endpoint returning lists of items

```typescript
interface PaginationParams {
    page: number;
    limit: number;
    sortBy?: string;
    sortOrder?: 'asc' | 'desc';
}

interface PaginatedResponse<T> {
    items: T[];
    pagination: {
        page: number;
        limit: number;
        total: number;
        totalPages: number;
        hasNext: boolean;
        hasPrev: boolean;
    };
}

// Pagination helper
export const paginate = async <T>(
    query: any,
    params: PaginationParams
): Promise<PaginatedResponse<T>> => {
    const { page, limit, sortBy = 'created_at', sortOrder = 'desc' } = params;
    const offset = (page - 1) * limit;
    
    // Get total count
    const totalResult = await query.clone().count('* as count').first();
    const total = parseInt(totalResult.count);
    
    // Get paginated results
    const items = await query
        .orderBy(sortBy, sortOrder)
        .limit(limit)
        .offset(offset);
    
    return {
        items,
        pagination: {
            page,
            limit,
            total,
            totalPages: Math.ceil(total / limit),
            hasNext: page * limit < total,
            hasPrev: page > 1
        }
    };
};
```

## Frontend Patterns

### Form State Management Pattern
**When to Use**: For complex forms with validation

```typescript
import { useState, useCallback } from 'react';

interface FormState<T> {
    values: T;
    errors: Partial<Record<keyof T, string>>;
    touched: Partial<Record<keyof T, boolean>>;
    isSubmitting: boolean;
}

export function useForm<T>(
    initialValues: T,
    validate: (values: T) => Partial<Record<keyof T, string>>,
    onSubmit: (values: T) => Promise<void>
) {
    const [state, setState] = useState<FormState<T>>({
        values: initialValues,
        errors: {},
        touched: {},
        isSubmitting: false
    });

    const handleChange = useCallback((name: keyof T, value: any) => {
        setState(prev => ({
            ...prev,
            values: { ...prev.values, [name]: value },
            touched: { ...prev.touched, [name]: true }
        }));
    }, []);

    const handleBlur = useCallback((name: keyof T) => {
        setState(prev => ({
            ...prev,
            touched: { ...prev.touched, [name]: true },
            errors: validate(prev.values)
        }));
    }, [validate]);

    const handleSubmit = useCallback(async (e: React.FormEvent) => {
        e.preventDefault();
        const errors = validate(state.values);
        
        if (Object.keys(errors).length > 0) {
            setState(prev => ({ ...prev, errors }));
            return;
        }

        setState(prev => ({ ...prev, isSubmitting: true }));
        
        try {
            await onSubmit(state.values);
        } finally {
            setState(prev => ({ ...prev, isSubmitting: false }));
        }
    }, [state.values, validate, onSubmit]);

    return {
        values: state.values,
        errors: state.errors,
        touched: state.touched,
        isSubmitting: state.isSubmitting,
        handleChange,
        handleBlur,
        handleSubmit
    };
}
```

### Data Fetching Pattern
**When to Use**: For consistent data fetching with loading and error states

```typescript
import { useState, useEffect } from 'react';

interface FetchState<T> {
    data: T | null;
    loading: boolean;
    error: Error | null;
    refetch: () => void;
}

export function useFetch<T>(
    fetchFn: () => Promise<T>,
    dependencies: any[] = []
): FetchState<T> {
    const [state, setState] = useState<FetchState<T>>({
        data: null,
        loading: true,
        error: null,
        refetch: () => {}
    });

    const fetchData = async () => {
        setState(prev => ({ ...prev, loading: true, error: null }));
        
        try {
            const data = await fetchFn();
            setState(prev => ({ ...prev, data, loading: false }));
        } catch (error) {
            setState(prev => ({ 
                ...prev, 
                error: error as Error, 
                loading: false 
            }));
        }
    };

    useEffect(() => {
        fetchData();
    }, dependencies);

    return {
        ...state,
        refetch: fetchData
    };
}

// Usage
const { data, loading, error, refetch } = useFetch(
    () => api.getUsers({ page: currentPage }),
    [currentPage]
);
```

## Error Handling Patterns

### Global Error Boundary
**When to Use**: To catch and handle React component errors gracefully

```typescript
import React, { Component, ErrorInfo, ReactNode } from 'react';

interface Props {
    children: ReactNode;
    fallback?: (error: Error, reset: () => void) => ReactNode;
}

interface State {
    hasError: boolean;
    error: Error | null;
}

export class ErrorBoundary extends Component<Props, State> {
    state: State = {
        hasError: false,
        error: null
    };

    static getDerivedStateFromError(error: Error): State {
        return { hasError: true, error };
    }

    componentDidCatch(error: Error, errorInfo: ErrorInfo) {
        console.error('Error caught by boundary:', error, errorInfo);
        // Send to error tracking service
    }

    reset = () => {
        this.setState({ hasError: false, error: null });
    };

    render() {
        if (this.state.hasError && this.state.error) {
            if (this.props.fallback) {
                return this.props.fallback(this.state.error, this.reset);
            }
            
            return (
                <div className="error-boundary-default">
                    <h2>Something went wrong</h2>
                    <details>
                        <summary>Error details</summary>
                        <pre>{this.state.error.toString()}</pre>
                    </details>
                    <button onClick={this.reset}>Try again</button>
                </div>
            );
        }

        return this.props.children;
    }
}
```

### API Error Handler
**When to Use**: For consistent API error handling

```typescript
export class ApiError extends Error {
    constructor(
        public code: string,
        message: string,
        public status: number,
        public details?: any
    ) {
        super(message);
        this.name = 'ApiError';
    }
}

export const handleApiError = async (response: Response) => {
    if (!response.ok) {
        let errorData;
        try {
            errorData = await response.json();
        } catch {
            errorData = { message: response.statusText };
        }

        throw new ApiError(
            errorData.code || 'UNKNOWN_ERROR',
            errorData.message || 'An error occurred',
            response.status,
            errorData.details
        );
    }
    return response;
};

// Usage in API client
export const apiClient = {
    async get<T>(url: string): Promise<T> {
        const response = await fetch(url);
        await handleApiError(response);
        return response.json();
    },
    
    async post<T>(url: string, data: any): Promise<T> {
        const response = await fetch(url, {
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify(data)
        });
        await handleApiError(response);
        return response.json();
    }
};
```

## State Management Patterns

### Context with Reducer Pattern
**When to Use**: For complex state management without external libraries

```typescript
import React, { createContext, useContext, useReducer, ReactNode } from 'react';

// State type
interface AppState {
    user: User | null;
    theme: 'light' | 'dark';
    notifications: Notification[];
}

// Action types
type AppAction =
    | { type: 'SET_USER'; payload: User | null }
    | { type: 'SET_THEME'; payload: 'light' | 'dark' }
    | { type: 'ADD_NOTIFICATION'; payload: Notification }
    | { type: 'REMOVE_NOTIFICATION'; payload: string };

// Reducer
const appReducer = (state: AppState, action: AppAction): AppState => {
    switch (action.type) {
        case 'SET_USER':
            return { ...state, user: action.payload };
        case 'SET_THEME':
            return { ...state, theme: action.payload };
        case 'ADD_NOTIFICATION':
            return { 
                ...state, 
                notifications: [...state.notifications, action.payload] 
            };
        case 'REMOVE_NOTIFICATION':
            return {
                ...state,
                notifications: state.notifications.filter(
                    n => n.id !== action.payload
                )
            };
        default:
            return state;
    }
};

// Context
const AppContext = createContext<{
    state: AppState;
    dispatch: React.Dispatch<AppAction>;
} | null>(null);

// Provider
export const AppProvider: React.FC<{ children: ReactNode }> = ({ children }) => {
    const [state, dispatch] = useReducer(appReducer, {
        user: null,
        theme: 'light',
        notifications: []
    });

    return (
        <AppContext.Provider value={{ state, dispatch }}>
            {children}
        </AppContext.Provider>
    );
};

// Hook
export const useApp = () => {
    const context = useContext(AppContext);
    if (!context) {
        throw new Error('useApp must be used within AppProvider');
    }
    return context;
};

// Action creators
export const appActions = {
    setUser: (user: User | null) => ({ 
        type: 'SET_USER' as const, 
        payload: user 
    }),
    setTheme: (theme: 'light' | 'dark') => ({ 
        type: 'SET_THEME' as const, 
        payload: theme 
    }),
    addNotification: (notification: Notification) => ({ 
        type: 'ADD_NOTIFICATION' as const, 
        payload: notification 
    }),
    removeNotification: (id: string) => ({ 
        type: 'REMOVE_NOTIFICATION' as const, 
        payload: id 
    })
};
```

## Testing Patterns

### Test Data Factory Pattern
**When to Use**: To create consistent test data

```typescript
// Factory function
export const createTestUser = (overrides: Partial<User> = {}): User => ({
    id: 'test-user-id',
    email: 'test@example.com',
    name: 'Test User',
    role: 'user',
    createdAt: new Date('2024-01-01'),
    updatedAt: new Date('2024-01-01'),
    ...overrides
});

// Builder pattern for complex objects
export class TestUserBuilder {
    private user: User = createTestUser();

    withAdmin(): this {
        this.user.role = 'admin';
        return this;
    }

    withEmail(email: string): this {
        this.user.email = email;
        return this;
    }

    withMetadata(metadata: Record<string, any>): this {
        this.user.metadata = metadata;
        return this;
    }

    build(): User {
        return { ...this.user };
    }
}

// Usage
const adminUser = new TestUserBuilder()
    .withAdmin()
    .withEmail('admin@example.com')
    .build();
```

### API Mock Pattern
**When to Use**: For testing components that make API calls

```typescript
export const createMockApi = () => {
    const mocks: Map<string, any> = new Map();

    return {
        mock: (method: string, url: string, response: any, status = 200) => {
            const key = `${method}:${url}`;
            mocks.set(key, { response, status });
        },

        fetch: async (url: string, options: RequestInit = {}) => {
            const method = options.method || 'GET';
            const key = `${method}:${url}`;
            const mock = mocks.get(key);

            if (!mock) {
                throw new Error(`No mock found for ${key}`);
            }

            return {
                ok: mock.status >= 200 && mock.status < 300,
                status: mock.status,
                json: async () => mock.response
            };
        },

        clear: () => mocks.clear()
    };
};

// Usage in tests
const mockApi = createMockApi();
mockApi.mock('GET', '/api/users', { users: [createTestUser()] });

// Inject into component/service
const users = await userService.getUsers(mockApi.fetch);
```

## Performance Patterns

### Debounce Pattern
**When to Use**: For search inputs, autosave, or any rapid fire events

```typescript
export function debounce<T extends (...args: any[]) => any>(
    func: T,
    wait: number
): (...args: Parameters<T>) => void {
    let timeout: NodeJS.Timeout;

    return (...args: Parameters<T>) => {
        clearTimeout(timeout);
        timeout = setTimeout(() => func(...args), wait);
    };
}

// React hook version
export function useDebounce<T>(value: T, delay: number): T {
    const [debouncedValue, setDebouncedValue] = useState(value);

    useEffect(() => {
        const handler = setTimeout(() => {
            setDebouncedValue(value);
        }, delay);

        return () => {
            clearTimeout(handler);
        };
    }, [value, delay]);

    return debouncedValue;
}

// Usage
const SearchComponent = () => {
    const [searchTerm, setSearchTerm] = useState('');
    const debouncedSearchTerm = useDebounce(searchTerm, 300);

    useEffect(() => {
        if (debouncedSearchTerm) {
            // Perform search
        }
    }, [debouncedSearchTerm]);
};
```

### Memoization Pattern
**When to Use**: For expensive computations or to prevent unnecessary re-renders

```typescript
// Generic memoization
export function memoize<T extends (...args: any[]) => any>(fn: T): T {
    const cache = new Map();

    return ((...args: Parameters<T>) => {
        const key = JSON.stringify(args);
        
        if (cache.has(key)) {
            return cache.get(key);
        }

        const result = fn(...args);
        cache.set(key, result);
        return result;
    }) as T;
}

// React component memoization
export const ExpensiveComponent = React.memo(
    ({ data, onAction }: Props) => {
        // Expensive render
        return <div>...</div>;
    },
    (prevProps, nextProps) => {
        // Custom comparison
        return (
            prevProps.data.id === nextProps.data.id &&
            prevProps.data.version === nextProps.data.version
        );
    }
);

// Selector memoization
export const useExpensiveComputation = (data: Data[]) => {
    return useMemo(() => {
        // Expensive computation
        return data.reduce((acc, item) => {
            // Complex logic
            return acc;
        }, {});
    }, [data]);
};
```

---

## Pattern Usage Guidelines

1. **Don't Over-Engineer**: Use patterns only when they solve a real problem
2. **Consistency**: Once a pattern is adopted, use it consistently
3. **Documentation**: Always document why a pattern was chosen
4. **Evolution**: Patterns should evolve with the codebase
5. **Testing**: All patterns should include testing strategies

## Contributing New Patterns

When adding a new pattern:
1. Ensure it's been used successfully at least 3 times
2. Include clear "When to Use" guidelines
3. Provide complete, working examples
4. Document any trade-offs or limitations
5. Include testing approaches
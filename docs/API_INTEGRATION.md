# API Integration (Day 2 - Afternoon)

## 1. Axios Setup (2:00 PM - 3:00 PM)
<!-- Documentation: https://axios-http.com/docs/intro -->

### Basic Configuration
```jsx
/**
 * Axios Instance Setup
 * Key Concepts:
 * - Base configuration
 * - Interceptors
 * - Error handling
 */

import axios from 'axios';

// Create axios instance
const api = axios.create({
  baseURL: process.env.REACT_APP_API_URL,
  timeout: 10000,
  headers: {
    'Content-Type': 'application/json'
  }
});

// Request interceptor
api.interceptors.request.use(
  config => {
    const token = localStorage.getItem('token');
    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }
    return config;
  },
  error => Promise.reject(error)
);

// Response interceptor
api.interceptors.response.use(
  response => response.data,
  error => {
    if (error.response?.status === 401) {
      // Handle unauthorized
      localStorage.removeItem('token');
      window.location.href = '/login';
    }
    return Promise.reject(error);
  }
);
```

## 2. Custom Hooks for API (3:00 PM - 4:00 PM)

### Data Fetching Hooks
```jsx
/**
 * Custom Hook Patterns
 * Key Concepts:
 * - Loading states
 * - Error handling
 * - Data caching
 */

const useQuery = (endpoint, options = {}) => {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchData = async () => {
      try {
        setLoading(true);
        const response = await api.get(endpoint, options);
        setData(response);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    };

    fetchData();
  }, [endpoint]);

  return { data, loading, error };
};

// Mutation hook
const useMutation = (endpoint) => {
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);

  const mutate = async (data) => {
    try {
      setLoading(true);
      const response = await api.post(endpoint, data);
      return response;
    } catch (err) {
      setError(err.message);
      throw err;
    } finally {
      setLoading(false);
    }
  };

  return { mutate, loading, error };
};
```

## 3. Error Handling (4:00 PM - 5:00 PM)

### Error Handling Patterns
```jsx
/**
 * Error Management
 * Key Concepts:
 * - Error boundaries
 * - Toast notifications
 * - Retry mechanisms
 */

// Error boundary component
class ErrorBoundary extends React.Component {
  state = { hasError: false, error: null };

  static getDerivedStateFromError(error) {
    return { hasError: true, error };
  }

  componentDidCatch(error, info) {
    console.error('Error:', error);
    console.error('Info:', info);
  }

  render() {
    if (this.state.hasError) {
      return <ErrorFallback error={this.state.error} />;
    }
    return this.props.children;
  }
}

// Retry mechanism
const useRetry = (callback, maxAttempts = 3) => {
  const attempt = useRef(0);

  const execute = async (...args) => {
    try {
      return await callback(...args);
    } catch (error) {
      if (attempt.current < maxAttempts) {
        attempt.current += 1;
        return execute(...args);
      }
      throw error;
    }
  };

  return execute;
};
```

## 4. Data Caching (5:00 PM - 6:00 PM)

### Caching Strategies
```jsx
/**
 * Cache Implementation
 * Key Concepts:
 * - In-memory cache
 * - Cache invalidation
 * - Stale-while-revalidate
 */

const cache = new Map();

const useCachedQuery = (key, fetcher, options = {}) => {
  const { staleTime = 5000, cacheTime = 30000 } = options;
  
  const getCachedData = () => {
    const cached = cache.get(key);
    if (!cached) return null;
    
    const isStale = Date.now() - cached.timestamp > staleTime;
    return isStale ? null : cached.data;
  };

  const [data, setData] = useState(getCachedData);
  const [loading, setLoading] = useState(!data);

  useEffect(() => {
    const fetchData = async () => {
      try {
        const fresh = await fetcher();
        cache.set(key, {
          data: fresh,
          timestamp: Date.now()
        });
        setData(fresh);
      } finally {
        setLoading(false);
      }
    };

    if (!getCachedData()) {
      fetchData();
    }

    // Cache cleanup
    return () => {
      setTimeout(() => {
        cache.delete(key);
      }, cacheTime);
    };
  }, [key, fetcher, staleTime, cacheTime]);

  return { data, loading };
};
```

## Practice Exercises

### 1. API Client Implementation
```jsx
// Exercise: Create a complete API client
const apiClient = {
  // Implement GET, POST, PUT, DELETE methods
};
```

### 2. Custom Hook Creation
```jsx
// Exercise: Build a paginated data hook
const usePaginatedData = (endpoint) => {
  // Implement pagination logic
};
```

### 3. Error Handler
```jsx
// Exercise: Create a global error handler
const useErrorHandler = () => {
  // Implement error handling logic
};
```

## Additional Resources
- [Axios Documentation](https://axios-http.com/docs/intro)
- [React Query](https://tanstack.com/query/latest)
- [Error Boundary Pattern](https://react.dev/reference/react/Component#catching-rendering-errors-with-an-error-boundary)
- [HTTP Cache Headers](https://developer.mozilla.org/en-US/docs/Web/HTTP/Caching) 
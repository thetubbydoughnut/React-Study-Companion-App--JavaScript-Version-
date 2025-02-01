# React Security Guide

## Authentication & Authorization
<!-- 
Documentation:
- JWT: https://jwt.io/introduction
- React Router Auth: https://reactrouter.com/docs/en/v6/examples/auth
- Security Best Practices: https://cheatsheetseries.owasp.org/cheatsheets/React_Security_Cheat_Sheet.html
-->

### 1. JWT Authentication
```jsx
/**
 * JWT Authentication Setup
 * Documentation: https://jwt.io/introduction
 */

// Auth Context
const AuthContext = createContext();

const AuthProvider = ({ children }) => {
  const [token, setToken] = useState(localStorage.getItem('token'));
  const [user, setUser] = useState(null);

  // Login handler
  const login = async (credentials) => {
    try {
      const response = await api.post('/auth/login', credentials);
      const { token, user } = response.data;
      
      localStorage.setItem('token', token);
      setToken(token);
      setUser(user);
    } catch (error) {
      throw new Error('Authentication failed');
    }
  };

  // Logout handler
  const logout = () => {
    localStorage.removeItem('token');
    setToken(null);
    setUser(null);
  };

  return (
    <AuthContext.Provider value={{ token, user, login, logout }}>
      {children}
    </AuthContext.Provider>
  );
};
```

### 2. Protected Routes
```jsx
/**
 * Protected Route Component
 * Documentation: https://reactrouter.com/docs/en/v6/examples/auth
 */

const ProtectedRoute = ({ children }) => {
  const { token } = useContext(AuthContext);
  const location = useLocation();

  if (!token) {
    return <Navigate to="/login" state={{ from: location }} replace />;
  }

  return children;
};

// Usage in Router
const AppRouter = () => (
  <Routes>
    <Route path="/login" element={<LoginPage />} />
    <Route
      path="/dashboard"
      element={
        <ProtectedRoute>
          <DashboardPage />
        </ProtectedRoute>
      }
    />
  </Routes>
);
```

### 3. Token Management
```jsx
/**
 * Token Refresh Pattern
 * Documentation: https://auth0.com/docs/secure/tokens/refresh-tokens
 */

const useTokenRefresh = () => {
  const { token, logout } = useContext(AuthContext);

  useEffect(() => {
    if (!token) return;

    const refreshToken = async () => {
      try {
        const response = await api.post('/auth/refresh');
        // Update token in context/storage
      } catch (error) {
        logout(); // Token expired or invalid
      }
    };

    // Set up refresh interval
    const intervalId = setInterval(refreshToken, 15 * 60 * 1000); // 15 minutes
    return () => clearInterval(intervalId);
  }, [token, logout]);
};
```

## Security Best Practices

### 1. XSS Prevention
```jsx
/**
 * XSS Prevention Patterns
 * Documentation: https://reactjs.org/docs/dom-elements.html#dangerouslysetinnerhtml
 */

// ❌ Unsafe
const UnsafeComponent = () => (
  <div dangerouslySetInnerHTML={{ __html: userInput }} />
);

// ✅ Safe
const SafeComponent = () => (
  <div>{userInput}</div> // React automatically escapes content
);

// When HTML is needed, use DOMPurify
import DOMPurify from 'dompurify';

const SafeHTML = ({ html }) => (
  <div dangerouslySetInnerHTML={{ 
    __html: DOMPurify.sanitize(html) 
  }} />
);
```

### 2. CSRF Protection
```jsx
/**
 * CSRF Protection Setup
 * Documentation: https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html
 */

// Axios interceptor for CSRF token
api.interceptors.request.use(config => {
  const token = document.querySelector('meta[name="csrf-token"]').content;
  config.headers['X-CSRF-Token'] = token;
  return config;
});
```

### 3. Secure Form Handling
```jsx
/**
 * Secure Form Implementation
 * Documentation: https://reactjs.org/docs/forms.html
 */

const SecureForm = () => {
  const [formData, setFormData] = useState({
    username: '',
    password: ''
  });

  const handleSubmit = async (e) => {
    e.preventDefault();
    
    // Validate input before sending
    if (!validateInput(formData)) {
      return;
    }

    try {
      await api.post('/auth/login', formData);
    } catch (error) {
      // Handle error without exposing sensitive info
      console.error('Login failed');
    }
  };

  return (
    <form onSubmit={handleSubmit} autoComplete="off">
      <input
        type="text"
        name="username"
        autoComplete="username"
        onChange={e => setFormData(prev => ({
          ...prev,
          username: e.target.value.trim()
        }))}
      />
      <input
        type="password"
        name="password"
        autoComplete="current-password"
        onChange={e => setFormData(prev => ({
          ...prev,
          password: e.target.value
        }))}
      />
    </form>
  );
};
```

## Security Testing

### 1. Authentication Testing
```jsx
/**
 * Auth Testing Pattern
 * Documentation: https://testing-library.com/docs/example-react-router/
 */

describe('Protected Route', () => {
  it('redirects to login when not authenticated', () => {
    const { history } = render(
      <MemoryRouter initialEntries={['/dashboard']}>
        <ProtectedRoute>
          <DashboardPage />
        </ProtectedRoute>
      </MemoryRouter>
    );

    expect(history.location.pathname).toBe('/login');
  });
});
```

### 2. Security Headers
```javascript
// vercel.json or similar deployment config
{
  "headers": [
    {
      "source": "/(.*)",
      "headers": [
        {
          "key": "Content-Security-Policy",
          "value": "default-src 'self'; script-src 'self' 'unsafe-inline'"
        },
        {
          "key": "X-Frame-Options",
          "value": "DENY"
        },
        {
          "key": "X-Content-Type-Options",
          "value": "nosniff"
        }
      ]
    }
  ]
}
```

## Additional Resources
- [OWASP React Security Guide](https://cheatsheetseries.owasp.org/cheatsheets/React_Security_Cheat_Sheet.html)
- [JWT Best Practices](https://auth0.com/blog/jwt-security-best-practices/)
- [React Router Authentication](https://reactrouter.com/docs/en/v6/examples/auth)
- [Web Security Headers](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers) 
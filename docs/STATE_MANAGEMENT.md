# State Management (Day 2 - Morning)

## 1. Context API Implementation (9:00 AM - 10:30 AM)
<!-- Documentation: https://react.dev/learn/passing-data-deeply-with-context -->

### Basic Context Setup
```jsx
/**
 * Context API Pattern
 * Key Concepts:
 * - Context creation
 * - Provider implementation
 * - Consumer usage
 */

// Create context with default value
const AppContext = createContext({
  theme: 'light',
  toggleTheme: () => {},
  user: null,
  setUser: () => {}
});

// Provider Component
const AppProvider = ({ children }) => {
  const [theme, setTheme] = useState('light');
  const [user, setUser] = useState(null);

  // Memoize context value
  const contextValue = useMemo(() => ({
    theme,
    toggleTheme: () => setTheme(prev => prev === 'light' ? 'dark' : 'light'),
    user,
    setUser
  }), [theme, user]);

  return (
    <AppContext.Provider value={contextValue}>
      {children}
    </AppContext.Provider>
  );
};

// Custom hook for context usage
const useApp = () => {
  const context = useContext(AppContext);
  if (!context) {
    throw new Error('useApp must be used within AppProvider');
  }
  return context;
};
```

### Context Best Practices
```jsx
/**
 * Context Optimization
 * Key Concepts:
 * - Split contexts
 * - Memoization
 * - Performance considerations
 */

// Split contexts by domain
const ThemeContext = createContext();
const UserContext = createContext();

// Composition pattern
const AppProviders = ({ children }) => (
  <ThemeContext.Provider value={useThemeValue()}>
    <UserContext.Provider value={useUserValue()}>
      {children}
    </UserContext.Provider>
  </ThemeContext.Provider>
);
```

## 2. Redux Setup & Patterns (10:30 AM - 12:00 PM)
<!-- Documentation: https://redux-toolkit.js.org/introduction/getting-started -->

### Store Configuration
```jsx
/**
 * Redux Toolkit Setup
 * Key Concepts:
 * - Store creation
 * - Slice pattern
 * - Middleware setup
 */

// Store setup
import { configureStore } from '@reduxjs/toolkit';

const store = configureStore({
  reducer: {
    user: userReducer,
    posts: postsReducer,
    theme: themeReducer
  },
  middleware: (getDefaultMiddleware) =>
    getDefaultMiddleware().concat(logger)
});

// Slice creation
import { createSlice } from '@reduxjs/toolkit';

const userSlice = createSlice({
  name: 'user',
  initialState: {
    data: null,
    loading: false,
    error: null
  },
  reducers: {
    setUser: (state, action) => {
      state.data = action.payload;
    },
    setLoading: (state, action) => {
      state.loading = action.payload;
    },
    setError: (state, action) => {
      state.error = action.payload;
    }
  }
});
```

### Redux Hooks & Patterns
```jsx
/**
 * Redux Usage Patterns
 * Key Concepts:
 * - useSelector
 * - useDispatch
 * - Action creators
 */

const UserProfile = () => {
  const dispatch = useDispatch();
  const user = useSelector(state => state.user.data);
  const loading = useSelector(state => state.user.loading);

  // Memoized selector
  const userPosts = useSelector(
    state => selectUserPosts(state, user?.id),
    shallowEqual
  );

  useEffect(() => {
    dispatch(fetchUserData(userId));
  }, [dispatch, userId]);

  if (loading) return <Loading />;

  return (
    <div>
      <h1>{user.name}</h1>
      <PostList posts={userPosts} />
    </div>
  );
};
```

## 3. State Architecture (12:00 PM - 1:00 PM)

### Comparison & Decision Making
```jsx
/**
 * When to Use What
 * Key Concepts:
 * - Context vs Redux
 * - Performance implications
 * - Scalability considerations
 */

// Context: Simple shared state
const SimpleTheme = () => {
  const { theme } = useTheme();
  return <div className={theme}>Simple themed component</div>;
};

// Redux: Complex state with many updates
const ComplexDashboard = () => {
  const { data, filters, sorting } = useSelector(selectDashboardState);
  return <Dashboard data={data} filters={filters} sorting={sorting} />;
};
```

## Practice Exercises

### 1. Context Implementation
```jsx
// Exercise: Create a shopping cart context
const CartContext = createContext();
const CartProvider = ({ children }) => {
  // Implement cart state and methods
};
```

### 2. Redux Store Setup
```jsx
// Exercise: Implement a todo list with Redux
const todoSlice = createSlice({
  // Implement todo state management
});
```

### 3. State Migration
```jsx
// Exercise: Migrate from Context to Redux
// 1. Identify shared state
// 2. Create slices
// 3. Update components
```

## Additional Resources
- [Redux Style Guide](https://redux.js.org/style-guide/style-guide)
- [Context API Best Practices](https://react.dev/learn/passing-data-deeply-with-context)
- [Redux Toolkit Documentation](https://redux-toolkit.js.org/)
- [State Management Patterns](https://redux.js.org/usage/structuring-reducers/normalizing-state-shape) 
- [Redux DevTools](https://github.com/reduxjs/redux-devtools) 
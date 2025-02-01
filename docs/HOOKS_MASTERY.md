# React Hooks Mastery (Day 1 - Afternoon)

## 1. useState & useEffect (2:00 PM - 3:00 PM)
<!-- Documentation: https://react.dev/reference/react/useState -->

### Basic Hooks Usage
```jsx
/**
 * useState Patterns
 * Key Concepts:
 * - State initialization
 * - Update patterns
 * - Lazy initialization
 */
const UserProfile = () => {
  // Basic state
  const [name, setName] = useState('');
  
  // Object state with function update
  const [user, setUser] = useState(() => ({
    name: localStorage.getItem('userName') || '',
    preferences: {}
  }));

  // Array state
  const [history, setHistory] = useState([]);
  
  // Multiple related states
  const [isLoading, setIsLoading] = useState(false);
  const [error, setError] = useState(null);
  const [data, setData] = useState(null);
};
```

### useEffect Implementation
```jsx
/**
 * useEffect Patterns
 * Key Concepts:
 * - Dependencies array
 * - Cleanup function
 * - API calls
 */
const DataFetcher = ({ userId }) => {
  useEffect(() => {
    // Setup
    const fetchData = async () => {
      setIsLoading(true);
      try {
        const data = await api.getUser(userId);
        setData(data);
      } catch (err) {
        setError(err);
      } finally {
        setIsLoading(false);
      }
    };

    fetchData();

    // Cleanup
    return () => {
      // Cancel requests, clear timers
    };
  }, [userId]); // Dependencies
};
```

## 2. useContext & useReducer (3:00 PM - 4:00 PM)
<!-- Documentation: https://react.dev/reference/react/useContext -->

### Context Setup
```jsx
/**
 * Context Implementation
 * Key Concepts:
 * - Context creation
 * - Provider pattern
 * - Consumer usage
 */
const ThemeContext = createContext();

const ThemeProvider = ({ children }) => {
  const [theme, setTheme] = useState('light');
  
  const value = useMemo(() => ({
    theme,
    setTheme
  }), [theme]);

  return (
    <ThemeContext.Provider value={value}>
      {children}
    </ThemeContext.Provider>
  );
};
```

### Reducer Pattern
```jsx
/**
 * useReducer Implementation
 * Key Concepts:
 * - Action types
 * - Reducer function
 * - Dispatch usage
 */
const initialState = { count: 0, error: null };

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return { ...state, count: state.count + 1 };
    case 'decrement':
      return { ...state, count: state.count - 1 };
    case 'reset':
      return initialState;
    case 'error':
      return { ...state, error: action.payload };
    default:
      return state;
  }
}

const Counter = () => {
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <div>
      Count: {state.count}
      <button onClick={() => dispatch({ type: 'increment' })}>+</button>
      <button onClick={() => dispatch({ type: 'decrement' })}>-</button>
      <button onClick={() => dispatch({ type: 'reset' })}>Reset</button>
    </div>
  );
};
```

## 3. Custom Hooks (4:00 PM - 5:00 PM)
<!-- Documentation: https://react.dev/learn/reusing-logic-with-custom-hooks -->

### Custom Hook Patterns
```jsx
/**
 * Custom Hook Creation
 * Key Concepts:
 * - Logic reuse
 * - Hook composition
 * - State management
 */
const useLocalStorage = (key, initialValue) => {
  const [storedValue, setStoredValue] = useState(() => {
    try {
      const item = window.localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch (error) {
      return initialValue;
    }
  });

  useEffect(() => {
    window.localStorage.setItem(key, JSON.stringify(storedValue));
  }, [key, storedValue]);

  return [storedValue, setStoredValue];
};

// Usage
const Component = () => {
  const [name, setName] = useLocalStorage('name', '');
};
```

## 4. Performance Hooks (5:00 PM - 6:00 PM)
<!-- Documentation: https://react.dev/reference/react/useMemo -->

### useMemo & useCallback
```jsx
/**
 * Performance Optimization
 * Key Concepts:
 * - Memoization
 * - Dependency arrays
 * - Performance tradeoffs
 */
const ExpensiveComponent = ({ data, onItemSelect }) => {
  // Memoize expensive calculations
  const processedData = useMemo(() => {
    return data.map(item => expensiveOperation(item));
  }, [data]);

  // Memoize callbacks
  const handleSelect = useCallback((item) => {
    onItemSelect(item.id);
  }, [onItemSelect]);

  return (
    <div>
      {processedData.map(item => (
        <Item 
          key={item.id}
          data={item}
          onSelect={handleSelect}
        />
      ))}
    </div>
  );
};
```

## Practice Exercises

### 1. Custom Hook Creation
```jsx
// Exercise: Create a useForm hook
const useForm = (initialValues) => {
  // Implement form state management
};
```

### 2. Context Implementation
```jsx
// Exercise: Create an authentication context
const useAuth = () => {
  // Implement auth state and methods
};
```

### 3. Performance Optimization
```jsx
// Exercise: Optimize a list component
const OptimizedList = ({ items }) => {
  // Implement with proper memoization
};
```

## Additional Resources
- [Hooks API Reference](https://react.dev/reference/react)
- [Custom Hooks Guide](https://react.dev/learn/reusing-logic-with-custom-hooks)
- [Context Design Patterns](https://react.dev/learn/passing-data-deeply-with-context)
- [Performance Optimization](https://react.dev/learn/managing-state) 
# Interview Preparation (Day 4 - Afternoon)

## 1. React Core Concepts (2:00 PM - 3:00 PM)
<!-- Documentation: https://react.dev/learn -->

### Common Interview Questions
```jsx
/**
 * Key Topics:
 * - Virtual DOM
 * - Component Lifecycle
 * - State vs Props
 * - Hooks vs Classes
 */

// Q: Explain Virtual DOM
const VirtualDOMExample = () => {
  // React creates a virtual representation
  return (
    <div>
      <h1>Hello</h1>
      <p>World</p>
    </div>
  );
  // React compares virtual DOM with real DOM
  // Only updates what's necessary
};

// Q: Explain useState vs useReducer
const StateManagementExample = () => {
  // useState for simple state
  const [count, setCount] = useState(0);

  // useReducer for complex state
  const [state, dispatch] = useReducer(reducer, {
    count: 0,
    history: []
  });
};
```

## 2. Coding Challenges (3:00 PM - 4:00 PM)

### Common React Patterns
```jsx
/**
 * Implementation Challenges
 * Key Concepts:
 * - Component composition
 * - Custom hooks
 * - State management
 */

// Challenge 1: Implement a Compound Component
const Select = {
  Root: ({ children, value, onChange }) => {
    const [isOpen, setIsOpen] = useState(false);
    return (
      <SelectContext.Provider value={{ value, onChange, isOpen, setIsOpen }}>
        {children}
      </SelectContext.Provider>
    );
  },
  
  Option: ({ value, children }) => {
    const { onChange, setIsOpen } = useSelectContext();
    return (
      <div onClick={() => {
        onChange(value);
        setIsOpen(false);
      }}>
        {children}
      </div>
    );
  }
};

// Challenge 2: Custom Hook with Debounce
const useDebounce = (value, delay) => {
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {
    const timer = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);

    return () => clearTimeout(timer);
  }, [value, delay]);

  return debouncedValue;
};
```

## 3. System Design (4:00 PM - 5:00 PM)

### Frontend Architecture
```jsx
/**
 * System Design Patterns
 * Key Concepts:
 * - Component architecture
 * - State management
 * - Performance optimization
 */

// Design Pattern: Feature-based Architecture
src/
  features/
    auth/
      components/
        LoginForm.jsx
        SignupForm.jsx
      hooks/
        useAuth.js
      api/
        authApi.js
      slice.js
    products/
      components/
        ProductList.jsx
        ProductDetail.jsx
      hooks/
        useProducts.js

// Design Pattern: Shared Components
const withErrorBoundary = (WrappedComponent) => {
  return class ErrorBoundary extends React.Component {
    state = { hasError: false };

    static getDerivedStateFromError(error) {
      return { hasError: true };
    }

    render() {
      if (this.state.hasError) {
        return <ErrorFallback />;
      }
      return <WrappedComponent {...this.props} />;
    }
  };
};
```

## 4. Mock Interview Practice (5:00 PM - 6:00 PM)

### Technical Questions
```jsx
// Q: Implement a throttled search component
const ThrottledSearch = () => {
  const [query, setQuery] = useState('');
  const throttledSearch = useThrottle(searchAPI, 1000);

  return (
    <input
      type="text"
      value={query}
      onChange={e => {
        setQuery(e.target.value);
        throttledSearch(e.target.value);
      }}
    />
  );
};

// Q: Implement infinite scroll
const InfiniteList = () => {
  const [items, setItems] = useState([]);
  const [page, setPage] = useState(1);

  const handleScroll = useCallback(() => {
    if (window.innerHeight + window.scrollY >= document.body.offsetHeight - 100) {
      setPage(p => p + 1);
    }
  }, []);

  useEffect(() => {
    window.addEventListener('scroll', handleScroll);
    return () => window.removeEventListener('scroll', handleScroll);
  }, [handleScroll]);

  return (
    <div>
      {items.map(item => (
        <ListItem key={item.id} {...item} />
      ))}
    </div>
  );
};
```

## Practice Questions

### 1. Component Design
```jsx
// Exercise: Design a reusable modal component
const Modal = ({ isOpen, onClose, children }) => {
  // Implement modal with portal
};
```

### 2. State Management
```jsx
// Exercise: Implement a shopping cart with Context
const CartContext = createContext();
const CartProvider = ({ children }) => {
  // Implement cart logic
};
```

### 3. Performance
```jsx
// Exercise: Optimize a list rendering
const OptimizedList = ({ items }) => {
  // Implement with virtualization
};
```

## Additional Resources
- [React Interview Questions](https://github.com/sudheerj/reactjs-interview-questions)
- [System Design Primer](https://github.com/donnemartin/system-design-primer)
- [Frontend System Design](https://www.patterns.dev/)
- [React Performance Tips](https://reactjs.org/docs/optimizing-performance.html) 
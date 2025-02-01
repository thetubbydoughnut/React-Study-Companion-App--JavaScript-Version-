# Performance Optimization (Day 3 - Afternoon)

## 1. React.memo & Component Optimization (2:00 PM - 3:00 PM)
<!-- Documentation: https://react.dev/reference/react/memo -->

### Memoization Patterns
```jsx
/**
 * Component Memoization
 * Key Concepts:
 * - Preventing unnecessary re-renders
 * - Custom comparison functions
 * - Props optimization
 */

// Basic memo usage
const MemoizedComponent = React.memo(({ data }) => {
  return <div>{data.text}</div>;
});

// Custom comparison function
const areEqual = (prevProps, nextProps) => {
  return prevProps.data.id === nextProps.data.id;
};

const OptimizedList = React.memo(({ items, onItemClick }) => {
  return (
    <ul>
      {items.map(item => (
        <ListItem 
          key={item.id}
          item={item}
          onClick={onItemClick}
        />
      ))}
    </ul>
  );
}, areEqual);

// Props optimization
const Parent = () => {
  // Memoize callback
  const handleClick = useCallback((id) => {
    console.log('Item clicked:', id);
  }, []); // Empty deps array as it doesn't depend on any values

  // Memoize data transformation
  const processedItems = useMemo(() => {
    return items.map(item => ({
      ...item,
      processed: expensiveOperation(item)
    }));
  }, [items]);

  return (
    <OptimizedList 
      items={processedItems}
      onItemClick={handleClick}
    />
  );
};
```

## 2. Code Splitting (3:00 PM - 4:00 PM)
<!-- Documentation: https://react.dev/reference/react/lazy -->

### Dynamic Imports
```jsx
/**
 * Code Splitting Patterns
 * Key Concepts:
 * - React.lazy
 * - Suspense
 * - Route-based splitting
 */

// Component level splitting
const HeavyComponent = React.lazy(() => import('./HeavyComponent'));

const App = () => {
  return (
    <Suspense fallback={<Loading />}>
      <HeavyComponent />
    </Suspense>
  );
};

// Route level splitting
const Routes = () => {
  return (
    <Suspense fallback={<Loading />}>
      <Switch>
        <Route 
          path="/dashboard"
          component={React.lazy(() => import('./Dashboard'))}
        />
        <Route 
          path="/profile"
          component={React.lazy(() => import('./Profile'))}
        />
      </Switch>
    </Suspense>
  );
};

// Preloading components
const preloadComponent = () => {
  const Component = React.lazy(() => import('./Component'));
  return { Component };
};
```

## 3. Lazy Loading (4:00 PM - 5:00 PM)

### Implementation Patterns
```jsx
/**
 * Lazy Loading Patterns
 * Key Concepts:
 * - Image lazy loading
 * - Intersection Observer
 * - Virtual scrolling
 */

// Image lazy loading
const LazyImage = ({ src, alt }) => {
  const [isLoaded, setIsLoaded] = useState(false);
  const imgRef = useRef();

  useEffect(() => {
    const observer = new IntersectionObserver(
      ([entry]) => {
        if (entry.isIntersecting) {
          imgRef.current.src = src;
          setIsLoaded(true);
          observer.disconnect();
        }
      },
      { threshold: 0.1 }
    );

    if (imgRef.current) {
      observer.observe(imgRef.current);
    }

    return () => observer.disconnect();
  }, [src]);

  return (
    <img
      ref={imgRef}
      alt={alt}
      className={isLoaded ? 'loaded' : 'loading'}
    />
  );
};

// Virtual scrolling
import { FixedSizeList } from 'react-window';

const VirtualList = ({ items }) => {
  const Row = ({ index, style }) => (
    <div style={style}>
      {items[index].content}
    </div>
  );

  return (
    <FixedSizeList
      height={400}
      width={600}
      itemCount={items.length}
      itemSize={50}
    >
      {Row}
    </FixedSizeList>
  );
};
```

## 4. Performance Profiling (5:00 PM - 6:00 PM)

### Profiling Tools
```jsx
/**
 * Performance Measurement
 * Key Concepts:
 * - React Profiler
 * - Performance metrics
 * - Optimization strategies
 */

// Profiler component
const withProfiler = (WrappedComponent, id) => {
  return function ProfilerComponent(props) {
    const handleRender = (
      id,
      phase,
      actualDuration,
      baseDuration,
      startTime,
      commitTime
    ) => {
      console.log({
        id,
        phase,
        actualDuration,
        baseDuration,
        startTime,
        commitTime
      });
    };

    return (
      <Profiler id={id} onRender={handleRender}>
        <WrappedComponent {...props} />
      </Profiler>
    );
  };
};

// Performance monitoring
const usePerformanceMonitor = () => {
  useEffect(() => {
    const observer = new PerformanceObserver((list) => {
      for (const entry of list.getEntries()) {
        console.log(`${entry.name}: ${entry.duration}`);
      }
    });

    observer.observe({ entryTypes: ['measure'] });
    return () => observer.disconnect();
  }, []);
};
```

## Practice Exercises

### 1. Component Optimization
```jsx
// Exercise: Optimize a complex list component
const ComplexList = ({ items, onItemSelect }) => {
  // Implement with proper memoization
};
```

### 2. Lazy Loading Implementation
```jsx
// Exercise: Create a lazy loading image component
const LazyLoadedImage = ({ src, alt }) => {
  // Implement lazy loading logic
};
```

### 3. Performance Monitoring
```jsx
// Exercise: Create a performance monitoring hook
const usePerformance = (componentName) => {
  // Implement performance tracking
};
```

## Additional Resources
- [React Performance](https://react.dev/learn/render-and-commit)
- [Code Splitting Guide](https://reactjs.org/docs/code-splitting.html)
- [React Window](https://react-window.vercel.app/)
- [Web Vitals](https://web.dev/vitals/) 
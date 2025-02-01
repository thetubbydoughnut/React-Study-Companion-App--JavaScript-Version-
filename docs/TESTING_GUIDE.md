# Testing Fundamentals (Day 3 - Morning)

## 1. Jest Setup & Configuration (9:00 AM - 10:00 AM)
<!-- Documentation: https://jestjs.io/docs/getting-started -->

### Basic Setup
```javascript
/**
 * Jest Configuration
 * Key Concepts:
 * - Test environment setup
 * - Test runners
 * - Matchers
 */

// jest.config.js
module.exports = {
  testEnvironment: 'jsdom',
  setupFilesAfterEnv: ['<rootDir>/src/setupTests.js'],
  moduleNameMapper: {
    '\\.(css|less|scss)$': 'identity-obj-proxy',
    '^@/(.*)$': '<rootDir>/src/$1'
  },
  collectCoverageFrom: [
    'src/**/*.{js,jsx}',
    '!src/index.js',
    '!src/reportWebVitals.js'
  ]
};

// setupTests.js
import '@testing-library/jest-dom';
import 'jest-extended';

// Mock fetch API
global.fetch = jest.fn();
```

## 2. Component Testing (10:00 AM - 11:00 AM)
<!-- Documentation: https://testing-library.com/docs/react-testing-library/intro -->

### Component Test Patterns
```jsx
/**
 * Component Testing
 * Key Concepts:
 * - Rendering components
 * - User interactions
 * - Assertions
 */

import { render, screen, fireEvent } from '@testing-library/react';
import userEvent from '@testing-library/user-event';

describe('Button Component', () => {
  it('renders with correct text', () => {
    render(<Button>Click me</Button>);
    expect(screen.getByText('Click me')).toBeInTheDocument();
  });

  it('handles click events', async () => {
    const handleClick = jest.fn();
    render(<Button onClick={handleClick}>Click me</Button>);
    
    await userEvent.click(screen.getByText('Click me'));
    expect(handleClick).toHaveBeenCalledTimes(1);
  });

  it('is disabled when loading', () => {
    render(<Button loading>Click me</Button>);
    expect(screen.getByRole('button')).toBeDisabled();
  });
});
```

## 3. Hook Testing (11:00 AM - 12:00 PM)
<!-- Documentation: https://react-hooks-testing-library.com/ -->

### Testing Custom Hooks
```jsx
/**
 * Hook Testing Patterns
 * Key Concepts:
 * - renderHook
 * - Act
 * - Async testing
 */

import { renderHook, act } from '@testing-library/react-hooks';

describe('useCounter', () => {
  it('increments counter', () => {
    const { result } = renderHook(() => useCounter());

    act(() => {
      result.current.increment();
    });

    expect(result.current.count).toBe(1);
  });

  it('handles async state updates', async () => {
    const { result, waitForNextUpdate } = renderHook(() => useAsync());

    expect(result.current.loading).toBe(true);
    await waitForNextUpdate();
    expect(result.current.loading).toBe(false);
  });
});
```

## 4. Integration Testing (12:00 PM - 1:00 PM)

### Integration Test Patterns
```jsx
/**
 * Integration Testing
 * Key Concepts:
 * - Component interaction
 * - State management
 * - API mocking
 */

describe('ShoppingCart Integration', () => {
  beforeEach(() => {
    // Mock API responses
    jest.spyOn(global, 'fetch').mockImplementation(() =>
      Promise.resolve({
        json: () => Promise.resolve({ items: mockItems })
      })
    );
  });

  it('completes checkout flow', async () => {
    render(
      <Provider store={store}>
        <ShoppingCart />
      </Provider>
    );

    // Add item to cart
    await userEvent.click(screen.getByTestId('add-item'));
    expect(screen.getByTestId('cart-count')).toHaveTextContent('1');

    // Proceed to checkout
    await userEvent.click(screen.getByText('Checkout'));
    expect(screen.getByText('Payment Details')).toBeInTheDocument();

    // Complete payment
    await userEvent.type(screen.getByLabelText('Card Number'), '4242424242424242');
    await userEvent.click(screen.getByText('Pay Now'));
    
    expect(screen.getByText('Order Confirmed')).toBeInTheDocument();
  });
});
```

## Practice Exercises

### 1. Component Testing
```jsx
// Exercise: Test a form component
describe('LoginForm', () => {
  it('validates input fields', async () => {
    // Implement form validation tests
  });
});
```

### 2. Hook Testing
```jsx
// Exercise: Test a data fetching hook
describe('useDataFetching', () => {
  it('handles loading and error states', async () => {
    // Implement loading and error state tests
  });
});
```

### 3. Integration Testing
```jsx
// Exercise: Test a feature flow
describe('UserProfile', () => {
  it('completes edit profile flow', async () => {
    // Implement edit profile flow test
  });
});
```

## Additional Resources
- [Jest Documentation](https://jestjs.io/docs/getting-started)
- [Testing Library Principles](https://testing-library.com/docs/guiding-principles)
- [React Testing Best Practices](https://kentcdodds.com/blog/common-mistakes-with-react-testing-library)
- [Testing Recipes](https://reactjs.org/docs/testing-recipes.html) 
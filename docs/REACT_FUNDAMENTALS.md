# React Fundamentals (Day 1 - Morning)

## 1. Functional Components & JSX (9:00 AM - 10:00 AM)
<!-- Documentation: https://react.dev/learn/your-first-component -->

### Basic Component Structure
```jsx
/**
 * Modern Functional Component Pattern
 * Key Concepts:
 * - Function declaration
 * - JSX syntax
 * - Component export
 */
const Greeting = ({ name }) => {
  return (
    <div className="greeting">
      <h1>Hello, {name}!</h1>
    </div>
  );
};

export default Greeting;
```

### JSX Rules & Patterns
```jsx
/**
 * JSX Syntax Guidelines
 * Key Concepts:
 * - Expression embedding
 * - Fragment usage
 * - Conditional rendering
 */
const UserProfile = ({ user, isAdmin }) => {
  return (
    <>
      {/* Expression in JSX */}
      <h2>{user.name.toUpperCase()}</h2>

      {/* Conditional Rendering */}
      {isAdmin && <AdminPanel />}

      {/* Ternary in JSX */}
      <div className={isAdmin ? 'admin-view' : 'user-view'}>
        {user.role}
      </div>
    </>
  );
};
```

## 2. Props & PropTypes (10:00 AM - 11:00 AM)
<!-- Documentation: https://react.dev/learn/passing-props-to-a-component -->

### Props Patterns
```jsx
/**
 * Props Implementation Patterns
 * Key Concepts:
 * - Props destructuring
 * - Default props
 * - PropTypes validation
 */
import PropTypes from 'prop-types';

const Button = ({ 
  text = 'Click Me',  // Default prop
  onClick,
  variant = 'primary',
  disabled = false
}) => {
  return (
    <button
      className={`btn btn-${variant}`}
      onClick={onClick}
      disabled={disabled}
    >
      {text}
    </button>
  );
};

Button.propTypes = {
  text: PropTypes.string,
  onClick: PropTypes.func.isRequired,
  variant: PropTypes.oneOf(['primary', 'secondary']),
  disabled: PropTypes.bool
};
```

## 3. State Management (11:00 AM - 12:00 PM)
<!-- Documentation: https://react.dev/learn/state-a-components-memory -->

### useState Implementation
```jsx
/**
 * State Management Patterns
 * Key Concepts:
 * - State initialization
 * - State updates
 * - Derived state
 */
const Counter = () => {
  // Basic state
  const [count, setCount] = useState(0);
  
  // Object state
  const [user, setUser] = useState({
    name: '',
    email: ''
  });

  // Derived state
  const isEven = count % 2 === 0;

  return (
    <div>
      <p>Count: {count}</p>
      <p>Is Even: {isEven ? 'Yes' : 'No'}</p>
      <button onClick={() => setCount(count + 1)}>
        Increment
      </button>
    </div>
  );
};
```

## 4. Event Handling (12:00 PM - 1:00 PM)
<!-- Documentation: https://react.dev/learn/responding-to-events -->

### Event Patterns
```jsx
/**
 * Event Handling Patterns
 * Key Concepts:
 * - Event handlers
 * - Event parameters
 * - Form events
 */
const FormComponent = () => {
  const [formData, setFormData] = useState({
    username: '',
    email: ''
  });

  // Form event handler
  const handleSubmit = (e) => {
    e.preventDefault();
    console.log('Form submitted:', formData);
  };

  // Input change handler
  const handleChange = (e) => {
    const { name, value } = e.target;
    setFormData(prev => ({
      ...prev,
      [name]: value
    }));
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        name="username"
        value={formData.username}
        onChange={handleChange}
      />
      <input
        name="email"
        value={formData.email}
        onChange={handleChange}
      />
      <button type="submit">Submit</button>
    </form>
  );
};
```

## Practice Exercises (Throughout the Session)

### 1. Component Creation
```jsx
// Exercise: Create a Card component with props
const Card = ({ title, content, footer }) => {
  // Implement the card structure
};
```

### 2. State Management
```jsx
// Exercise: Implement a toggle component
const Toggle = () => {
  // Implement state and toggle functionality
};
```

### 3. Form Handling
```jsx
// Exercise: Create a controlled form component
const LoginForm = () => {
  // Implement form with validation
};
```

## Additional Resources
- [Components and Props](https://react.dev/learn/your-first-component)
- [JSX In Depth](https://react.dev/learn/writing-markup-with-jsx)
- [State Management](https://react.dev/learn/managing-state)
- [Handling Events](https://react.dev/learn/responding-to-events) 
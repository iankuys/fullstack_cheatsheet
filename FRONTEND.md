# 🎯 Complete Frontend Interview Cheat Sheet

---

## 📝 HTML Fundamentals

### Semantic HTML Elements
HTML5 provides semantic elements that describe their meaning in a human- and machine-readable way.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Semantic HTML Example</title>
</head>
<body>
    <header>
        <nav>
            <ul>
                <li><a href="#home">Home</a></li>
                <li><a href="#about">About</a></li>
            </ul>
        </nav>
    </header>
    
    <main>
        <section>
            <article>
                <h1>Article Title</h1>
                <p>Article content...</p>
            </article>
        </section>
        
        <aside>
            <h2>Related Links</h2>
            <ul>
                <li><a href="#">Link 1</a></li>
            </ul>
        </aside>
    </main>
    
    <footer>
        <p>&copy; 2024 Company Name</p>
    </footer>
</body>
</html>
```

### Common HTML Input Types

```html
<!-- Basic inputs -->
<input type="text" placeholder="Enter text">
<input type="password" placeholder="Password">
<input type="email" placeholder="email@example.com">
<input type="number" min="0" max="100">
<input type="date">
<input type="checkbox" id="terms">
<input type="radio" name="gender" value="male">
<input type="file" accept=".pdf,.jpg,.png">

<!-- Form validation -->
<input type="email" required>
<input type="text" minlength="3" maxlength="20">
<input type="number" min="18" max="99">
```

### HTML Accessibility (A11y) Essentials

```html
<!-- Always use labels -->
<label for="email">Email:</label>
<input type="email" id="email" name="email">

<!-- ARIA attributes -->
<button aria-label="Close dialog">×</button>
<div role="alert">Error message</div>
<nav aria-label="Main navigation">

<!-- Alt text for images -->
<img src="chart.png" alt="Sales increased 25% in Q4">

<!-- Skip links for keyboard navigation -->
<a href="#main-content" class="skip-link">Skip to main content</a>
```

---

## 🚀 JavaScript Fundamentals

### Data Types & Type Checking

```javascript
// Primitive types
typeof "hello"      // "string"
typeof 42           // "number" 
typeof true         // "boolean"
typeof undefined    // "undefined"
typeof null         // "object" (historical quirk!)
typeof Symbol()     // "symbol"
typeof 123n         // "bigint"

// Reference types
typeof {}           // "object"
typeof []           // "object"
typeof function(){} // "function"

// Better type checking
Array.isArray([])   // true
Number.isNaN(NaN)   // true
```

### Array Methods (Essential for Interviews)

```javascript
const nums = [1, 2, 3, 4, 5];

// Transform data
nums.map(x => x * 2);              // [2, 4, 6, 8, 10]
nums.filter(x => x > 2);           // [3, 4, 5]
nums.reduce((acc, x) => acc + x, 0); // 15

// Find elements
nums.find(x => x > 3);             // 4
nums.findIndex(x => x > 3);        // 3
nums.includes(3);                  // true

// Test conditions
nums.some(x => x > 4);             // true
nums.every(x => x > 0);            // true

// Flatten arrays
[[1, 2], [3, 4]].flat();           // [1, 2, 3, 4]
[1, [2, [3]]].flat(2);             // [1, 2, 3]

// Create arrays
Array.from({length: 3}, (_, i) => i); // [0, 1, 2]
Array(3).fill(0);                  // [0, 0, 0]
```

### Object Manipulation & ES6+ Features

```javascript
// Destructuring
const {name, age} = user;
const [first, second] = array;

// Spread operator
const newObj = {...oldObj, newProp: 'value'};
const newArr = [...oldArr, newItem];

// Object methods
Object.keys(obj);       // Get property names
Object.values(obj);     // Get property values  
Object.entries(obj);    // Get [key, value] pairs

// Template literals
const message = `Hello ${name}, you are ${age} years old`;

// Optional chaining & nullish coalescing
user?.address?.street;  // Safe property access
const name = user?.name ?? 'Anonymous'; // Default value
```

### Functions & Arrow Functions

```javascript
// Function declarations vs expressions
function regular() { return 'regular'; }
const arrow = () => 'arrow';

// Arrow function variations
const single = x => x * 2;
const multiple = (x, y) => x + y;
const block = x => {
  const result = x * 2;
  return result;
};

// Higher-order functions
const withLogging = (fn) => (...args) => {
  console.log('Calling function with:', args);
  return fn(...args);
};

// Closures
function createCounter() {
  let count = 0;
  return () => ++count;
}
const counter = createCounter();
counter(); // 1
counter(); // 2
```

### Asynchronous JavaScript

```javascript
// Promises
fetch('/api/data')
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.error(error));

// Async/await
async function fetchData() {
  try {
    const response = await fetch('/api/data');
    const data = await response.json();
    return data;
  } catch (error) {
    console.error('Error:', error);
    throw error;
  }
}

// Promise utilities
Promise.all([fetch('/api/1'), fetch('/api/2')]);     // Wait for all
Promise.race([fetch('/api/1'), fetch('/api/2')]);    // First to resolve
Promise.allSettled([promise1, promise2]);            // All results (success/failure)
```

---

## 🎨 CSS Fundamentals

### Box Model
```css
.element {
  margin: 16px;      /* Space outside */
  padding: 8px;      /* Space inside */
  border: 1px solid #ccc;
  width: 200px;
  height: 50px;
  box-sizing: border-box; /* Include padding/border in width */
}
```

### Flexbox
```css
.container {
  display: flex;
  justify-content: center;    /* Horizontal alignment */
  align-items: center;        /* Vertical alignment */
  flex-direction: row;        /* or column */
  gap: 10px;                  /* Space between items */
}

.item {
  flex: 1;           /* Grow to fill space */
  flex-shrink: 0;    /* Don't shrink */
  align-self: flex-start; /* Override container alignment */
}
```

### CSS Grid
```css
.grid {
  display: grid;
  grid-template-columns: 1fr 2fr 1fr;
  grid-template-rows: auto 1fr auto;
  gap: 20px;
  grid-template-areas: 
    "header header header"
    "sidebar main aside"
    "footer footer footer";
}

.header { grid-area: header; }
.sidebar { grid-area: sidebar; }
```

### Responsive Design
```css
/* Mobile-first approach */
.container {
  width: 100%;
  padding: 1rem;
}

/* Tablet */
@media (min-width: 768px) {
  .container {
    max-width: 750px;
    margin: 0 auto;
  }
}

/* Desktop */
@media (min-width: 1024px) {
  .container {
    max-width: 1200px;
    display: grid;
    grid-template-columns: 1fr 3fr;
  }
}

/* Common breakpoints */
/* Mobile: 320px - 767px */
/* Tablet: 768px - 1023px */
/* Desktop: 1024px+ */
```

### CSS Positioning
```css
.element {
  position: relative;  /* Positioned relative to normal position */
  position: absolute;  /* Positioned relative to nearest positioned ancestor */
  position: fixed;     /* Positioned relative to viewport */
  position: sticky;    /* Switches between relative and fixed */
  
  top: 10px;
  left: 20px;
  z-index: 10;        /* Stacking order */
}
```

---

## ⚛️ React Fundamentals

### Component Patterns

```javascript
// Functional component with hooks
import React, { useState, useEffect } from 'react';

function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchUser = async () => {
      try {
        setLoading(true);
        const response = await fetch(`/api/users/${userId}`);
        if (!response.ok) throw new Error('Failed to fetch user');
        const userData = await response.json();
        setUser(userData);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    };

    fetchUser();
  }, [userId]);

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;
  if (!user) return <div>User not found</div>;

  return (
    <div>
      <h1>{user.name}</h1>
      <p>{user.email}</p>
    </div>
  );
}
```

### Essential React Hooks

```javascript
// useState - state management
const [count, setCount] = useState(0);
const [user, setUser] = useState({ name: '', email: '' });

// useEffect - side effects
useEffect(() => {
  // Effect logic
  return () => {
    // Cleanup
  };
}, [dependencies]);

// useContext - consume context
const theme = useContext(ThemeContext);

// useReducer - complex state logic
const [state, dispatch] = useReducer(reducer, initialState);

// useMemo - expensive calculations
const expensiveValue = useMemo(() => {
  return expensiveCalculation(data);
}, [data]);

// useCallback - memoize functions
const memoizedCallback = useCallback(() => {
  doSomething(a, b);
}, [a, b]);

// useRef - DOM references or mutable values
const inputRef = useRef(null);
const countRef = useRef(0);
```

### Event Handling Best Practices

```javascript
function Form() {
  const [formData, setFormData] = useState({ name: '', email: '' });

  // Use arrow functions for event handlers to avoid binding issues
  const handleInputChange = (e) => {
    const { name, value } = e.target;
    setFormData(prev => ({
      ...prev,
      [name]: value
    }));
  };

  const handleSubmit = (e) => {
    e.preventDefault(); // Prevent page reload
    // Handle form submission
  };

  return (
    <form onSubmit={handleSubmit}>
      <input 
        name="name"
        value={formData.name}
        onChange={handleInputChange}
      />
      <input 
        name="email"
        type="email"
        value={formData.email}
        onChange={handleInputChange}
      />
      <button type="submit">Submit</button>
    </form>
  );
}
```

### Performance Optimization

```javascript
import React, { memo, useMemo, useCallback } from 'react';

// Memoize component to prevent unnecessary re-renders
const ExpensiveComponent = memo(({ data, onUpdate }) => {
  // Expensive calculation
  const processedData = useMemo(() => {
    return data.map(item => ({
      ...item,
      processed: expensiveProcess(item)
    }));
  }, [data]);

  // Memoize callback to prevent child re-renders
  const handleClick = useCallback((id) => {
    onUpdate(id);
  }, [onUpdate]);

  return (
    <div>
      {processedData.map(item => (
        <Item 
          key={item.id} 
          data={item} 
          onClick={handleClick}
        />
      ))}
    </div>
  );
});

// React.lazy for code splitting
const LazyComponent = React.lazy(() => import('./LazyComponent'));

function App() {
  return (
    <React.Suspense fallback={<div>Loading...</div>}>
      <LazyComponent />
    </React.Suspense>
  );
}
```

### Error Boundaries

```javascript
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false, error: null };
  }

  static getDerivedStateFromError(error) {
    return { hasError: true, error };
  }

  componentDidCatch(error, errorInfo) {
    console.error('Error caught by boundary:', error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return (
        <div>
          <h2>Something went wrong</h2>
          <details>
            {this.state.error && this.state.error.toString()}
          </details>
        </div>
      );
    }

    return this.props.children;
  }
}
```

---

## 🔧 Common Interview Topics

### State Management Patterns

```javascript
// Local state
const [user, setUser] = useState(null);

// Context for global state
const UserContext = createContext();

function UserProvider({ children }) {
  const [user, setUser] = useState(null);
  
  const login = async (credentials) => {
    const user = await api.login(credentials);
    setUser(user);
  };

  return (
    <UserContext.Provider value={{ user, login }}>
      {children}
    </UserContext.Provider>
  );
}

// Custom hook for using context
function useUser() {
  const context = useContext(UserContext);
  if (!context) {
    throw new Error('useUser must be used within UserProvider');
  }
  return context;
}
```

### API Integration

```javascript
// Custom hook for API calls
function useApi(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchData = async () => {
      try {
        const response = await fetch(url);
        if (!response.ok) throw new Error(`HTTP error! status: ${response.status}`);
        const result = await response.json();
        setData(result);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    };

    fetchData();
  }, [url]);

  return { data, loading, error };
}

// Usage
function UserList() {
  const { data: users, loading, error } = useApi('/api/users');

  if (loading) return <LoadingSpinner />;
  if (error) return <ErrorMessage error={error} />;

  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

### Form Handling

```javascript
// Custom hook for form handling
function useForm(initialValues, validationRules) {
  const [values, setValues] = useState(initialValues);
  const [errors, setErrors] = useState({});
  const [touched, setTouched] = useState({});

  const handleChange = (e) => {
    const { name, value } = e.target;
    setValues(prev => ({ ...prev, [name]: value }));
    
    // Clear error when user starts typing
    if (errors[name]) {
      setErrors(prev => ({ ...prev, [name]: '' }));
    }
  };

  const handleBlur = (e) => {
    const { name } = e.target;
    setTouched(prev => ({ ...prev, [name]: true }));
    
    // Validate field on blur
    if (validationRules[name]) {
      const error = validationRules[name](values[name]);
      setErrors(prev => ({ ...prev, [name]: error }));
    }
  };

  const validate = () => {
    const newErrors = {};
    Object.keys(validationRules).forEach(field => {
      const error = validationRules[field](values[field]);
      if (error) newErrors[field] = error;
    });
    setErrors(newErrors);
    return Object.keys(newErrors).length === 0;
  };

  return {
    values,
    errors,
    touched,
    handleChange,
    handleBlur,
    validate,
    setValues,
  };
}
```

---

## 🎯 Interview Questions & Answers

### "What's the difference between `let`, `const`, and `var`?"

```javascript
// var - function scoped, hoisted, can be redeclared
var x = 1;
var x = 2; // OK

function example() {
  console.log(y); // undefined (hoisted)
  var y = 3;
}

// let - block scoped, hoisted but in temporal dead zone
let a = 1;
// let a = 2; // Error: Cannot redeclare

{
  let b = 3; // Only exists in this block
}

// const - block scoped, must be initialized, cannot be reassigned
const c = 1;
// c = 2; // Error: Cannot reassign

const obj = { name: 'John' };
obj.name = 'Jane'; // OK - object properties can change
```

### "Explain JavaScript's `this` keyword"

```javascript
// Global context
console.log(this); // Window object (browser) or global (Node.js)

// Object method
const person = {
  name: 'John',
  greet() {
    console.log(this.name); // 'John'
  }
};

// Arrow functions don't have their own `this`
const person2 = {
  name: 'Jane',
  greet: () => {
    console.log(this.name); // undefined (inherits from enclosing scope)
  }
};

// Event handlers
button.addEventListener('click', function() {
  console.log(this); // The button element
});

button.addEventListener('click', () => {
  console.log(this); // Window object (arrow function)
});

// Explicit binding
const boundGreet = person.greet.bind(person2);
boundGreet(); // 'Jane'
```

### "How does React's reconciliation work?"

React's reconciliation is the process of updating the DOM efficiently:

1. **Virtual DOM**: React creates a virtual representation of the DOM
2. **Diffing**: When state changes, React compares (diffs) the new virtual DOM with the previous version
3. **Reconciliation**: React calculates the minimum changes needed
4. **Commit**: React applies these changes to the real DOM

**Key optimizations**:
- Uses `key` props to identify list items
- Assumes components of different types produce different trees
- Uses heuristics to make O(n) instead of O(n³) comparison

---

## 🧪 Testing Fundamentals

### React Testing Library Examples

```javascript
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';

// Basic component test
test('renders greeting message', () => {
  render(<Greeting name="John" />);
  expect(screen.getByText('Hello, John!')).toBeInTheDocument();
});

// Testing user interactions
test('increments counter when button clicked', async () => {
  const user = userEvent.setup();
  render(<Counter />);
  
  const button = screen.getByRole('button', { name: /increment/i });
  const count = screen.getByText('Count: 0');
  
  await user.click(button);
  
  expect(screen.getByText('Count: 1')).toBeInTheDocument();
});

// Testing async operations
test('displays user data after loading', async () => {
  render(<UserProfile userId="123" />);
  
  expect(screen.getByText('Loading...')).toBeInTheDocument();
  
  await waitFor(() => {
    expect(screen.getByText('John Doe')).toBeInTheDocument();
  });
});
```

---

## 🚀 Performance & Best Practices

### Optimization Techniques

```javascript
// Debouncing for search inputs
function useDebounce(value, delay) {
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

// Image lazy loading
function LazyImage({ src, alt, ...props }) {
  const [isLoaded, setIsLoaded] = useState(false);
  const [isInView, setIsInView] = useState(false);
  const imgRef = useRef();

  useEffect(() => {
    const observer = new IntersectionObserver(
      ([entry]) => {
        if (entry.isIntersecting) {
          setIsInView(true);
          observer.disconnect();
        }
      },
      { threshold: 0.1 }
    );

    if (imgRef.current) {
      observer.observe(imgRef.current);
    }

    return () => observer.disconnect();
  }, []);

  return (
    <div ref={imgRef} {...props}>
      {isInView && (
        <img
          src={src}
          alt={alt}
          onLoad={() => setIsLoaded(true)}
          style={{
            opacity: isLoaded ? 1 : 0,
            transition: 'opacity 0.3s'
          }}
        />
      )}
    </div>
  );
}
```

### Security Best Practices

```javascript
// Sanitize user input
import DOMPurify from 'dompurify';

function SafeHTML({ html }) {
  const sanitizedHTML = DOMPurify.sanitize(html);
  return <div dangerouslySetInnerHTML={{ __html: sanitizedHTML }} />;
}

// Validate environment variables
const API_URL = process.env.REACT_APP_API_URL;
if (!API_URL) {
  throw new Error('REACT_APP_API_URL is required');
}

// Use HTTPS for API calls
const apiClient = {
  get: (url) => fetch(`${API_URL}${url}`, {
    credentials: 'include', // Include cookies
    headers: {
      'Content-Type': 'application/json',
    },
  }),
};
```

---

## 📚 Common HTTP Status Codes

- **200 OK** - Request successful
- **201 Created** - Resource created successfully  
- **204 No Content** - Success, no response body
- **400 Bad Request** - Invalid request data
- **401 Unauthorized** - Authentication required
- **403 Forbidden** - Access denied
- **404 Not Found** - Resource not found
- **422 Unprocessable Entity** - Validation error
- **429 Too Many Requests** - Rate limited
- **500 Internal Server Error** - Server error

---

## 🎯 Key Takeaways for Interviews

1. **Know the fundamentals** - JavaScript, HTML, CSS basics
2. **Understand React concepts** - Components, hooks, state management
3. **Practice problem-solving** - Array methods, async code, algorithms
4. **Be able to explain trade-offs** - Performance vs readability, different approaches
5. **Show real examples** - Use your actual projects as talking points
6. **Ask good questions** - About the codebase, team practices, challenges
7. **Think out loud** - Explain your reasoning and approach
8. **Handle edge cases** - Consider null values, empty arrays, error states

**Remember**: Interviews are conversations about solving problems together!

# 🎨 Frontend Development Cheat Sheet

## 📝 HTML Essentials

```html
<!-- Semantic Structure -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Semantic HTML Example</title>
</head>
<body>
    <header><nav></nav></header>
    <main>
        <section><article><h1>Title</h1><p>Content...</p></article></section>
        <aside><h2>Related Links</h2></aside>
    </main>
    <footer><p>&copy; 2024 Company Name</p></footer>
</body>
</html>

<!-- Form Inputs -->
<input type="text|email|password|number|date|file">
<input type="checkbox|radio" name="group">
<input required minlength="3" maxlength="20">
<input type="file" accept=".pdf,.jpg,.png">

<!-- Accessibility -->
<label for="email">Email:</label>
<input id="email" type="email">
<button aria-label="Close">×</button>
<img src="chart.png" alt="Sales up 25%">
<a href="#main-content" class="skip-link">Skip to content</a>
```

## ⚡ JavaScript Essentials

```javascript
// Types & Checking
typeof "hello"     // "string"
typeof 42          // "number"
typeof null        // "object" (quirk!)
Array.isArray([])  // true

// Array Methods (Interview Gold)
const nums = [1, 2, 3, 4, 5];
nums.map(x => x * 2)              // [2, 4, 6, 8, 10]
nums.filter(x => x > 2)           // [3, 4, 5]
nums.reduce((acc, x) => acc + x)  // 15
nums.find(x => x > 3)             // 4
nums.some(x => x > 4)             // true

// ES6+ Features
const {name, age} = user;         // Destructuring
const newObj = {...oldObj, new: 'value'}; // Spread
const msg = `Hello ${name}`;      // Template literals
user?.address?.street;            // Optional chaining

// Functions
const arrow = (x, y) => x + y;
const single = x => x * 2;

// Async/Await
async function fetchData() {
  try {
    const response = await fetch('/api/data');
    return await response.json();
  } catch (error) {
    console.error(error);
  }
}
```

## 🎨 CSS Essentials

```css
/* Box Model */
.element {
  box-sizing: border-box;
  margin: 16px;    /* Outside space */
  padding: 8px;    /* Inside space */
  border: 1px solid #ccc;
}

/* Flexbox */
.flex-container {
  display: flex;
  justify-content: center;  /* Horizontal */
  align-items: center;      /* Vertical */
  gap: 10px;
}
.flex-item { flex: 1; }    /* Grow to fill */

/* Grid */
.grid {
  display: grid;
  grid-template-columns: 1fr 2fr 1fr;
  gap: 20px;
  grid-template-areas: "header header header";
}
.header { grid-area: header; }

/* Responsive */
@media (min-width: 768px) {
  .container { max-width: 750px; }
}

/* Positioning */
.element {
  position: relative|absolute|fixed|sticky;
  top: 10px; left: 20px; z-index: 10;
}
```

## ⚛️ React Essentials

```javascript
// Component Pattern
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    async function fetchUser() {
      try {
        const res = await fetch(`/api/users/${userId}`);
        setUser(await res.json());
      } catch (err) {
        console.error(err);
      } finally {
        setLoading(false);
      }
    }
    fetchUser();
  }, [userId]);

  if (loading) return <div>Loading...</div>;
  return <div><h1>{user?.name}</h1></div>;
}

// Hooks Cheat Sheet
const [state, setState] = useState(initialValue);
useEffect(() => { /* side effect */ return cleanup; }, [deps]);
const memoValue = useMemo(() => expensiveCalc(data), [data]);
const memoCallback = useCallback(() => doSomething(), [deps]);
const ref = useRef(initialValue);

// Form Handling
const [form, setForm] = useState({ name: '', email: '' });
const handleChange = (e) => {
  setForm(prev => ({ ...prev, [e.target.name]: e.target.value }));
};

// Performance
const Component = memo(({ data }) => {
  const processed = useMemo(() => data.map(transform), [data]);
  return <div>{processed}</div>;
});
```

## 🔧 Common Patterns

```javascript
// Context for Global State
const UserContext = createContext();
function UserProvider({ children }) {
  const [user, setUser] = useState(null);
  const login = async (creds) => setUser(await api.login(creds));
  return (
    <UserContext.Provider value={{ user, login }}>
      {children}
    </UserContext.Provider>
  );
}

// Custom API Hook
function useApi(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  
  useEffect(() => {
    fetch(url)
      .then(res => res.json())
      .then(setData)
      .catch(setError)
      .finally(() => setLoading(false));
  }, [url]);
  
  return { data, loading, error };
}

// Custom Form Hook
function useForm(initialValues) {
  const [values, setValues] = useState(initialValues);
  const handleChange = (e) => {
    setValues(prev => ({ ...prev, [e.target.name]: e.target.value }));
  };
  return { values, handleChange };
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

## 🎯 Interview Q&A

**"Difference between let, const, var?"**
- `var`: Function-scoped, hoisted, can redeclare
- `let`: Block-scoped, temporal dead zone
- `const`: Block-scoped, cannot reassign (but object properties can change)

**"Explain `this` keyword"**
- Global: `window` object
- Method: Object that owns the method
- Arrow functions: Inherit from enclosing scope
- Event handlers: The element (regular functions)

**"How does React reconciliation work?"**
1. Virtual DOM represents real DOM
2. State change triggers diffing
3. React calculates minimum changes
4. Updates applied to real DOM
5. Uses `key` props for list optimization

## 🧪 Testing Essentials

```javascript
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';

// Basic Test
test('renders greeting', () => {
  render(<Greeting name="John" />);
  expect(screen.getByText('Hello, John!')).toBeInTheDocument();
});

// User Interaction
test('button click', async () => {
  const user = userEvent.setup();
  render(<Counter />);
  await user.click(screen.getByRole('button'));
  expect(screen.getByText('Count: 1')).toBeInTheDocument();
});

// Async Operations
test('async loading', async () => {
  render(<UserProfile />);
  expect(screen.getByText('Loading...')).toBeInTheDocument();
  await waitFor(() => {
    expect(screen.getByText('John Doe')).toBeInTheDocument();
  });
});
```

## 🚀 Performance & Security

```javascript
// Debounce Hook
function useDebounce(value, delay) {
  const [debounced, setDebounced] = useState(value);
  useEffect(() => {
    const handler = setTimeout(() => setDebounced(value), delay);
    return () => clearTimeout(handler);
  }, [value, delay]);
  return debounced;
}

// Lazy Loading
function LazyImage({ src, alt }) {
  const [inView, setInView] = useState(false);
  const ref = useRef();
  
  useEffect(() => {
    const observer = new IntersectionObserver(([entry]) => {
      if (entry.isIntersecting) setInView(true);
    });
    if (ref.current) observer.observe(ref.current);
    return () => observer.disconnect();
  }, []);
  
  return <div ref={ref}>{inView && <img src={src} alt={alt} />}</div>;
}

// Security
import DOMPurify from 'dompurify';
const SafeHTML = ({ html }) => (
  <div dangerouslySetInnerHTML={{ __html: DOMPurify.sanitize(html) }} />
);
```

## 📚 HTTP Status Codes

- **200** OK - Success
- **201** Created - Resource created
- **400** Bad Request - Invalid data
- **401** Unauthorized - Login required
- **403** Forbidden - Access denied
- **404** Not Found - Resource missing
- **500** Server Error - Backend issue

## 🎯 Interview Tips

✅ **Know fundamentals**: JS, HTML, CSS, React  
✅ **Practice algorithms**: Array methods, async patterns  
✅ **Explain trade-offs**: Performance vs readability  
✅ **Use real examples**: Your actual projects  
✅ **Think out loud**: Show your reasoning process  
✅ **Handle edge cases**: null, empty arrays, errors  
✅ **Ask questions**: About codebase, team, challenges

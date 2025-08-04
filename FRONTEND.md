## 🚀 JavaScript Functions & Tricks Cheat Sheet

### Array Methods

- **map**  
  Transform each element in an array.
  ```js
  const doubled = [1, 2, 3].map(x => x * 2); // [2, 4, 6]
  ```

- **filter**  
  Filter elements based on a condition.
  ```js
  const evens = [1, 2, 3, 4].filter(x => x % 2 === 0); // [2, 4]
  ```

- **reduce**  
  Accumulate values (like Python’s `functools.reduce`).
  ```js
  const sum = [1, 2, 3].reduce((acc, x) => acc + x, 0); // 6
  ```

- **find**  
  Find the first element matching a condition.
  ```js
  const found = [1, 2, 3].find(x => x > 1); // 2
  ```

- **some / every**  
  Test if any/all elements match a condition.
  ```js
  [1, 2, 3].some(x => x > 2); // true
  [1, 2, 3].every(x => x > 0); // true
  ```

- **forEach**  
  Execute a function for each array element.
  ```js
  [1, 2, 3].forEach(x => console.log(x)); // logs 1, 2, 3
  ```

- **flatMap**  
  Map and flatten in one step.
  ```js
  [[1, 2], [3, 4]].flatMap(arr => arr.map(x => x * 2)); // [2, 4, 6, 8]
  ```

- **flat**  
  Flatten nested arrays.
  ```js
  [1, [2, 3], [4, [5]]].flat(); // [1, 2, 3, 4, [5]]
  [1, [2, 3], [4, [5]]].flat(2); // [1, 2, 3, 4, 5]
  ```

- **findIndex**  
  Find the index of the first matching element.
  ```js
  [10, 20, 30].findIndex(x => x > 15); // 1
  ```

- **includes**  
  Check if array contains a value.
  ```js
  [1, 2, 3].includes(2); // true
  ```

- **join**  
  Join array elements into a string.
  ```js
  ['a', 'b', 'c'].join('-'); // "a-b-c"
  ```

- **slice**  
  Extract a portion of an array.
  ```js
  [1, 2, 3, 4, 5].slice(1, 3); // [2, 3]
  ```

- **splice**  
  Add/remove elements from an array (mutates original).
  ```js
  const arr = [1, 2, 3];
  arr.splice(1, 1, 'new'); // [1, 'new', 3]
  ```

- **sort**  
  Sort array elements (mutates original).
  ```js
  [3, 1, 2].sort(); // [1, 2, 3]
  [3, 1, 2].sort((a, b) => b - a); // [3, 2, 1]
  ```

- **reverse**  
  Reverse array order (mutates original).
  ```js
  [1, 2, 3].reverse(); // [3, 2, 1]
  ```

### Object & Array Tricks

- **Spread Operator**  
  Clone or merge arrays/objects.
  ```js
  const arr = [1, 2];
  const arr2 = [...arr, 3]; // [1, 2, 3]

  const obj = {a: 1};
  const obj2 = {...obj, b: 2}; // {a: 1, b: 2}
  ```

- **Destructuring**  
  Unpack values from arrays/objects.
  ```js
  const [a, b] = [1, 2];
  const {x, y} = {x: 10, y: 20};
  ```

### Function Patterns

- **Arrow Functions**
  ```js
  const add = (a, b) => a + b;
  ```

- **Default Parameters**
  ```js
  function greet(name = "World") {
    return `Hello, ${name}`;
  }
  ```

- **Rest Parameters**
  ```js
  function sum(...args) {
    return args.reduce((a, b) => a + b, 0);
  }
  ```

### Async Patterns

- **Promises & Async/Await**
  ```js
  async function fetchData() {
    const res = await fetch('/api');
    const data = await res.json();
    return data;
  }
  ```

### Miscellaneous

- **Template Literals**
  ```js
  const name = "Alice";
  const msg = `Hello, ${name}!`;
  ```

- **Short-circuit Evaluation**
  ```js
  const value = input || "default";
  ```

- **Optional Chaining**
  ```js
  const city = user?.address?.city;
  ```

- **Nullish Coalescing**
  ```js
  const value = input ?? "default"; // Only uses "default" if input is null or undefined
  ```

---

## 🔑 Common HTML `<input>` Types

- `type="text"`  
  Standard single-line text input.

- `type="password"`  
  Masks input (shows dots or asterisks).

- `type="email"`  
  Validates email format and brings up email keyboard on mobile.

- `type="number"`  
  Only allows numeric input (with up/down arrows).

- `type="checkbox"`  
  For boolean (checked/unchecked) values.

- `type="radio"`  
  For selecting one option from a group.

- `type="date"`  
  Date picker.

- `type="file"`  
  For file uploads.

- `type="submit"`  
  Submits the form.

- `type="button"`  
  Generic button (does not submit form).

---

**Example:**
```html
<input type="text" />
<input type="password" />
<input type="email" />
<input type="number" />
<input type="checkbox" />
<input type="radio" />
<input type="date" />
<input type="file" />
<input type="submit" />
<input type="button" />
```

**Tip:** Most of these are used daily in React and

## 🎨 CSS Styles & Concepts Cheat Sheet

**Box Model**
```css
margin: 16px;
padding: 8px;
border: 1px solid #ccc;
width: 200px;
height: 50px;
```

**Flexbox**
```css
display: flex;
justify-content: center;
align-items: center;
flex-direction: row;
```

**Grid**
```css
display: grid;
grid-template-columns: 1fr 2fr;
gap: 10px;
```

**Positioning**
```css
position: absolute;
top: 10px;
left: 20px;
z-index: 2;
```

**Typography**
```css
font-size: 18px;
font-family: Arial, sans-serif;
font-weight: bold;
line-height: 1.5;
color: #333;
```

**Backgrounds**
```css
background-color: #f1f0f0;
background-image: url('image.png');
background-size: cover;
background-position: center;
```

**Borders & Radius**
```css
border: 2px solid #007bff;
border-radius: 12px;
```

**Responsive Design**
```css
@media (max-width: 600px) {
  .chat-bubble {
    width: 100%;
    font-size: 14px;
  }
}
```

**Transitions & Animations**
```css
transition: background 0.3s;
```

**Visibility**
```css
display: none;
visibility: hidden;
opacity: 0.5;
```

**Overflow**
```css
overflow: auto;
overflow-x: scroll;
overflow-y: hidden;
```

**Pseudo-classes/elements**
```css
.chat-bubble:hover {
  background: #e0e0e0;
}
.chat-bubble::before {
  content: "💬";
}
```

---

## 📝 JavaScript Data Types

- **string**: 'hello'
- **number**: 42, 3.14
- **boolean**: true, false
- **null**: intentional empty value
- **undefined**: variable declared but not assigned
- **symbol**: unique value (rare)
- **bigint**: large integers (123n)
- **object**: { key: 'value' }
- **array**: [1, 2, 3]
- **function**: function() {}

**Type checking:**
```js
typeof 'hi' // 'string'
typeof 1    // 'number'
typeof []   // 'object'
typeof {}   // 'object'
typeof null // 'object' (quirk)
typeof undefined // 'undefined'
typeof (() => {}) // 'function'
```

---

## ⚡️ React Event Handling

- Pass function reference: `<form onSubmit={handleSubmit}>`
- Use arrow function for arguments: `<button onClick={() => doSomething(arg)}>`
- Never call a function directly in JSX: `onClick={myFunc()}` (runs on render!)

---

## 🪝 React Hooks to Remember

- `useState` – local state
- `useEffect` – side effects (fetch, subscriptions)
- `useRef` – access DOM nodes, persist values
- `useContext` – global state/context
- `useMemo` – memoize expensive calculations
- `useCallback` – memoize functions

---

## ♿️ Accessibility (a11y) Tips

- Use semantic HTML: `<button>`, `<label>`, `<form>`, `<nav>`, etc.
- Use `alt` on images: `<img alt="description" />`
- Use `aria-` attributes for extra accessibility
- Use `<button>` for actions, not `<a>`
- Ensure keyboard navigation works (Tab, Enter, Space)

---

## 🌐 Common HTTP Status Codes

- **200**: OK (success)
- **201**: Created (resource made)
- **204**: No Content (success, no body)
- **400**: Bad Request (client error)
- **401**: Unauthorized (auth required)
- **403**: Forbidden (no permission)
- **404**: Not Found
- **500**: Internal Server Error

---

**Tip:** Practice explaining your code and choices out loud—communication is key in interviews!

---

## 🧠 More General Frontend Interview Tips

- **Semantic HTML:**
  ```html
  <header>Header</header>
  <main>Main content</main>
  <footer>Footer</footer>
  ```
- **ARIA roles:**
  ```html
  <div role="alert">Error!</div>
  <button role="button">Click</button>
  ```
- **Alt text:**
  ```html
  <img src="logo.png" alt="Company logo" />
  ```
- **Tabindex:**
  ```html
  <div tabindex="0">Focusable</div>
  ```
- **Responsive design:**
  ```css
  @media (max-width: 600px) {
    body { font-size: 14px; }
  }
  ```
- **Box-sizing:**
  ```css
  * { box-sizing: border-box; }
  ```
- **BEM naming:**
  ```css
  .block__element--modifier { color: red; }
  ```
- **Forms:**
  ```html
  <label for="email">Email</label>
  <input id="email" type="email" required />
  ```
- **Validation:**
  ```js
  if (!email.includes('@')) setError('Invalid email');
  ```
- **Debouncing:**
  ```js
  const debounce = (fn, delay) => {
    let timer;
    return (...args) => {
      clearTimeout(timer);
      timer = setTimeout(() => fn(...args), delay);
    };
  };
  ```
- **Security:**
  ```js
  // Never trust user input, always sanitize/validate on backend
  ```
- **Testing:**
  ```js
  // Example with Jest
  test('adds 1 + 2', () => {
    expect(1 + 2).toBe(3);
  });
  ```
- **Version control:**
  ```sh
  git status
  git add .
  git commit -m "Message"
  git push
  ```

---

## 🔍 More Interview Essentials

### Performance Optimization

- **Lazy Loading:**
  ```js
  const LazyComponent = React.lazy(() => import('./Component'));
  ```

- **Memoization:**
  ```js
  const MemoComponent = React.memo(Component);
  const memoizedValue = useMemo(() => expensiveCalculation(a, b), [a, b]);
  ```

- **Debouncing/Throttling:**
  ```js
  const throttle = (fn, delay) => {
    let lastCall = 0;
    return (...args) => {
      const now = Date.now();
      if (now - lastCall >= delay) {
        lastCall = now;
        fn(...args);
      }
    };
  };
  ```

### Browser APIs

- **Local Storage:**
  ```js
  localStorage.setItem('key', JSON.stringify(data));
  const data = JSON.parse(localStorage.getItem('key'));
  ```

- **Fetch API:**
  ```js
  fetch('/api/data', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(data)
  });
  ```

- **Event Listeners:**
  ```js
  element.addEventListener('click', handler);
  element.removeEventListener('click', handler);
  ```

### Error Handling

- **Try/Catch:**
  ```js
  try {
    const result = await riskyOperation();
  } catch (error) {
    console.error('Error:', error.message);
  }
  ```

- **Error Boundaries (React):**
  ```js
  class ErrorBoundary extends React.Component {
    constructor(props) {
      super(props);
      this.state = { hasError: false };
    }
    
    static getDerivedStateFromError(error) {
      return { hasError: true };
    }
    
    render() {
      if (this.state.hasError) {
        return <h1>Something went wrong.</h1>;
      }
      return this.props.children;
    }
  }
  ```

### State Management Patterns

- **Context + Reducer:**
  ```js
  const reducer = (state, action) => {
    switch (action.type) {
      case 'INCREMENT':
        return { count: state.count + 1 };
      default:
        return state;
    }
  };
  
  const [state, dispatch] = useReducer(reducer, { count: 0 });
  ```

### Common Algorithms

- **Binary Search:**
  ```js
  function binarySearch(arr, target) {
    let left = 0, right = arr.length - 1;
    while (left <= right) {
      const mid = Math.floor((left + right) / 2);
      if (arr[mid] === target) return mid;
      if (arr[mid] < target) left = mid + 1;
      else right = mid - 1;
    }
    return -1;
  }
  ```

- **Two Pointers:**
  ```js
  function twoSum(arr, target) {
    let left = 0, right = arr.length - 1;
    while (left < right) {
      const sum = arr[left] + arr[right];
      if (sum === target) return [left, right];
      if (sum < target) left++;
      else right--;
    }
    return [];
  }
  ```

### CSS Advanced

- **CSS Variables:**
  ```css
  :root {
    --primary-color: #007bff;
    --spacing: 16px;
  }
  
  .button {
    background: var(--primary-color);
    padding: var(--spacing);
  }
  ```

- **CSS Grid Advanced:**
  ```css
  .grid {
    display: grid;
    grid-template-areas: 
      "header header"
      "sidebar main";
    grid-template-rows: auto 1fr;
  }
  ```

### Security Basics

- **XSS Prevention:**
  ```js
  // Never use dangerouslySetInnerHTML with user input
  // Always sanitize data
  const sanitize = (str) => str.replace(/[<>]/g, '');
  ```

- **CSRF Protection:**
  ```js
  // Always validate tokens on sensitive operations
  // Use SameSite cookies
  ```

---


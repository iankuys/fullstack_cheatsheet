## 🧠 More React Interview Tips

- **Error boundaries:**
  ```js
  class ErrorBoundary extends React.Component {
    componentDidCatch(error, info) {
      // handle error
    }
    render() {
      return this.props.children;
    }
  }
  ```
- **State management:**
  Use local state, context, or a library (Redux, Zustand) as needed.
- **API error handling:**
  ```js
  try {
    await fetch(...);
  } catch (e) {
    setError('Failed to fetch');
  }
  ```
- **Lazy loading:**
  ```js
  const LazyComponent = React.lazy(() => import('./LazyComponent'));
  ```
- **Suspense:**
  ```js
  <React.Suspense fallback={<div>Loading...</div>}>
    <LazyComponent />
  </React.Suspense>
  ```
- **Testing components:**
  ```js
  import { render, screen } from '@testing-library/react';
  test('renders hello', () => {
    render(<div>Hello</div>);
    expect(screen.getByText('Hello')).toBeInTheDocument();
  });
  ```
## ⚛️ More Useful React Tips

- **Component names must start with a capital letter** (e.g., `MyComponent` not `myComponent`).
- **Keys in lists:** Always provide a unique `key` prop when rendering lists (e.g., `{items.map(item => <li key={item.id}>{item.name}</li>)}`).
- **Props are read-only:** You cannot modify props inside a component.
- **State updates are async:** Don't rely on immediate state changes after calling `setState`/`setX`.
- **Lifting state up:** Share state between components by moving it to their closest common ancestor.
- **Controlled vs uncontrolled inputs:** Controlled inputs use state (`value={x}`), uncontrolled use refs.
- **Fragments:** Use `<></>` or `<React.Fragment>` to group elements without adding extra nodes to the DOM.
- **Conditional rendering:** Use `{condition && <Component />}` or ternaries for showing/hiding UI.
- **Effect cleanup:** Return a function from `useEffect` to clean up subscriptions, timers, etc.
- **Avoid inline functions in render** for performance in large lists (use `useCallback`).

---

## ⚡️ Building Reactive Components (Like SidePanel)

### 1. **Loading States**
Always manage loading states for better UX:
```js
const [isLoading, setIsLoading] = useState(false);

// Usage
{isLoading ? <Spinner /> : <Content />}
```

### 2. **Selection State**
Track selected items with state:
```js
const [selectedId, setSelectedId] = useState(null);

// Usage
<div 
  style={{ 
    backgroundColor: selectedId === item.id ? '#e0e0e0' : 'transparent' 
  }}
  onClick={() => setSelectedId(item.id)}
>
```

### 3. **Conditional Rendering Patterns**
```js
// List with fallback
{items.length > 0 ? (
  items.map(item => <Item key={item.id} {...item} />)
) : (
  <EmptyState />
)}

// Show/hide based on condition
{showPanel && <SidePanel />}

// Loading overlay
{isLoading && <LoadingOverlay />}
```

### 4. **Event Handling Best Practices**
```js
// Use event.target.id for dynamic handling
function handleSelect(e) {
  setSelectedId(e.target.id);
  onItemSelect(e.target.id);
}

// Pass data through data attributes
<div data-id={item.id} onClick={handleClick}>
```

### 5. **State Updates with Functional Form**
```js
// When updating based on previous state
setItems(prev => [...prev, newItem]);

// When state depends on props
useEffect(() => {
  if (initialData) {
    setItems(initialData);
  }
}, [initialData]);
```

### 6. **Component Communication Patterns**
```js
// Parent passes callback to child
<SidePanel onItemSelect={handleItemSelect} />

// Child calls parent function
function SidePanel({ onItemSelect }) {
  return (
    <div onClick={() => onItemSelect(itemId)}>
  );
}
```

### 7. **Error Boundaries & Fallbacks**
```js
// Handle failed API calls gracefully
const [error, setError] = useState(null);

if (error) {
  return <ErrorMessage message={error} />;
}
```

### 8. **Performance Optimizations**
```js
// Memoize expensive calculations
const filteredItems = useMemo(() => 
  items.filter(item => item.name.includes(searchTerm)), 
  [items, searchTerm]
);

// Memoize callback functions
const handleClick = useCallback((id) => {
  onItemSelect(id);
}, [onItemSelect]);
```

### 9. **Responsive Design in Components**
```js
// Dynamic styling based on screen size
const [isMobile, setIsMobile] = useState(window.innerWidth < 768);

useEffect(() => {
  const handleResize = () => setIsMobile(window.innerWidth < 768);
  window.addEventListener('resize', handleResize);
  return () => window.removeEventListener('resize', handleResize);
}, []);
```

### 10. **Common Reactive Patterns**
```js
// Auto-fetch when component mounts
useEffect(() => {
  fetchData();
}, []);

// Re-fetch when dependencies change  
useEffect(() => {
  fetchUserData(userId);
}, [userId]);

// Cleanup on unmount
useEffect(() => {
  const timer = setInterval(fetchData, 5000);
  return () => clearInterval(timer);
}, []);
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
## 🪝 React `ref` Examples

Refs let you access DOM elements or React components directly.

### 1. **Scrolling to Bottom (Chat Example)**
```javascript
import React, { useRef, useEffect, useState } from 'react';

function Chat() {
  const [messages, setMessages] = useState([]);
  const chatRef = useRef(null);

  useEffect(() => {
    if (chatRef.current) {
      chatRef.current.scrollTop = chatRef.current.scrollHeight;
    }
  }, [messages]);

  return (
    <div
      ref={chatRef}
      style={{ maxHeight: 300, overflowY: 'auto' }}
    >
      {messages.map((msg, i) => <div key={i}>{msg}</div>)}
    </div>
  );
}
```

### 2. **Focusing an Input**
```javascript
import React, { useRef } from 'react';

function FocusInput() {
  const inputRef = useRef(null);

  const handleClick = () => {
    inputRef.current.focus();
  };

  return (
    <>
      <input ref={inputRef} />
      <button onClick={handleClick}>Focus Input</button>
    </>
  );
}
```

### 3. **Measuring Element Size**
```javascript
import React, { useRef, useEffect } from 'react';

function MeasureDiv() {
  const divRef = useRef(null);

  useEffect(() => {
    if (divRef.current) {
      console.log('Width:', divRef.current.offsetWidth);
    }
  }, []);

  return <div ref={divRef} style={{ width: 200 }}>Measure me!</div>;
}
```

---

**When to use refs:**  
- Scrolling, focusing, measuring, or triggering imperative actions on DOM elements.
- Not for managing React state or triggering re-renders.
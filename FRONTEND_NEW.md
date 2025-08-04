# 🎯 Frontend Interview Preparation Guide

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

### HTML Form Elements & Input Types

Forms are crucial for user interaction and data collection.

```html
<form action="/submit" method="POST">
    <!-- Text Inputs -->
    <label for="username">Username:</label>
    <input type="text" id="username" name="username" required>
    
    <label for="password">Password:</label>
    <input type="password" id="password" name="password" required>
    
    <label for="email">Email:</label>
    <input type="email" id="email" name="email" required>
    
    <!-- Number and Range -->
    <label for="age">Age:</label>
    <input type="number" id="age" name="age" min="18" max="100">
    
    <label for="volume">Volume:</label>
    <input type="range" id="volume" name="volume" min="0" max="100">
    
    <!-- Date and Time -->
    <label for="birthday">Birthday:</label>
    <input type="date" id="birthday" name="birthday">
    
    <label for="appointment">Appointment:</label>
    <input type="datetime-local" id="appointment" name="appointment">
    
    <!-- Selection -->
    <label for="country">Country:</label>
    <select id="country" name="country">
        <option value="us">United States</option>
        <option value="ca">Canada</option>
    </select>
    
    <!-- Checkboxes and Radio -->
    <fieldset>
        <legend>Preferred Contact Method:</legend>
        <input type="radio" id="email-contact" name="contact" value="email">
        <label for="email-contact">Email</label>
        
        <input type="radio" id="phone-contact" name="contact" value="phone">
        <label for="phone-contact">Phone</label>
    </fieldset>
    
    <input type="checkbox" id="newsletter" name="newsletter">
    <label for="newsletter">Subscribe to newsletter</label>
    
    <!-- File Upload -->
    <label for="avatar">Profile Picture:</label>
    <input type="file" id="avatar" name="avatar" accept="image/*">
    
    <!-- Textarea -->
    <label for="message">Message:</label>
    <textarea id="message" name="message" rows="4" cols="50"></textarea>
    
    <!-- Buttons -->
    <button type="submit">Submit</button>
    <button type="reset">Reset</button>
    <button type="button" onclick="doSomething()">Custom Action</button>
</form>
```

### HTML Accessibility (A11y) Essentials

Making your HTML accessible ensures everyone can use your application.

```html
<!-- Always use alt text for images -->
<img src="chart.png" alt="Sales increased 25% from Q1 to Q2">

<!-- Use proper heading hierarchy -->
<h1>Main Title</h1>
    <h2>Section Title</h2>
        <h3>Subsection Title</h3>

<!-- Associate labels with form controls -->
<label for="search">Search:</label>
<input type="text" id="search" name="search">

<!-- Use ARIA attributes when needed -->
<button aria-expanded="false" aria-controls="menu">Menu</button>
<ul id="menu" aria-hidden="true">
    <li><a href="#">Link 1</a></li>
</ul>

<!-- Use proper table structure -->
<table>
    <caption>Monthly Sales Data</caption>
    <thead>
        <tr>
            <th scope="col">Month</th>
            <th scope="col">Sales</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>January</td>
            <td>$50,000</td>
        </tr>
    </tbody>
</table>

<!-- Use landmarks for navigation -->
<nav aria-label="Main navigation">
    <ul>
        <li><a href="#main">Skip to main content</a></li>
    </ul>
</nav>
```

### Common HTML Attributes & Meta Tags

```html
<!-- Essential meta tags -->
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<meta name="description" content="Page description for SEO">
<meta name="keywords" content="keyword1, keyword2">

<!-- Open Graph for social media -->
<meta property="og:title" content="Page Title">
<meta property="og:description" content="Page description">
<meta property="og:image" content="image-url.jpg">

<!-- Common attributes -->
<div class="container" id="main" data-testid="main-container">
    <a href="https://example.com" target="_blank" rel="noopener noreferrer">External Link</a>
    <img src="image.jpg" alt="Description" loading="lazy">
    <button disabled>Disabled Button</button>
    <input required placeholder="Enter text...">
</div>
```

---

## 🚀 JavaScript Fundamentals

### Data Types & Type Checking

JavaScript has several primitive and non-primitive data types.

```js
// Primitive Types
const str = 'hello';           // string
const num = 42;                // number
const bool = true;             // boolean
const nothing = null;          // null (intentional empty value)
const notDefined = undefined;  // undefined (declared but not assigned)
const sym = Symbol('id');      // symbol (unique identifier)
const bigInt = 123n;          // bigint (large integers)

// Non-primitive Types
const obj = { name: 'John' };  // object
const arr = [1, 2, 3];        // array (also an object)
const func = function() {};    // function (also an object)

// Type Checking
console.log(typeof str);       // 'string'
console.log(typeof num);       // 'number'
console.log(typeof bool);      // 'boolean'
console.log(typeof nothing);   // 'object' (JavaScript quirk!)
console.log(typeof notDefined); // 'undefined'
console.log(typeof obj);       // 'object'
console.log(typeof arr);       // 'object'
console.log(typeof func);      // 'function'

// Better type checking
console.log(Array.isArray(arr));           // true
console.log(nothing === null);             // true
console.log(obj.constructor.name);         // 'Object'
```

### Array Methods (Essential for Interviews)

Arrays are fundamental in JavaScript and these methods are commonly tested.

```js
const numbers = [1, 2, 3, 4, 5];
const users = [
  { id: 1, name: 'John', age: 25 },
  { id: 2, name: 'Jane', age: 30 },
  { id: 3, name: 'Bob', age: 35 }
];

// ✅ Transformation Methods
const doubled = numbers.map(x => x * 2);                    // [2, 4, 6, 8, 10]
const userNames = users.map(user => user.name);             // ['John', 'Jane', 'Bob']

// ✅ Filtering Methods
const evens = numbers.filter(x => x % 2 === 0);             // [2, 4]
const adults = users.filter(user => user.age >= 30);        // [Jane, Bob]

// ✅ Reduction Methods
const sum = numbers.reduce((acc, x) => acc + x, 0);         // 15
const totalAge = users.reduce((acc, user) => acc + user.age, 0); // 90

// ✅ Search Methods
const found = numbers.find(x => x > 3);                     // 4
const foundUser = users.find(user => user.name === 'Jane'); // {id: 2, name: 'Jane', age: 30}
const foundIndex = numbers.findIndex(x => x > 3);           // 3

// ✅ Testing Methods
const hasLargeNumber = numbers.some(x => x > 4);            // true
const allPositive = numbers.every(x => x > 0);              // true
const includesTwo = numbers.includes(2);                    // true

// ✅ Iteration Methods
numbers.forEach(x => console.log(x));                       // logs each number

// ✅ Flattening Methods
const nested = [[1, 2], [3, 4], [5]];
const flattened = nested.flat();                            // [1, 2, 3, 4, 5]
const deepNested = [1, [2, [3, [4]]]];
const deepFlattened = deepNested.flat(3);                   // [1, 2, 3, 4]

const mappedAndFlattened = nested.flatMap(arr => arr.map(x => x * 2)); // [2, 4, 6, 8, 10]

// ✅ String Conversion
const joined = numbers.join('-');                           // "1-2-3-4-5"

// ✅ Extraction Methods
const slice = numbers.slice(1, 3);                          // [2, 3] (doesn't modify original)

// ✅ Mutating Methods (change original array)
const arr = [1, 2, 3];
arr.splice(1, 1, 'new');                                    // [1, 'new', 3]
arr.sort();                                                 // sorts in place
arr.reverse();                                              // reverses in place
arr.push(4);                                                // adds to end
arr.pop();                                                  // removes from end
arr.unshift(0);                                             // adds to beginning
arr.shift();                                                // removes from beginning
```

### Object Manipulation & ES6+ Features

Modern JavaScript provides powerful tools for working with objects and data.

```js
// Object Creation & Manipulation
const person = {
  name: 'John',
  age: 30,
  city: 'New York'
};

// ✅ Destructuring
const { name, age } = person;                              // Extract properties
const { name: userName, age: userAge } = person;           // Rename while destructuring
const [first, second, ...rest] = [1, 2, 3, 4, 5];         // Array destructuring

// ✅ Spread Operator
const newPerson = { ...person, country: 'USA' };          // Clone and add property
const numbers = [1, 2, 3];
const moreNumbers = [...numbers, 4, 5];                   // Clone and add elements

// ✅ Object Methods
const keys = Object.keys(person);                          // ['name', 'age', 'city']
const values = Object.values(person);                      // ['John', 30, 'New York']
const entries = Object.entries(person);                    // [['name', 'John'], ...]

// ✅ Modern Object Features
const computedKey = 'dynamic';
const modernObject = {
  // Computed property names
  [computedKey]: 'value',
  
  // Method shorthand
  greet() {
    return `Hello, ${this.name}`;
  },
  
  // Property shorthand
  name,  // equivalent to name: name
  age    // equivalent to age: age
};

// ✅ Optional Chaining & Nullish Coalescing
const user = { profile: { address: { city: 'NYC' } } };
const city = user?.profile?.address?.city;                 // Safe property access
const defaultValue = someVariable ?? 'default';           // Only null/undefined use default
```

### Functions & Arrow Functions

Understanding different function types and their behaviors is crucial.

```js
// ✅ Function Declaration (hoisted)
function regularFunction(a, b) {
  return a + b;
}

// ✅ Function Expression (not hoisted)
const functionExpression = function(a, b) {
  return a + b;
};

// ✅ Arrow Functions (concise, lexical this)
const arrowFunction = (a, b) => a + b;                     // Implicit return
const arrowWithBlock = (a, b) => {                         // Explicit return
  const result = a + b;
  return result;
};

// ✅ Default Parameters
function greet(name = 'World', greeting = 'Hello') {
  return `${greeting}, ${name}!`;
}

// ✅ Rest Parameters
function sum(...numbers) {
  return numbers.reduce((acc, num) => acc + num, 0);
}

// ✅ Higher-Order Functions
function createMultiplier(factor) {
  return function(number) {
    return number * factor;
  };
}

const double = createMultiplier(2);
console.log(double(5)); // 10

// ✅ Closures (functions remember their lexical scope)
function counter() {
  let count = 0;
  return function() {
    return ++count;
  };
}

const myCounter = counter();
console.log(myCounter()); // 1
console.log(myCounter()); // 2
```

### Asynchronous JavaScript

Understanding async patterns is essential for modern web development.

```js
// ✅ Promises
const fetchData = () => {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      const success = Math.random() > 0.5;
      if (success) {
        resolve({ data: 'Successfully fetched!' });
      } else {
        reject(new Error('Failed to fetch data'));
      }
    }, 1000);
  });
};

// ✅ Promise Chaining
fetchData()
  .then(result => {
    console.log(result.data);
    return 'processed';
  })
  .then(processed => {
    console.log(processed);
  })
  .catch(error => {
    console.error('Error:', error.message);
  })
  .finally(() => {
    console.log('Cleanup operations');
  });

// ✅ Async/Await (cleaner syntax)
async function handleData() {
  try {
    const result = await fetchData();
    console.log(result.data);
    
    // Multiple awaits
    const data1 = await fetchData();
    const data2 = await fetchData();
    
    return { data1, data2 };
  } catch (error) {
    console.error('Error:', error.message);
    throw error; // Re-throw if needed
  }
}

// ✅ Promise Utilities
const promises = [fetchData(), fetchData(), fetchData()];

Promise.all(promises)           // Wait for all to complete
  .then(results => console.log('All completed:', results))
  .catch(error => console.error('One failed:', error));

Promise.allSettled(promises)    // Wait for all to settle (resolve or reject)
  .then(results => console.log('All settled:', results));

Promise.race(promises)          // First to complete wins
  .then(result => console.log('First completed:', result));
```

### Common Algorithms & Problem Solving

These patterns frequently appear in technical interviews.

```js
// ✅ Two Pointers Technique
function twoSum(nums, target) {
  const map = new Map();
  for (let i = 0; i < nums.length; i++) {
    const complement = target - nums[i];
    if (map.has(complement)) {
      return [map.get(complement), i];
    }
    map.set(nums[i], i);
  }
  return [];
}

// ✅ Binary Search
function binarySearch(arr, target) {
  let left = 0;
  let right = arr.length - 1;
  
  while (left <= right) {
    const mid = Math.floor((left + right) / 2);
    
    if (arr[mid] === target) {
      return mid;
    } else if (arr[mid] < target) {
      left = mid + 1;
    } else {
      right = mid - 1;
    }
  }
  
  return -1;
}

// ✅ Debouncing (delay execution)
function debounce(func, delay) {
  let timeoutId;
  return function(...args) {
    clearTimeout(timeoutId);
    timeoutId = setTimeout(() => func.apply(this, args), delay);
  };
}

// ✅ Throttling (limit execution rate)
function throttle(func, limit) {
  let lastCall = 0;
  return function(...args) {
    const now = Date.now();
    if (now - lastCall >= limit) {
      lastCall = now;
      func.apply(this, args);
    }
  };
}

// Usage examples
const expensiveOperation = (query) => console.log('Searching for:', query);
const debouncedSearch = debounce(expensiveOperation, 300);
const throttledScroll = throttle(() => console.log('Scrolling...'), 100);
```

---

## 🎨 CSS Fundamentals

### CSS Box Model & Layout Basics

Understanding the box model is fundamental to CSS layout.

```css
/* Box Model Components */
.box-model-example {
  /* Content area */
  width: 200px;
  height: 100px;
  
  /* Padding (space inside border) */
  padding: 20px;                    /* All sides */
  padding: 10px 20px;              /* Vertical | Horizontal */
  padding: 10px 15px 20px 25px;    /* Top | Right | Bottom | Left */
  
  /* Border */
  border: 2px solid #333;
  border-radius: 8px;              /* Rounded corners */
  
  /* Margin (space outside border) */
  margin: 16px;
  margin: 10px auto;               /* Center horizontally */
  
  /* Box-sizing controls how width/height are calculated */
  box-sizing: border-box;          /* Include padding/border in width/height */
}

/* Display Types */
.block-element {
  display: block;                  /* Takes full width, stacks vertically */
}

.inline-element {
  display: inline;                 /* Only takes needed width, flows horizontally */
}

.inline-block-element {
  display: inline-block;           /* Flows horizontally but accepts width/height */
}
```

### Flexbox Layout System

Flexbox is perfect for one-dimensional layouts and component alignment.

```css
/* Flex Container */
.flex-container {
  display: flex;
  
  /* Main axis direction */
  flex-direction: row;             /* row | row-reverse | column | column-reverse */
  
  /* Main axis alignment */
  justify-content: center;         /* flex-start | flex-end | center | space-between | space-around | space-evenly */
  
  /* Cross axis alignment */
  align-items: center;             /* flex-start | flex-end | center | baseline | stretch */
  
  /* Wrap behavior */
  flex-wrap: wrap;                 /* nowrap | wrap | wrap-reverse */
  
  /* Gap between items */
  gap: 16px;                       /* Modern way to add spacing */
}

/* Flex Items */
.flex-item {
  /* Flex grow (how much extra space to take) */
  flex-grow: 1;                    /* Default: 0 */
  
  /* Flex shrink (how much to shrink when needed) */
  flex-shrink: 1;                  /* Default: 1 */
  
  /* Flex basis (initial size before free space distribution) */
  flex-basis: 200px;               /* Default: auto */
  
  /* Shorthand: flex: grow shrink basis */
  flex: 1 1 200px;
  flex: 1;                         /* Common: flex: 1 1 0% */
  
  /* Individual alignment override */
  align-self: flex-end;            /* Override container's align-items */
}

/* Common Flex Patterns */
.navbar {
  display: flex;
  justify-content: space-between;  /* Logo left, menu right */
  align-items: center;
}

.card-grid {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
}

.card-grid > .card {
  flex: 1 1 300px;                 /* Grow, shrink, min 300px width */
}

.center-content {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;               /* Full viewport height */
}
```

### CSS Grid Layout System

CSS Grid is perfect for two-dimensional layouts and complex designs.

```css
/* Grid Container */
.grid-container {
  display: grid;
  
  /* Define columns */
  grid-template-columns: 200px 1fr 100px;        /* Fixed | Flexible | Fixed */
  grid-template-columns: repeat(3, 1fr);         /* 3 equal columns */
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr)); /* Responsive columns */
  
  /* Define rows */
  grid-template-rows: auto 1fr auto;             /* Header | Content | Footer */
  
  /* Gap between grid items */
  gap: 20px;                                     /* Row and column gap */
  grid-gap: 20px 10px;                          /* Row gap | Column gap */
  
  /* Named grid areas */
  grid-template-areas: 
    "header header header"
    "sidebar main aside"
    "footer footer footer";
}

/* Grid Items */
.grid-item {
  /* Position by line numbers */
  grid-column: 1 / 3;                           /* Start line 1, end line 3 */
  grid-row: 2 / 4;
  
  /* Position by span */
  grid-column: span 2;                          /* Span 2 columns */
  grid-row: span 3;                             /* Span 3 rows */
  
  /* Named area placement */
  grid-area: header;                            /* Place in "header" area */
  
  /* Individual alignment */
  justify-self: center;                         /* Horizontal alignment */
  align-self: start;                            /* Vertical alignment */
}

/* Common Grid Patterns */
.layout {
  display: grid;
  grid-template-areas: 
    "header"
    "main"
    "footer";
  grid-template-rows: auto 1fr auto;
  min-height: 100vh;
}

.header { grid-area: header; }
.main { grid-area: main; }
.footer { grid-area: footer; }

@media (min-width: 768px) {
  .layout {
    grid-template-areas: 
      "header header"
      "sidebar main"
      "footer footer";
    grid-template-columns: 250px 1fr;
  }
}
```

### CSS Positioning & Z-Index

Understanding positioning is crucial for creating complex layouts and overlays.

```css
/* Position Types */
.static-element {
  position: static;                             /* Default: normal document flow */
}

.relative-element {
  position: relative;                           /* Positioned relative to itself */
  top: 10px;                                   /* Move 10px down from normal position */
  left: 20px;                                  /* Move 20px right from normal position */
}

.absolute-element {
  position: absolute;                           /* Positioned relative to nearest positioned ancestor */
  top: 0;
  right: 0;                                    /* Top-right corner of positioned parent */
  z-index: 10;                                 /* Stacking order */
}

.fixed-element {
  position: fixed;                              /* Positioned relative to viewport */
  bottom: 20px;
  right: 20px;                                 /* Fixed to bottom-right of screen */
  z-index: 1000;                               /* Above other content */
}

.sticky-element {
  position: sticky;                             /* Sticky positioning (scroll-aware) */
  top: 0;                                      /* Stick to top when scrolling */
  z-index: 100;
}

/* Z-Index Stacking Context */
.modal-backdrop {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
  z-index: 1000;                               /* Behind modal */
}

.modal {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);            /* Center perfectly */
  z-index: 1001;                               /* Above backdrop */
}
```

### Typography & Text Styling

Proper typography improves readability and user experience.

```css
/* Font Properties */
.typography-example {
  /* Font family with fallbacks */
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  
  /* Font size (responsive units preferred) */
  font-size: 1.125rem;                        /* 18px if root is 16px */
  font-size: clamp(1rem, 2.5vw, 1.5rem);     /* Responsive font size */
  
  /* Font weight */
  font-weight: 400;                           /* 400 (normal) | 700 (bold) */
  font-weight: bold;
  
  /* Line height (for readability) */
  line-height: 1.6;                          /* 1.4-1.8 is good for body text */
  
  /* Letter and word spacing */
  letter-spacing: 0.025em;
  word-spacing: 0.1em;
  
  /* Text color */
  color: #333;                                /* Dark gray for good contrast */
  color: hsl(220, 15%, 25%);                 /* HSL for easier color manipulation */
}

/* Text Alignment & Decoration */
.text-styles {
  text-align: center;                         /* left | right | center | justify */
  text-decoration: underline;                 /* none | underline | line-through */
  text-transform: capitalize;                 /* none | uppercase | lowercase | capitalize */
  
  /* Text overflow handling */
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;                    /* Add ... when text overflows */
}

/* Modern Typography Scale */
:root {
  --font-size-xs: 0.75rem;     /* 12px */
  --font-size-sm: 0.875rem;    /* 14px */
  --font-size-base: 1rem;      /* 16px */
  --font-size-lg: 1.125rem;    /* 18px */
  --font-size-xl: 1.25rem;     /* 20px */
  --font-size-2xl: 1.5rem;     /* 24px */
  --font-size-3xl: 1.875rem;   /* 30px */
  --font-size-4xl: 2.25rem;    /* 36px */
}

.heading-1 { font-size: var(--font-size-4xl); }
.heading-2 { font-size: var(--font-size-3xl); }
.heading-3 { font-size: var(--font-size-2xl); }
.body-text { font-size: var(--font-size-base); }
.small-text { font-size: var(--font-size-sm); }
```

### Colors, Backgrounds & Visual Effects

Creating appealing visual designs with CSS.

```css
/* Color Values */
.color-examples {
  /* Different color formats */
  color: #ff6b6b;                             /* Hex */
  color: rgb(255, 107, 107);                  /* RGB */
  color: rgba(255, 107, 107, 0.8);            /* RGB with alpha */
  color: hsl(0, 100%, 71%);                   /* HSL (easier to manipulate) */
  color: hsla(0, 100%, 71%, 0.8);             /* HSL with alpha */
  
  /* CSS Custom Properties (Variables) */
  --primary-color: #007bff;
  --secondary-color: #6c757d;
  --success-color: #28a745;
  --danger-color: #dc3545;
  
  color: var(--primary-color);
}

/* Background Properties */
.background-examples {
  /* Solid colors */
  background-color: #f8f9fa;
  
  /* Gradients */
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  background: radial-gradient(circle, #ff6b6b, #4ecdc4);
  
  /* Images */
  background-image: url('hero-bg.jpg');
  background-size: cover;                     /* cover | contain | 100% 100% */
  background-position: center center;
  background-repeat: no-repeat;
  background-attachment: fixed;               /* Parallax effect */
  
  /* Multiple backgrounds */
  background: 
    linear-gradient(rgba(0,0,0,0.4), rgba(0,0,0,0.4)),
    url('image.jpg') center/cover no-repeat;
}

/* Shadows & Effects */
.visual-effects {
  /* Box shadows */
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);                    /* Subtle */
  box-shadow: 0 4px 6px -1px rgba(0,0,0,0.1);              /* Material Design */
  box-shadow: 0 10px 15px -3px rgba(0,0,0,0.1);            /* Elevated */
  box-shadow: inset 0 2px 4px rgba(0,0,0,0.1);             /* Inset shadow */
  
  /* Text shadows */
  text-shadow: 1px 1px 2px rgba(0,0,0,0.3);
  
  /* Border radius */
  border-radius: 8px;                                       /* Rounded corners */
  border-radius: 50%;                                       /* Circle */
  border-radius: 8px 8px 0 0;                              /* Top corners only */
  
  /* Opacity */
  opacity: 0.8;                                             /* 0 (transparent) to 1 (opaque) */
}
```

### CSS Transitions & Animations

Adding smooth interactions and animations to enhance user experience.

```css
/* Transitions (smooth changes between states) */
.transition-example {
  background-color: #007bff;
  color: white;
  padding: 12px 24px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  
  /* Transition property */
  transition: all 0.3s ease;                               /* Property | Duration | Timing Function */
  transition: background-color 0.3s ease, transform 0.2s ease; /* Multiple properties */
}

.transition-example:hover {
  background-color: #0056b3;
  transform: translateY(-2px);                             /* Slight lift effect */
  box-shadow: 0 4px 8px rgba(0,0,0,0.2);
}

/* Timing Functions */
.timing-functions {
  transition-timing-function: ease;                        /* Default */
  transition-timing-function: linear;                      /* Constant speed */
  transition-timing-function: ease-in;                     /* Slow start */
  transition-timing-function: ease-out;                    /* Slow end */
  transition-timing-function: ease-in-out;                 /* Slow start and end */
  transition-timing-function: cubic-bezier(0.25, 0.46, 0.45, 0.94); /* Custom */
}

/* Keyframe Animations */
@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes bounce {
  0%, 20%, 53%, 80%, 100% {
    transform: translate3d(0, 0, 0);
  }
  40%, 43% {
    transform: translate3d(0, -30px, 0);
  }
  70% {
    transform: translate3d(0, -15px, 0);
  }
  90% {
    transform: translate3d(0, -4px, 0);
  }
}

.animated-element {
  animation: fadeIn 0.6s ease-out;                         /* Name | Duration | Timing | Delay */
  animation: bounce 2s infinite;                           /* Infinite animation */
  
  /* Animation properties */
  animation-name: fadeIn;
  animation-duration: 0.6s;
  animation-timing-function: ease-out;
  animation-delay: 0.2s;
  animation-iteration-count: 1;                            /* 1 | infinite | number */
  animation-direction: normal;                             /* normal | reverse | alternate */
  animation-fill-mode: forwards;                           /* none | forwards | backwards | both */
  animation-play-state: running;                           /* running | paused */
}
```

### Responsive Design & Media Queries

Creating layouts that work on all device sizes.

```css
/* Mobile-First Approach (Recommended) */
/* Base styles (mobile) */
.responsive-container {
  padding: 16px;
  font-size: 14px;
}

/* Small tablets and large phones */
@media (min-width: 576px) {
  .responsive-container {
    padding: 20px;
    font-size: 16px;
  }
}

/* Medium tablets */
@media (min-width: 768px) {
  .responsive-container {
    padding: 24px;
    max-width: 720px;
    margin: 0 auto;
  }
}

/* Large tablets and small laptops */
@media (min-width: 992px) {
  .responsive-container {
    max-width: 960px;
    padding: 32px;
  }
}

/* Large laptops and desktops */
@media (min-width: 1200px) {
  .responsive-container {
    max-width: 1140px;
    padding: 40px;
  }
}

/* Responsive Units */
.responsive-units {
  /* Viewport units */
  width: 100vw;                                           /* 100% of viewport width */
  height: 100vh;                                          /* 100% of viewport height */
  font-size: 4vw;                                         /* 4% of viewport width */
  
  /* Relative units */
  padding: 1em;                                           /* Relative to element's font-size */
  margin: 1rem;                                           /* Relative to root font-size */
  
  /* Percentage */
  width: 50%;                                             /* 50% of parent width */
  
  /* Modern responsive functions */
  font-size: clamp(1rem, 2.5vw, 2rem);                   /* Min | Preferred | Max */
  width: min(90%, 600px);                                 /* Use smaller value */
  width: max(200px, 30%);                                 /* Use larger value */
}

/* Container Queries (Modern) */
@container (min-width: 400px) {
  .card {
    display: flex;
    flex-direction: row;
  }
}

/* Print Styles */
@media print {
  .no-print {
    display: none;
  }
  
  body {
    font-size: 12pt;
    color: black;
    background: white;
  }
}

/* Dark Mode Support */
@media (prefers-color-scheme: dark) {
  body {
    background-color: #1a1a1a;
    color: #ffffff;
  }
}

/* Reduced Motion Preference */
@media (prefers-reduced-motion: reduce) {
  * {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
  }
}
```

---

## 🌐 Web APIs & Browser Features

### DOM Manipulation

Understanding how to interact with the DOM is fundamental for frontend development.

```js
// ✅ Element Selection
const element = document.getElementById('myId');
const elements = document.getElementsByClassName('myClass');
const element2 = document.querySelector('.my-class');
const elements2 = document.querySelectorAll('.my-class');

// ✅ Element Creation & Modification
const newDiv = document.createElement('div');
newDiv.textContent = 'Hello World';
newDiv.innerHTML = '<strong>Bold Text</strong>';
newDiv.className = 'my-class';
newDiv.setAttribute('data-id', '123');

// ✅ DOM Tree Manipulation
const parent = document.querySelector('.parent');
parent.appendChild(newDiv);                               // Add to end
parent.insertBefore(newDiv, parent.firstChild);         // Add to beginning
parent.removeChild(newDiv);                              // Remove child
newDiv.remove();                                         // Remove self (modern)

// ✅ Event Handling
element.addEventListener('click', function(event) {
  event.preventDefault();                                // Stop default behavior
  event.stopPropagation();                              // Stop event bubbling
  console.log('Clicked!', event.target);
});

// Modern event handling with arrow functions
element.addEventListener('click', (e) => {
  console.log('Button clicked!');
});

// Remove event listener
const handleClick = (e) => console.log('Clicked!');
element.addEventListener('click', handleClick);
element.removeEventListener('click', handleClick);

// ✅ Style Manipulation
element.style.backgroundColor = 'red';
element.style.fontSize = '16px';
element.classList.add('active');
element.classList.remove('inactive');
element.classList.toggle('visible');
element.classList.contains('active');                    // Returns boolean
```

### Browser Storage APIs

Different ways to store data in the browser for various use cases.

```js
// ✅ Local Storage (persistent, ~5-10MB limit)
localStorage.setItem('username', 'john_doe');
localStorage.setItem('settings', JSON.stringify({ theme: 'dark' }));

const username = localStorage.getItem('username');
const settings = JSON.parse(localStorage.getItem('settings')) || {};

localStorage.removeItem('username');
localStorage.clear();                                    // Remove all items

// ✅ Session Storage (tab-specific, cleared when tab closes)
sessionStorage.setItem('tempData', 'temporary');
const tempData = sessionStorage.getItem('tempData');

// ✅ Cookies (sent with HTTP requests, ~4KB limit)
document.cookie = 'user=john; expires=Thu, 18 Dec 2024 12:00:00 UTC; path=/';

// Helper function to get cookie value
function getCookie(name) {
  const value = `; ${document.cookie}`;
  const parts = value.split(`; ${name}=`);
  if (parts.length === 2) return parts.pop().split(';').shift();
}

// ✅ IndexedDB (more complex, for large amounts of structured data)
const request = indexedDB.open('MyDatabase', 1);
request.onsuccess = (event) => {
  const db = event.target.result;
  // Use database...
};
```

### Fetch API & HTTP Requests

Modern way to make HTTP requests in JavaScript.

```js
// ✅ Basic GET Request
async function fetchData() {
  try {
    const response = await fetch('/api/users');
    
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    
    const data = await response.json();
    return data;
  } catch (error) {
    console.error('Fetch error:', error);
    throw error;
  }
}

// ✅ POST Request with JSON
async function createUser(userData) {
  try {
    const response = await fetch('/api/users', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'Authorization': `Bearer ${token}`
      },
      body: JSON.stringify(userData)
    });
    
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    
    return await response.json();
  } catch (error) {
    console.error('Create user error:', error);
    throw error;
  }
}

// ✅ File Upload
async function uploadFile(file) {
  const formData = new FormData();
  formData.append('file', file);
  
  try {
    const response = await fetch('/api/upload', {
      method: 'POST',
      body: formData  // Don't set Content-Type header for FormData
    });
    
    return await response.json();
  } catch (error) {
    console.error('Upload error:', error);
    throw error;
  }
}

// ✅ Request with Timeout
async function fetchWithTimeout(url, options = {}, timeout = 5000) {
  const controller = new AbortController();
  const timeoutId = setTimeout(() => controller.abort(), timeout);
  
  try {
    const response = await fetch(url, {
      ...options,
      signal: controller.signal
    });
    
    clearTimeout(timeoutId);
    return response;
  } catch (error) {
    clearTimeout(timeoutId);
    if (error.name === 'AbortError') {
      throw new Error('Request timeout');
    }
    throw error;
  }
}
```

---

## 🔧 Performance & Best Practices

### Performance Optimization Techniques

```js
// ✅ Debouncing (delay execution until after events stop firing)
function debounce(func, delay) {
  let timeoutId;
  return function (...args) {
    clearTimeout(timeoutId);
    timeoutId = setTimeout(() => func.apply(this, args), delay);
  };
}

// Usage: Search as user types
const searchInput = document.querySelector('#search');
const debouncedSearch = debounce((query) => {
  console.log('Searching for:', query);
  // Make API call here
}, 300);

searchInput.addEventListener('input', (e) => {
  debouncedSearch(e.target.value);
});

// ✅ Throttling (limit execution rate)
function throttle(func, limit) {
  let inThrottle;
  return function (...args) {
    if (!inThrottle) {
      func.apply(this, args);
      inThrottle = true;
      setTimeout(() => inThrottle = false, limit);
    }
  };
}

// Usage: Scroll events
const throttledScroll = throttle(() => {
  console.log('Scrolling...');
}, 100);

window.addEventListener('scroll', throttledScroll);

// ✅ Lazy Loading Images
const lazyImages = document.querySelectorAll('img[data-src]');
const imageObserver = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      const img = entry.target;
      img.src = img.dataset.src;
      img.removeAttribute('data-src');
      imageObserver.unobserve(img);
    }
  });
});

lazyImages.forEach(img => imageObserver.observe(img));
```

### Security Best Practices

```js
// ✅ Input Sanitization (always validate on backend too!)
function sanitizeInput(input) {
  const div = document.createElement('div');
  div.textContent = input;
  return div.innerHTML; // Escapes HTML characters
}

// ✅ Avoid XSS (Cross-Site Scripting)
// Never use innerHTML with user input
// element.innerHTML = userInput; // ❌ Dangerous!
element.textContent = userInput; // ✅ Safe

// ✅ Content Security Policy (CSP) - set in HTTP headers
// Content-Security-Policy: default-src 'self'; script-src 'self' 'unsafe-inline'

// ✅ Validate URLs before redirecting
function isValidURL(url) {
  try {
    const parsedURL = new URL(url);
    return ['http:', 'https:'].includes(parsedURL.protocol);
  } catch {
    return false;
  }
}

// ✅ Use HTTPS for sensitive data
// Always use secure cookies for authentication
document.cookie = "sessionId=abc123; Secure; HttpOnly; SameSite=Strict";
```

---

## 📊 Common HTTP Status Codes

Understanding HTTP status codes is essential for API integration and debugging.

```js
// ✅ Success Codes (2xx)
// 200 OK - Request successful, response contains data
// 201 Created - Resource successfully created
// 204 No Content - Request successful, no response body

// ✅ Client Error Codes (4xx)
// 400 Bad Request - Invalid request syntax or parameters
// 401 Unauthorized - Authentication required
// 403 Forbidden - Server understood request but refuses to authorize
// 404 Not Found - Requested resource not found
// 422 Unprocessable Entity - Request valid but contains semantic errors
// 429 Too Many Requests - Rate limit exceeded

// ✅ Server Error Codes (5xx)
// 500 Internal Server Error - Generic server error
// 502 Bad Gateway - Invalid response from upstream server
// 503 Service Unavailable - Server temporarily unavailable

// Example error handling
async function apiRequest(url) {
  try {
    const response = await fetch(url);
    
    switch (response.status) {
      case 200:
        return await response.json();
      case 401:
        throw new Error('Please log in to continue');
      case 403:
        throw new Error('You don\'t have permission to access this resource');
      case 404:
        throw new Error('Resource not found');
      case 429:
        throw new Error('Too many requests. Please try again later');
      case 500:
        throw new Error('Server error. Please try again later');
      default:
        throw new Error(`Request failed with status ${response.status}`);
    }
  } catch (error) {
    console.error('API Error:', error);
    throw error;
  }
}
```

---

## 💡 Interview Tips

### Technical Communication
- **Explain your thought process**: Walk through your reasoning step by step
- **Ask clarifying questions**: "Should I handle edge cases like empty arrays?"
- **Discuss trade-offs**: "This approach is faster but uses more memory"
- **Admit when you don't know**: "I'm not familiar with that, but here's how I'd research it"

### Code Quality
- **Use meaningful variable names**: `userAge` instead of `a`
- **Write readable code**: Choose clarity over cleverness
- **Consider edge cases**: Empty arrays, null values, invalid input
- **Think about performance**: Time and space complexity

### Problem-Solving Approach
1. **Understand the problem**: Restate requirements in your own words
2. **Plan your solution**: Outline the approach before coding
3. **Start simple**: Get a working solution first, then optimize
4. **Test as you go**: Use examples to verify your logic
5. **Refactor if needed**: Clean up your code for readability

Remember: Interviews are conversations, not interrogations. Show curiosity, enthusiasm, and genuine interest in solving problems!

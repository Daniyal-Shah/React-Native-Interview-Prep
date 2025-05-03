# **Comprehensive Guide to Testing in React Native with Jest and Testing Library**

This guide provides an in-depth look at unit testing, component testing, and integration testing in React Native, including all essential matchers, functions, and practical examples.

---

## **1. Unit Testing (Business Logic)**
Unit tests verify individual functions, utilities, or pure business logic in isolation.

### **Key Matchers**
| Matcher | Purpose | Example |
|---------|---------|---------|
| `toBe()` | Strict equality (primitives) | `expect(sum(1, 2)).toBe(3)` |
| `toEqual()` | Deep equality (objects/arrays) | `expect(user).toEqual({ name: 'John' })` |
| `toBeTruthy()`/`toBeFalsy()` | Truthy/falsy checks | `expect(isValid).toBeTruthy()` |
| `toHaveLength()` | Array/string length | `expect(list).toHaveLength(3)` |
| `toContain()` | Array/string inclusion | `expect(colors).toContain('red')` |
| `toMatch()` | Regex matching | `expect(error.message).toMatch(/invalid/i)` |
| `toThrow()` | Error throwing | `expect(() => parse('bad')).toThrow()` |

### **Mocking Functions**
```javascript
// Create a mock function
const mockFn = jest.fn();

// Mock return value
mockFn.mockReturnValue(42);

// Mock implementation
mockFn.mockImplementation((x, y) => x + y);

// Assertions
expect(mockFn).toHaveBeenCalled();
expect(mockFn).toHaveBeenCalledWith(1, 2);
expect(mockFn).toHaveReturnedWith(3);
```

### **Example: Testing a Utility Function**
```javascript
// utils.js
export const formatName = (first, last) => `${first} ${last}`;

// utils.test.js
test('formats full name', () => {
  expect(formatName('John', 'Doe')).toBe('John Doe');
  expect(formatName('', '')).toBe(' ');
});
```

---

## **2. Component Testing (UI)**
Component tests verify React Native components render and behave as expected.

### **Key Queries**
| Query | Purpose | Example |
|-------|---------|---------|
| `getByText` | Find by text content | `getByText('Submit')` |
| `getByTestId` | Find by `testID` prop | `getByTestId('login-btn')` |
| `getByPlaceholderText` | Find inputs by placeholder | `getByPlaceholderText('Email')` |
| `getByRole` | Find by accessibility role | `getByRole('button')` |
| `queryBy*` | Return null (safe for assertions) | `expect(queryByText('Error')).toBeNull()` |
| `findBy*` | Async element query | `await findByText('Success')` |

### **User Interaction**
```javascript
import { render, fireEvent } from '@testing-library/react-native';

test('button triggers onPress', () => {
  const mockPress = jest.fn();
  const { getByText } = render(<Button onPress={mockPress} title="Click" />);
  
  fireEvent.press(getByText('Click'));
  expect(mockPress).toHaveBeenCalled();
});
```

### **Testing Hooks**
```javascript
test('useState hook', () => {
  const TestComponent = () => {
    const [count, setCount] = useState(0);
    return (
      <Button onPress={() => setCount(c => c + 1)} title={`Count: ${count}`} />
    );
  };

  const { getByText } = render(<TestComponent />);
  fireEvent.press(getByText('Count: 0'));
  expect(getByText('Count: 1')).toBeTruthy();
});
```

---

## **3. Integration Testing (Multi-Component)**
Integration tests verify how components work together with state, navigation, and APIs.

### **Mocking Redux**
```javascript
import { Provider } from 'react-redux';
import configureStore from 'redux-mock-store';

test('displays user from Redux', () => {
  const mockStore = configureStore()({
    user: { name: 'Alice' }
  });

  const { getByText } = render(
    <Provider store={mockStore}>
      <UserProfile />
    </Provider>
  );

  expect(getByText('Alice')).toBeTruthy();
});
```

### **Mocking API Calls**
```javascript
// Mock fetch globally
global.fetch = jest.fn(() => 
  Promise.resolve({
    json: () => Promise.resolve([{ id: 1, title: 'Test' }])
  })
);

test('loads posts', async () => {
  const { findByText } = render(<PostsScreen />);
  expect(await findByText('Test')).toBeTruthy();
});
```

### **Mocking Navigation**
```javascript
jest.mock('@react-navigation/native', () => ({
  useNavigation: () => ({
    navigate: jest.fn(),
    goBack: jest.fn()
  })
}));

test('navigates on button press', () => {
  const { getByText } = render(<HomeScreen />);
  fireEvent.press(getByText('Go to Details'));
  expect(useNavigation().navigate).toHaveBeenCalledWith('Details');
});
```

---

## **4. Advanced Patterns**
### **Snapshot Testing**
```javascript
test('matches snapshot', () => {
  const tree = renderer.create(<Component />).toJSON();
  expect(tree).toMatchSnapshot();
});
```

### **Timers (setTimeout/setInterval)**
```javascript
jest.useFakeTimers();

test('button is disabled for 1 second', () => {
  const { getByText } = render(<PaymentButton />);
  fireEvent.press(getByText('Pay'));
  
  jest.advanceTimersByTime(1000);
  expect(getByText('Processing...')).toBeDisabled();
});
```

### **Accessibility Testing**
```javascript
import { isInaccessible } from '@testing-library/react-native';

test('modal is accessible', () => {
  const { getByRole } = render(<Modal />);
  expect(isInaccessible(getByRole('modal'))).toBe(false);
});
```

---

## **5. Folder Structure**
```
__tests__/
├── unit/
│   ├── utils.test.js
│   └── selectors.test.js
├── components/
│   ├── Button.test.js
│   └── ListItem.test.js
└── integration/
    ├── authFlow.test.js
    └── checkoutFlow.test.js
```

---

## **Key Takeaways**
1. **Unit Tests**: Isolate pure functions with `jest.fn()` and matchers.
2. **Component Tests**: Use `@testing-library/react-native` queries and `fireEvent`.
3. **Integration Tests**: Mock Redux, APIs, and Navigation for multi-component flows.
4. **Advanced**: Snapshots, timers, and accessibility checks.

This comprehensive approach ensures your React Native app is **well-tested at all levels**. Practice these patterns to ace technical interviews! 🚀


# **Understanding `act()` in React Native Testing**

The `act()` function is a **critical utility** for testing React components properly. It ensures that all state updates, effects, and renders are completed before assertions are made. Here's a deep dive:

---

## **1. What is `act()`?**
- **Purpose**: Synchronizes React's state updates and effects with your test assertions.
- **Problem it solves**: Without `act()`, you might assert before React finishes rendering updates.
- **Source**: Comes from `react-test-renderer` or `@testing-library/react-native`.

---

## **2. When to Use `act()`?**
Use it whenever your test involves:
- **State updates** (`useState`, `useReducer`)
- **Effects** (`useEffect`, `useLayoutEffect`)
- **Async operations** (data fetching, timers)
- **User interactions** that trigger state changes

---

## **3. How to Use `act()`**
### **Basic Syntax**
```javascript
import { act } from '@testing-library/react-native';

act(() => {
  // Trigger state updates or effects here
});
```

---

### **Example 1: Testing State Updates**
```jsx
import { render, act } from '@testing-library/react-native';

test('counter increments', () => {
  const Counter = () => {
    const [count, setCount] = useState(0);
    return (
      <Button 
        onPress={() => setCount(c => c + 1)} 
        title={`Count: ${count}`} 
      />
    );
  };

  const { getByText } = render(<Counter />);
  
  act(() => {
    fireEvent.press(getByText('Count: 0'));
  });

  expect(getByText('Count: 1')).toBeTruthy();
});
```

**Key Points**:
- `act()` wraps the interaction that triggers a state update.
- Without it, the test might assert before the re-render.

---

### **Example 2: Async Effects (Data Fetching)**
```jsx
import { act, render, waitFor } from '@testing-library/react-native';

test('loads user data', async () => {
  const UserProfile = () => {
    const [user, setUser] = useState(null);

    useEffect(() => {
      fetchUser().then(data => setUser(data));
    }, []);

    return <Text>{user?.name}</Text>;
  };

  // Mock API
  jest.spyOn(global, 'fetch').mockResolvedValue({
    json: () => Promise.resolve({ name: 'John' }),
  });

  const { findByText } = render(<UserProfile />);

  await act(async () => {
    await waitFor(() => expect(findByText('John')).toBeTruthy());
  });
});
```

**Key Points**:
- `act()` + `waitFor` ensures async operations complete.
- Without `act()`, the test might finish before the state updates.

---

### **Example 3: Timers (setTimeout/setInterval)**
```jsx
import { act, render } from '@testing-library/react-native';

test('button disabled for 1 second', () => {
  const PaymentButton = () => {
    const [isDisabled, setIsDisabled] = useState(false);

    const handlePress = () => {
      setIsDisabled(true);
      setTimeout(() => setIsDisabled(false), 1000);
    };

    return (
      <Button 
        onPress={handlePress} 
        disabled={isDisabled} 
        title="Pay" 
      />
    );
  };

  jest.useFakeTimers();
  const { getByText } = render(<PaymentButton />);

  act(() => {
    fireEvent.press(getByText('Pay'));
    jest.advanceTimersByTime(1000); // Fast-forward timers
  });

  expect(getByText('Pay')).not.toBeDisabled();
});
```

**Key Points**:
- `act()` wraps both the user interaction and timer advancement.
- Ensures all state updates from timers are processed.

---

## **4. Common Mistakes**
### **Mistake 1: Forgetting `act()`**
```javascript
// ❌ Wrong: Might assert before state updates
fireEvent.press(getByText('Click'));
expect(getByText('Updated')).toBeTruthy();

// ✅ Correct
act(() => {
  fireEvent.press(getByText('Click'));
});
expect(getByText('Updated')).toBeTruthy();
```

### **Mistake 2: Nested `act()`**
```javascript
// ❌ Unnecessary nesting
act(() => {
  act(() => {
    fireEvent.press(button);
  });
});

// ✅ Better
act(() => {
  fireEvent.press(button);
});
```

### **Mistake 3: Async without `await`**
```javascript
// ❌ Might miss async updates
act(() => {
  fetchData(); // Missing await
});

// ✅ Correct
await act(async () => {
  await fetchData();
});
```

---

## **5. When NOT to Use `act()`**
- **Static renders**: No state updates or effects.
- **Queries only**: If you're just querying elements without triggering updates.
  ```javascript
  const { getByText } = render(<StaticComponent />);
  expect(getByText('Hello')).toBeTruthy(); // No act needed
  ```

---

## **6. `act()` with `@testing-library/react-native`**
The library **automatically wraps** these in `act()`:
- `fireEvent`
- `userEvent` (if using)
- `render` updates

So you often don’t need explicit `act()` for basic interactions:
```javascript
// ✅ Already wrapped in act() internally
fireEvent.press(getByText('Click'));
```

**Explicit `act()` is still needed for**:
- Manual state updates
- Custom async logic
- Timer manipulation

---

## **7. Summary**
| Scenario | Solution |
|----------|----------|
| State updates | `act(() => setState(...))` |
| Async effects | `await act(async () => {...})` |
| Timers | `act(() => jest.advanceTimersByTime())` |
| User events | Usually auto-wrapped (check lib docs) |

---

### **Key Takeaways**
1. **Always use `act()`** for state updates, effects, and async code.
2. **Async?** Use `await act(async () => {...})`.
3. **Timers?** Combine with `jest.useFakeTimers()`.
4. **Testing Library** often handles `act()` for you with `fireEvent`.

Practice with real examples to master `act()`! 🚀

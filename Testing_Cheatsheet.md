# Here’s a **cheat sheet** of essential matchers, functions, and patterns for **unit, component, and integration testing** in React Native using **Jest** and **`@testing-library/react-native`**:

---

### **1. Unit Testing (Jest)**
#### **Key Matchers**:
| Matcher | Purpose | Example |
|---------|---------|---------|
| `expect().toBe()` | Strict equality (primitive values) | `expect(sum(1, 2)).toBe(3)` |
| `expect().toEqual()` | Deep equality (objects/arrays) | `expect(user).toEqual({ name: 'John' })` |
| `expect().toBeTruthy()` | Checks for truthy values | `expect(isLoggedIn).toBeTruthy()` |
| `expect().toHaveBeenCalled()` | Mock function called? | `expect(mockFn).toHaveBeenCalled()` |
| `expect().toMatchSnapshot()` | Compares with stored snapshot | `expect(renderer.create(<Component />).toJSON()).toMatchSnapshot()` |

#### **Key Functions**:
- `jest.fn()`: Create a mock function.  
- `jest.mock()`: Mock modules (e.g., `react-native` APIs).  
- `describe()`/`it()`: Group tests.  

**Example**:  
```javascript
test('adds 1 + 2 to equal 3', () => {
  expect(sum(1, 2)).toBe(3);
});
```

---

### **2. Component Testing (`@testing-library/react-native`)**
#### **Key Queries**:
| Query | Purpose | Example |
|-------|---------|---------|
| `getByText` | Find element by text | `getByText('Submit')` |
| `getByTestId` | Find by `testID` | `getByTestId('login-button')` |
| `getByPlaceholderText` | Find input by placeholder | `getByPlaceholderText('Email')` |
| `queryBy*` | Return `null` if not found (safe for assertions) | `expect(queryByText('Error')).toBeNull()` |
| `findBy*` | Async wait for element | `await findByText('Success')` |

#### **Key Actions**:
- `fireEvent`: Simulate interactions.  
  ```javascript
  fireEvent.press(getByText('Submit'));
  fireEvent.changeText(getByPlaceholderText('Email'), 'test@example.com');
  ```
- `act()`: Wrap state updates (for hooks).  

**Example**:  
```javascript
test('button click triggers action', () => {
  const mockPress = jest.fn();
  const { getByText } = render(<Button onPress={mockPress} title="Click" />);
  fireEvent.press(getByText('Click'));
  expect(mockPress).toHaveBeenCalled();
});
```

---

### **3. Integration Testing (Combining Both)**
#### **Key Scenarios**:
- **Redux Store**: Mock provider and test connected components.  
  ```javascript
  import { Provider } from 'react-redux';
  const store = configureStore({ reducer: { user: userReducer } });
  render(<Provider store={store}><UserProfile /></Provider>);
  ```
- **API Calls**: Mock `fetch` or API libraries.  
  ```javascript
  global.fetch = jest.fn(() => Promise.resolve({ json: () => ({ user: 'John' }) }));
  ```
- **Navigation**: Mock `react-navigation`.  
  ```javascript
  jest.mock('@react-navigation/native', () => ({
    useNavigation: () => ({ navigate: jest.fn() }),
  }));
  ```

**Example**:  
```javascript
test('login flow', async () => {
  mockApi.login.mockResolvedValue({ token: '123' });
  const { getByPlaceholderText, getByText } = render(<LoginScreen />);
  fireEvent.changeText(getByPlaceholderText('Email'), 'test@example.com');
  fireEvent.press(getByText('Login'));
  await waitFor(() => expect(mockApi.login).toHaveBeenCalled());
});
```

---

### **4. Advanced Matchers (Custom Scenarios)**
| Matcher | Purpose | Example |
|---------|---------|---------|
| `expect().toHaveStyle()` | Check styles (with `@testing-library/jest-native`) | `expect(getByTestId('button')).toHaveStyle({ backgroundColor: 'blue' })` |
| `expect().toThrow()` | Test error-throwing functions | `expect(() => parseJSON('invalid')).toThrow()` |
| `expect().toHaveBeenCalledWith()` | Check mock function arguments | `expect(mockFn).toHaveBeenCalledWith('arg1')` |

**Example**:  
```javascript
test('styled button', () => {
  const { getByTestId } = render(<Button testID="my-btn" />);
  expect(getByTestId('my-btn')).toHaveStyle({ borderRadius: 8 });
});
```

---

### **5. Mocking Deep Dive**
#### **Mocking Native Modules**:
```javascript
jest.mock('react-native/Libraries/EventEmitter/NativeEventEmitter');
jest.mock('react-native-device-info', () => ({
  getVersion: jest.fn(() => '1.0.0'),
}));
```

#### **Mocking React Navigation**:
```javascript
jest.mock('@react-navigation/native', () => ({
  ...jest.requireActual('@react-navigation/native'),
  useNavigation: () => ({ navigate: jest.fn() }),
}));
```

#### **Mocking Redux**:
```javascript
const mockStore = {
  getState: () => ({ user: { name: 'John' } }),
  dispatch: jest.fn(),
  subscribe: jest.fn(),
};
```

---

### **Key Takeaways for Interviews**  
1. **Unit Tests**: Use `jest.fn()`, `toBe`, `toEqual`.  
2. **Component Tests**: Use `getByText`, `fireEvent`, `toHaveStyle`.  
3. **Integration Tests**: Mock APIs, Redux, Navigation.  
4. **Async**: Use `findBy*`, `waitFor`, `act`.  

**Pro Tip**: Memorize 2-3 examples (e.g., testing a button, mocking API calls) to demonstrate fluency. 🚀

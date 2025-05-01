#  Deep Dive Into React Native 

# **1. React Native Architecture: A Deep Dive**

React Native's architecture has evolved significantly, especially with the **new architecture (v0.68+)**. Below is a structured breakdown of how it works, including key components and how they interact.

---

## **1. Core Layers of React Native**
React Native consists of **three main layers**:

| Layer              | Description                                                                 |
|--------------------|-----------------------------------------------------------------------------|
| **JavaScript**     | Runs React code, business logic, and UI definitions (JS/TS).                |
| **Bridge (Old) / JSI (New)** | Facilitates communication between JS and Native worlds.                     |
| **Native**         | Platform-specific UI rendering (iOS/Android) and native module integrations. |

---

## **2. Old Architecture (Bridge-Based)**
### **How It Worked**
1. **JavaScript Thread**:
   - Executes React code and business logic.
   - Generates Virtual DOM diff.
2. **Bridge**:
   - Serializes messages into JSON.
   - Passes them asynchronously to the Native side.
3. **Native Threads**:
   - **UI Thread (Main Thread)**: Renders native components.
   - **Shadow Thread**: Calculates layouts using Yoga (Facebook's layout engine).

### **Problems with the Old Architecture**
- **Performance Bottleneck**: The Bridge caused delays due to serialization.
- **Asynchronous Only**: JS couldn’t call native methods directly.
- **High Latency**: UI updates were slow (~5-10ms per call).

---

## **3. New Architecture (JSI + Fabric + TurboModules)**
Introduced in **React Native v0.68+**, the new architecture removes the Bridge and improves performance.

### **Key Components**
#### **(A) JavaScript Interface (JSI)**
- **Purpose**: Replaces the Bridge with **direct, synchronous communication**.
- **How It Works**:
  - JS and Native can **hold references to each other’s objects**.
  - No serialization needed (unlike the Bridge).
  - Enables **true shared memory** between JS and Native.

#### **(B) Fabric (New Renderer)**
- **Purpose**: Improves rendering performance.
- **Key Features**:
  - **Synchronous UI Updates**: No more waiting for the Bridge.
  - **Priority-Based Rendering**: Critical updates (e.g., animations) get priority.
  - **Simplified Shadow Tree**: Faster layout calculations.

#### **(C) TurboModules**
- **Purpose**: Optimizes native module performance.
- **Key Features**:
  - **Lazy Loading**: Native modules load only when needed.
  - **Type-Safe**: Uses **Codegen** for better type checking.
  - **Stronger Integration**: Works seamlessly with JSI.

---

## **4. Threading Model (New Architecture)**
React Native now uses **fewer threads** with smarter communication:

| Thread              | Purpose                                                                 |
|---------------------|-------------------------------------------------------------------------|
| **JavaScript Thread** | Runs React logic, handles business rules.                              |
| **UI Thread (Main)** | Renders native components (iOS/Android).                               |
| **Background Thread** | Handles layout (Yoga) and low-priority tasks.                          |

### **Key Improvements**
- **No More Bridge Queue**: JSI allows **direct method calls**.
- **Faster UI Updates**: Fabric renders changes **synchronously**.
- **Better Memory Usage**: Shared C++ core reduces duplication.

---

## **5. How Data Flows in the New Architecture**
1. **User Interaction** (e.g., button press):
   - Native → JS (via JSI).
2. **State Update**:
   - JS computes new UI state.
3. **Rendering**:
   - Fabric applies changes **directly to the UI thread** (no Bridge delays).
4. **Native Modules**:
   - TurboModules load **only when needed**.

---

## **6. Performance Comparison**
| Metric               | Old Architecture (Bridge) | New Architecture (JSI/Fabric) |
|----------------------|--------------------------|------------------------------|
| **JS → Native Calls** | ~5-10ms (async)          | ~0.1ms (synchronous)         |
| **UI Responsiveness** | Janky (due to Bridge)    | Smooth (60 FPS)              |
| **Memory Usage**     | Higher (duplicate data)  | Lower (shared memory)        |
| **Startup Time**     | Slower (eager module load)| Faster (TurboModules lazy load) |

---

## **7. Practical Implications**
### **For Developers**
- **Better Performance**: Apps feel more native.
- **Easier Debugging**: Fewer threading issues.
- **Type Safety**: TurboModules + Codegen reduce runtime errors.

### **For App Users**
- **Faster Load Times**: TurboModules reduce TTI (~30% improvement).
- **Smooth Animations**: Fabric enables 60 FPS rendering.
- **Lower Crash Rates**: Stronger type checking in native modules.

---

## **8. Migration to the New Architecture**
If you’re upgrading:
1. **Enable Fabric**:
   ```bash
   # In react-native.config.js
   module.exports = {
     dependencies: {
       'react-native': {
         unstable_reactLegacyComponentNames: false,
       },
     },
   };
   ```
2. **Adopt TurboModules**:
   - Use **Codegen** for type-safe native modules.
3. **Test Thoroughly**:
   - Use **React Native Debugger** and **Flipper** to verify performance.

---

## **9. Summary**
| **Aspect**         | **Old Architecture** | **New Architecture** |
|---------------------|----------------------|----------------------|
| **Communication**   | Async Bridge (JSON)  | Synchronous JSI      |
| **Rendering**       | Slow (Bridge queue)  | Fast (Fabric)        |
| **Native Modules**  | Eager-loaded         | Lazy-loaded (TurboModules) |
| **Performance**     | Good                 | Excellent            |

### **Key Takeaways**
- The **new architecture** (JSI + Fabric + TurboModules) makes React Native **faster and more reliable**.
- **No more Bridge bottlenecks** – direct JS ↔ Native communication.
- **Adopt TurboModules** for better startup performance.

---

# **2. React Basics for React Native: The Essential Crash Course** 🚀

Let's break this down like we're building a React Native app from scratch in a hacker garage. I'll give you the straight fire you need to crush interviews and ship apps.

---

## **1. The Nuts and Bolts: How React Native Actually Works**
### **The Magic Trick**
- Your JS code gets translated to **real native views** (no webviews!)
- On iOS: `<View>` becomes `UIView`
- On Android: `<View>` becomes `ViewGroup`
- **Secret Sauce**: The "Bridge" (or JSI in new architecture) connects JS and Native worlds

### **Component Blueprint**
```javascript
// Functional Component (Modern Way)
const Pistol = ({ bullets }) => {
  return <Text>🔥 {bullets} shots fired!</Text>;
};

// Class Component (Old School)
class Shotgun extends React.Component {
  render() {
    return <Text>💥 {this.props.shells} shells loaded!</Text>;
  }
}
```

---

## **2. State vs Props: The Cage Match**
### **Props (Immutable Ammo)**
```javascript
// Parent component "passes the ammo"
<Weapon bullets={50} />

// Child component "receives the ammo"
const Weapon = ({ bullets }) => {
  // bullets is READ-ONLY here!
};
```

### **State (Mutable Kill Count)**
```javascript
const [kills, setKills] = useState(0);

// Update state like a boss
const addKill = () => setKills(prev => prev + 1);
```

**Golden Rule**:  
Props flow down, events bubble up!

---

## **3. Hooks: Your Utility Belt**
### **useState - The OG**
```javascript
const [health, setHealth] = useState(100);

// Headshot!
setHealth(0); 
```

### **useEffect - The Sidekick**
```javascript
useEffect(() => {
  // Runs after render
  console.log('Component mounted!');
  
  return () => {
    // Cleanup before unmount
    console.log('Component destroyed 💀');
  };
}, []); // Empty array = runs once
```

### **useContext - The Shared Armory**
```javascript
// 1. Create context
const AmmoContext = createContext();

// 2. Provide value
<AmmoContext.Provider value={50}>
  <Player />
</AmmoContext.Provider>

// 3. Consume value
const ammo = useContext(AmmoContext);
```

---

## **4. Lifecycle in Functional Components**
Forget class lifecycles - here's the hook version:

| Class Lifecycle       | Hook Equivalent               |
|-----------------------|-------------------------------|
| componentDidMount     | `useEffect(() => {}, [])`     |
| componentDidUpdate    | `useEffect(() => {}, [dep])`  |
| componentWillUnmount  | `useEffect cleanup`           |
| shouldComponentUpdate | `React.memo`                  |

---

## **5. Performance Hacks**
### **Memoization Spells**
```javascript
// Cache computations
const damage = useMemo(() => calculateDamage(stats), [stats]);

// Cache functions
const fire = useCallback(() => shoot(target), [target]);
```

### **FlatList Ninja Moves**
```javascript
<FlatList
  data={enemies}
  renderItem={({ item }) => <Enemy data={item} />}
  keyExtractor={item => item.id}
  initialNumToRender={5}
  windowSize={10}
/>
```

---

## **6. Event Handling: From Peashooter to RPG**
```javascript
<TouchableOpacity onPress={() => console.log('Headshot!')}>
  <Text>Shoot</Text>
</TouchableOpacity>

<TextInput
  onChangeText={text => setAmmoType(text)}
  placeholder="Enter ammo type"
/>
```

---

## **7. Styling: CSS on Steroids**
```javascript
const styles = StyleSheet.create({
  gun: {
    color: '#f00',
    fontSize: 20,
    // Flexbox works exactly like web
    margin: 10,
    padding: 20
  }
});

<Text style={[styles.gun, { transform: [{ rotate: '45deg' }] }]}>
  🔫
</Text>
```

---

## **8. The React Native Cheat Codes**
1. **Debugging**:  
   - `console.log` → Shows in Metro terminal
   - React Native Debugger → Full inspector
   - Flipper → Advanced diagnostics

2. **Hot Reloading**:  
   - Save file → See changes instantly (no rebuild!)

3. **Native Modules**:  
   - Need Bluetooth? Camera?  
   - `react-native-camera`, `react-native-ble-plx`

---

## **9. Interview Kill Shots**
**Q: "How does Virtual DOM work in React Native?"**  
*A: "It's a lightweight copy of the UI that React uses to compute minimal changes before sending updates to native views through the Bridge/JSI."*

**Q: "When would you use useReducer vs useState?"**  
*A: "When my state logic gets complex with multiple sub-values or when the next state depends on the previous one (like a health bar system)."*

**Q: "What's the difference between ScrollView and FlatList?"**  
*A: "ScrollView renders all children at once (good for few items), while FlatList lazy-renders (for long lists) and recycles views like a pro."*

---

## **10. The Ultimate Truth**
React Native is just React with two superpowers:
1. **It speaks native platform language** (no webviews)
2. **It gives you full access to device hardware**

Great — here's a detailed, conversational explanation of each React and React Native hook you mentioned, with clear usage context and code examples.

---
# 3. Core Components
Absolutely! Let's delve into React Native's Core Components and APIs, providing detailed explanations and practical examples to enhance your understanding.

---

## 🧱 Core Components

React Native offers a set of built-in components that serve as the building blocks for your application. These components are categorized based on their functionality.

### 1. **Basic Components**

These are fundamental components used in almost every React Native app:

- **`View`**: A container that supports layout with flexbox, style, touch handling, and accessibility controls. It's analogous to a `<div>` in web development. ([Core Components and Native Components - React Native](https://reactnative.dev/docs/intro-react-native-components?utm_source=chatgpt.com))

  ```jsx
  import React from 'react';
  import { View } from 'react-native';

  const App = () => (
    <View style={{ flex: 1, backgroundColor: 'white' }}>
      {/* Other components go here */}
    </View>
  );
  ```

- **`Text`**: Used for displaying text. It supports nesting, styling, and touch handling.

  ```jsx
  import React from 'react';
  import { Text } from 'react-native';

  const App = () => (
    <Text style={{ fontSize: 20, color: 'black' }}>
      Hello, React Native!
    </Text>
  );
  ```

- **`Image`**: Displays images, supporting different formats and remote or local sources.

  ```jsx
  import React from 'react';
  import { Image } from 'react-native';

  const App = () => (
    <Image
      source={{ uri: 'https://example.com/image.png' }}
      style={{ width: 100, height: 100 }}
    />
  );
  ```

- **`TextInput`**: Allows users to input text. It's a basic component for forms and user input.

  ```jsx
  import React, { useState } from 'react';
  import { TextInput } from 'react-native';

  const App = () => {
    const [text, setText] = useState('');
    return (
      <TextInput
        style={{ height: 40, borderColor: 'gray', borderWidth: 1 }}
        onChangeText={setText}
        value={text}
      />
    );
  };
  ```

- **`ScrollView`**: A scrollable container that can host multiple components and views. Useful for layouts that require scrolling. ([Components and APIs - React Native Archive](https://archive.reactnative.dev/docs/0.8/components-and-apis?utm_source=chatgpt.com))

  ```jsx
  import React from 'react';
  import { ScrollView, Text } from 'react-native';

  const App = () => (
    <ScrollView>
      <Text>Scrollable content goes here</Text>
      {/* More content */}
    </ScrollView>
  );
  ```

- **`StyleSheet`**: Provides an abstraction similar to CSS stylesheets. It helps in organizing and optimizing styles. ([Core Components and APIs - React Native](https://reactnative.dev/docs/components-and-apis?utm_source=chatgpt.com))

  ```jsx
  import { StyleSheet } from 'react-native';

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      backgroundColor: 'white',
    },
    text: {
      fontSize: 20,
      color: 'black',
    },
  });
  ```

### 2. **User Interface Components**

These components are used for common user interface controls:

- **`Button`**: A basic button component for handling presses.

  ```jsx
  import React from 'react';
  import { Button } from 'react-native';

  const App = () => (
    <Button
      title="Press Me"
      onPress={() => alert('Button Pressed')}
    />
  );
  ```

- **`Switch`**: A boolean input component that represents an on/off toggle.

  ```jsx
  import React, { useState } from 'react';
  import { Switch } from 'react-native';

  const App = () => {
    const [isEnabled, setIsEnabled] = useState(false);
    return (
      <Switch
        value={isEnabled}
        onValueChange={setIsEnabled}
      />
    );
  };
  ```

### 3. **List Views**

For rendering lists of data efficiently:

- **`FlatList`**: Displays a scrolling list of changing, but similarly structured, data. It only renders items that are currently visible on the screen, making it more efficient than `ScrollView` for long lists. ([Components and APIs - React Native Archive](https://archive.reactnative.dev/docs/0.8/components-and-apis?utm_source=chatgpt.com))

  ```jsx
  import React from 'react';
  import { FlatList, Text } from 'react-native';

  const data = [{ key: 'Devin' }, { key: 'Dan' }, { key: 'Dominic' }];

  const App = () => (
    <FlatList
      data={data}
      renderItem={({ item }) => <Text>{item.key}</Text>}
    />
  );
  ```

- **`SectionList`**: Similar to `FlatList`, but with section headers to group data. ([Components and APIs - React Native Archive](https://archive.reactnative.dev/docs/0.8/components-and-apis?utm_source=chatgpt.com))

  ```jsx
  import React from 'react';
  import { SectionList, Text } from 'react-native';

  const sections = [
    { title: 'D', data: ['Devin', 'Dan', 'Dominic'] },
    { title: 'J', data: ['Jackson', 'James', 'Jillian'] },
  ];

  const App = () => (
    <SectionList
      sections={sections}
      renderItem={({ item }) => <Text>{item}</Text>}
      renderSectionHeader={({ section }) => (
        <Text style={{ fontWeight: 'bold' }}>{section.title}</Text>
      )}
    />
  );
  ```

### 4. **Platform-Specific Components**

Components that are specific to Android or iOS:

- **Android**:

  - **`DrawerLayoutAndroid`**: Provides a drawer navigation component. ([Core Components and APIs - React Native](https://reactnative.dev/docs/components-and-apis?utm_source=chatgpt.com))

  - **`TouchableNativeFeedback`**: Provides native feedback (like ripple effect) on touch events.

- **iOS**:

  - **`InputAccessoryView`**: Allows customization of the keyboard accessory view.

  - **`SafeAreaView`**: Renders content within the safe area boundaries of a device.

### 5. **Other Useful Components**

- **`ActivityIndicator`**: Displays a circular loading indicator. ([Core Components and APIs - React Native](https://reactnative.dev/docs/components-and-apis?utm_source=chatgpt.com))

  ```jsx
  import React from 'react';
  import { ActivityIndicator } from 'react-native';

  const App = () => (
    <ActivityIndicator size="large" color="#0000ff" />
  );
  ```

- **`Modal`**: Presents content above an enclosing view. ([Components and APIs - React Native Archive](https://archive.reactnative.dev/docs/0.8/components-and-apis?utm_source=chatgpt.com))

  ```jsx
  import React, { useState } from 'react';
  import { Modal, View, Text, Button } from 'react-native';

  const App = () => {
    const [modalVisible, setModalVisible] = useState(false);
    return (
      <View>
        <Button title="Show Modal" onPress={() => setModalVisible(true)} />
        <Modal
          visible={modalVisible}
          onRequestClose={() => setModalVisible(false)}
        >
          <View>
            <Text>Hello Modal!</Text>
            <Button title="Hide Modal" onPress={() => setModalVisible(false)} />
          </View>
        </Modal>
      </View>
    );
  };
  ```

- **`Pressable`**: A core component wrapper that can detect various types of interactions.

  ```jsx
  import React from 'react';
  import { Pressable, Text } from 'react-native';

  const App = () => (
    <Pressable onPress={() => alert('Pressed!')}>
      <Text>Press Me</Text>
    </Pressable>
  );
  ```

- **`RefreshControl`**: Used inside a `ScrollView` or `ListView` to add pull-to-refresh functionality.

  ```jsx
  import React, { useState } from 'react';
  import { ScrollView, RefreshControl, Text } from 'react-native';

  const App = () => {
    const [refreshing, setRefreshing] = useState(false);

    const onRefresh = () => {
      setRefreshing(true);
      // Simulate a network request
      setTimeout(() => setRefreshing(false), 2000);
    };

    return (
      <ScrollView
        refreshControl={
          <RefreshControl refreshing={refreshing} onRefresh={onRefresh} />
        }
      >
        <Text>Pull down to refresh</Text>
      </ScrollView>
    );
  };
  ```

---

## ⚙️ Core APIs

React Native also provides several APIs to interact with the device's functionalities:

- **`Alert`**: Launches an alert dialog with the specified title and message. ([Core Components and APIs - React Native](https://reactnative.dev/docs/components-and-apis?utm_source=chatgpt.com))

  ```jsx
  import { Alert, Button } from 'react-native';

  const showAlert = () =>
    Alert.alert('Alert Title', 'My Alert Msg', [
      { text: 'OK', onPress: () => console.log('OK Pressed') },
    ]);

  const App = () => <Button title="Show Alert" onPress={showAlert} />;
  ```

- **`Animated`**: A library for creating fluid, powerful animations. ([Core Components and APIs - React Native](https://reactnative.dev/docs/components-and-apis?utm_source=chatgpt.com))

- **`Dimensions`**: Provides an interface for getting device dimensions. ([Core Components and APIs - React Native](https://reactnative.dev/docs/components-and-apis?utm_source=chatgpt.com))

  ```jsx
  import { Dimensions } from 'react-native';

  const windowWidth = Dimensions.get('window').width;
  const windowHeight = Dimensions.get('window').height;
  ```

- **`KeyboardAvoidingView`**: A component that automatically adjusts its position when the keyboard appears.

- **`Linking`**: Provides a general interface to interact with both incoming and outgoing app links. ([Components and APIs - React Native Archive](https://archive.reactnative.dev/docs/0.8/components-and-apis?utm_source=chatgpt.com))

- **`PixelRatio`**: Provides access to the device pixel density. ([Components and APIs - React Native Archive](https://archive.reactnative.dev/docs/0.8/components-and-apis?utm_source=chatgpt.com))

- **`StatusBar`**: Component to control the app's status bar. ([Components and APIs - React Native Archive](https://archive.reactnative.dev/docs/0.8/components-and-apis?utm_source=chatgpt.com))

---

# **4. React Native Hooks in depth.

### 🟢 **State Hooks**

#### 1. `useState`
`useState` is the most basic hook used to add state to functional components. Think of it like a variable that can remember its value across re-renders, but also causes the component to re-render when changed.

```js
const [count, setCount] = useState(0);
```

- `count` is the current state value.
- `setCount` is the function to update it.

You use this when your component needs to respond to user interaction like form inputs, toggles, or counters.

#### 2. `useReducer`
This hook is helpful when your state logic is more complex than just setting a value. It mimics the reducer pattern from Redux: you dispatch actions and a reducer function determines the next state.

```js
const [state, dispatch] = useReducer(reducer, initialState);
```

Use this when:
- You have multiple related state variables.
- The next state depends on the previous state.
- You want more predictable updates using actions.

Example:
```js
const reducer = (state, action) => {
  switch (action.type) {
    case 'increment': return { count: state.count + 1 };
    case 'decrement': return { count: state.count - 1 };
    default: return state;
  }
};
```

---

### 🔵 **Effect Hooks**

#### 3. `useEffect`
`useEffect` lets you perform side effects — things that aren't purely UI rendering, like:
- Fetching data
- Subscribing to events
- Setting timeouts or intervals

```js
useEffect(() => {
  console.log('Component mounted or updated');
  return () => console.log('Cleanup on unmount or re-run');
}, [dependency]);
```

The `[]` array means “only run this on mount/unmount”. If you put variables inside it, the effect runs when they change.

#### 4. `useLayoutEffect`
This is like `useEffect`, but it fires synchronously *after* all DOM mutations and *before* the browser paints. It blocks painting, so only use it when necessary.

In React Native, use it when you need to:
- Measure layout dimensions
- Force an update before painting to avoid flickering

---

### 🟠 **Context Hook**

#### 5. `useContext`
This hook simplifies consuming context values without writing a `<Context.Consumer>`.

Example:
```js
const theme = useContext(ThemeContext);
```

Useful when:
- You need access to shared data like authentication, theme, or language.
- You want to avoid passing props manually through many components.

---

### 🟤 **Ref Hooks**

#### 6. `useRef`
`useRef` stores a value that persists across renders but doesn’t cause a re-render when updated.

Use it for:
- Accessing native DOM or component instances (`ref.current`)
- Keeping track of previous values
- Debounce timers or animation values

```js
const intervalRef = useRef(null);
```

#### 7. `useImperativeHandle`
Used with `forwardRef` to expose imperative methods from a child to a parent.

```js
useImperativeHandle(ref, () => ({
  scrollToTop: () => scrollViewRef.current.scrollTo({ y: 0 }),
}));
```

Use when you want a parent to call a function inside the child, e.g., resetting form inputs from the parent.

---

### ⚙️ **Performance Hooks**

#### 8. `useMemo`
This hook memoizes expensive calculations. It only recalculates the value if dependencies change.

```js
const expensiveValue = useMemo(() => compute(), [input]);
```

Prevents unnecessary re-computation when nothing relevant changed.

#### 9. `useCallback`
This memoizes a function so it isn’t re-created on every render — especially useful when passing callbacks to child components.

```js
const handlePress = useCallback(() => doSomething(), []);
```

Use it with React.memo or dependencies like useEffect that depend on stable functions.

---

### 🟣 **Other Hooks**

#### 10. `useDebugValue`
This is mainly for custom hooks. It helps label values in React DevTools.

```js
useDebugValue(authenticated ? 'Authenticated' : 'Guest');
```

#### 11. `useDeferredValue`
Helps prioritize updates by deferring less important renders, useful for large lists or slow UIs.

```js
const deferredQuery = useDeferredValue(query);
```

#### 12. `useId`
Generates a unique ID — useful for matching input-label pairs or accessibility.

```js
const id = useId();
```

#### 13. `useSyncExternalStore`
Used when syncing external stores with React state — useful for building your own state management.

```js
const state = useSyncExternalStore(subscribe, getSnapshot);
```

#### 14. `useInsertionEffect`
Lets you insert styles before the browser paints. Used by CSS-in-JS libraries like styled-components.

```js
useInsertionEffect(() => {
  // Add styles
}, []);
```

---

### ✅ **React Native Specific Hooks**

#### 15. `useAccessibilityInfo`
Checks if the screen reader is enabled on the device.

```js
useEffect(() => {
  AccessibilityInfo.isScreenReaderEnabled().then(setIsEnabled);
}, []);
```

#### 16. `useAppState`
Tracks if the app is in the background, foreground, or inactive.

```js
const appState = useAppState(); // returns 'active', 'background', etc.
```

#### 17. `useBackHandler`
Lets you override the Android hardware back button.

```js
useBackHandler(() => {
  if (isModalOpen) {
    closeModal();
    return true; // prevent default
  }
  return false; // allow default
});
```

#### 18. `useImageDimensions`
Gives you width and height of a local image.

```js
const { width, height } = useImageDimensions(require('./img.png'));
```

#### 19. `useKeyboard`
Detects if the keyboard is open and its height — helpful for adjusting UI.

```js
const { keyboardShown, keyboardHeight } = useKeyboard();
```

#### 20. `useInteractionManager`
Schedules code to run only after all animations and interactions are done, improving performance.

```js
useEffect(() => {
  InteractionManager.runAfterInteractions(() => {
    // Heavy task
  });
}, []);
```

#### 21. `useDeviceOrientation`
Returns device orientation — landscape or portrait.

```js
const { landscape } = useDeviceOrientation();
```

#### 22. `useLayout`
Helps you measure a component’s size and position using `onLayout`.

```js
const onLayout = (event) => {
  const { width, height } = event.nativeEvent.layout;
};
```

#### 23. `useRefresh`
Manages pull-to-refresh logic.

```js
const { refreshing, onRefresh } = useRefresh(fetchData);
```

---

Here's a comprehensive breakdown of every API from your list, complete with purposes, definitions, and code examples. I'll go deeper on the most critical ones:

---

# **5. React Native APIs**

#### 1. **AccessibilityInfo**
- **Purpose**: Check screen reader status and manage accessibility features
- **Example**:
```javascript
import { AccessibilityInfo } from 'react-native';

// Check if screen reader is enabled
AccessibilityInfo.isScreenReaderEnabled().then(enabled => {
  console.log('Screen reader:', enabled);
});
```

#### 2. **Alert**
- **Purpose**: Show native alert dialogs
- **Example**:
```javascript
Alert.alert(
  'Delete Item',
  'Are you sure?',
  [
    { text: 'Cancel', style: 'cancel' },
    { text: 'OK', onPress: () => deleteItem() }
  ]
);
```

#### 3. **Animated** (Critical 🔥)
- **Purpose**: Create smooth animations
- **Deep Dive**:
  - Uses declarative API
  - Works with native driver for 60 FPS performance
- **Example**:
```javascript
const fadeAnim = new Animated.Value(0);

Animated.timing(fadeAnim, {
  toValue: 1,
  duration: 1000,
  useNativeDriver: true
}).start();
```

#### 4. **AppRegistry**
- **Purpose**: App entry point (used in index.js)
- **Example**:
```javascript
AppRegistry.registerComponent('App', () => MainComponent);
```

#### 5. **AppState**
- **Purpose**: Detect app foreground/background state
- **Example**:
```javascript
const [appState, setAppState] = useState(AppState.currentState);

useEffect(() => {
  const subscription = AppState.addEventListener('change', setAppState);
  return () => subscription.remove();
}, []);
```

---

### **Layout & Styling APIs**

#### 6. **Dimensions**
- **Purpose**: Get screen dimensions
- **Example**:
```javascript
const { width, height } = Dimensions.get('window');
```

#### 7. **PixelRatio**
- **Purpose**: Handle device pixel density
- **Example**:
```javascript
const scaledSize = PixelRatio.getPixelSizeForLayoutSize(50);
```

#### 8. **StyleSheet** (Critical 🔥)
- **Purpose**: Optimized stylesheet creation
- **Deep Dive**:
  - Validates styles at creation
  - Better performance than inline styles
- **Example**:
```javascript
const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff'
  }
});
```

---

### **Device Interaction APIs**

#### 9. **Keyboard**
- **Purpose**: Control keyboard behavior
- **Example**:
```javascript
Keyboard.dismiss();
```

#### 10. **Vibration**
- **Purpose**: Trigger device vibration
- **Example**:
```javascript
Vibration.vibrate([500, 500, 500]); // Pattern
```

#### 11. **Linking** (Critical 🔥)
- **Purpose**: Handle deep links and URLs
- **Deep Dive**:
  - Supports universal links
  - Works with other apps
- **Example**:
```javascript
Linking.openURL('https://reactnative.dev');
```

---

### **Platform-Specific APIs**

#### Android:
```javascript
// BackHandler (Android back button)
BackHandler.addEventListener('hardwareBackPress', handleBack);

// ToastAndroid
ToastAndroid.show('Message', ToastAndroid.SHORT);
```

#### iOS:
```javascript
// ActionSheetIOS
ActionSheetIOS.showActionSheetWithOptions(
  { options: ['Delete', 'Cancel'], destructiveButtonIndex: 0 },
  (buttonIndex) => {}
);
```

---

### **Advanced APIs (Critical 🔥)**

#### 12. **PanResponder**
- **Purpose**: Advanced gesture handling
- **Deep Dive**:
  - Provides touch tracking
  - Works with Animated API
- **Example**:
```javascript
const panResponder = PanResponder.create({
  onMoveShouldSetPanResponder: () => true,
  onPanResponderMove: (_, gestureState) => {
    // Handle movement
  }
});
```

#### 13. **LayoutAnimation**
- **Purpose**: Automatically animate layout changes
- **Example**:
```javascript
LayoutAnimation.configureNext(LayoutAnimation.Presets.spring);
setState({ width: newWidth });
```

#### 14. **InteractionManager**
- **Purpose**: Defer tasks until animations complete
- **Example**:
```javascript
InteractionManager.runAfterInteractions(() => {
  // Run heavy task
});
```

---

### **Hooks**

#### 1. **useColorScheme**
- **Purpose**: Detect dark/light mode
- **Example**:
```javascript
const colorScheme = useColorScheme();
```

#### 2. **useWindowDimensions**
- **Purpose**: Responsive dimensions
- **Example**:
```javascript
const { width } = useWindowDimensions();
```

---

### **Key Takeaways**
1. **Must-Know APIs**: Animated, StyleSheet, Linking, PanResponder
2. **Performance**: Use `useNativeDriver` with Animated
3. **Platform**: Remember Android/iOS specific APIs
4. **Hooks**: Prefer `useWindowDimensions` over `Dimensions.get()`

---

# **5. Animated vs PanResponder vs LayoutAnimation

### **1. Animated**
#### **Purpose**  
Create **precise, controlled animations** (opacity, transforms, etc.) with full control over timing and curves.

#### **Key Features**  
- Imperative API  
- Supports interpolation and complex sequences  
- Can use native driver (`useNativeDriver: true`) for better performance  

#### **Example: Fade + Scale Animation**  
```javascript
import { Animated, View, Button } from 'react-native';

function AnimatedBox() {
  const fadeAnim = new Animated.Value(0);
  const scaleAnim = new Animated.Value(0.5);

  const animate = () => {
    Animated.parallel([
      Animated.timing(fadeAnim, {
        toValue: 1,
        duration: 1000,
        useNativeDriver: true,
      }),
      Animated.spring(scaleAnim, {
        toValue: 1,
        friction: 2,
        useNativeDriver: true,
      }),
    ]).start();
  };

  return (
    <View>
      <Animated.View
        style={{
          opacity: fadeAnim,
          transform: [{ scale: scaleAnim }],
          width: 100,
          height: 100,
          backgroundColor: 'red',
        }}
      />
      <Button title="Animate" onPress={animate} />
    </View>
  );
}
```

#### **When to Use**  
✅ Complex animations (e.g., sequences, parallel animations)  
✅ Interactive gestures (combined with `PanResponder`)  
✅ Precise control over timing (e.g., `easing`, `delay`)  

#### **Performance**  
- With `useNativeDriver: true`: Runs on UI thread (60 FPS)  
- Without native driver: Runs on JS thread (may cause jank)  

---

### **2. PanResponder**
#### **Purpose**  
Handle **complex touch gestures** (dragging, swiping, multi-touch) with low-level control.

#### **Key Features**  
- Tracks touch coordinates and velocity  
- Works with `Animated` for gesture-driven animations  
- Provides touch lifecycle methods (`onPress`, `onPanResponderMove`)  

#### **Example: Draggable Box**  
```javascript
import { PanResponder, Animated, View } from 'react-native';

function DraggableBox() {
  const pan = new Animated.ValueXY();

  const panResponder = PanResponder.create({
    onStartShouldSetPanResponder: () => true,
    onPanResponderMove: Animated.event(
      [null, { dx: pan.x, dy: pan.y }],
      { useNativeDriver: false } // Required for transforms
    ),
    onPanResponderRelease: () => {
      Animated.spring(pan, {
        toValue: { x: 0, y: 0 },
        useNativeDriver: true,
      }).start();
    },
  });

  return (
    <Animated.View
      {...panResponder.panHandlers}
      style={{
        transform: [{ translateX: pan.x }, { translateY: pan.y }],
        width: 100,
        height: 100,
        backgroundColor: 'blue',
      }}
    />
  );
}
```

#### **When to Use**  
✅ Drag-and-drop interfaces  
✅ Custom swipe gestures  
✅ Games or interactive UIs  

#### **Performance**  
- Avoid heavy JS logic in gesture handlers (can block the JS thread).  
- Use `useNativeDriver: true` for `Animated` transformations.  

---

### **3. LayoutAnimation**
#### **Purpose**  
Automatically animate **layout changes** (size, position, flexbox) with minimal code.

#### **Key Features**  
- Declarative API (animates layout updates automatically)  
- Always runs on the native thread (smooth)  
- Limited to preset animations (`spring`, `easeInEaseOut`)  

#### **Example: Expanding Box**  
```javascript
import { LayoutAnimation, View, Button, StyleSheet } from 'react-native';

function ExpandableBox() {
  const [expanded, setExpanded] = useState(false);

  const toggleExpand = () => {
    LayoutAnimation.configureNext(LayoutAnimation.Presets.spring);
    setExpanded(!expanded);
  };

  return (
    <View>
      <View style={[styles.box, { height: expanded ? 200 : 100 }]} />
      <Button title="Toggle" onPress={toggleExpand} />
    </View>
  );
}

const styles = StyleSheet.create({
  box: { width: 100, backgroundColor: 'green' },
});
```

#### **When to Use**  
✅ Animating layout changes (e.g., expanding/collapsing)  
✅ List reordering  
✅ Simple state transitions  

#### **Performance**  
- Always runs on the native thread (no JS overhead).  
- Limited control over animation curves.  

---

### **Comparison Table**
| Feature                | `Animated`                     | `PanResponder`                 | `LayoutAnimation`              |
|------------------------|--------------------------------|--------------------------------|--------------------------------|
| **Primary Use**        | Property animations            | Touch gestures                 | Layout transitions             |
| **Control Level**      | High (custom curves)           | Low-level (XY coordinates)     | Low (preset animations)        |
| **Performance**        | Good (with native driver)      | JS-heavy (needs optimization)  | Excellent (native thread)      |
| **Code Complexity**    | High                           | High                           | Low                            |
| **Best For**           | Fades, rotations, sequences   | Drag, swipe interactions       | Size/position changes          |

---

### **Key Takeaways**
1. **Combine `Animated` + `PanResponder`** for interactive gestures (e.g., draggable cards).  
2. **Use `LayoutAnimation`** for simple layout changes (no manual animation values needed).  
3. **Performance Tips**:  
   - Always use `useNativeDriver: true` with `Animated` when possible.  
   - Avoid heavy JS logic in `PanResponder` handlers.  
   - Prefer `LayoutAnimation` for layout updates (native thread).  

---

### **When to Choose Which?**
- **"I need to animate a button press"** → `Animated`  
- **"I want a draggable component"** → `PanResponder` + `Animated`  
- **"I need to smoothly expand a view"** → `LayoutAnimation`  


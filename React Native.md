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

# **6. Animated vs PanResponder vs LayoutAnimation**

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

# **7. Container vs Smart Components in React Native**

## **1. Definitions**
### **Container (Smart) Components**
- **Purpose**: Manage **state**, **data fetching**, and **business logic**.
- **Also Called**: "Smart" components, controllers.
- **Characteristics**:
  - Know about Redux/MobX/store.
  - Handle API calls.
  - Pass data down to presentational components.
  - Rarely have their own styles.

### **Presentational (Dumb) Components**
- **Purpose**: Focus solely on **how things look**.
- **Characteristics**:
  - Receive data and callbacks via props.
  - Don't know about app state.
  - Often reusable and style-heavy.
  - No dependencies on app logic.

---

## **2. Key Differences**
| Feature                | Container Components       | Presentational Components  |
|------------------------|----------------------------|----------------------------|
| **State Management**   | Own state/Redux connect    | Stateless (props only)     |
| **Side Effects**       | API calls, subscriptions   | None                       |
| **Reusability**        | Low (app-specific)         | High                       |
| **Styling**            | Minimal                    | Extensive                  |
| **Testing**            | Complex (logic heavy)      | Easy (visual only)         |

---

## **3. Code Examples**
### **Container Component (Smart)**
```javascript
// UserContainer.js
import React, { useEffect, useState } from 'react';
import { fetchUser } from './api';
import UserProfile from './UserProfile'; // Presentational component

const UserContainer = ({ userId }) => {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    const loadData = async () => {
      const data = await fetchUser(userId);
      setUser(data);
      setLoading(false);
    };
    loadData();
  }, [userId]);

  if (loading) return <ActivityIndicator />;

  return <UserProfile user={user} />;
};

export default UserContainer;
```

### **Presentational Component (Dumb)**
```javascript
// UserProfile.js
import React from 'react';
import { View, Text, Image, StyleSheet } from 'react-native';

const UserProfile = ({ user }) => (
  <View style={styles.container}>
    <Image source={{ uri: user.avatar }} style={styles.avatar} />
    <Text style={styles.name}>{user.name}</Text>
    <Text>{user.bio}</Text>
  </View>
);

const styles = StyleSheet.create({
  container: { padding: 20 },
  avatar: { width: 100, height: 100, borderRadius: 50 },
  name: { fontSize: 20, fontWeight: 'bold' }
});

export default UserProfile;
```

---

## **4. When to Use Each**
### **Use Container Components When:**
- You need to **fetch data**.
- You **manage state** (local or global).
- You **handle business logic**.
- You **connect to Redux/MobX**.

### **Use Presentational Components When:**
- You're **displaying UI**.
- You want **reusable components**.
- You need **style isolation**.
- You're **not handling logic**.

---

## **5. Evolution with Hooks**
Modern React (with hooks) blurs these lines, but the **separation of concerns** remains valuable:

```javascript
// Modern hybrid component using hooks
const UserProfile = ({ userId }) => {
  // Logic (traditionally container responsibility)
  const [user, setUser] = useState(null);
  useEffect(() => { /* fetch user */ }, [userId]);

  // Presentation (traditionally presentational responsibility)
  if (!user) return <ActivityIndicator />;
  
  return (
    <View style={styles.container}>
      <Text>{user.name}</Text>
    </View>
  );
};
```

---

## **6. Best Practices**
1. **Single Responsibility**: Keep containers and presentational components separate.
2. **Prop Types**: Document props for presentational components.
3. **Reusability**: Maximize presentational component reuse across projects.
4. **Testing**:
   - Test containers for **behavior**.
   - Test presentational components for **rendering**.

---

## **7. Interview Questions**
### **Q: Why separate containers and presentational components?**
**A**:  
- **Separation of concerns** (logic vs UI).  
- **Easier testing** (isolated units).  
- **Better reusability** (dumb components work anywhere).  

### **Q: How do hooks change this pattern?**
**A**:  
Hooks allow mixing logic and presentation, but the **conceptual separation** (what vs how) remains important for maintainability.

### **Q: Would you use Redux in a presentational component?**
**A**:  
**Never**. Presentational components should receive everything via props. Containers connect to Redux.

---

## **Summary**
| **Pattern**          | **Classic Approach**       | **Modern Hooks Approach**       |
|-----------------------|----------------------------|----------------------------------|
| **Logic Location**    | Container components       | Custom hooks (e.g., `useUser`)   |
| **UI Location**       | Presentational components  | Components using hooks           |
| **Data Flow**         | Props drilling             | Context API + hooks              |

While patterns evolve, the **core principle** remains:  
**Separate what your app does from how it looks**.

# **8. Design Patterns in React Native: A Practical Guide**

Design patterns are reusable solutions to common problems in app development. Here are the most important patterns for React Native, with examples and use cases:

---

## **1. Component Patterns**
### **A. Presentational & Container Components**
- **Purpose**: Separate logic from UI
- **Example**:
  ```javascript
  // Container (logic)
  const UserContainer = () => {
    const [user, setUser] = useState(null);
    
    useEffect(() => {
      fetchUser().then(setUser);
    }, []);

    return <UserProfile user={user} />;
  };

  // Presentational (UI)
  const UserProfile = ({ user }) => (
    <View>
      <Text>{user?.name}</Text>
      <Image source={{ uri: user?.avatar }} />
    </View>
  );
  ```

### **B. Compound Components**
- **Purpose**: Group related components that share state
- **Example** (Tab Navigation):
  ```javascript
  <Tabs>
    <Tabs.Tab title="Home" />
    <Tabs.Tab title="Profile" />
  </Tabs>
  ```

---

## **2. State Management Patterns**
### **A. Provider Pattern (Context API)**
- **Purpose**: Global state without prop drilling
- **Example**:
  ```javascript
  const ThemeContext = createContext();

  const App = () => (
    <ThemeContext.Provider value="dark">
      <ChildComponent />
    </ThemeContext.Provider>
  );

  const ChildComponent = () => {
    const theme = useContext(ThemeContext);
    return <Text>Current theme: {theme}</Text>;
  };
  ```

### **B. Redux Pattern**
- **Purpose**: Predictable state container
- **Key Elements**:
  - Actions → Reducers → Store
- **React Native Example**:
  ```javascript
  // Slice
  const userSlice = createSlice({
    name: 'user',
    initialState: { name: '' },
    reducers: {
      setName: (state, action) => {
        state.name = action.payload;
      }
    }
  });

  // Component
  const UserName = () => {
    const name = useSelector(state => state.user.name);
    const dispatch = useDispatch();
    
    return (
      <TextInput 
        value={name}
        onChangeText={text => dispatch(setName(text))}
      />
    );
  };
  ```

---

## **3. Navigation Patterns**
### **A. Router Pattern**
- **Purpose**: Decouple navigation logic
- **Example** (React Navigation):
  ```javascript
  const Stack = createStackNavigator();

  const AppNavigator = () => (
    <Stack.Navigator>
      <Stack.Screen name="Home" component={HomeScreen} />
      <Stack.Screen name="Profile" component={ProfileScreen} />
    </Stack.Navigator>
  );
  ```

### **B. Modal Gateway Pattern**
- **Purpose**: Centralize modal management
- **Implementation**:
  ```javascript
  const ModalContext = createContext();

  const App = () => {
    const [currentModal, setModal] = useState(null);

    return (
      <ModalContext.Provider value={{ setModal }}>
        <MainContent />
        {currentModal === 'login' && <LoginModal />}
        {currentModal === 'logout' && <LogoutModal />}
      </ModalContext.Provider>
    );
  };
  ```

---

## **4. Performance Patterns**
### **A. Memoization**
- **Purpose**: Avoid unnecessary re-renders
- **Tools**:
  - `React.memo`
  - `useMemo`
  - `useCallback`
- **Example**:
  ```javascript
  const ExpensiveComponent = React.memo(({ data }) => {
    // Only re-renders when data changes
    return <Text>{data}</Text>;
  });
  ```

### **B. Virtualization**
- **Purpose**: Optimize long lists
- **Components**:
  - `FlatList`
  - `SectionList`
- **Example**:
  ```javascript
  <FlatList
    data={bigData}
    renderItem={({ item }) => <ListItem item={item} />}
    keyExtractor={item => item.id}
    initialNumToRender={10}
  />
  ```

---

## **5. Architectural Patterns**
### **A. Repository Pattern**
- **Purpose**: Abstract data sources
- **Example** (API Service):
  ```javascript
  class UserRepository {
    async getUsers() {
      return fetch('/api/users');
    }
    
    async getUser(id) {
      return fetch(`/api/users/${id}`);
    }
  }
  ```

### **B. Factory Pattern**
- **Purpose**: Create objects dynamically
- **React Native Use Case** (Component Factory):
  ```javascript
  const componentFactory = (type) => {
    switch(type) {
      case 'text': return TextComponent;
      case 'image': return ImageComponent;
      default: return DefaultComponent;
    }
  };

  const DynamicComponent = componentFactory(props.type);
  return <DynamicComponent {...props} />;
  ```

---

## **6. Advanced Patterns**
### **A. Higher-Order Components (HOCs)**
- **Purpose**: Component logic reuse
- **Example** (withLoading HOC):
  ```javascript
  const withLoading = (WrappedComponent) => {
    return (props) => {
      const [loading, setLoading] = useState(true);
      
      useEffect(() => {
        setLoading(false);
      }, []);

      return loading ? <ActivityIndicator /> : <WrappedComponent {...props} />;
    };
  };

  const EnhancedComponent = withLoading(MyComponent);
  ```

### **B. Render Props**
- **Purpose**: Share code between components
- **Example**:
  ```javascript
  const MouseTracker = ({ render }) => {
    const [position, setPosition] = useState({ x: 0, y: 0 });
    
    const handleTouchMove = (event) => {
      setPosition({
        x: event.nativeEvent.pageX,
        y: event.nativeEvent.pageY
      });
    };

    return (
      <View onTouchMove={handleTouchMove}>
        {render(position)}
      </View>
    );
  };

  // Usage
  <MouseTracker render={({ x, y }) => (
    <Text>Position: {x}, {y}</Text>
  )} />
  ```

---

## **When to Use Which Pattern?**
| **Problem**                          | **Pattern**                     |
|--------------------------------------|---------------------------------|
| Shared state across many components  | Provider Pattern                |
| Complex state logic                  | Redux                           |
| Optimizing list performance          | Virtualization                  |
| Reusable component logic             | HOCs or Render Props            |
| Dynamic component creation           | Factory Pattern                 |
| Decoupled navigation                 | Router Pattern                  |

---

## **Key Takeaways**
1. **Start Simple**: Begin with Presentational/Container separation
2. **Scale Wisely**: Introduce Redux/Context when prop drilling becomes painful
3. **Performance First**: Memoize and virtualize early
4. **Patterns Are Tools**: Not every app needs every pattern

These patterns will help you build **maintainable**, **scalable**, and **performant** React Native apps. Choose based on your app's specific needs! 🚀


# **Component-Driven Development (CDD) in React Native: The Complete Guide**

Component-Driven Development (CDD) is a methodology where UIs are built as **reusable, self-contained components** first, then composed into complex interfaces. Here's how it applies to React Native:

---

## **9. Core Principles of CDD in React Native**
### **A. Atomic Design Hierarchy**
Break components into scalable levels:

1. **Atoms**: Smallest units (Buttons, Text inputs)  
2. **Molecules**: Groups of atoms (Search bar = Input + Button)  
3. **Organisms**: Complex components (Header, Product card)  
4. **Templates**: Page layouts (App shell)  
5. **Pages**: Final compositions with real data  

### **B. Key Characteristics**
- **Reusability**: Components work anywhere in the app  
- **Isolation**: Develop/test independently  
- **Composability**: Combine like Lego blocks  
- **Documentation**: Storybook-style catalog  

---

## **2. Implementing CDD in React Native**
### **A. Folder Structure**
```
/src
  /components
    /atoms
      Button.js
      TextInput.js
    /molecules
      SearchBar.js
    /organisms
      ProductCard.js
    /templates
      AppLayout.js
  /screens
    HomeScreen.js
```

### **B. Component Design Standards**
#### **1. Props API Design**
```javascript
type ProductCardProps = {
  title: string;
  price: number;
  imageUrl: string;
  onPress?: () => void;
  variant?: 'default' | 'compact';
};
```

#### **2. Styling Approach**
```javascript
// Using StyleSheet with variants
const styles = StyleSheet.create({
  base: { borderRadius: 8 },
  compact: { padding: 8 },
  default: { padding: 16 }
});

const ProductCard = ({ variant = 'default', ...props }) => {
  return (
    <View style={[styles.base, styles[variant]]>
      {/* Content */}
    </View>
  );
};
```

#### **3. Composition Example**
```javascript
const SearchScreen = () => (
  <AppLayout>
    <SearchBar />
    <FlatList
      data={products}
      renderItem={({ item }) => (
        <ProductCard 
          title={item.name}
          price={item.price}
          imageUrl={item.image}
        />
      )}
    />
  </AppLayout>
);
```

---

## **3. Tools for CDD in React Native**
### **A. Storybook for React Native**
Visual component catalog:
```bash
npx -p @storybook/cli sb init --type react_native
```
**Example Story**:
```javascript
// Button.stories.js
export default {
  title: 'Atoms/Button',
  component: Button,
};

export const Primary = () => (
  <Button onPress={() => {}}>Primary Button</Button>
);

export const Disabled = () => (
  <Button disabled>Disabled Button</Button>
);
```

### **B. Bit.dev**
Cloud component hub:
```bash
bit init
bit create react-component atoms/button
```

---

## **4. Benefits for React Native Teams**
1. **Faster Development**: Reuse components across projects  
2. **Better Consistency**: Enforce design system rules  
3. **Easier Testing**: Isolated component tests  
4. **Parallel Work**: Multiple teams can work on different components  

---

## **5. CDD Workflow**
1. **Design in Isolation**  
   ```javascript
   // Develop Button component standalone
   const Button = ({ children }) => (
     <TouchableOpacity style={styles.button}>
       <Text>{children}</Text>
     </TouchableOpacity>
   );
   ```

2. **Document Variations**  
   ![Storybook UI showing button states](https://storybook.js.org/static/example-button-states-6b6d73d43b68bc6a5d9c35b28dd2c318.png)

3. **Integrate with App**  
   ```javascript
   // In your screen
   import { Button } from '@your-ui-library/atoms';
   ```

4. **Iterate Based on Usage**  
   - Collect feedback from usage  
   - Update component API as needed  

---

## **6. Real-World Example: Airbnb's Design System**
Airbnb's React Native components follow strict CDD principles:
- **Base components**: Button, Input, Text  
- **Composites**: DatePicker, LocationSearch  
- **Screen templates**: ListingDetail, BookingFlow  

```javascript
// Example from their system
<ListingCard
  title={listing.name}
  price={listing.price}
  rating={listing.rating}
  onPress={() => navigate('Detail')}
/>
```

---

## **7. Challenges & Solutions**
| **Challenge**               | **Solution**                          |
|-----------------------------|---------------------------------------|
| Props bloat                 | Compound components                   |
| Style conflicts             | Design tokens (centralized styles)    |
| Native dependencies         | Platform-specific component variants  |
| Testing interactions        | React Native Testing Library          |

---

## **8. Starter Template**
```bash
# Clone a CDD-ready React Native template
npx react-native init MyApp --template @ui-library/cdd-template
```

**Key Files**:
- `design-tokens.js` (Colors, spacing)  
- `.storybook/` (Component stories)  
- `component-linter.js` (Enforce CDD rules)  

---

## **9. Interview Ready: CDD Questions**
### **Q: How do you ensure component reusability?**
**A**:  
"By following Atomic Design principles, strict prop APIs, and documenting all variants in Storybook. For example, our Button component works in any context because it handles icons, loading states, and theming through clearly defined props."

### **Q: How does CDD improve team workflow?**
**A**:  
"Frontend and mobile teams share the same component library. Designers use Storybook as a single source of truth, reducing back-and-forth. We ship features faster because 60% of new screens just recompose existing components."

### **Q: How do you handle platform differences?**
**A**:  
"We create platform-specific component variants (e.g., `Button.ios.js` and `Button.android.js`) that share a common API but implement native patterns appropriately."

---

## **Key Takeaways**
1. **Start Small**: Build atoms first (Buttons, Inputs)  
2. **Document Religiously**: Storybook is your UI contract  
3. **Enforce Contracts**: Use TypeScript/prop-types  
4. **Compose Fearlessly**: Combine like Lego blocks  

CDD turns your React Native codebase into a **scalable, maintainable design system**. Teams at Airbnb, Uber, and Shopify swear by it! 🚀


## **10. Flexbox in React Native**
- **Purpose**: Primary layout system (similar to web but defaults to `flexDirection: 'column'`)
- **Key Props**:
  - `flex`: Size relative to siblings
  - `flexDirection`: `row` | `column` (default)
  - `justifyContent`: Align children along main axis
  - `alignItems`: Align children along cross axis
  - `alignSelf`: Override parent alignment
- **Example**:
  ```javascript
  <View style={{ flex: 1, flexDirection: 'row', justifyContent: 'center' }}>
    <View style={{ width: 50, height: 50, backgroundColor: 'red' }} />
  </View>
  ```

### **Other Layout Methods**
1. **Absolute Positioning**:
   ```javascript
   position: 'absolute',
   top: 10,
   right: 20
   ```
2. **Dimensions API**:
   ```javascript
   const { width } = Dimensions.get('window');
   ```
3. **Percentage Values**:
   ```javascript
   width: '50%'
   ```
4. **Platform-Specific Layouts**:
   ```javascript
   padding: Platform.OS === 'ios' ? 10 : 5
   ```

### **Key Differences from Web**
- No `display: grid` (use nested `View`s)
- No `float` or `clear`
- `flexShrink`/`flexGrow` behave slightly differently

**Best Practice**: Use Flexbox for 90% of layouts, absolute positioning sparingly.

### **11. Custom Hooks in React Native**  
Custom Hooks are reusable JavaScript functions (prefix with `use`) that leverage React Hooks (like `useState`, `useEffect`) to encapsulate logic. They work identically in React Native as in React.

---

### **Key Features**  
1. **Reusable Logic**: Share stateful logic across components.  
2. **Clean Components**: Move complex logic out of components.  
3. **Hook Rules**: Follow React’s Hook rules (only call at the top level).  

---

### **Basic Example: `useFetch` Hook**  
```javascript
import { useState, useEffect } from 'react';
import axios from 'axios';

const useFetch = (url) => {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchData = async () => {
      try {
        const response = await axios.get(url);
        setData(response.data);
      } catch (err) {
        setError(err);
      } finally {
        setLoading(false);
      }
    };

    fetchData();
  }, [url]);

  return { data, loading, error };
};

// Usage in Component
const MyComponent = () => {
  const { data, loading, error } = useFetch('https://api.example.com/data');
  
  if (loading) return <Text>Loading...</Text>;
  if (error) return <Text>Error: {error.message}</Text>;
  return <Text>{JSON.stringify(data)}</Text>;
};
```

---

### **Common Custom Hooks for React Native**  
#### **1. `useDeviceOrientation`**  
```javascript
import { useState, useEffect } from 'react';
import { Dimensions } from 'react-native';

const useDeviceOrientation = () => {
  const [orientation, setOrientation] = useState(
    Dimensions.get('window').width > Dimensions.get('window').height ? 'LANDSCAPE' : 'PORTRAIT'
  );

  useEffect(() => {
    const subscription = Dimensions.addEventListener('change', ({ window }) => {
      setOrientation(window.width > window.height ? 'LANDSCAPE' : 'PORTRAIT');
    });
    return () => subscription?.remove();
  }, []);

  return orientation;
};
```

#### **2. `useKeyboard` (Handle Keyboard Events)**  
```javascript
import { useState, useEffect } from 'react';
import { Keyboard } from 'react-native';

const useKeyboard = () => {
  const [keyboardHeight, setKeyboardHeight] = useState(0);

  useEffect(() => {
    const showSub = Keyboard.addListener('keyboardDidShow', (e) => {
      setKeyboardHeight(e.endCoordinates.height);
    });
    const hideSub = Keyboard.addListener('keyboardDidHide', () => {
      setKeyboardHeight(0);
    });
    return () => {
      showSub.remove();
      hideSub.remove();
    };
  }, []);

  return keyboardHeight;
};
```

#### **3. `useDebounce` (Optimize Inputs)**  
```javascript
import { useState, useEffect } from 'react';

const useDebounce = (value, delay = 500) => {
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {
    const timer = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);
    return () => clearTimeout(timer);
  }, [value, delay]);

  return debouncedValue;
};

// Usage: Search input optimization
const SearchComponent = () => {
  const [query, setQuery] = useState('');
  const debouncedQuery = useDebounce(query, 300);

  useEffect(() => {
    if (debouncedQuery) {
      // Trigger search API call
    }
  }, [debouncedQuery]);
};
```

---

### **Rules for Custom Hooks**  
1. **Naming**: Always start with `use` (e.g., `useFetch`).  
2. **Isolation**: Should be pure (no JSX).  
3. **Composability**: Can call other Hooks.  

---

### **When to Use Custom Hooks?**  
- **Shared Logic**: API calls, form handling, subscriptions.  
- **Complex Effects**: Keyboard listeners, animations.  
- **Performance**: Debouncing/throttling.  

---

### **Advantages**  
✅ **Reusability** (Use across components)  
✅ **Testability** (Logic is decoupled)  
✅ **Cleaner Code** (Reduce component complexity)  

Custom Hooks maximize code reuse while keeping components clean. They’re a **must-know** for modern React Native development. 🚀

### **12. `useState` vs `useRef` in React Native**  

| Feature               | `useState` | `useRef` |
|----------------------|------------|----------|
| **Purpose**          | Manage state that triggers re-renders | Store mutable values without re-renders |
| **Re-renders**       | Yes (updates UI) | No (does not trigger re-render) |
| **Mutable Value**    | Immutable (use setter) | Mutable (`.current` can be changed directly) |
| **Use Cases**        | Dynamic UI updates, form inputs | Accessing DOM/native elements, timers, tracking previous values |
| **Persistence**      | Resets on re-render unless preserved | Persists across re-renders |

---

### **When to Use `useState`?**  
**For state that affects UI and requires re-renders:**  
- Form inputs (`text`, `checkbox`)  
- Toggles (`isLoading`, `isVisible`)  
- Dynamic data from API responses  

**Example:**  
```javascript
const [count, setCount] = useState(0); // Triggers re-render on change
return <Text onPress={() => setCount(count + 1)}>{count}</Text>;
```

---

### **When to Use `useRef`?**  
**For values that should NOT trigger re-renders:**  
- Accessing/manipulating DOM/native elements  
- Storing timers/intervals  
- Keeping track of previous state  
- Storing mutable variables (like a counter in a loop)  

**Example:**  
```javascript
const inputRef = useRef(null); // Does not trigger re-render
const timerRef = useRef(null);

useEffect(() => {
  timerRef.current = setInterval(() => console.log("Running"), 1000);
  return () => clearInterval(timerRef.current);
}, []);

return <TextInput ref={inputRef} />;
```

---

### **Key Differences**  
1. **Re-renders:**  
   - `useState` → Updates UI.  
   - `useRef` → No UI update (silent changes).  

2. **Mutability:**  
   - `useState` → Immutable (must use setter).  
   - `useRef` → Directly mutate `.current`.  

3. **Persistence:**  
   - Both persist across re-renders, but `useRef` does not cause them.  

---

### **When to Choose Which?**  
| Scenario | `useState` | `useRef` |
|----------|------------|----------|
| Need UI to update? | ✅ Yes | ❌ No |
| Need to access a DOM element? | ❌ No | ✅ Yes |
| Need to store a value that shouldn’t trigger re-renders? | ❌ No | ✅ Yes |
| Need to track previous state? | ❌ (Use `useEffect` + `useRef`) | ✅ Yes |

---

### **Advanced Example: Combining Both**  
```javascript
const [count, setCount] = useState(0);
const prevCountRef = useRef();

useEffect(() => {
  prevCountRef.current = count; // Track previous value without re-render
}, [count]);

return (
  <View>
    <Text>Now: {count}, Before: {prevCountRef.current}</Text>
    <Button title="Increment" onPress={() => setCount(count + 1)} />
  </View>
);
```

---

### **Summary**  
- **`useState`** → For state that **should update the UI**.  
- **`useRef`** → For **mutable values that shouldn’t re-render** (DOM refs, timers, etc.).  

Use `useState` for dynamic UI, `useRef` for behind-the-scenes data. 🚀

### **12. Checking Environment in React Native**  

#### **1. Debug Mode**  
```javascript
const isDebug = __DEV__; // true in development, false in production
```  

#### **2. Environment Variables** (Using `react-native-config`)  
```javascript
import Config from 'react-native-config';  

const env = Config.ENV; // 'dev', 'staging', 'prod'  
```  

#### **3. Detect Release Build**  
```javascript
const isRelease = !__DEV__;  
```  

#### **4. Platform-Specific Checks**  
```javascript
import { Platform } from 'react-native';  

const isAndroid = Platform.OS === 'android';  
const isIOS = Platform.OS === 'ios';  
```  

#### **5. Metro Bundler Dev Server**  
```javascript
const isMetroRunning = !!global.originalRequire; // true if connected to dev server  
```  

---

### **When to Use?**  
- **`__DEV__`** → Simple debug checks (logs, mock APIs).  
- **`react-native-config`** → Multi-environment setups (API URLs).  
- **`Platform`** → OS-specific behavior.  

**Example:**  
```javascript
if (__DEV__) console.log("Debug mode!");  
```  

Use these to toggle features, APIs, or logs based on the environment. 🚀

### **13. Controlled vs Uncontrolled Components in React Native**  

#### **1. Controlled Components**  
- **Definition**: Managed by React state.  
- **Usage**: Forms, dynamic inputs.  
- **Example**:  
  ```javascript
  const [text, setText] = useState('');
  <TextInput
    value={text}
    onChangeText={setText} // State controls the input
  />
  ```  
- **Pros**: Predictable, easy validation.  
- **Cons**: More boilerplate.  

#### **2. Uncontrolled Components**  
- **Definition**: Managed by DOM/ref (React doesn’t control value).  
- **Usage**: Simple forms, file inputs.  
- **Example**:  
  ```javascript
  const inputRef = useRef(null);
  <TextInput
    ref={inputRef} // Access value via ref later
    defaultValue="Initial" // Not state-controlled
  />
  // Get value: inputRef.current.value
  ```  
- **Pros**: Less code, better performance for large forms.  
- **Cons**: Harder to validate/reset.  

### **When to Use?**  
- **Controlled**: Most cases (forms, dynamic UIs).  
- **Uncontrolled**: File inputs, performance-critical forms.  

**Rule of Thumb**: Prefer controlled for React Native. 🚀


## **14. Secure Storage in React Native: Keychain (iOS) & Keystore (Android) Explained**  

#### **1. Keychain (iOS)**
- **What?**  
  Apple’s encrypted system-level storage for sensitive data (passwords, tokens, certificates).  
- **Why?**  
  - Hardware-backed encryption (secure even if device is compromised).  
  - Auto-locks when device is locked.  
  - Supports biometric (FaceID/TouchID) protection.  

#### **2. Keystore (Android)**  
- **What?**  
  Android’s secure container for cryptographic keys and sensitive data.  
- **Why?**  
  - Uses hardware security modules (HSM) on supported devices.  
  - Prevents extraction even with root access.  

---

### **How to Use Them?**  
#### **Step 1: Install `react-native-keychain`**  
```bash
npm install react-native-keychain
npx pod-install  # For iOS
```

#### **Step 2: Save Data Securely**  
```javascript
import * as Keychain from 'react-native-keychain';

// Save (automatically uses Keychain/Keystore based on OS)
await Keychain.setGenericPassword('username', 'secret_token', {
  service: 'com.your.app',  // Unique identifier
  accessControl: Keychain.ACCESS_CONTROL.BIOMETRY_ANY,  // Optional biometric lock
});
```

#### **Step 3: Retrieve Data**  
```javascript
const credentials = await Keychain.getGenericPassword({
  service: 'com.your.app',
});
console.log(credentials.password);  // 'secret_token'
```

#### **Step 4: Delete Data**  
```javascript
await Keychain.resetGenericPassword({
  service: 'com.your.app',
});
```

---

### **Key Features**  
| Feature                | Keychain (iOS)           | Keystore (Android)       |  
|------------------------|--------------------------|--------------------------|  
| **Encryption**         | Hardware-backed          | Hardware-backed (if available) |  
| **Biometric Lock**     | Yes (FaceID/TouchID)     | Yes (Fingerprint)        |  
| **Data Persistence**   | Survives app uninstall   | Survives app uninstall   |  
| **Access Control**     | Configurable (e.g., `BIOMETRY_CURRENT_SET`) | Configurable (e.g., `USER_PRESENCE`) |  

---

### **When to Use?**  
- **✅ Auth tokens**  
- **✅ API keys**  
- **✅ User credentials**  
- **❌ Large data** (use encrypted files instead)  

---

### **Example: Secure Token Storage**  
```javascript
// Save after login
await Keychain.setGenericPassword('user', 'jwt_token', {
  service: 'com.myapp.auth',
  accessControl: Keychain.ACCESS_CONTROL.BIOMETRY_ANY,
});

// Retrieve for API calls
const { password } = await Keychain.getGenericPassword({ service: 'com.myapp.auth' });
fetch('/api/user', {
  headers: { Authorization: `Bearer ${password}` },
});
```

---

### **Best Practices**  
1. **Always use a unique `service` ID** (e.g., `com.your.appname`).  
2. **Enable biometrics** for critical operations.  
3. **Fallback**: If Keychain fails, force re-authentication.  

This is the **most secure** way to handle sensitive data in React Native. 🔐  

For advanced use cases (e.g., RSA keys), explore:  
- [`react-native-sensitive-info`](https://github.com/mCodex/react-native-sensitive-info) (Android Keystore + iOS Keychain)  
- Android’s `EncryptedSharedPreferences` for non-token data.


### **15. Functional Programming in JavaScript**  
Functional Programming (FP) is a programming paradigm where:  
- **Functions are first-class citizens** (can be assigned to variables, passed as arguments, returned from other functions).  
- **Pure functions** (no side effects, same input → same output) are preferred.  
- **Immutability** (data is never modified, but copied/transformed).  
- **Avoid shared state and mutable data**.  

---

## **Core Concepts with Examples**  

### **1. Pure Functions**  
A function that:  
- **Always returns the same output** for the same input.  
- **No side effects** (doesn’t modify external state).  

✅ **Pure:**  
```javascript
const add = (a, b) => a + b;  
console.log(add(2, 3)); // Always 5  
```

❌ **Impure:**  
```javascript
let tax = 0.1;  
const calculateTotal = (price) => price + (price * tax); // Depends on external `tax`  
```

---

### **2. Immutability**  
Instead of modifying data, **create new copies**.  

✅ **Immutable (Good):**  
```javascript
const numbers = [1, 2, 3];  
const newNumbers = [...numbers, 4]; // Doesn’t modify `numbers`  
```

❌ **Mutable (Bad):**  
```javascript
numbers.push(4); // Modifies original array  
```

---

### **3. Higher-Order Functions (HOFs)**  
Functions that:  
- **Take functions as arguments**, or  
- **Return functions**.  

**Example:**  
```javascript
// HOF: Takes a function as argument
const applyOperation = (a, b, operation) => operation(a, b);  

const add = (x, y) => x + y;  
console.log(applyOperation(2, 3, add)); // 5  
```

**Built-in HOFs in JavaScript:**  
```javascript
// Array methods
[1, 2, 3].map(x => x * 2); // [2, 4, 6]  
[1, 2, 3].filter(x => x > 1); // [2, 3]  
[1, 2, 3].reduce((acc, x) => acc + x, 0); // 6  
```

---

### **4. Function Composition**  
Combining small functions into larger ones.  

**Example:**  
```javascript
const double = x => x * 2;  
const square = x => x * x;  

// Compose: square(double(x))
const doubleThenSquare = x => square(double(x));  
console.log(doubleThenSquare(3)); // 36  
```

**Using `compose` (Lodash/Ramda style):**  
```javascript
const compose = (f, g) => x => f(g(x));  
const doubleThenSquare = compose(square, double);  
console.log(doubleThenSquare(3)); // 36  
```

---

### **5. Avoiding Side Effects**  
Side effects = Changes outside the function (e.g., modifying globals, DOM, API calls).  

✅ **No Side Effects:**  
```javascript
const greet = (name) => `Hello, ${name}!`;  
```

❌ **Side Effects:**  
```javascript
let greeting = "";  
const setGreeting = (name) => {  
  greeting = `Hello, ${name}!`; // Modifies external state  
};  
```

---

### **6. Currying**  
Transforming a function with multiple arguments into a sequence of single-argument functions.  

**Example:**  
```javascript
// Normal function
const add = (a, b) => a + b;  

// Curried version
const curriedAdd = a => b => a + b;  
const add5 = curriedAdd(5);  
console.log(add5(3)); // 8  
```

**Practical Use (e.g., React event handlers):**  
```javascript
const handleChange = field => event => {  
  setFormData({ ...formData, [field]: event.target.value });  
};  

<input onChange={handleChange("email")} />  
```

---

## **Why Use Functional Programming?**  
✔ **Easier debugging** (pure functions = predictable)  
✔ **Better readability** (small, reusable functions)  
✔ **Concurrency-friendly** (no shared state = fewer race conditions)  
✔ **Testable** (no hidden dependencies)  

---

## **FP Libraries for JavaScript**  
- **Lodash/FP** (functional helpers)  
- **Ramda** (curried, composable utilities)  
- **Immer** (immutable updates)  

---

### **Final Example: FP-Style Data Processing**  
```javascript
const users = [
  { id: 1, name: "Alice", age: 25 },
  { id: 2, name: "Bob", age: 30 },
];

// Get names of users over 28
const result = users
  .filter(user => user.age > 28)
  .map(user => user.name);

console.log(result); // ["Bob"]  
```

---

### **Key Takeaways**  
🔹 **Use pure functions** (avoid side effects).  
🔹 **Prefer immutability** (don’t modify, create new data).  
🔹 **Leverage HOFs** (`map`, `filter`, `reduce`).  
🔹 **Compose functions** for complex logic.  

Would you like a deeper dive into any specific FP concept? 🚀

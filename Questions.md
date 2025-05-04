### **JavaScript (Advanced)**

#### **Q1: Explain the Event Loop with an example. How does it differ in Node.js vs browsers?**  
**Answer:**  
The event loop handles asynchronous operations. Key differences:  

| Feature          | Browser              | Node.js              |
|------------------|----------------------|----------------------|
| **Microtasks**   | `Promise.then`, `queueMicrotask` | Same + `process.nextTick` (higher priority) |
| **Macrotasks**   | `setTimeout`, DOM events | `setTimeout`, `setImmediate`, I/O |  

**Example:**  
```javascript
console.log('Start');
setTimeout(() => console.log('Timeout'), 0);
Promise.resolve().then(() => console.log('Promise'));
// Browser: Start → Promise → Timeout
// Node.js: Start → Promise → Timeout (unless `nextTick` exists)
```

---

#### **Q2: How does `this` binding work in arrow functions vs regular functions?**  
**Answer:**  
- **Regular functions**: Dynamic `this` (depends on invocation).  
- **Arrow functions**: Lexical `this` (inherited from enclosing scope).  

**Bug Fix Example:**  
```javascript
// Broken: `this` refers to DOM element
button.addEventListener('click', this.handleClick); 
// Fixed: Arrow preserves `this`
button.addEventListener('click', () => this.handleClick());
```

---

#### **Q3: Implement a debounce function from scratch.**  
**Answer:**  
```javascript
function debounce(func, delay) {
  let timeoutId;
  return (...args) => {
    clearTimeout(timeoutId);
    timeoutId = setTimeout(() => func(...args), delay);
  };
}
// Usage: `<TextInput onChangeText={debounce(search, 300)} />`
```

---

#### **Q4: What are generator functions? How would you use them to handle async flows without `async/await`?**  
**Answer:**  
Generators (`function*`) pause/resume execution using `yield`.  

**Async Flow Example:**  
```javascript
function* fetchUser() {
  const user = yield fetch('/user');
  const posts = yield fetch(`/posts/${user.id}`);
  return posts;
}

// Manual execution
const generator = fetchUser();
generator.next().value
  .then(res => generator.next(res).value)
  .then(res => generator.next(res));
```

---

#### **Q5: Explain how `Object.defineProperty` enables reactivity in Vue.js.**  
**Answer:**  
Vue 2 uses `Object.defineProperty` to track changes:  
```javascript
const data = { count: 0 };
Object.defineProperty(data, 'count', {
  get() { /* Track dependency */ },
  set(newVal) { /* Trigger updates */ }
});
```
**React's Approach**: Synthetic events + state comparison (shallow by default).

---

#### **Q6: How would you clone a deeply nested object without `JSON.parse(JSON.stringify())`?**  
**Answer:**  
```javascript
function deepClone(obj, map = new WeakMap()) {
  if (obj === null || typeof obj !== 'object') return obj;
  if (map.has(obj)) return map.get(obj); // Handle circular refs

  const clone = Array.isArray(obj) ? [] : {};
  map.set(obj, clone);
  for (const key in obj) {
    clone[key] = deepClone(obj[key], map);
  }
  return clone;
}
```

---

#### **Q7: What are WeakMaps? How are they useful in React Native performance optimization?**  
**Answer:**  
**WeakMaps** hold weak references (garbage-collected when keys are unused).  

**Use Case**: Private class fields without memory leaks:  
```javascript
const privateData = new WeakMap();

class User {
  constructor(name) {
    privateData.set(this, { name });
  }
}
```

---

#### **Q8: Explain the difference between `for...in`, `for...of`, and `Array.forEach`.**  
**Answer:**  

| Method        | Used For              | Prototype Chain | Returns       |
|---------------|-----------------------|------------------|---------------|
| `for...in`    | Enumerable properties | Includes inherited | Keys         |
| `for...of`    | Iterables (Arrays, Map) | No              | Values       |
| `forEach`     | Arrays only           | No              | Undefined    |

---

#### **Q9: How does `Promise.allSettled` differ from `Promise.all`?**  
**Answer:**  
- **`Promise.all`**: Fails fast (rejects if any promise rejects).  
- **`Promise.allSettled`**: Waits for all, returns statuses.  

**Use Case**:  
```javascript
// Use allSettled for analytics batch
const results = await Promise.allSettled([trackEvent1(), trackEvent2()]);
const successful = results.filter(r => r.status === 'fulfilled');
```

---

#### **Q10: Write a polyfill for `Array.prototype.flatMap`.**  
**Answer:**  
```javascript
Array.prototype.flatMap = function(callback, thisArg) {
  return this.reduce((acc, item, index) => {
    const result = callback.call(thisArg, item, index, this);
    return acc.concat(Array.isArray(result) ? result : [result]);
  }, []);
};
```

---

### **React Native (Advanced)**

#### **Q11: How does React Native’s new architecture (JSI, Fabric, TurboModules) improve performance?**  
**Answer:**  
- **JSI**: JavaScript Interface (C++ layer) enables direct native communication.  
- **Fabric**: Synchronous rendering pipeline (no bridge bottleneck).  
- **TurboModules**: Lazy-loaded native modules.  

---

#### **Q12: Debug a frozen React Native app. What tools would you use?**  
**Answer:**  
1. **Hermes Debugger**: Check JavaScript thread stalls.  
2. **Flipper**: Inspect native threads (especially UI thread).  
3. **Systrace**: Identify slow operations (`adb shell systrace`).  

---

### **React Native (Advanced) Continued**

#### **Q13: Why does `setState` sometimes update components inconsistently?**  
**Answer:**  
**Cause:** React batches state updates for performance, but this can lead to stale state if not handled properly.  

**Fix:**  
```javascript
// Use functional updates for dependent states
setCount(prevCount => prevCount + 1);
// Or use unstable_batchedUpdates in React 17-
unstable_batchedUpdates(() => {
  setCount(c => c + 1);
  setFlag(f => !f);
});
```

---

#### **Q14: Implement a deep linking solution with authentication flow handling.**  
**Answer:**  
```javascript
// App.tsx
useEffect(() => {
  Linking.getInitialURL().then(handleDeepLink);
  Linking.addEventListener('url', ({ url }) => handleDeepLink(url));
}, []);

const handleDeepLink = (url) => {
  if (isAuthRoute(url) && !user) {
    navigate('Login', { redirect: url }); // Redirect after auth
  } else {
    navigate(parseRoute(url));
  }
};
```

---

#### **Q15: How would you reduce a React Native app’s startup time by 50%?**  
**Answer:**  
1. **Hermes**: Precompile JS to bytecode.  
2. **Code Splitting**: Load only essential screens.  
3. **Native Profiling**: Optimize `didFinishLaunching` in Xcode/Android Studio.  
4. **Image Optimization**: Use smaller assets or SVGs.  

---

#### **Q16: Explain how `useMemo` differs from `useCallback`.**  
**Answer:**  
| Hook           | Purpose                           | Example                          |
|----------------|-----------------------------------|----------------------------------|
| `useMemo`      | Memoizes *values* (e.g., computed data) | `const filteredList = useMemo(() => list.filter(...), [list])` |
| `useCallback`  | Memoizes *functions* (e.g., event handlers) | `const handlePress = useCallback(() => {...}, [deps])` |

**Avoid When:**  
- Computation is trivial.  
- Dependencies change frequently.  

---

#### **Q17: Why does `FlatList` outperform `ScrollView`?**  
**Answer:**  
- **Windowing**: Renders only visible items (10 items vs 10,000).  
- **Optimization Tips**:  
  ```javascript
  <FlatList
    getItemLayout={(_, index) => ({
      length: ITEM_HEIGHT,
      offset: ITEM_HEIGHT * index,
      index
    })}
    initialNumToRender={10}
    windowSize={5} // Render 5x visible items
  />
  ```

---

#### **Q18: Handle a memory leak in a React Native app with recurring animations.**  
**Answer:**  
```javascript
useEffect(() => {
  const animation = Animated.timing(/* ... */);
  animation.start();
  return () => {
    animation.stop(); // Cleanup animation
    animation.removeAllListeners();
  };
}, []);
```

---

#### **Q19: Implement a responsive grid layout for foldable devices.**  
**Answer:**  
```javascript
const { width } = useWindowDimensions();
const isFolded = width < 600; // Breakpoint for foldables

return (
  <View style={{ flexDirection: isFolded ? 'column' : 'row' }}>
    <View style={{ flex: 1 }}>Panel 1</View>
    <View style={{ flex: 1 }}>Panel 2</View>
  </View>
);
```

---

#### **Q20: What’s the difference between `react-native-reanimated` and the standard `Animated` API?**  
**Answer:**  
| Feature          | `Animated`               | `Reanimated`          |
|------------------|--------------------------|-----------------------|
| **Thread**       | JS thread (bridge)       | UI thread             |
| **Performance**  | Slow for gestures        | 60FPS animations      |
| **Syntax**       | Declarative              | Worklets (inline JS)  |

---

#### **Q21: How do you securely store JWT tokens in a React Native app?**  
**Answer:**  
```javascript
import * as SecureStore from 'expo-secure-store';

// Save token with device encryption
await SecureStore.setItemAsync('token', jwt, {
  keychainAccessible: SecureStore.WHEN_UNLOCKED_THIS_DEVICE_ONLY,
});
```

---

#### **Q22: Debug a native module crash in production.**  
**Answer:**  
1. **Symbolicate** crash logs using `ndk-stack` (Android) or Xcode (iOS).  
2. **Sentry**: Upload debug symbols for readable stack traces.  
3. **Reproduce**: Test edge cases in debug builds with native logs.  

---

#### **Q23: Why might `zIndex` not work as expected in React Native?**  
**Answer:**  
**Common Causes:**  
- Parent view has `overflow: 'hidden'`.  
- Android requires `elevation` instead of `zIndex`.  

**Fix:**  
```javascript
{
  position: 'absolute',
  zIndex: 10,    // iOS
  elevation: 10, // Android
}
```

---

#### **Q24: Implement a WebSocket-based real-time chat with offline support.**  
**Answer:**  
```javascript
const queue = [];
const socket = new WebSocket('ws://...');

// Send queued messages on reconnect
socket.onopen = () => {
  queue.forEach(msg => socket.send(msg));
  queue.length = 0;
};

const send = (msg) => {
  if (socket.readyState !== 1) {
    queue.push(msg); // Offline: add to queue
  } else {
    socket.send(msg); // Online: send immediately
  }
};
```

---

#### **Q25: How would you test a component that uses `react-native-maps`?**  
**Answer:**  
**Mock Native Modules:**  
```javascript
jest.mock('react-native-maps', () => {
  const { View } = require('react-native');
  return {
    __esModule: true,
    default: (props) => <View {...props} testID="map" />,
    Marker: (props) => <View {...props} />,
  };
});
```

---

### **System Design**

#### **Q26: Design a React Native architecture for a team of 10 developers.**  
**Answer:**  
- **Monorepo**: Shared components via `yarn workspaces` or `npm workspaces`.  
- **Feature Flags**: LaunchDarkly for gradual rollouts.  
- **CI/CD**: EAS (Expo) or Fastlane (bare React Native).  
- **State Management**: Redux Toolkit or Zustand for shared state.  

---

#### **Q27: Migrate a legacy app from class components to hooks.**  
**Answer:**  
1. **Incremental Migration**:  
   - Convert leaf components first.  
   - Use `useState` for local state.  
2. **Side Effects**:  
   - Replace `componentDidMount` with `useEffect`.  
3. **Codemods**:  
   - Tools like `jscodeshift` for bulk conversions.  

---

Here are the complete answers to all 42 questions, formatted exactly as requested:

---

### **React Navigation (v6+)**

#### **1. How do you handle authentication flows (protected routes) in React Navigation?**
**Answer:**
```javascript
// Using a navigation state listener
const App = () => {
  const { user } = useAuth();
  const navigation = useNavigation();

  useEffect(() => {
    const unsubscribe = navigation.addListener('state', (e) => {
      if (!user && e.data.state.routes.some(route => route.name === 'Protected')) {
        navigation.navigate('Login');
      }
    });
    return unsubscribe;
  }, [user]);

  return (
    <NavigationContainer>
      <Stack.Navigator>
        {user ? (
          <Stack.Screen name="Protected" component={ProtectedScreen} />
        ) : (
          <Stack.Screen name="Login" component={LoginScreen} />
        )}
      </Stack.Navigator>
    </NavigationContainer>
  );
};
```

#### **2. Implement a bottom tab navigator where one tab is a modal overlay.**
**Answer:**
```javascript
const Tab = createBottomTabNavigator();
const RootStack = createNativeStackNavigator();

function TabNavigator() {
  return (
    <Tab.Navigator>
      <Tab.Screen name="Home" component={HomeScreen} />
      <Tab.Screen name="ModalTab" component={EmptyScreen} />
    </Tab.Navigator>
  );
}

function App() {
  return (
    <RootStack.Navigator>
      <RootStack.Screen name="Tabs" component={TabNavigator} options={{ headerShown: false }} />
      <RootStack.Screen 
        name="Modal" 
        component={ModalScreen} 
        options={{ presentation: 'modal' }} 
      />
    </RootStack.Navigator>
  );
}
```

#### **3. Why does navigation prop sometimes become undefined?**
**Answer:**
This happens when:
1. Component isn't rendered inside a NavigationContainer
2. Using navigation prop in a deeply nested child without proper forwarding
3. Accessing navigation during initial render before it's available

**Fix:**
```javascript
// Use the useNavigation hook instead
const navigation = useNavigation();

// Or ensure proper nesting
<NavigationContainer>
  <App />
</NavigationContainer>
```

#### **4. Persist navigation state when app restarts**
**Answer:**
```javascript
const [isReady, setIsReady] = useState(false);
const [initialState, setInitialState] = useState();

useEffect(() => {
  const restoreState = async () => {
    try {
      const savedState = await AsyncStorage.getItem('NAV_STATE');
      if (savedState) setInitialState(JSON.parse(savedState));
    } finally {
      setIsReady(true);
    }
  };

  restoreState();
}, []);

if (!isReady) return null;

return (
  <NavigationContainer
    initialState={initialState}
    onStateChange={(state) => 
      AsyncStorage.setItem('NAV_STATE', JSON.stringify(state))
    }
  >
    {/* ... */}
  </NavigationContainer>
);
```

#### **5. Debug deep link working on iOS but not Android**
**Answer:**
Check these Android-specific issues:
1. Missing intent filter in AndroidManifest.xml:
```xml
<intent-filter>
  <action android:name="android.intent.action.VIEW" />
  <category android:name="android.intent.category.DEFAULT" />
  <category android:name="android.intent.category.BROWSABLE" />
  <data android:scheme="yourapp" />
</intent-filter>
```
2. Verify proper linking setup:
```javascript
// In App.js
const config = {
  screens: {
    Profile: 'user/:id',
  },
};

const linking = {
  prefixes: ['yourapp://'],
  config,
};
```

#### **6. Custom transition animation**
**Answer:**
```javascript
const options = {
  cardStyleInterpolator: ({ current, next, layouts }) => ({
    cardStyle: {
      transform: [
        {
          translateX: current.progress.interpolate({
            inputRange: [0, 1],
            outputRange: [layouts.screen.width, 0],
          }),
        },
        {
          rotate: current.progress.interpolate({
            inputRange: [0, 1],
            outputRange: ['180deg', '0deg'],
          }),
        },
      ],
    },
  }),
};

<Stack.Screen name="Animated" component={Screen} options={options} />
```

#### **7. useFocusEffect memory leaks**
**Answer:**
Always include cleanup:
```javascript
useFocusEffect(
  useCallback(() => {
    const subscription = BackHandler.addEventListener('hardwareBackPress', handleBack);

    return () => {
      subscription.remove();
      // Any other cleanup
    };
  }, [handleBack])
);
```

---

### **UI/UX Best Practices**

#### **8. Responsive layout for phones/tablets**
**Answer:**
```javascript
const { width, height } = useWindowDimensions();
const isTablet = width >= 768;

return (
  <View style={styles.container}>
    {isTablet ? (
      <View style={styles.tabletLayout}>
        <Sidebar />
        <MainContent />
      </View>
    ) : (
      <ScrollView>
        <MobileLayout />
      </ScrollView>
    )}
  </View>
);

const styles = StyleSheet.create({
  tabletLayout: {
    flexDirection: 'row',
  },
});
```

#### **9. High-performance splash screen**
**Answer:**
```javascript
// 1. Use react-native-bootsplash for native splash
// 2. For animation:
function AnimatedSplash({ onAnimationEnd }) {
  const fadeAnim = useRef(new Animated.Value(1)).current;

  useEffect(() => {
    Animated.timing(fadeAnim, {
      toValue: 0,
      duration: 1000,
      useNativeDriver: true,
    }).start(onAnimationEnd);
  }, []);

  return (
    <Animated.View style={{ opacity: fadeAnim }}>
      <Lottie 
        source={require('./splash.json')} 
        autoPlay 
        loop={false}
      />
    </Animated.View>
  );
}
```

#### **10. Optimize 50+ touchable items**
**Answer:**
```javascript
const renderItem = useCallback(({ item }) => (
  <MemoizedListItem 
    item={item}
    onPress={handlePress}
  />
), [handlePress]);

return (
  <FlatList
    data={data}
    renderItem={renderItem}
    keyExtractor={item => item.id}
    initialNumToRender={12}
    maxToRenderPerBatch={6}
    windowSize={10}
    removeClippedSubviews={true}
  />
);

const MemoizedListItem = React.memo(ListItem, (prev, next) => 
  prev.item.id === next.item.id && prev.item.isSelected === next.item.isSelected
);
```

#### **11. Draggable grid implementation**
**Answer:**
```javascript
import Animated, { useSharedValue } from 'react-native-reanimated';
import { GestureDetector, Gesture } from 'react-native-gesture-handler';

function DraggableGrid() {
  const positions = useSharedValue(
    Object.fromEntries(data.map((item, index) => [item.id, { x: 0, y: index * 100 }])
  );

  const gesture = Gesture.Pan()
    .onStart(() => { /* store initial position */ })
    .onUpdate((e) => { /* update shared value */ });

  return (
    <View>
      {data.map((item) => (
        <GestureDetector gesture={gesture} key={item.id}>
          <Animated.View style={[styles.item, { transform: [{ translateX }, { translateY }] }]} />
        </GestureDetector>
      ))}
    </View>
  );
}
```

#### **12. Shadow differences iOS/Android**
**Answer:**
```javascript
const styles = StyleSheet.create({
  shadow: {
    // iOS
    shadowColor: '#000',
    shadowOffset: { width: 0, height: 2 },
    shadowOpacity: 0.25,
    shadowRadius: 3.84,
    // Android
    elevation: 5,
    // Optional: Add background color for Android shadows to appear
    backgroundColor: 'white',
  },
});
```

#### **13. Text overflow in multi-language apps**
**Answer:**
```javascript
<Text
  numberOfLines={2}
  ellipsizeMode="tail"
  adjustsFontSizeToFit={true}
  minimumFontScale={0.5}
  style={[styles.text, { fontSize: calculateFontSizeBasedOnLanguage(text)]}
>
  {text}
</Text>

// Helper function
function calculateFontSizeBasedOnLanguage(text) {
  // Return smaller size for languages with longer words (e.g., German)
  return text.length > 20 ? 14 : 16;
}
```

#### **14. Theme switcher with system defaults**
**Answer:**
```javascript
const ThemeContext = createContext();

function ThemeProvider({ children }) {
  const systemTheme = useColorScheme();
  const [theme, setTheme] = useState(systemTheme);

  const toggleTheme = () => {
    setTheme(prev => prev === 'light' ? 'dark' : 'light');
  };

  const themePreference = {
    theme,
    isDark: theme === 'dark',
    toggleTheme,
  };

  return (
    <ThemeContext.Provider value={themePreference}>
      <PaperProvider theme={theme === 'dark' ? DarkTheme : DefaultTheme}>
        {children}
      </PaperProvider>
    </ThemeContext.Provider>
  );
}

// Usage
const { isDark, toggleTheme } = useContext(ThemeContext);
```

---

### **Core React Concepts**

#### **15. Reconciliation Algorithm with Fibers**
**Answer:**
React's reconciliation works through:
1. **Virtual DOM**: Lightweight representation of UI
2. **Diffing Algorithm**:
   - Elements of different types → Tear down and rebuild
   - Same DOM element types → Update only changed attributes
   - Component types → Update instance and recurse
3. **Fiber Architecture**:
   - Breaks work into units (fibers)
   - Enables interruption/resumption of work
   - Prioritizes updates (e.g., animations over data fetching)

**Key Optimization**: Keys help React identify which items changed/added/removed

#### **16. useReducer vs useState**
**Answer:**
Use `useReducer` when:
- State logic is complex
- Next state depends on previous state
- Need to manage multiple sub-values

**Example: Form with validation**
```javascript
const formReducer = (state, action) => {
  switch (action.type) {
    case 'UPDATE_FIELD':
      return { ...state, [action.field]: action.value };
    case 'VALIDATE':
      return { ...state, errors: validate(state) };
    case 'SUBMIT':
      return { ...state, isSubmitting: true };
    default:
      return state;
  }
};

const [form, dispatch] = useReducer(formReducer, initialState);
```

#### **17. useRef vs document.getElementById**
**Answer:**
`useRef` is preferred because:
1. Works consistently across React Native and React DOM
2. Provides stable reference across re-renders
3. Better for React's declarative model
4. Doesn't break component encapsulation

**Example:**
```javascript
const inputRef = useRef(null);

// Access later
inputRef.current.focus();

// Attach to element
<TextInput ref={inputRef} />
```

#### **18. Debug useEffect running too frequently**
**Answer:**
Common fixes:
1. Check dependency array:
```javascript
useEffect(() => {
  // Effect logic
}, [dep1, dep2]); // Only runs when these change
```
2. Use useCallback for functions:
```javascript
const fetchData = useCallback(() => { ... }, [dep]);

useEffect(() => {
  fetchData();
}, [fetchData]);
```
3. Use useMemo for objects:
```javascript
const config = useMemo(() => ({ option: value }), [value]);

useEffect(() => {
  // Uses config
}, [config]);
```

#### **19. Synthetic Events vs Native Events**
**Answer:**
**Synthetic Events:**
- Cross-browser wrapper around native events
- Pooled (reused for performance)
- Consistent API across browsers
- Event delegation (single listener at root)

**Native Events:**
- Browser-specific implementations
- May have performance differences
- Direct DOM access required

#### **20. Custom hook for device orientation**
**Answer:**
```javascript
function useDeviceOrientation() {
  const [orientation, setOrientation] = useState(
    Dimensions.get('window').width > Dimensions.get('window').height 
      ? 'LANDSCAPE' 
      : 'PORTRAIT'
  );

  useEffect(() => {
    const update = () => {
      const { width, height } = Dimensions.get('window');
      setOrientation(width > height ? 'LANDSCAPE' : 'PORTRAIT');
    };

    Dimensions.addEventListener('change', update);
    return () => Dimensions.removeEventListener('change', update);
  }, []);

  return orientation;
}
```

#### **21. When React.memo doesn't help**
**Answer:**
`React.memo` might not improve performance when:
1. Props change frequently anyway
2. Component is simple (shallow comparison cost > re-render cost)
3. Children components aren't memoized
4. You pass new object/function references each render

**When to avoid:**
- For components that always receive new props
- For very simple components
- When parent already controls re-renders

---

### **React Native Advanced**

#### **22. 1000+ Map Markers**
**Answer:**
```javascript
import Cluster from 'react-native-maps-super-cluster';

function MapWithClusters() {
  const renderMarker = (marker) => (
    <Marker coordinate={marker.location}>
      <CustomMarker />
    </Marker>
  );

  const renderCluster = (cluster) => (
    <Marker onPress={cluster.onPress}>
      <ClusterView count={cluster.pointCount} />
    </Marker>
  );

  return (
    <MapView style={StyleSheet.absoluteFill}>
      <Cluster
        data={markers}
        renderMarker={renderMarker}
        renderCluster={renderCluster}
        radius={50}
      />
    </MapView>
  );
}
```

#### **23. Bluetooth LE with Auto-Reconnect**
**Answer:**
```javascript
import { BleManager } from 'react-native-ble-plx';

const manager = new BleManager();

// Connection with retry
async function connectWithRetry(deviceId, retries = 3) {
  try {
    const device = await manager.connectToDevice(deviceId, {
      autoConnect: true,
      requestMTU: 512,
    });
    await device.discoverAllServicesAndCharacteristics();
    return device;
  } catch (error) {
    if (retries > 0) {
      await new Promise(resolve => setTimeout(resolve, 1000));
      return connectWithRetry(deviceId, retries - 1);
    }
    throw error;
  }
}

// Handle app state changes
AppState.addEventListener('change', (state) => {
  if (state === 'active') {
    // Attempt reconnect
  }
});
```

#### **24. flex: 1 Not Filling Screen**
**Answer:**
Common causes and fixes:
1. Missing `flex: 1` in parent:
```javascript
// Fix:
<View style={{ flex: 1 }}>
  <View style={{ flex: 1 }}> {/* Now fills screen */}
</View>
```
2. Absolute positioning interfering
3. Parent with fixed height/width
4. SafeAreaView not properly configured

#### **25. Optimize Slow FlatList**
**Answer:**
```javascript
<FlatList
  data={data}
  renderItem={({ item }) => <MemoizedItem item={item} />}
  keyExtractor={item => item.id}
  getItemLayout={(data, index) => ({
    length: ITEM_HEIGHT,
    offset: ITEM_HEIGHT * index,
    index
  })}
  initialNumToRender={8}
  maxToRenderPerBatch={10}
  updateCellsBatchingPeriod={50}
  windowSize={21} // ~10 screens ahead
  removeClippedSubviews={true}
/>
```

#### **26. Biometric Auth with Fallback**
**Answer:**
```javascript
import * as LocalAuthentication from 'expo-local-authentication';

async function authenticate() {
  const hasHardware = await LocalAuthentication.hasHardwareAsync();
  const isEnrolled = await LocalAuthentication.isEnrolledAsync();

  if (!hasHardware || !isEnrolled) {
    return fallbackToPinAuth();
  }

  try {
    const result = await LocalAuthentication.authenticateAsync({
      promptMessage: 'Authenticate',
      fallbackLabel: 'Use PIN',
    });
    
    if (result.success) {
      // Proceed
    } else {
      fallbackToPinAuth();
    }
  } catch (error) {
    fallbackToPinAuth();
  }
}

function fallbackToPinAuth() {
  // Show PIN input screen
}
```

#### **27. Debug Android API 22 Crash**
**Answer:**
1. Check for deprecated APIs:
   - Update all native dependencies
   - Use AndroidX libraries
2. Add polyfills:
```javascript
// polyfill.js
if (typeof Symbol.asyncIterator === 'undefined') {
  Symbol.asyncIterator = Symbol.for('Symbol.asyncIterator');
}
```
3. Check NDK versions in build.gradle
4. Test with different build tools versions
5. Enable multidex if method limit exceeded:
```gradle
android {
    defaultConfig {
        multiDexEnabled true
    }
}
```

---

### **State Management**

#### **28. Redux Toolkit vs Zustand vs Context**
**Answer:**
**Comparison Table:**

| Feature         | Redux Toolkit       | Zustand           | Context          |
|-----------------|---------------------|-------------------|------------------|
| Boilerplate     | Moderate            | Minimal           | Minimal          |
| Learning Curve  | Steep               | Easy              | Easy             |
| DevTools        | Excellent           | Basic             | None             |
| Middleware      | Yes                 | Yes               | No               |
| Performance     | Good (with selectors)| Excellent         | Poor             |
| Bundle Size     | Larger              | Smaller           | Smallest         |
| Best For        | Large apps          | Small/Medium apps | Theme/Simple state |

#### **29. Undo/Redo Implementation**
**Answer:**
```javascript
function useUndo(initialState) {
  const [past, setPast] = useState([]);
  const [present, setPresent] = useState(initialState);
  const [future, setFuture] = useState([]);

  const undo = () => {
    if (past.length === 0) return;
    
    const newPast = [...past];
    const newPresent = newPast.pop();

    setPast(newPast);
    setFuture([present, ...future]);
    setPresent(newPresent);
  };

  const redo = () => {
    if (future.length === 0) return;
    
    const newFuture = [...future];
    const newPresent = newFuture.shift();

    setPast([...past, present]);
    setFuture(newFuture);
    setPresent(newPresent);
  };

  const update = (newState) => {
    setPast([...past, present]);
    setPresent(newState);
    setFuture([]);
  };

  return [present, update, undo, redo];
}
```

#### **30. Redux Unnecessary Re-renders**
**Answer:**
**Causes:**
1. Selectors creating new references
2. Connecting entire state tree
3. Not memoizing selectors

**Fixes:**
1. Use `reselect` for memoized selectors:
```javascript
const selectItems = state => state.items;
const selectFilter = state => state.filter;

const selectFilteredItems = createSelector(
  [selectItems, selectFilter],
  (items, filter) => items.filter(item => item.includes(filter))
);
```
2. Use shallow equality checks:
```javascript
const mapState = (state) => ({
  items: selectFilteredItems(state),
});

export default connect(mapState, null, null, {
  areStatesEqual: shallowEqual,
  areOwnPropsEqual: shallowEqual,
})(Component);
```

#### **31. API Caching: RTK Query vs React Query**
**Answer:**
**RTK Query:**
```javascript
// apiSlice.js
const apiSlice = createApi({
  reducerPath: 'api',
  baseQuery: fetchBaseQuery({ baseUrl: '/api' }),
  endpoints: (builder) => ({
    getPosts: builder.query({
      query: () => 'posts',
      providesTags: ['Posts'],
    }),
    addPost: builder.mutation({
      query: (post) => ({
        url: 'posts',
        method: 'POST',
        body: post,
      }),
      invalidatesTags: ['Posts'],
    }),
  }),
});
```

**React Query:**
```javascript
function usePosts() {
  return useQuery('posts', fetchPosts, {
    staleTime: 5 * 60 * 1000, // 5 minutes
    cacheTime: 30 * 60 * 1000, // 30 minutes
  });
}

function useAddPost() {
  const queryClient = useQueryClient();
  return useMutation(addPost, {
    onSuccess: () => {
      queryClient.invalidateQueries('posts');
    },
  });
}
```

---

### **Performance**

#### **32. Reduce Bundle Size by 60%**
**Answer:**
1. **Enable Hermes**:
   ```gradle
   // android/app/build.gradle
   project.ext.react = [
     enableHermes: true
   ]
   ```
2. **Android ProGuard**:
   ```gradle
   def enableProguardInReleaseBuilds = true
   ```
3. **Remove unused assets**:
   ```bash
   npx react-native-asset --remove-unused
   ```
4. **Dynamic imports**:
   ```javascript
   const HeavyComponent = React.lazy(() => import('./HeavyComponent'));
   ```
5. **Analyze bundle**:
   ```bash
   npx react-native-bundle-visualizer
   ```

#### **33. Fix Dropped Animation Frames**
**Answer:**
1. Use native driver:
```javascript
Animated.timing(animValue, {
  toValue: 1,
  duration: 500,
  useNativeDriver: true, // Critical for performance
}).start();
```
2. Simplify complex animations
3. Use Reanimated 2 for gesture-based animations:
```javascript
const offset = useSharedValue(0);
const animatedStyle = useAnimatedStyle(() => ({
  transform: [{ translateX: offset.value }],
}));

const gesture = Gesture.Pan()
  .onUpdate((e) => {
    offset.value = e.translationX;
  });

return (
  <GestureDetector gesture={gesture}>
    <Animated.View style={[styles.box, animatedStyle]} />
  </GestureDetector>
);
```

#### **34. Lazy Load Native Modules**
**Answer:**
```javascript
const [mapsLoaded, setMapsLoaded] = useState(false);

useEffect(() => {
  if (needsMaps) {
    const loadMaps = async () => {
      await require('react-native-maps');
      setMapsLoaded(true);
    };
    loadMaps();
  }
}, [needsMaps]);

return mapsLoaded ? (
  <MapView style={styles.map} />
) : (
  <LoadingView />
);
```

---

### **Testing**

#### **35. Test Navigation-Dependent Components**
**Answer:**
```javascript
import { render, fireEvent } from '@testing-library/react-native';
import { NavigationContainer } from '@react-navigation/native';

test('navigates on button press', () => {
  const mockNavigate = jest.fn();
  
  jest.mock('@react-navigation/native', () => ({
    ...jest.requireActual('@react-navigation/native'),
    useNavigation: () => ({
      navigate: mockNavigate,
    }),
  }));

  const { getByText } = render(
    <NavigationContainer>
      <MyComponent />
    </NavigationContainer>
  );

  fireEvent.press(getByText('Go to Details'));
  expect(mockNavigate).toHaveBeenCalledWith('Details');
});
```

#### **36. Debug Failing Detox Test**
**Answer:**
1. Check timing issues:
```javascript
await device.launchApp({
  newInstance: true,
  permissions: { location: 'always' },
});
await waitFor(element(by.id('home-screen'))).toBeVisible().withTimeout(5000);
```
2. Add synchronization:
```javascript
beforeEach(async () => {
  await device.reloadReactNative();
  await device.setURLBlacklist(['.*analytics.*']);
});
```
3. Debug with:
```bash
detox test --loglevel trace
```

---

### **New Architecture**

#### **37. Migrate to TurboModules**
**Answer:**
1. Create spec (NativeMyModule.js):
```cpp
#include <ReactCommon/TurboModuleUtils.h>

@protocol NativeMyModuleSpec <RCTTurboModule>
- (void)doSomething:(NSString *)param
       callback:(RCTResponseSenderBlock)callback;
@end

@interface RCTMyModuleSpec : RCTTurboModule <NativeMyModuleSpec>
@end
```
2. Implement module (MyModule.mm):
```objectivec
RCT_EXPORT_METHOD(doSomething:(NSString *)param
                  callback:(RCTResponseSenderBlock)callback) {
  callback(@[@"Result"]);
}
```
3. Register module:
```cpp
std::shared_ptr<TurboModule> getTurboModule(
  const std::string &name,
  const std::shared_ptr<CallInvoker> &jsInvoker
) override {
  if (name == "MyModule") {
    return std::make_shared<NativeMyModuleSpecJSI>(jsInvoker);
  }
  return nullptr;
}
```

#### **38. Fabric Reduces RAM Usage**
**Answer:**
Fabric improves memory by:
1. **Simplified Shadow Tree**: More efficient diffing algorithm
2. **C++ Core**: Shared between platforms reduces duplication
3. **Lazy View Allocation**: Views created only when needed
4. **Synchronous Updates**: Avoids intermediate state storage

---

### **Tricky Scenarios**

#### **39. Race Condition in API Calls**
**Answer:**
```javascript
useEffect(() => {
  const controller = new AbortController();
  
  const fetchData = async () => {
    try {
      const response = await fetch('/data', { 
        signal: controller.signal 
      });
      setData(await response.json());
    } catch (error) {
      if (error.name !== 'AbortError') {
        console.error('Fetch failed:', error);
      }
    }
  };

  fetchData();
  return () => controller.abort();
}, [dependency]);
```

#### **40. Real-Time Collaborative Editor**
**Answer:**
```javascript
// Using WebSocket and Operational Transformation
const socket = new WebSocket('wss://collab.example.com');

function handleChange(delta) {
  // Send changes to server
  socket.send(JSON.stringify({
    type: 'EDIT',
    delta,
    documentId,
  }));
}

useEffect(() => {
  socket.onmessage = (event) => {
    const message = JSON.parse(event.data);
    if (message.type === 'UPDATE') {
      // Apply transformed changes
      applyOperation(message.delta);
    }
  };
}, []);
```

#### **41. TextInput Lag on Android**
**Answer:**
```javascript
const [text, setText] = useState('');
const debouncedText = useDebounce(text, 300);

useEffect(() => {
  // Heavy operation with debouncedText
}, [debouncedText]);

return (
  <TextInput
    onChangeText={setText} // Lightweight
    value={text}
  />
);

// Custom debounce hook
function useDebounce(value, delay) {
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {
    const handler = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);

    return () => clearTimeout(handler);
  }, [value, delay]);

  return debouncedValue;
}
```

#### **42. Debug "Unable to resolve module" in Production**
**Answer:**
1. Check Metro config:
```javascript
// metro.config.js
module.exports = {
  resolver: {
    extraNodeModules: {
      // Add any missing aliases
    },
  },
};
```
2. Clear caches:
```bash
watchman watch-del-all
rm -rf node_modules
npm install
npm start -- --reset-cache
```
3. Verify all files are included in the bundle:
```bash
npx react-native bundle --platform android --dev false \
  --entry-file index.js \
  --bundle-output /tmp/bundle.android.js \
  --assets-dest /tmp
```

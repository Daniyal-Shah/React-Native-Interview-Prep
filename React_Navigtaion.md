# **Complete Guide to React Native Navigation (2024)**

## **🔹 Navigation Types in React Native**
React Native offers 3 core navigation patterns:

| Type | Purpose | Key Characteristics |
|------|---------|---------------------|
| **Stack** | Screen-to-screen navigation | Push/pop screens, header management |
| **Bottom Tabs** | Main app sections | Persistent tab bar |
| **Drawer** | Side menu navigation | Slide-in panel |

---

## **🔹 1. Stack Navigation**
### **Installation**
```bash
yarn add @react-navigation/native @react-navigation/stack
yarn add react-native-screens react-native-safe-area-context
```

### **Basic Implementation**
```javascript
import { createStackNavigator } from '@react-navigation/stack';

const Stack = createStackNavigator();

function App() {
  return (
    <NavigationContainer>
      <Stack.Navigator initialRouteName="Home">
        <Stack.Screen 
          name="Home" 
          component={HomeScreen}
          options={{ title: 'Welcome' }}
        />
        <Stack.Screen 
          name="Details" 
          component={DetailsScreen}
          options={({ route }) => ({ title: route.params.itemName })}
        />
      </Stack.Navigator>
    </NavigationContainer>
  );
}
```

### **Key Methods**
| Method | Purpose | Example |
|--------|---------|---------|
| `navigation.navigate()` | Go to screen | `navigate('Details', { id: 1 })` |
| `navigation.push()` | Add new screen | `push('Details')` |
| `navigation.goBack()` | Return to previous | `goBack()` |
| `navigation.popToTop()` | Return to first screen | `popToTop()` |

### **Special Configurations**
```javascript
<Stack.Navigator
  screenOptions={{
    headerStyle: { backgroundColor: '#4CAF50' },
    headerTintColor: '#fff',
    headerTitleStyle: { fontWeight: 'bold' },
    gestureEnabled: true // iOS swipe back
  }}
>
```

---

## **🔹 2. Bottom Tab Navigation**
### **Installation**
```bash
yarn add @react-navigation/bottom-tabs
```

### **Basic Implementation**
```javascript
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';

const Tab = createBottomTabNavigator();

function App() {
  return (
    <NavigationContainer>
      <Tab.Navigator
        screenOptions={({ route }) => ({
          tabBarIcon: ({ color, size }) => {
            let iconName;
            if (route.name === 'Home') iconName = 'home';
            if (route.name === 'Settings') iconName = 'cog';
            return <Ionicons name={iconName} size={size} color={color} />;
          },
        })}
      >
        <Tab.Screen name="Home" component={HomeScreen} />
        <Tab.Screen name="Settings" component={SettingsScreen} />
      </Tab.Navigator>
    </NavigationContainer>
  );
}
```

### **Key Features**
| Feature | Implementation |
|---------|---------------|
| **Custom Icons** | Use `tabBarIcon` in `screenOptions` |
| **Badges** | `tabBarBadge: 3` in screen options |
| **Hide Tab Bar** | `tabBarVisible: false` per screen |

---

## **🔹 3. Drawer Navigation**
### **Installation**
```bash
yarn add @react-navigation/drawer
yarn add react-native-gesture-handler react-native-reanimated
```

### **Basic Implementation**
```javascript
import { createDrawerNavigator } from '@react-navigation/drawer';

const Drawer = createDrawerNavigator();

function App() {
  return (
    <NavigationContainer>
      <Drawer.Navigator
        drawerContent={(props) => <CustomDrawerContent {...props} />}
        drawerStyle={{ width: '80%' }}
      >
        <Drawer.Screen name="Home" component={HomeScreen} />
        <Drawer.Screen name="Profile" component={ProfileScreen} />
      </Drawer.Navigator>
    </NavigationContainer>
  );
}
```

### **Custom Drawer Content**
```javascript
function CustomDrawerContent({ navigation }) {
  return (
    <View>
      <Button 
        title="Go Home" 
        onPress={() => navigation.navigate('Home')} 
      />
      <Button
        title="Close Drawer"
        onPress={() => navigation.closeDrawer()}
      />
    </View>
  );
}
```

---

## **🔹 Advanced Navigation Techniques**
### **1. Navigation Container Events**
```javascript
<NavigationContainer
  onStateChange={(state) => {
    // Track screen changes
    const currentRoute = state.routes[state.index];
    Analytics.trackScreen(currentRoute.name);
  }}
  onReady={() => {
    // Navigation is ready
  }}
>
```

### **2. Deep Linking**
```javascript
// Define links
const linking = {
  prefixes: ['myapp://', 'https://myapp.com'],
  config: {
    screens: {
      Home: 'home',
      Profile: 'profile/:id',
    },
  },
};

// Add to NavigationContainer
<NavigationContainer linking={linking}>
```

### **3. Authentication Flow**
```javascript
function AuthStack() {
  return (
    <Stack.Navigator>
      <Stack.Screen name="Login" component={LoginScreen} />
      <Stack.Screen name="Register" component={RegisterScreen} />
    </Stack.Navigator>
  );
}

function App() {
  const { user } = useAuth();
  
  return (
    <NavigationContainer>
      {user ? <MainStack /> : <AuthStack />}
    </NavigationContainer>
  );
}
```

### **4. Nested Navigators**
```javascript
function HomeTabs() {
  return (
    <Tab.Navigator>
      <Tab.Screen name="Feed" component={FeedScreen} />
      <Tab.Screen name="Messages" component={MessagesScreen} />
    </Tab.Navigator>
  );
}

function App() {
  return (
    <NavigationContainer>
      <Stack.Navigator>
        <Stack.Screen 
          name="Home" 
          component={HomeTabs} 
          options={{ headerShown: false }}
        />
        <Stack.Screen name="Details" component={DetailsScreen} />
      </Stack.Navigator>
    </NavigationContainer>
  );
}
```

---

## **🔹 Performance Optimization**
1. **Lazy Loading Screens**
```javascript
const DetailsScreen = React.lazy(() => import('./DetailsScreen'));

<Stack.Screen 
  name="Details" 
  component={DetailsScreen}
  options={{ lazy: true }}
/>
```

2. **Preventing Unnecessary Renders**
```javascript
import { useNavigationState } from '@react-navigation/native';

function Screen() {
  // Only re-render when this screen is focused
  const isFocused = useIsFocused();
}
```

3. **Memory Management**
```javascript
<Stack.Navigator
  screenOptions={{
    detachPreviousScreen: true // Android memory optimization
  }}
>
```

---

## **🔹 Common Pitfalls & Solutions**
| Issue | Solution |
|-------|----------|
| **Header not showing** | Ensure `headerShown: true` |
| **Tab bar jumps** | Set `lazy: false` on initial tab |
| **TypeScript errors** | Use `NativeStackNavigationProp` types |
| **Android back button** | Use `BackHandler` with `useEffect` |

---

## **🔹 Key Takeaways**
1. **Stack Nav**: Best for screen flows with headers
2. **Bottom Tabs**: Ideal for main app sections
3. **Drawer**: Perfect for secondary navigation
4. **Combine them**: Nest navigators for complex UIs
5. **Optimize**: Use lazy loading and state awareness

# **Complex React Native Navigation Scenarios (2024)**

Let me demonstrate **3 advanced navigation patterns** used in production apps, combining stack, tabs, drawer, and authentication flows with complete code implementations.

---

## **🔹 Scenario 1: Auth Flow + Bottom Tabs + Nested Stack Navigators**
*(Common in banking/fintech apps like Revolut, MoneyLion)*

```javascript
// App.js
import { NavigationContainer } from '@react-navigation/native';
import { createStackNavigator } from '@react-navigation/stack';
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';

// 1. Create navigators
const AuthStack = createStackNavigator();
const MainStack = createStackNavigator();
const Tab = createBottomTabNavigator();

// 2. Tab Navigator Component
function HomeTabs() {
  return (
    <Tab.Navigator
      screenOptions={({ route }) => ({
        tabBarIcon: ({ color, size }) => {
          // Custom icons logic
          let iconName;
          if (route.name === 'Dashboard') iconName = 'home';
          if (route.name === 'Invest') iconName = 'trending-up';
          return <Ionicons name={iconName} size={size} color={color} />;
        },
      })}
    >
      <Tab.Screen name="Dashboard" component={DashboardStack} />
      <Tab.Screen name="Invest" component={InvestStack} />
    </Tab.Navigator>
  );
}

// 3. Nested Stack Navigators
function DashboardStack() {
  return (
    <Stack.Navigator>
      <Stack.Screen name="Main" component={DashboardScreen} />
      <Stack.Screen name="TransactionDetail" component={DetailScreen} />
    </Stack.Navigator>
  );
}

function InvestStack() {
  return (
    <Stack.Navigator>
      <Stack.Screen name="Portfolio" component={PortfolioScreen} />
      <Stack.Screen name="StockDetail" component={StockDetailScreen} />
    </Stack.Navigator>
  );
}

// 4. Main App Component with Auth Flow
export default function App() {
  const { user, isLoading } = useAuth();

  if (isLoading) {
    return <SplashScreen />;
  }

  return (
    <NavigationContainer>
      {user ? (
        <MainStack.Navigator screenOptions={{ headerShown: false }}>
          <MainStack.Screen name="MainApp" component={HomeTabs} />
          <MainStack.Screen 
            name="Settings" 
            component={SettingsScreen}
            options={{ presentation: 'modal' }} // iOS-style modal
          />
        </MainStack.Navigator>
      ) : (
        <AuthStack.Navigator>
          <AuthStack.Screen name="Login" component={LoginScreen} />
          <AuthStack.Screen name="SignUp" component={SignUpScreen} />
        </AuthStack.Navigator>
      )}
    </NavigationContainer>
  );
}
```

**Key Features:**
- Seamless auth flow transition
- Each tab has its own nested stack
- Modal presentation for settings
- Tab bar persists across screens

---

## **🔹 Scenario 2: Drawer + Dynamic Stack Reset**
*(Used in apps like Spotify, Netflix)*

```javascript
// App.js
import { createDrawerNavigator } from '@react-navigation/drawer';

const Drawer = createDrawerNavigator();

function CustomDrawerContent({ navigation }) {
  const dispatch = useDispatch();
  
  return (
    <View style={{ paddingTop: 50 }}>
      <Button
        title="Home"
        onPress={() => {
          navigation.navigate('Main');
          dispatch(resetHomeState()); // Redux action
        }}
      />
      <Button
        title="Profile"
        onPress={() => navigation.navigate('Profile')}
      />
    </View>
  );
}

function MainStack() {
  return (
    <Stack.Navigator>
      <Stack.Screen name="Feed" component={FeedScreen} />
      <Stack.Screen name="Details" component={DetailScreen} />
    </Stack.Navigator>
  );
}

export default function App() {
  return (
    <NavigationContainer>
      <Drawer.Navigator
        drawerContent={(props) => <CustomDrawerContent {...props} />}
        screenOptions={{
          drawerType: 'slide', // Android-style drawer
          overlayColor: 'transparent',
          drawerStyle: { width: '75%' }
        }}
      >
        <Drawer.Screen 
          name="Main" 
          component={MainStack}
          options={{ headerShown: false }}
        />
        <Drawer.Screen name="Profile" component={ProfileScreen} />
      </Drawer.Navigator>
    </NavigationContainer>
  );
}
```

**Advanced Techniques:**
- Custom drawer with Redux integration
- Dynamic stack reset on drawer item press
- Platform-specific drawer styles
- Overlay control for animations

---

## **🔹 Scenario 3: Deep Linking + Universal Links**
*(Used in apps like Twitter, WhatsApp)*

```javascript
// App.js
const linking = {
  prefixes: ['myapp://', 'https://myapp.com'],
  config: {
    screens: {
      Home: {
        path: 'home',
        screens: {
          Feed: 'feed',
          Notifications: 'notifications',
        },
      },
      Profile: 'user/:id',
      Settings: 'settings',
    },
  },
};

function App() {
  return (
    <NavigationContainer
      linking={linking}
      fallback={<LoadingView />} // Show during deep link resolve
      onStateChange={(state) => {
        // Track screen views
        const route = state.routes[state.index];
        if (route.state) {
          // Handle nested navigators
          const nestedRoute = route.state.routes[route.state.index];
          Analytics.trackScreen(nestedRoute.name);
        } else {
          Analytics.trackScreen(route.name);
        }
      }}
    >
      {/* Main navigator structure */}
    </NavigationContainer>
  );
}

// Handling dynamic links
const handleDynamicLink = (link) => {
  if (link.url.includes('user/')) {
    const userId = link.url.split('user/')[1];
    navigation.navigate('Profile', { id: userId });
  }
};

useEffect(() => {
  const unsubscribe = dynamicLinks().onLink(handleDynamicLink);
  return () => unsubscribe();
}, []);
```

**Implementation Notes:**
- Supports both custom scheme (`myapp://`) and HTTPS links
- Handles nested navigators in path config
- Analytics integration for screen tracking
- Fallback UI during link resolution

---

## **🔹 Bonus: Advanced Transition Animations**
```javascript
// Custom transition for Stack Navigator
const forFade = ({ current }) => ({
  cardStyle: {
    opacity: current.progress,
  },
});

<Stack.Navigator
  screenOptions={{
    cardStyleInterpolator: forFade,
    cardOverlayEnabled: true,
    gestureResponseDistance: 200, // iOS swipe back distance
    transitionSpec: {
      open: { animation: 'timing', config: { duration: 500 } },
      close: { animation: 'timing', config: { duration: 500 } },
    }
  }}
>
  <Stack.Screen name="Screen1" component={Screen1} />
  <Stack.Screen 
    name="Screen2" 
    component={Screen2}
    options={{
      presentation: 'transparentModal', // See-through background
      cardOverlay: () => (
        <View style={{ flex: 1, backgroundColor: 'rgba(0,0,0,0.5)' }} />
      ),
    }}
  />
</Stack.Navigator>
```

**Animation Types Available:**
- `fade` (as shown above)
- `slide_from_right` (default iOS)
- `slide_from_bottom` (Android default)
- `flip`
- `none`

---

## **🔹 Key Pro Tips**
1. **Memory Optimization**: 
   ```javascript
   <Stack.Navigator 
     screenOptions={{ detachPreviousScreen: true }} // Android only
   >
   ```

2. **Preventing Multiple Navigation Calls**:
   ```javascript
   const isMounted = useRef(true);
   useEffect(() => {
     return () => { isMounted.current = false; };
   }, []);

   const safeNavigate = (name) => {
     if (isMounted.current) navigation.navigate(name);
   };
   ```

3. **Conditional Tab Rendering**:
   ```javascript
   <Tab.Screen
     name="Premium"
     component={PremiumScreen}
     options={{ tabBarVisible: user.isPremium }}
   />
   ```

4. **Deep Link Verification**:
   ```javascript
   const verifyLink = (link) => {
     if (!allowedDomains.includes(link.host)) return false;
     return true;
   };
   ```

Would you like me to demonstrate any specific interaction patterns like:
- **Shared element transitions** between screens
- **Custom tab bar** with animations
- **Authentication gate** with biometric check
- **Onboarding flow** with horizontal swipe? 🚀

# Deep Dive Into React Native 

### **React Native Architecture: A Deep Dive**

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

By understanding these layers, you can **optimize your app** and troubleshoot performance issues effectively. 🚀

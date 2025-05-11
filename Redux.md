### **1. Redux (Core Library)**
**Purpose**:  
Redux is a predictable state container for JavaScript apps that centralizes application state and logic. It follows a strict unidirectional data flow pattern.

**Key Concepts**:
- **Store**: Single source of truth holding the entire app state
- **Actions**: Plain objects describing what happened (`{type: 'ADD_TODO', payload: 'Buy milk'}`)
- **Reducers**: Pure functions that take current state + action → return new state
- **Middleware**: Extend Redux with async logic (like `redux-thunk`)

**Usage**:
```javascript
// Action
const addTodo = (text) => ({ type: 'ADD_TODO', payload: text });

// Reducer
const todosReducer = (state = [], action) => {
  switch(action.type) {
    case 'ADD_TODO': 
      return [...state, action.payload];
    default:
      return state;
  }
};

// Store
const store = createStore(todosReducer);
store.dispatch(addTodo('Learn Redux'));
```

**Problems it Solves**:
- State management for complex apps
- Predictable state transitions
- Easy debugging with Redux DevTools

---

### **2. Redux Toolkit (RTK)**
**Purpose**:  
The official, opinionated toolset for efficient Redux development that reduces boilerplate code.

**Key Improvements Over Plain Redux**:
1. **`createSlice`**: Auto-generates action creators & action types
2. **Immer Integration**: Write "mutating" logic in reducers (handles immutability internally)
3. **`configureStore`**: Simplified store setup with DevTools middleware pre-configured

**Usage**:
```javascript
import { createSlice, configureStore } from '@reduxjs/toolkit';

const todosSlice = createSlice({
  name: 'todos',
  initialState: [],
  reducers: {
    addTodo: (state, action) => {
      state.push(action.payload); // Thanks to Immer!
    }
  }
});

const store = configureStore({
  reducer: {
    todos: todosSlice.reducer
  }
});

// Auto-generated action creator
const { addTodo } = todosSlice.actions;
store.dispatch(addTodo('Learn RTK'));
```

**When to Use**:  
Always prefer RTK over plain Redux for new projects.

---

### **3. Redux Thunk**
**Purpose**:  
Middleware that allows writing async logic that interacts with the Redux store.

**Key Characteristics**:
- Lets action creators return functions (thunks) instead of objects
- Perfect for basic async operations like API calls

**Usage**:
```javascript
const fetchUser = (userId) => {
  return async (dispatch) => {
    dispatch({ type: 'USER_FETCH_START' });
    try {
      const user = await api.getUser(userId);
      dispatch({ type: 'USER_FETCH_SUCCESS', payload: user });
    } catch (err) {
      dispatch({ type: 'USER_FETCH_FAILURE', payload: err });
    }
  };
};

// Dispatch like a normal action
store.dispatch(fetchUser(123));
```

**When to Use**:  
For simple async workflows before adopting RTK Query.

---

### **4. RTK Query**
**Purpose**:  
A specialized data-fetching and caching solution included in Redux Toolkit.

**Key Features**:
- Automatic caching & deduplication
- Built-in loading states
- Auto-generated React hooks
- Fine-grained cache invalidation

**Usage**:
```javascript
import { createApi, fetchBaseQuery } from '@reduxjs/toolkit/query/react';

const api = createApi({
  reducerPath: 'api',
  baseQuery: fetchBaseQuery({ baseUrl: '/api' }),
  endpoints: (builder) => ({
    getTodos: builder.query({
      query: () => 'todos',
    }),
    addTodo: builder.mutation({
      query: (text) => ({
        url: 'todos',
        method: 'POST',
        body: { text }
      }),
    }),
  }),
});

// Auto-generated hooks
const { useGetTodosQuery, useAddTodoMutation } = api;
```

**When to Use**:  
For all data fetching needs in modern Redux apps (replaces thunks/sagas for API calls).

---

### **Comparison Table**

| Feature               | Redux | Redux Toolkit | Redux Thunk | RTK Query |
|-----------------------|-------|--------------|------------|-----------|
| Boilerplate           | High  | Low          | Medium     | Minimal   |
| Async Handling        | No    | No           | Yes        | Yes       |
| Caching               | No    | No           | No         | Yes       |
| Data Fetching         | Manual| Manual       | Via Thunks | Built-in  |
| Immutability Handling | Manual| Auto (Immer) | Manual     | Auto      |
| Learning Curve        | Steep | Moderate     | Moderate   | Easy      |

---

### **Evolution of Redux**
1. **Plain Redux (2015)**: Verbose but predictable
2. **+ Thunk/Saga (2016)**: Added async capabilities
3. **Redux Toolkit (2019)**: Reduced boilerplate
4. **RTK Query (2021)**: Complete data fetching solution

**Modern Recommendation**:
- Use **Redux Toolkit** as your core state management
- Use **RTK Query** for all data fetching
- Only use **thunks** for complex async logic not covered by RTK Query
- Avoid writing plain Redux code in new projects

This combination provides the best developer experience while maintaining Redux's core benefits of predictability and centralized state management.

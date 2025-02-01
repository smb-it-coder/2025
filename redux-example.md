I’ll provide a **step-by-step Redux example using the Indian stock market**. This will cover **all key Redux concepts**, including:  

✅ **State Management** – Normalization, persistence  
✅ **Redux Thunk vs Redux Saga** – API handling, middleware comparison  
✅ **Optimistic Updates** – Updating stock prices before API confirmation  
✅ **WebSockets** – Real-time stock price updates  
✅ **Performance Optimization** – Large-scale application handling  

---

## **Project: Indian Stock Market Dashboard**  

### **Tech Stack**  
- **React + Redux Toolkit** (for structured state management)  
- **Redux Thunk & Redux Saga** (to compare both middleware)  
- **WebSockets** (for live market updates)  
- **Redux Persist** (to store watchlist across browser refreshes)  

---

### **Step 1: Project Setup**  
1. **Create React App**  
   ```bash
   npx create-react-app stock-market-dashboard --template redux
   cd stock-market-dashboard
   npm install @reduxjs/toolkit react-redux redux-thunk redux-saga redux-persist reselect normalizr
   ```
2. **Project Structure**  
```
/src
  /store
    /features
      /stocks
        stockSlice.js
        stockSelectors.js
        stockSaga.js
      /watchlist
        watchlistSlice.js
    rootReducer.js
    store.js
  /components
    StockList.js
    StockDetails.js
    Watchlist.js
  App.js
```

---

### **Step 2: Define Redux Store**  
📌 **`store/rootReducer.js`** – Combines all reducers  
```javascript
import { combineReducers } from "@reduxjs/toolkit";
import stockReducer from "./features/stocks/stockSlice";
import watchlistReducer from "./features/watchlist/watchlistSlice";

const rootReducer = combineReducers({
  stocks: stockReducer,
  watchlist: watchlistReducer
});

export default rootReducer;
```

📌 **`store/store.js`** – Configure the store  
```javascript
import { configureStore } from "@reduxjs/toolkit";
import rootReducer from "./rootReducer";
import createSagaMiddleware from "redux-saga";
import stockSaga from "./features/stocks/stockSaga";
import { persistStore, persistReducer } from "redux-persist";
import storage from "redux-persist/lib/storage";

const sagaMiddleware = createSagaMiddleware();

const persistConfig = { key: "root", storage };
const persistedReducer = persistReducer(persistConfig, rootReducer);

const store = configureStore({
  reducer: persistedReducer,
  middleware: (getDefaultMiddleware) =>
    getDefaultMiddleware({ serializableCheck: false }).concat(sagaMiddleware)
});

sagaMiddleware.run(stockSaga);

export const persistor = persistStore(store);
export default store;
```

---

### **Step 3: Define Stock Reducer with Normalization**  
📌 **`store/features/stocks/stockSlice.js`**  
```javascript
import { createSlice, createAsyncThunk } from "@reduxjs/toolkit";
import { normalize, schema } from "normalizr";

// Define normalization schema
const stockSchema = new schema.Entity("stocks");
const stockListSchema = new schema.Array(stockSchema);

export const fetchStocks = createAsyncThunk("stocks/fetch", async () => {
  const response = await fetch("https://api.example.com/stocks");
  const data = await response.json();
  return normalize(data, stockListSchema);
});

const stockSlice = createSlice({
  name: "stocks",
  initialState: { entities: {}, ids: [], loading: false },
  reducers: {},
  extraReducers: (builder) => {
    builder.addCase(fetchStocks.pending, (state) => {
      state.loading = true;
    });
    builder.addCase(fetchStocks.fulfilled, (state, action) => {
      state.loading = false;
      state.entities = action.payload.entities.stocks;
      state.ids = action.payload.result;
    });
  }
});

export default stockSlice.reducer;
```
✅ **Why Normalization?**  
- Prevents duplication of stock data  
- Improves performance for large-scale applications  

---

### **Step 4: Define Selectors for Performance Optimization**  
📌 **`store/features/stocks/stockSelectors.js`**  
```javascript
import { createSelector } from "reselect";

const selectStockEntities = (state) => state.stocks.entities;
const selectStockIds = (state) => state.stocks.ids;

export const selectAllStocks = createSelector(
  [selectStockEntities, selectStockIds],
  (entities, ids) => ids.map((id) => entities[id])
);
```
✅ **Why Use Selectors?**  
- **Memoization** prevents unnecessary re-renders  
- **Efficient state access**  

---

### **Step 5: Define WebSocket Middleware for Real-time Updates**  
📌 **`store/features/stocks/stockSaga.js`**  
```javascript
import { takeEvery, put, call } from "redux-saga/effects";

function connectWebSocket() {
  return new Promise((resolve) => {
    const socket = new WebSocket("wss://example.com/stocks");
    socket.onmessage = (event) => resolve(JSON.parse(event.data));
  });
}

function* watchStockUpdates() {
  while (true) {
    const stockData = yield call(connectWebSocket);
    yield put({ type: "stocks/updatePrices", payload: stockData });
  }
}

export default function* stockSaga() {
  yield takeEvery("stocks/fetch", watchStockUpdates);
}
```
✅ **Why Use WebSockets?**  
- **Live updates** for stock prices  
- **Efficient streaming data handling**  

---

### **Step 6: Implement Optimistic Updates**  
📌 **Modify `stockSlice.js`**  
```javascript
const stockSlice = createSlice({
  name: "stocks",
  initialState: { entities: {}, ids: [], loading: false },
  reducers: {
    updateStockPriceOptimistic: (state, action) => {
      state.entities[action.payload.id].price = action.payload.price;
    },
    revertStockPrice: (state, action) => {
      state.entities[action.payload.id].price = action.payload.oldPrice;
    }
  }
});
```

📌 **Handling API Response with Optimistic UI**  
```javascript
const optimisticUpdateStockPrice = (stockId, newPrice) => (dispatch, getState) => {
  const oldPrice = getState().stocks.entities[stockId].price;
  
  dispatch(updateStockPriceOptimistic({ id: stockId, price: newPrice }));

  fetch("/api/updateStock", {
    method: "POST",
    body: JSON.stringify({ stockId, newPrice })
  }).catch(() => {
    dispatch(revertStockPrice({ id: stockId, oldPrice }));
  });
};
```
✅ **Why Optimistic Updates?**  
- **Instant UI updates** before API response  
- **Better user experience**  

---

### **Step 7: Connect Redux to React Components**  
📌 **`components/StockList.js`**  
```javascript
import { useSelector, useDispatch } from "react-redux";
import { selectAllStocks } from "../store/features/stocks/stockSelectors";
import { fetchStocks } from "../store/features/stocks/stockSlice";

const StockList = () => {
  const stocks = useSelector(selectAllStocks);
  const dispatch = useDispatch();

  return (
    <div>
      <button onClick={() => dispatch(fetchStocks())}>Fetch Stocks</button>
      <ul>
        {stocks.map(stock => (
          <li key={stock.id}>{stock.name}: ₹{stock.price}</li>
        ))}
      </ul>
    </div>
  );
};
export default StockList;
```

---

## **Conclusion**
🎯 **This example covered:**  
✅ State normalization  
✅ WebSocket real-time updates  
✅ Redux Thunk vs Redux Saga  
✅ Performance optimization  
✅ Optimistic updates  
✅ State persistence  

Would you like **pagination, sorting, or search functionality** added? 🚀

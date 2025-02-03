To plot stock data every 1 minute on a **candlestick chart** using **Node.js**, follow these steps:

---

## **Step 1: Set Up Your Project**
You'll need a Node.js backend to fetch stock data and serve it to a frontend that will render the candlestick chart.

### **1. Initialize a Node.js Project**
Run the following in your terminal:

```sh
mkdir stock-candlestick-chart
cd stock-candlestick-chart
npm init -y
```

### **2. Install Required Dependencies**
```sh
npm install express axios socket.io
```

- **express** → Backend framework to serve stock data.
- **axios** → Fetch stock data from an API.
- **socket.io** → Real-time updates for the chart.

---

## **Step 2: Get Stock Data (Using an API)**
You'll need a stock market API like:
- [Alpha Vantage](https://www.alphavantage.co/)
- [Yahoo Finance](https://www.npmjs.com/package/yahoo-finance2)
- [Polygon.io](https://polygon.io/)

For this guide, we’ll use **Alpha Vantage** (free API key required).

### **1. Get an API Key**
Sign up at [Alpha Vantage](https://www.alphavantage.co/support/#api-key) and get your free API key.

### **2. Create a Backend (`server.js`)**
Create a file **`server.js`** to fetch and serve stock data:

```javascript
const express = require("express");
const axios = require("axios");
const http = require("http");
const socketIo = require("socket.io");

const app = express();
const server = http.createServer(app);
const io = socketIo(server);

const API_KEY = "YOUR_ALPHA_VANTAGE_API_KEY"; // Replace with your API key
const SYMBOL = "AAPL"; // Change to your preferred stock symbol
const INTERVAL = "1min"; // Fetch data every 1 minute

// Function to fetch stock data
const fetchStockData = async () => {
  try {
    const url = `https://www.alphavantage.co/query?function=TIME_SERIES_INTRADAY&symbol=${SYMBOL}&interval=${INTERVAL}&apikey=${API_KEY}`;
    const response = await axios.get(url);
    const timeSeries = response.data["Time Series (1min)"];

    if (!timeSeries) {
      console.error("Error fetching data:", response.data);
      return null;
    }

    // Convert API data into candlestick format
    const data = Object.keys(timeSeries).map((time) => ({
      time, 
      open: parseFloat(timeSeries[time]["1. open"]),
      high: parseFloat(timeSeries[time]["2. high"]),
      low: parseFloat(timeSeries[time]["3. low"]),
      close: parseFloat(timeSeries[time]["4. close"]),
    }));

    return data;
  } catch (error) {
    console.error("Error fetching stock data:", error);
    return null;
  }
};

// Emit stock data every minute
setInterval(async () => {
  const stockData = await fetchStockData();
  if (stockData) io.emit("stockData", stockData);
}, 60000); // Fetch every 1 minute

io.on("connection", (socket) => {
  console.log("Client connected");
  socket.on("disconnect", () => console.log("Client disconnected"));
});

server.listen(3000, () => console.log("Server running on port 3000"));
```

---

## **Step 3: Create Frontend (Candlestick Chart)**
### **1. Install Charting Library**
We’ll use **Lightweight Charts by TradingView**.

```sh
npm install lightweight-charts
```

### **2. Create `index.html`**
Inside your project, create a **public** folder and add an `index.html` file.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Live Stock Chart</title>
    <script src="/socket.io/socket.io.js"></script>
    <script src="https://unpkg.com/lightweight-charts@3.4.0/dist/lightweight-charts.standalone.production.js"></script>
</head>
<body>
    <div id="chart" style="width: 800px; height: 400px;"></div>

    <script>
        const socket = io();
        const chart = LightweightCharts.createChart(document.getElementById("chart"), { width: 800, height: 400 });
        const candleSeries = chart.addCandlestickSeries();

        socket.on("stockData", (data) => {
            const chartData = data.map(d => ({
                time: new Date(d.time).getTime() / 1000, // Convert to UNIX timestamp
                open: d.open,
                high: d.high,
                low: d.low,
                close: d.close
            }));
            candleSeries.setData(chartData);
        });
    </script>
</body>
</html>
```

---

## **Step 4: Serve the Frontend**
Modify `server.js` to serve the `public` folder.

Add this line before `server.listen(3000)`:
```javascript
app.use(express.static("public"));
```

---

## **Step 5: Run Your App**
Start the server:
```sh
node server.js
```

Open **`http://localhost:3000`** in your browser, and you'll see a **live updating candlestick chart** every 1 minute!

---

## **Enhancements**
- **Use WebSockets for real-time updates** instead of polling every 1 minute.
- **Add multiple stock symbols**.
- **Store historical data** in a database (MongoDB/PostgreSQL).
- **Use Docker** for deployment.

---

Would you like help adding WebSocket-based real-time updates or integrating a different stock API? 🚀

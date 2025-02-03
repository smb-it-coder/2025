**1. Core Node.js Concepts**

### **Event Loop & Asynchronous Programming**
#### *Explain the Node.js event loop and its phases.*
Node.js uses an event-driven, non-blocking I/O model that allows handling many concurrent operations efficiently. The **event loop** is the mechanism that enables this.

**Phases of the Event Loop:**
1. **Timers Phase**: Executes `setTimeout()` and `setInterval()` callbacks.
2. **I/O Callbacks Phase**: Executes I/O-related callbacks (excluding timers and close events).
3. **Idle, Prepare Phase**: Internal use for optimizations.
4. **Poll Phase**: Retrieves new I/O events and executes their callbacks.
5. **Check Phase**: Executes `setImmediate()` callbacks.
6. **Close Callbacks Phase**: Handles closed connections and cleanup.

```js
setTimeout(() => console.log("Timeout"), 0);
setImmediate(() => console.log("Immediate"));
console.log("Synchronous");
```
*Output:*  
```
Synchronous  
Immediate  
Timeout  
```

#### *Difference between `setTimeout`, `setImmediate`, and `process.nextTick`*
- `setTimeout(callback, 0)`: Executes in the **timers phase**.
- `setImmediate(callback)`: Executes in the **check phase**.
- `process.nextTick(callback)`: Executes **immediately after the current operation**, before the next event loop cycle.

```js
setTimeout(() => console.log("Timeout"), 0);
setImmediate(() => console.log("Immediate"));
process.nextTick(() => console.log("Next Tick"));
```
*Output:*  
```
Next Tick  
Immediate  
Timeout  
```

### **Concurrency & Multi-threading**
#### *How does Node.js handle multi-threading?*
Node.js is **single-threaded**, but it uses:
- **Worker threads** (`worker_threads` module) for CPU-intensive tasks.
- **Child processes** for process-level parallelism.

Example using **worker threads**:
```js
const { Worker, isMainThread, parentPort } = require('worker_threads');
if (isMainThread) {
    const worker = new Worker(__filename);
    worker.on('message', msg => console.log(`Received: ${msg}`));
} else {
    parentPort.postMessage("Hello from worker thread");
}
```

### **Buffers & Streams**
#### *Explain streams and their types.*
Streams efficiently handle large data chunks. Types:
1. **Readable** - Read data (`fs.createReadStream`)
2. **Writable** - Write data (`fs.createWriteStream`)
3. **Duplex** - Both read and write (`net.Socket`)
4. **Transform** - Modifies data (`zlib.createGzip()`)

```js
const fs = require('fs');
const readStream = fs.createReadStream('file.txt');
readStream.on('data', chunk => console.log(`Received ${chunk.length} bytes`));
```

### **Error Handling**
#### *How do you handle uncaught exceptions?*
```js
process.on("uncaughtException", (err) => {
  console.error("Unhandled Error:", err);
});
```

---

**2. Node.js Performance Optimization**

### **Profiling & Debugging**
#### *How do you analyze memory leaks?*
1. Use `heapdump` to capture memory snapshots.
2. Use Chrome DevTools to analyze heap snapshots.

```sh
node --inspect app.js
```

### **Optimizing API Performance**
#### *How do you handle high concurrency?*
- **Use Caching** (Redis, Memcached)
- **Optimize Database Queries**
- **Use Load Balancing** (Nginx, PM2 clustering)

---

**3. Microservices Architecture**

### **Event-Driven Architecture with Kafka**
#### *How does Kafka fit into Node.js microservices?*
Kafka provides **asynchronous communication** between microservices.

```js
const { Kafka } = require("kafkajs");
const kafka = new Kafka({ clientId: "my-app", brokers: ["localhost:9092"] });
```

---

**4. Security Best Practices**

### **Authentication & Authorization**
#### *JWT-based authentication*
```js
const jwt = require("jsonwebtoken");
const token = jwt.sign({ userId: 123 }, "secret", { expiresIn: "1h" });
```

---

**5. System Design & High Scalability**

### **Real-time System Design**
#### *How do you design a real-time stock trading system?*
- Use **WebSockets** for low-latency updates.
- Use **Redis Pub/Sub** for broadcasting data.
- Use **Kafka** for event streaming.

```js
const WebSocket = require("ws");
const wss = new WebSocket.Server({ port: 8080 });
wss.on("connection", ws => {
  ws.send("Welcome to real-time trading system");
});
```

---

**6. DevOps & Cloud Deployment**

### **Containerization with Docker**
#### *How do you deploy Node.js microservices using Docker?*
Dockerfile:
```dockerfile
FROM node:14
WORKDIR /app
COPY . .
RUN npm install
CMD ["node", "server.js"]
```

---

**7. Frontend Integration**

### **GraphQL with Node.js**
```js
const { ApolloServer, gql } = require("apollo-server");
const typeDefs = gql`
  type Query {
    hello: String
  }
`;
const resolvers = {
  Query: { hello: () => "Hello World" }
};
const server = new ApolloServer({ typeDefs, resolvers });
server.listen(4000, () => console.log("GraphQL API running"));
```

---

This document provides an in-depth, step-by-step explanation of key Node.js interview topics with practical examples.


## **Middleware vs API Routes in Next.js** 🚀  

### **What is Middleware in Next.js?**
Middleware in Next.js is a function that **executes before a request reaches a page or API route**. It allows you to modify requests, perform authentication, or rewrite URLs at the **Edge layer**, improving performance.

### **How Middleware Works**
- Runs **before** a request is processed.
- Can **modify requests and responses** (e.g., redirect, rewrite, or block requests).
- Runs **on the Edge**, making it faster than API routes.
- Applied globally or to specific routes.

### **Example: Basic Middleware (Logging Requests)**
Middleware must be placed in `middleware.js` or `middleware.ts` at the project root.

```javascript
import { NextResponse } from "next/server";

export function middleware(req) {
  console.log("Middleware executed for:", req.nextUrl.pathname);
  return NextResponse.next(); // Continue to the requested page
}
```
---

## **What are API Routes in Next.js?**
API routes in Next.js allow you to **create backend endpoints** inside the `pages/api/` directory. These routes work like Express.js endpoints.

### **How API Routes Work**
- Run **only when explicitly called** (e.g., `fetch('/api/data')`).
- Used for **handling backend logic**, like database queries, authentication, etc.
- Run **on the server** (not at the Edge like Middleware).

### **Example: API Route (`pages/api/hello.js`)**
```javascript
export default function handler(req, res) {
  res.status(200).json({ message: "Hello from API route!" });
}
```
---

## **Key Differences: Middleware vs API Routes**
| Feature            | Middleware | API Routes |
|--------------------|-----------|-----------|
| **Execution Time** | Before the request reaches the page | When an API endpoint is explicitly called |
| **Use Cases** | Authentication, redirects, URL rewrites | Database queries, form submissions, REST API |
| **Performance** | Runs at the Edge (faster) | Runs on the server |
| **Can Modify Request?** | ✅ Yes | ✅ Yes |
| **Can Modify Response?** | ✅ Yes | ✅ Yes |
| **File Location** | `middleware.js` (root) | `pages/api/` |
| **Triggered by** | Every request | Explicit API call |

---

### **When to Use Middleware vs API Routes**
✅ **Use Middleware when you need:**
- **Authentication & Authorization** (e.g., redirect unauthorized users).  
- **Rewriting URLs** (e.g., `/old-path → /new-path`).  
- **Logging & Analytics** before requests hit the server.  

✅ **Use API Routes when you need:**
- **Database queries** (e.g., fetching user data from MongoDB).  
- **Processing form submissions**.  
- **Creating RESTful APIs** for frontend communication.  

---

### **🚀 Summary**
- Middleware **runs before the page loads** and is great for authentication, redirects, and rewriting URLs.  
- API Routes **act as backend endpoints** and are used for handling data processing.  

Would you like an example where Middleware and API routes work together? 😊

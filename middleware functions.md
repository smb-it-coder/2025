## **Middleware in Next.js** 🚀  

### **What Are Middleware Functions in Next.js?**
Middleware in Next.js **executes before a request is processed by a route**. It allows you to modify the request and response objects, perform authentication, redirects, or rewrite URLs **before rendering a page or API route**.

---

### **How Middleware Works in Next.js**
1. Middleware runs **before** a request reaches a page or API route.
2. It can modify the **request (req)** and **response (res)** objects.
3. It can **redirect, rewrite, or respond with custom responses**.
4. Middleware runs **on the Edge (Edge Functions)**, improving performance by executing close to the user.

---

### **🔹 Example: Basic Middleware**
Middleware files must be placed at the root of your project as `middleware.js` or `middleware.ts`.

```javascript
import { NextResponse } from 'next/server';

export function middleware(req) {
  console.log("Middleware executed for:", req.nextUrl.pathname);

  return NextResponse.next(); // Continue to the requested page
}
```
- This runs on every request.
- `NextResponse.next()` allows the request to proceed as usual.

---

### **🔹 Example: Redirect Based on User Role**
Let's say you want to **redirect unauthenticated users** from `/dashboard` to `/login`:

```javascript
import { NextResponse } from 'next/server';

export function middleware(req) {
  const token = req.cookies.get("token"); // Get authentication token

  if (!token && req.nextUrl.pathname === "/dashboard") {
    return NextResponse.redirect(new URL("/login", req.url));
  }

  return NextResponse.next();
}
```
- If the user is not logged in, they are redirected to `/login`.
- Otherwise, they proceed to `/dashboard`.

---

### **🔹 Example: Rewriting URLs Without Redirecting**
Use Middleware to **serve a different page while keeping the URL the same**.

```javascript
export function middleware(req) {
  if (req.nextUrl.pathname === "/old-route") {
    return NextResponse.rewrite(new URL("/new-route", req.url));
  }
  return NextResponse.next();
}
```
- A request to `/old-route` will load `/new-route` **without changing the URL in the browser**.

---

### **Where Does Middleware Run?**
- Middleware **runs at the Edge**, meaning it's faster and doesn't require a full server round-trip.
- It **only works in production** (`next build && next start`).
- It applies to **all requests**, including pages, static files, and API routes.

---

### **Middleware vs API Routes**
| Feature | Middleware | API Route |
|---------|-----------|----------|
| **Runs Before Page Loads?** | ✅ Yes | ❌ No |
| **Modifies Request/Response?** | ✅ Yes | ✅ Yes |
| **Used for Authentication?** | ✅ Yes | ✅ Yes |
| **Can Rewrite URLs?** | ✅ Yes | ❌ No |
| **Runs on Edge?** | ✅ Yes | ❌ No |

---

### **Summary**
✅ **Middleware is useful for:**
- Authentication & Authorization 🚀  
- URL Rewriting & Redirects 🔄  
- Request Logging & Analytics 📊  
- Rate Limiting & Security 🔒  

Would you like a deeper dive into Edge Middleware or performance optimizations? 😊

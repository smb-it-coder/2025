# Explain the file-based routing system in Next.js.

### **File-Based Routing in Next.js**
Next.js provides a **file-based routing system**, which means that the structure of the `pages/` directory determines the routes of your application. Unlike React, where you manually define routes using `react-router`, Next.js automatically maps files inside the `pages/` directory to URL paths.

---

### **Basic Routing Example**
Each `.js`, `.jsx`, or `.ts` file inside the `pages/` directory automatically becomes a route.

#### **Project Structure:**
```
my-next-app/
├── pages/
│   ├── index.js         →  "/" (Homepage)
│   ├── about.js         →  "/about"
│   ├── contact.js       →  "/contact"
│   ├── blog/
│   │   ├── index.js     →  "/blog"
│   │   ├── post.js      →  "/blog/post"
```

#### **Example: `pages/index.js`**
```jsx
export default function Home() {
  return <h1>Welcome to the Homepage</h1>;
}
```
🔹 Accessing `/` will render **Home Page**.

#### **Example: `pages/about.js`**
```jsx
export default function About() {
  return <h1>About Us</h1>;
}
```
🔹 Accessing `/about` will render **About Page**.

---

### **Dynamic Routing (`[param].js`)**
You can create **dynamic routes** using square brackets `[param]`.  
For example, if you want to display different blog posts based on a unique `id`:

#### **Project Structure:**
```
pages/
├── blog/
│   ├── [id].js    → "/blog/:id"
```

#### **Example: `pages/blog/[id].js`**
```jsx
import { useRouter } from 'next/router';

export default function BlogPost() {
  const router = useRouter();
  const { id } = router.query; // Get the dynamic route parameter

  return <h1>Blog Post ID: {id}</h1>;
}
```
🔹 Visiting `/blog/123` will render **"Blog Post ID: 123"**.

---

### **Nested Routes**
You can create **nested directories** to reflect complex URLs.

#### **Project Structure:**
```
pages/
├── dashboard/
│   ├── index.js        → "/dashboard"
│   ├── settings.js     → "/dashboard/settings"
│   ├── profile.js      → "/dashboard/profile"
```

#### **Example: `pages/dashboard/profile.js`**
```jsx
export default function Profile() {
  return <h1>Dashboard Profile Page</h1>;
}
```
🔹 Visiting `/dashboard/profile` will render **"Dashboard Profile Page"**.

---

### **Catch-All Routes (`[...slug].js`)**
To handle multiple segments in a URL dynamically, use **catch-all routes**.

#### **Project Structure:**
```
pages/
├── docs/
│   ├── [...slug].js    → "/docs/*"
```

#### **Example: `pages/docs/[...slug].js`**
```jsx
import { useRouter } from 'next/router';

export default function Docs() {
  const router = useRouter();
  const { slug } = router.query;

  return <h1>Viewing Docs: {slug?.join('/')}</h1>;
}
```
🔹 Visiting `/docs/react/hooks` will render **"Viewing Docs: react/hooks"**.

---

### **API Routes (`pages/api/`)**
Next.js also allows you to define API endpoints inside the `pages/api/` directory.

#### **Example: `pages/api/hello.js`**
```jsx
export default function handler(req, res) {
  res.status(200).json({ message: "Hello from Next.js API!" });
}
```
🔹 Accessing `/api/hello` returns `{"message": "Hello from Next.js API!"}`.

---

### **Summary**
| Feature               | Example Path             | File Location |
|----------------------|--------------------|----------------|
| **Static Route**     | `/about`          | `pages/about.js` |
| **Dynamic Route**    | `/blog/:id`       | `pages/blog/[id].js` |
| **Nested Route**     | `/dashboard/profile` | `pages/dashboard/profile.js` |
| **Catch-All Route**  | `/docs/react/hooks` | `pages/docs/[...slug].js` |
| **API Route**        | `/api/hello`      | `pages/api/hello.js` |

Would you like more examples on advanced routing concepts like middleware, API routes, or dynamic rendering? 🚀

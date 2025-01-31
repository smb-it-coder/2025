### 🚀 `getInitialProps` in Next.js  

`getInitialProps` is a **data fetching method** used in Next.js to **fetch data on both server-side (SSR) and client-side (CSR)** during page rendering. It runs before a page is rendered and can be used in **pages and custom `_app.js`**.

---

## 🔹 How `getInitialProps` Works  

```tsx
const Page = async ({ data }) => {
  return <div>{data}</div>;
};

Page.getInitialProps = async () => {
  const res = await fetch('https://api.example.com/data');
  const data = await res.json();
  return { data };
};

export default Page;
```
### 🔍 What Happens Here?
- **On server-side**: It fetches data and sends it as props to the page before rendering.  
- **On client-side (navigations via `<Link>` or `Router.push`)**: It runs again, making a client-side request.

---

## ❌ Why `getInitialProps` is **Not Recommended Anymore**  

1. **Affects Performance**  
   - Runs **on every request** (even for static pages), preventing caching and increasing server load.

2. **Blocks Static Optimization**  
   - Since `getInitialProps` executes **on both client and server**, Next.js **cannot pre-render pages as static HTML** (SSG).

3. **Better Alternatives Exist**  
   - `getStaticProps` (SSG) → **For static pages**  
   - `getServerSideProps` (SSR) → **For server-rendered pages**  

---

## ✅ **Recommended Alternatives**

### 🔹 `getStaticProps` (SSG - Recommended for Static Pages)
```tsx
export async function getStaticProps() {
  const res = await fetch('https://api.example.com/data');
  const data = await res.json();

  return { props: { data }, revalidate: 10 }; // ISR enabled
}
```
**Why?**  
- **Runs at build time** → Super fast performance!  
- **Supports Incremental Static Regeneration (ISR)**  

---

### 🔹 `getServerSideProps` (SSR - Recommended for Dynamic Pages)
```tsx
export async function getServerSideProps() {
  const res = await fetch('https://api.example.com/data');
  const data = await res.json();

  return { props: { data } };
}
```
**Why?**  
- **Runs only on the server for each request** → No unnecessary client-side execution  
- **Good for highly dynamic content**  

---

### 🔹 `useEffect` for Client-Side Fetching (CSR)
If **SEO isn’t a concern**, fetch data on the client side using `useEffect`:
```tsx
import { useEffect, useState } from 'react';

const Page = () => {
  const [data, setData] = useState(null);

  useEffect(() => {
    fetch('/api/data')
      .then((res) => res.json())
      .then((data) => setData(data));
  }, []);

  return <div>{data}</div>;
};

export default Page;
```

---

## 🚀 **Final Verdict**
| Feature | `getInitialProps` | `getStaticProps` | `getServerSideProps` | `useEffect` |
|---------|----------------|-----------------|-----------------|-----------|
| **Runs on Server?** | ✅ | ❌ (Build Time) | ✅ | ❌ |
| **Runs on Client?** | ✅ | ❌ | ❌ | ✅ |
| **Supports Static Generation?** | ❌ | ✅ | ❌ | ❌ |
| **Best for SEO?** | ✅ | ✅ | ✅ | ❌ |
| **Best for Dynamic Data?** | ✅ (Not optimal) | ❌ | ✅ | ✅ |

📌 **Use `getStaticProps` for performance, `getServerSideProps` for dynamic content, and `useEffect` for client-side fetching.**  
⚠️ `getInitialProps` is **deprecated in App Router (`app/` directory)** and should be avoided.

Would you like a deeper dive into migrating old code using `getInitialProps`? 🚀

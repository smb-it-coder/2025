In **Next.js**, there are four primary rendering methods, each serving different use cases based on performance, SEO, and data-fetching needs:

### 1. **Static Site Generation (SSG)**
   - Pages are pre-rendered **at build time**.
   - HTML is generated ahead of time and served statically.
   - Best for **SEO** and performance.
   - Example:
     ```tsx
     export async function getStaticProps() {
       const data = await fetchData();
       return { props: { data } };
     }
     ```
   - **Use case:** Blogs, documentation, marketing pages.

---

### 2. **Server-Side Rendering (SSR)**
   - Pages are generated **on each request**.
   - HTML is built dynamically on the server.
   - Useful when data needs to be fresh **on every request**.
   - Example:
     ```tsx
     export async function getServerSideProps() {
       const data = await fetchData();
       return { props: { data } };
     }
     ```
   - **Use case:** Personalized dashboards, frequently updated content.

---

### 3. **Client-Side Rendering (CSR)**
   - Page loads with minimal HTML, and data is fetched **on the client-side** after initial render.
   - Uses React's `useEffect` to fetch data.
   - **SEO is not great** compared to SSR/SSG.
   - Example:
     ```tsx
     import { useEffect, useState } from "react";

     function Page() {
       const [data, setData] = useState(null);

       useEffect(() => {
         fetch("/api/data")
           .then((res) => res.json())
           .then((data) => setData(data));
       }, []);

       return <div>{data ? data.title : "Loading..."}</div>;
     }
     ```
   - **Use case:** Real-time data (e.g., chat apps), dashboards with frequent updates.

---

### 4. **Incremental Static Regeneration (ISR)**
   - A hybrid of **SSG and SSR**.
   - Allows pages to be updated **without a full rebuild**.
   - Regenerates static pages **after a certain interval**.
   - Example:
     ```tsx
     export async function getStaticProps() {
       const data = await fetchData();
       return { props: { data }, revalidate: 10 }; // Regenerates page every 10s
     }
     ```
   - **Use case:** News sites, e-commerce product pages.

---

### **Comparison Table**
| Method | When is HTML Generated? | SEO | Performance | Use Case |
|--------|------------------------|----|-------------|----------|
| **SSG** | At build time | ✅ Best | ⚡ Fastest | Blogs, Docs |
| **SSR** | On each request | ✅ Good | 🐢 Slower | User-specific pages |
| **CSR** | On client-side | ❌ Poor | 🚀 Fast UI updates | Real-time apps |
| **ISR** | On demand (after revalidate) | ✅ Good | 🔄 Dynamic SSG | News, Products |

Would you like examples based on a specific use case? 🚀

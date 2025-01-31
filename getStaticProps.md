### **Understanding `getStaticProps` in Next.js**  

`getStaticProps` is a special **data-fetching function** in Next.js used for **Static Site Generation (SSG)**. It allows you to fetch data **at build time**, and the generated page is **pre-rendered** as static HTML.  

---

## **1️⃣ Key Characteristics of `getStaticProps`**
✔ **Runs only at build time** (not on every request).  
✔ **Does not run on the client-side**, meaning it won't be included in the browser bundle.  
✔ Used only in **pages**, not in components.  
✔ Can be used with **Incremental Static Regeneration (ISR)** to update static content after deployment.  

---

## **2️⃣ Basic Usage of `getStaticProps`**
```tsx
export async function getStaticProps() {
  // Fetch external data
  const res = await fetch("https://jsonplaceholder.typicode.com/posts/1");
  const data = await res.json();

  return {
    props: { post: data }, // Passed to the page as props
  };
}

export default function Page({ post }) {
  return (
    <div>
      <h1>{post.title}</h1>
      <p>{post.body}</p>
    </div>
  );
}
```
👉 **What happens?**  
- Next.js **calls `getStaticProps` at build time**.  
- The fetched `post` data is **injected as props** into the page.  
- The page is generated as **static HTML** and served on request.  

---

## **3️⃣ `getStaticProps` with Dynamic Routes (`getStaticPaths`)**
If you're working with **dynamic pages** (e.g., `/posts/[id]`), you must also use `getStaticPaths` to tell Next.js which pages to generate at build time.

```tsx
export async function getStaticPaths() {
  // Fetch available post IDs
  const res = await fetch("https://jsonplaceholder.typicode.com/posts");
  const posts = await res.json();

  const paths = posts.map((post) => ({
    params: { id: post.id.toString() },
  }));

  return { paths, fallback: false }; // false = 404 for missing pages
}

export async function getStaticProps({ params }) {
  const res = await fetch(
    `https://jsonplaceholder.typicode.com/posts/${params.id}`
  );
  const post = await res.json();

  return { props: { post } };
}

export default function Post({ post }) {
  return (
    <div>
      <h1>{post.title}</h1>
      <p>{post.body}</p>
    </div>
  );
}
```
👉 **How it works?**  
- `getStaticPaths` **pre-generates** pages for each `id`.  
- `getStaticProps` fetches **individual post data** at build time.  
- If `fallback: false`, any `id` not pre-generated returns a **404 error**.  

---

## **4️⃣ `getStaticProps` with Incremental Static Regeneration (ISR)**
By default, `getStaticProps` **only runs at build time**. However, you can enable **automatic updates** after deployment using **`revalidate`**.

```tsx
export async function getStaticProps() {
  const res = await fetch("https://api.example.com/posts");
  const posts = await res.json();

  return {
    props: { posts },
    revalidate: 10, // Regenerate the page every 10 seconds
  };
}
```
🔹 The page **rebuilds in the background** every 10 seconds, ensuring fresh data **without rebuilding the entire site**.

---

## **5️⃣ When to Use `getStaticProps`?**
✅ **Best for:**  
- Static pages (blogs, docs, landing pages).  
- Content that changes **infrequently**.  
- Pages that **don’t need user-specific data**.  

❌ **Avoid if:**  
- Data changes frequently (**use ISR or SSR instead**).  
- User authentication is required (**use SSR instead**).  

---

## **6️⃣ Summary**
| Feature | `getStaticProps` |
|---------|----------------|
| **When does it run?** | At build time |
| **Where does it run?** | Only on the server (not in the client bundle) |
| **SEO-friendly?** | ✅ Yes |
| **Best for** | Blogs, landing pages, documentation |
| **Supports dynamic routes?** | ✅ Yes (with `getStaticPaths`) |
| **Can it fetch external APIs?** | ✅ Yes |
| **Supports live updates?** | ✅ Yes (with ISR `revalidate`) |

Would you like a **real-world example** for your use case? 🚀

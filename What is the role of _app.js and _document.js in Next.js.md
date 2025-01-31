In Next.js, `_app.js` and `_document.js` are special files used for **customizing the default behavior of the application**.  

---

# **📌 `_app.js` (or `_app.tsx`) - Custom App Component**
✅ **Purpose**:  
- Wraps every page in the app.  
- Used to **persist state, add global styles, and configure layouts**.  
- Runs **on both client & server**.

✅ **Common Use Cases**:  
- **Global CSS**  
- **Layout components**  
- **State management (Redux, Context API, etc.)**  
- **Custom page transitions**  

### 🔹 Example of `_app.js`
```tsx
import '../styles/globals.css'; // Global CSS
import { ThemeProvider } from 'next-themes'; // Example: Theme switching

export default function MyApp({ Component, pageProps }) {
  return (
    <ThemeProvider attribute="class">
      <Component {...pageProps} /> {/* Renders current page */}
    </ThemeProvider>
  );
}
```
### 🔍 **What happens here?**
- `Component` is the **active page component**.  
- `pageProps` are the props passed to the page.  
- The `ThemeProvider` wraps the entire app.

⚡ **Without `_app.js`**, Next.js uses its default behavior to render pages.

---

# **📌 `_document.js` (or `_document.tsx`) - Custom HTML Document**
✅ **Purpose**:  
- Modifies the **HTML structure** of the application.  
- Runs **only on the server** (never on the client).  
- Used to **customize `<html>`, `<head>`, and `<body>`**.  

✅ **Common Use Cases**:  
- **Custom fonts (Google Fonts, self-hosted fonts)**  
- **Meta tags that should not be updated dynamically**  
- **Preloading resources**  
- **Adding `lang` attribute to `<html>` for accessibility/SEO**  

### 🔹 Example of `_document.js`
```tsx
import { Html, Head, Main, NextScript } from 'next/document';

export default function MyDocument() {
  return (
    <Html lang="en">
      <Head>
        <link rel="preconnect" href="https://fonts.googleapis.com" />
        <link
          href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600&display=swap"
          rel="stylesheet"
        />
      </Head>
      <body>
        <Main /> {/* Page content is inserted here */}
        <NextScript /> {/* Next.js scripts are injected here */}
      </body>
    </Html>
  );
}
```

### 🔍 **What happens here?**
- `<Html>` wraps the whole document (can set `lang="en"` for SEO).  
- `<Head>` is used for **static meta tags** (fonts, favicons, analytics).  
- `<Main>` is where Next.js **injects the page content**.  
- `<NextScript>` loads Next.js scripts (e.g., `next/image`, `_app.js` logic).

⚡ **Without `_document.js`**, Next.js provides a default document structure.

---

# **🚀 Key Differences**
| Feature | `_app.js` | `_document.js` |
|---------|----------|---------------|
| **Scope** | Affects pages globally | Affects the entire document structure |
| **Runs on** | Server + Client | Server-only |
| **Used for** | Global state, layouts, CSS | Custom `<html>`, `<head>`, `<body>` |
| **Can modify** | Page rendering | Global HTML structure |
| **Executes on navigation?** | ✅ Yes | ❌ No (Only on initial load) |

---

# **✅ When to Use Which?**
- **Use `_app.js`** → If you need global styles, layouts, or persistent state.  
- **Use `_document.js`** → If you need to modify the `<html>` or **load fonts**.  

Would you like an example combining `_app.js` with Redux or TailwindCSS? 🚀

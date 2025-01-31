In Next.js, **environment variables** help manage configuration values like API keys, database URLs, or other sensitive data that should vary between different environments (e.g., development, production). Next.js provides a straightforward way to manage and use environment variables, both **client-side** and **server-side**.

### 1. **Defining Environment Variables**
Environment variables in Next.js can be set in several places, but the most common method is by creating a `.env` file in the project root. You can also use environment-specific files, like `.env.local`, `.env.development`, and `.env.production`.

### 🔹 **Creating Environment Files**

- `.env.local` – For **local development** (never committed to version control).  
- `.env.development` – For **development-specific variables**.  
- `.env.production` – For **production-specific variables**.

#### Example: `.env.local`
```bash
NEXT_PUBLIC_API_URL=https://api.example.com
DATABASE_URL=postgres://user:password@localhost:5432/mydb
```

### 🔑 **Prefixing Variables for Client-Side Use**
- To expose environment variables to the **client-side**, **they must be prefixed with `NEXT_PUBLIC_`**. This ensures that sensitive variables (without the prefix) are only available on the server-side.
  
```bash
NEXT_PUBLIC_API_URL=https://api.example.com  # Client-side accessible
DATABASE_URL=postgres://user:password@localhost:5432/mydb  # Server-side only
```

---

### 2. **Accessing Environment Variables in Code**

#### Server-Side Access (for SSR, API routes, etc.)
You can access environment variables directly using `process.env`.

```js
// Example in an API route (pages/api/data.js)
export default async function handler(req, res) {
  const dbUrl = process.env.DATABASE_URL; // Server-side only
  res.status(200).json({ message: "Success", dbUrl });
}
```

#### Client-Side Access
If a variable is prefixed with `NEXT_PUBLIC_`, you can access it in your React components like this:

```tsx
// Example in a React component
import { useEffect, useState } from 'react';

const MyComponent = () => {
  const [apiUrl, setApiUrl] = useState('');

  useEffect(() => {
    setApiUrl(process.env.NEXT_PUBLIC_API_URL); // Client-side accessible
  }, []);

  return <div>API URL: {apiUrl}</div>;
};

export default MyComponent;
```

---

### 3. **Environment Variables in `next.config.js`**

Next.js allows you to **use environment variables** in the configuration file (`next.config.js`). This is useful for adding environment-specific configurations during build time.

```js
// next.config.js
module.exports = {
  env: {
    NEXT_PUBLIC_API_URL: process.env.NEXT_PUBLIC_API_URL,
  },
};
```
This allows you to access the environment variable in your app code like:

```js
const apiUrl = process.env.NEXT_PUBLIC_API_URL;
```

---

### 4. **Environment Variables in Deployment**

- **Vercel**: If you're deploying on Vercel, you can set environment variables through the Vercel dashboard under **Project Settings > Environment Variables**. Vercel will automatically inject those variables into your app during build and runtime.
  
- **Other Hosting Platforms**: Most platforms like **Heroku**, **Netlify**, **AWS**, etc., support environment variable management. You’ll typically configure them in the platform’s dashboard or using CLI commands.

---

### 5. **Best Practices**
- **Never hard-code sensitive values** like API keys or database URLs directly into your codebase. Always use environment variables.
- **Prefix** client-side environment variables with `NEXT_PUBLIC_` to expose them to the browser.  
- **.env.local** should be ignored in version control (`.gitignore`).
- **Different environment files** (`.env.production`, `.env.development`, etc.) can be used to manage different configurations for each environment.

---

### 🚀 Example Workflow:
1. **Create `.env.local` file** with the following content:
   ```bash
   NEXT_PUBLIC_API_URL=https://api.example.com
   DATABASE_URL=postgres://user:password@localhost:5432/mydb
   ```

2. **Access Environment Variables**:
   - On the **server**: `process.env.DATABASE_URL`
   - On the **client**: `process.env.NEXT_PUBLIC_API_URL`

3. **Deploying to Vercel**:
   - Set the `NEXT_PUBLIC_API_URL` in **Vercel’s dashboard** under Environment Variables.

---

Would you like to see how to handle different environments (like staging vs. production) in more detail? 😎

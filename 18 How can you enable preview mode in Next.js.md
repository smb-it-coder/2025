In Next.js, **Preview Mode** allows you to view unpublished or draft content while still using the **Static Site Generation (SSG)** process. It is primarily used for content management systems (CMS) or any content that needs to be previewed before going live.

### **Key Points of Preview Mode**:
- You can enable it to display drafts, unpublished changes, or dynamically fetch content based on a session.
- It works alongside `getStaticProps` and `getStaticPaths`.
- It does **not require rebuilding the app** to preview unpublished content.
  
---

### **Steps to Enable Preview Mode in Next.js**:

1. **Create a Preview API Route**  
   You need an API route that will **activate the preview mode** and set the preview cookie.

2. **Modify `getStaticProps` to Check Preview Mode**  
   Inside your page component, check if the preview mode is enabled and fetch data accordingly.

3. **Exit Preview Mode**  
   You can also provide a way to exit preview mode, usually via a button or redirect.

---

### 1. **Set Up Preview Mode (API Route)**

Create an API route (`pages/api/preview.js`) that enables preview mode:

```js
// pages/api/preview.js
export default function handler(req, res) {
  // Check if the secret is provided
  if (req.query.secret !== process.env.PREVIEW_SECRET) {
    return res.status(401).json({ message: 'Invalid secret' });
  }

  // Enable Preview Mode
  res.setPreviewData({});

  // Redirect to the requested path
  res.redirect(req.query.slug || '/');
}
```

In the example above:
- **`process.env.PREVIEW_SECRET`**: Store a secret key in your environment variables for secure preview mode activation.
- **`setPreviewData`**: This function activates the preview mode and sets a preview cookie.

---

### 2. **Modify `getStaticProps` to Handle Preview Mode**

In your page, you need to modify `getStaticProps` to check if the preview mode is enabled and fetch unpublished content accordingly.

#### Example in `pages/posts/[id].js`:
```js
export async function getStaticProps({ params, preview = false }) {
  // Fetch data based on whether preview mode is enabled
  const data = preview 
    ? await fetchUnpublishedPost(params.id)  // Draft data
    : await fetchPublishedPost(params.id);   // Published data

  return {
    props: { data },
    revalidate: 10, // Incremental Static Regeneration (ISR)
  };
}
```

- **`preview`**: This parameter comes from `getStaticProps` and is automatically set to `true` if preview mode is active.
- Depending on whether the preview mode is enabled or not, you fetch either the **published** or **unpublished** data.

---

### 3. **Exit Preview Mode**

You may want to provide an option for users to exit preview mode and view the published version of the page. Create another API route for that:

```js
// pages/api/exit-preview.js
export default function handler(req, res) {
  res.clearPreviewData(); // Clear the preview mode cookie
  res.redirect(req.query.slug || '/'); // Redirect to the page
}
```

When a user clicks "Exit Preview Mode", this API will clear the preview cookie, and the page will render as a static page again (using the **published data**).

---

### 4. **Add a Preview Button (Optional)**

You can provide a preview button on your pages (e.g., in your admin interface) that triggers preview mode:

```jsx
import { useEffect, useState } from 'react';

const PreviewButton = ({ slug }) => {
  const [isPreviewing, setIsPreviewing] = useState(false);

  useEffect(() => {
    // Check if preview mode is active by inspecting the document cookie
    const previewCookie = document.cookie.includes('previewData');
    setIsPreviewing(previewCookie);
  }, []);

  if (isPreviewing) {
    return (
      <button onClick={() => window.location.href = `/api/exit-preview?slug=${slug}`}>Exit Preview Mode</button>
    );
  }

  return (
    <button onClick={() => window.location.href = `/api/preview?secret=${process.env.PREVIEW_SECRET}&slug=${slug}`}>
      Enter Preview Mode
    </button>
  );
};

export default PreviewButton;
```

- **`/api/preview`**: Activates preview mode.
- **`/api/exit-preview`**: Exits preview mode.

---

### **Example Workflow**:
1. When the user wants to preview a post, they visit a page like `/api/preview?secret=SECRET_KEY&slug=/posts/my-post`.
2. The `preview` flag in `getStaticProps` is set to `true`, and unpublished content (draft) is fetched.
3. To exit preview mode, they visit `/api/exit-preview?slug=/posts/my-post`, which removes the preview cookie and returns to the static version.

---

### **Why is Preview Mode Useful?**

- **CMS Integration**: It allows content editors to preview drafts or unpublished content (e.g., blog posts or products) before it goes live.
- **Faster Feedback Loop**: The preview feature works **without requiring full rebuilds**—only the content changes, allowing for faster content iteration.
- **Better User Experience**: Allows users to get a realistic experience of the final content (even while it's unpublished) without requiring actual deployment.

---

### 🚀 **Use Cases for Preview Mode**:
- **CMS-powered Websites**: Integrating with headless CMS (like Contentful, Sanity, or Strapi) to preview content before publishing.
- **Admin Dashboards**: Allowing admins or content managers to view drafts while in a staging environment.

Would you like to see a complete example of Preview Mode integrated with a headless CMS?

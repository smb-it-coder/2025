### `getServerSideProps` in Next.js

`getServerSideProps` is a special function in Next.js used for **server-side rendering (SSR)**. It runs **on the server at request time** and is used to fetch data dynamically for each request.

### When to Use `getServerSideProps`
You should use `getServerSideProps` when:
1. **You need fresh data on every request** – Useful for data that frequently changes, like stock prices, news feeds, or user-specific data.
2. **SEO is important** – Since the data is fetched at request time, it ensures that search engines can index the fully rendered page.
3. **You need request-specific data** – If the page content depends on cookies, authentication tokens, or request headers.
4. **You don't want to expose API calls to the client** – The function runs only on the server, keeping API keys or sensitive logic hidden.

### Example Usage
```javascript
export async function getServerSideProps(context) {
  const res = await fetch(`https://api.example.com/data`);
  const data = await res.json();

  return {
    props: { data }, // This will be passed to the page component as props
  };
}

export default function Page({ data }) {
  return <div>{data.message}</div>;
}
```

### Key Points:
- Runs **only on the server** (not in the browser).
- Executes **on every request** (unlike `getStaticProps`, which runs at build time).
- Slower than static generation (`getStaticProps`) since it runs at request time.
- Can access request-specific data via `context.req` and `context.query`.

Would you like a comparison with `getStaticProps` and `getInitialProps`?

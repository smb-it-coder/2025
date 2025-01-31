Next.js provides built-in **internationalization (i18n)** support for both **routing** and **translations**, making it easy to create multilingual applications. Here's how it works:

---

## 1. **i18n Routing in Next.js**
Next.js allows **automatic locale detection and routing** based on the browser's preferred language.

### 🔹 Configuration in `next.config.js`
You can define supported locales, a default locale, and enable domain-based routing.

```javascript
module.exports = {
  i18n: {
    locales: ['en', 'fr', 'de'], // Supported languages
    defaultLocale: 'en', // Default language
    localeDetection: true, // Auto-detect browser language
  },
};
```

### 🔹 URL-Based Localization
Next.js supports **two types of routing for i18n**:

1. **Subpath Routing**  
   - URLs change based on locale:  
     - `/en/about`, `/fr/about`, `/de/about`

2. **Domain Routing**  
   - Different domains serve different languages:
   ```javascript
   module.exports = {
     i18n: {
       locales: ['en', 'fr'],
       defaultLocale: 'en',
       domains: [
         { domain: 'example.com', defaultLocale: 'en' },
         { domain: 'example.fr', defaultLocale: 'fr' },
       ],
     },
   };
   ```

---

## 2. **Handling Translations**
Next.js **does not provide built-in translation management**, but you can use third-party libraries like **next-intl**, **react-intl**, or **i18next**.

### 🔹 Example Using `next-intl`
Install `next-intl`:
```bash
npm install next-intl
```

Create translation files in `/locales`:
```json
// locales/en.json
{
  "greeting": "Hello, {name}!"
}

// locales/fr.json
{
  "greeting": "Bonjour, {name}!"
}
```

Usage in a Component:
```tsx
import { useTranslations } from 'next-intl';

export default function Home() {
  const t = useTranslations();

  return <h1>{t('greeting', { name: 'John' })}</h1>;
}
```

---

## 3. **Dynamic Locale Switching**
You can programmatically change the locale using **Next.js router**.

```tsx
import { useRouter } from 'next/router';

const LanguageSwitcher = () => {
  const router = useRouter();

  const changeLanguage = (locale) => {
    router.push(router.pathname, router.asPath, { locale });
  };

  return (
    <button onClick={() => changeLanguage('fr')}>Switch to French</button>
  );
};
```

---

## 4. **SEO Optimization for i18n**
Next.js automatically adds the `<html lang="xx">` attribute, but you can enhance SEO with `next/head`:

```tsx
import Head from 'next/head';
import { useRouter } from 'next/router';

export default function SEO() {
  const { locale } = useRouter();

  return (
    <Head>
      <html lang={locale} />
      <link rel="alternate" hrefLang="en" href="https://example.com/en" />
      <link rel="alternate" hrefLang="fr" href="https://example.com/fr" />
    </Head>
  );
}
```

---

## ✅ **Key Takeaways**
- **Next.js supports automatic locale detection and URL-based routing.**  
- **Translation management requires third-party libraries like `next-intl` or `i18next`.**  
- **SEO can be enhanced with `<html lang>` and alternate links.**  
- **Programmatic locale switching is possible with `useRouter()`.**

Would you like a deeper dive into integration with a specific translation library? 🚀

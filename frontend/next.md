---
## Nextjs Interview Questions
---
# What is Next.js?
    **A:** Next.js is an open-source React framework for building full-stack web applications. It provides features like server-side rendering (SSR), static site generation (SSG), file-system routing, API routes, image optimization, and more, enabling developers to build fast, scalable, and SEO-friendly React applications.
# Why use Next.js over Create React App (CRA) or plain React?
    **A:**
    *   **Rendering Strategies:** Built-in support for SSR, SSG, ISR, offering better performance and SEO.
    *   **File-System Routing:** Simplified routing based on directory structure.
    *   **API Routes:** Easily create backend API endpoints within the same project.
    *   **Image Optimization:** Built-in `<Image>` component for automatic image optimization.
    *   **Code Splitting:** Automatic code splitting for faster page loads.
    *   **Developer Experience:** Fast Refresh, TypeScript support, ESLint integration.
    *   **Full-Stack Capabilities:** Can handle both frontend and backend logic.
# What are the main rendering strategies supported by Next.js?
    **A:**
    *   **Static Site Generation (SSG):** HTML is generated at build time.
    *   **Server-Side Rendering (SSR):** HTML is generated on the server for each request.
    *   **Incremental Static Regeneration (ISR):** A hybrid approach; static pages are regenerated in the background after a certain interval or on demand.
    *   **Client-Side Rendering (CSR):** Traditional React SPA behavior, content rendered in the browser (can be combined with other strategies).
    *   **React Server Components (RSC - with `app` directory):** Components render on the server, sending minimal or no JavaScript to the client for non-interactive parts.
# What is Pre-rendering in Next.js?
    **A:** Pre-rendering means Next.js generates the HTML for each page in advance, instead of having it all done by client-side JavaScript. This can result in better performance and SEO. The two forms of pre-rendering are Static Site Generation (SSG) and Server-Side Rendering (SSR).
# Explain the difference between SSG (Static Site Generation) and SSR (Server-Side Rendering).
    **A:**
    *   **SSG:** HTML is generated at *build time*. The same HTML file is served to all users for a given page. Ideal for content that doesn't change often per request (blogs, marketing sites, docs). Very fast.
    *   **SSR:** HTML is generated on the *server for each request*. Ideal for pages with frequently changing data or user-specific content. Slower than SSG as it requires server computation per request.
# What is File-System Routing? How does it work in Next.js?
    **A:** Next.js uses the file system to define routes.
    *   **`pages` directory:** Any `.js`, `.jsx`, `.ts`, or `.tsx` file in the `pages` directory automatically becomes a route. `pages/index.js` maps to `/`, `pages/about.js` maps to `/about`.
    *   **`app` directory:** Files named `page.js` or `page.tsx` within a folder define a route segment. `app/dashboard/page.js` maps to `/dashboard`.
# What is the `_app.js` (or `_app.tsx`) file in the `pages` directory?
    **A:** `_app.js` is a custom App component that Next.js uses to initialize pages. It allows you to:
    *   Persist layout between page changes.
    *   Keep state when navigating pages.
    *   Inject global CSS.
    *   Handle custom error handling using `componentDidCatch`.
    It receives `Component` (the active page) and `pageProps` as props.
# What is the `_document.js` (or `_document.tsx`) file in the `pages` directory?
    **A:** `_document.js` allows you to customize the server-rendered document's `<html>` and `<body>` tags. It's rendered only on the server.
    Use cases:
    *   Adding custom `lang` attributes to `<html>`.
    *   Setting up CSS-in-JS server-side rendering.
    *   Adding custom meta tags or scripts outside the main React tree.
    It should not contain application logic or event handlers.
# What is the `next/link` component used for?
    **A:** `<Link href="/path">` is used for client-side navigation between pages in a Next.js application. It enables SPA-like transitions without a full page reload. It pre-fetches page code for linked pages in the viewport for faster navigation.
# What is the `next/head` component used for?
    **A:** `<Head>` (from `next/head`) allows you to append elements to the `<head>` of the HTML document, such as `<title>`, `<meta>` tags, or `<link>` tags for SEO or other purposes. It ensures no duplicate tags are added.
# What is Automatic Code Splitting in Next.js?
    **A:** Next.js automatically splits your JavaScript bundle into smaller chunks, one for each page. This means when a user visits a page, they only download the JavaScript necessary for that specific page, leading to faster initial load times.
# What is Fast Refresh?
    **A:** Fast Refresh is a Next.js feature (and also in Create React App) that gives you instantaneous feedback on edits made to your React components during development. It intelligently updates only the changed components without losing component state.
# How does Next.js handle CSS? (Mention a few ways)
    **A:**
    *   **Global CSS:** Import CSS files in `_app.js` (for `pages` dir) or a root layout (for `app` dir).
    *   **CSS Modules:** `.module.css` files provide locally scoped class names.
    *   **Sass/SCSS:** Built-in support, just install `sass`.
    *   **CSS-in-JS Libraries:** Styled Components, Emotion, etc., can be integrated.
    *   **Tailwind CSS:** Popular utility-first CSS framework, well-supported.
# What is the `public` directory in a Next.js project used for?
    **A:** The `public` directory is used to serve static assets like images, fonts, `robots.txt`, `favicon.ico`, etc. Files inside `public` can be referenced from your code starting with `/` (e.g., `/my-image.png` maps to `public/my-image.png`).
# What is the `next dev`, `next build`, and `next start` commands?
    **A:**
    *   `next dev`: Starts the Next.js development server with Fast Refresh and other development features.
    *   `next build`: Creates an optimized production build of your application.
    *   `next start`: Starts a Node.js server that serves the production build created by `next build`.

# How do you create dynamic routes in the `pages` directory?
    **A:** By creating files or folders with square brackets in their names.
    *   `pages/posts/[id].js` matches `/posts/1`, `/posts/abc`. The `id` is available via `useRouter().query.id`.
    *   `pages/shop/[...slug].js` (catch-all route) matches `/shop/a`, `/shop/a/b`, `/shop/a/b/c`. `slug` will be an array.
    *   `pages/product/[[...slug]].js` (optional catch-all route) matches `/product` as well as `/product/a/b`.
# How do you access route parameters in a page component (using `pages` directory)?
    **A:** Using the `useRouter` hook from `next/router`.
    ```jsx
    import { useRouter } from 'next/router';
    function PostPage() {
      const router = useRouter();
      const { id } = router.query; // Accesses [id].js parameter
      return <p>Post ID: {id}</p>;
    }
    ```
    For SSG/SSR data fetching functions, parameters are available in the context object.
# How do you create dynamic route segments in the `app` directory?
    **A:** By creating folders with square brackets in their names.
    *   `app/blog/[slug]/page.js` matches `/blog/my-post`. `slug` is available as a prop to the page component.
    *   `app/shop/[...categories]/page.js` (catch-all)
    *   `app/docs/[[...path]]/page.js` (optional catch-all)
# How do you access route parameters in a page component (using `app` directory)?
    **A:** Dynamic segment parameters are passed as the `params` prop to `page.js`, `layout.js`, etc.
    ```jsx
    // app/posts/[id]/page.js
    export default function PostPage({ params }) {
      // params will be { id: 'the-actual-id' }
      return <h1>Post: {params.id}</h1>;
    }
    ```
# What is the purpose of `layout.js` (or `layout.tsx`) in the `app` directory?
    **A:** `layout.js` defines a UI that is shared across multiple routes within a segment and its children. Layouts preserve state, remain interactive, and do not re-render on navigation between child segments. They must accept a `children` prop. A root layout (`app/layout.js`) is required.
# What is the purpose of `template.js` (or `template.tsx`) in the `app` directory? How is it different from `layout.js`?
    **A:** `template.js` is similar to `layout.js` in that it wraps child routes. However, on navigation, a *new instance* of the `template` component is mounted, and state is *not* preserved. Useful for features that need to re-run on navigation, like enter/exit animations or `useEffect` logic that depends on route changes.
# What is a `loading.js` file in the `app` directory used for?
    **A:** `loading.js` allows you to create loading UI that is automatically shown using React Suspense while route segment content (e.g., data fetched in Server Components) is loading. It's rendered on the server and shown immediately during navigation.
# What is an `error.js` file in the `app` directory used for?
    **A:** `error.js` defines an error UI boundary for a route segment and its children. It catches errors that occur during rendering or data fetching within that segment and displays a fallback UI. It must be a Client Component (`'use client'`). It receives `error` and `reset` props.
# What is a `not-found.js` file in the `app` directory used for?
    **A:** `not-found.js` allows you to render a custom UI when the `notFound()` function is thrown within a route segment or when a URL doesn't match any routes.
# How do you programmatically navigate in Next.js?
    **A:**
    *   **`pages` directory:** `useRouter().push('/path')` or `useRouter().replace('/path')`.
    *   **`app` directory:** `useRouter()` from `next/navigation`. `router.push('/path')` or `router.replace('/path')`.
# What are Route Groups in the `app` directory?
    **A:** Route Groups allow you to organize routes logically without affecting the URL path. You create a folder with parentheses `(folderName)`. This is useful for grouping routes that share a layout or for organizing project structure. `app/(marketing)/about/page.js` still maps to `/about`.
# What are Parallel Routes in the `app` directory?
    **A:** Parallel Routes allow you to simultaneously render one or more pages within the same layout, each with its own independent navigation and loading states. They are defined using named "slots" (folders starting with `@`, e.g., `@sidebar`). These slots are passed as props to the shared parent layout.
# What are Intercepting Routes in the `app` directory?
    **A:** Intercepting Routes allow you to load a route from another part of your application within the current layout, often used for modals or lightboxes. When navigating directly to the intercepted route's URL, it renders normally. But when navigating via `<Link>` from within a specific context, it can be "intercepted" and shown within the current layout. Uses `(.)`, `(..)`, `(...)` conventions in folder names.
# How do shallow routing and dynamic imports work together for performance?
    **A:**
    *   **Shallow Routing (mostly `pages` dir concept):** `router.push('/same-page?foo=bar', undefined, { shallow: true })` updates the URL without re-running data fetching methods like `getServerSideProps` or `getStaticProps`. Useful for client-side state changes reflected in URL (filters, sort).
    *   **Dynamic Imports (`next/dynamic` or `React.lazy`):** Allow splitting code into separate chunks that are loaded only when a component is rendered. Next.js handles this automatically for pages.
    Together, they help improve performance by loading only necessary code and data.
# How can you create a custom 404 page in Next.js?
    **A:**
    *   **`pages` directory:** Create a `pages/404.js` file.
    *   **`app` directory:** Create a `not-found.js` file at the root or within a specific route segment. The `notFound()` function can also be used to trigger it.

# What is `getStaticProps`? When does it run?
    **A:** `getStaticProps` (Static Site Generation) is an `async` function you export from a page in the `pages` directory.
    *   It runs at *build time* in a Node.js environment (never on the client or for each request in production).
    *   It fetches data and passes it as props to the page component.
    *   Ideal for data that can be determined at build time (e.g., blog posts, product listings from a CMS).
    *   It can be combined with `getStaticPaths` for dynamic routes and ISR.
# What is `getStaticPaths`? When is it required?
    **A:** `getStaticPaths` is an `async` function you export from a dynamic route page (e.g., `pages/posts/[id].js`) when using `getStaticProps`.
    *   It runs at *build time*.
    *   It must return an object with a `paths` array specifying which dynamic routes to pre-render at build time. Each path object should contain `params` for the route.
    *   It also returns a `fallback` property (`false`, `true`, or `'blocking'`).
    *   Required if a dynamic route page uses `getStaticProps`.
# Explain the `fallback` property returned by `getStaticPaths`.
    **A:**
    *   `fallback: false`: Any path not returned by `getStaticPaths` will result in a 404 page.
    *   `fallback: true`: Paths not generated at build time will serve a "fallback" version of the page initially (e.g., a loading state via `router.isFallback`). Next.js will then attempt to generate the page in the background on the first request and cache it for future requests.
    *   `fallback: 'blocking'`: Paths not generated at build time will be server-rendered on the first request (SSR-like behavior) and then cached for future requests. The user won't see a fallback UI; the browser will wait for the page to be generated.
# What is `getServerSideProps`? When does it run?
    **A:** `getServerSideProps` (Server-Side Rendering) is an `async` function you export from a page in the `pages` directory.
    *   It runs on the *server for every incoming request* to that page.
    *   It fetches data and passes it as props to the page component.
    *   Ideal for data that must be fresh on every request or is user-specific.
    *   Slower than `getStaticProps` because it involves server computation per request.
# Can you use both `getStaticProps` and `getServerSideProps` in the same page?
    **A:** No, a page can only use one of them (or neither for client-side rendering). They define mutually exclusive rendering strategies for a page.
# How do you access the request object (e.g., headers, cookies) in `getServerSideProps`?
    **A:** `getServerSideProps` receives a `context` object as an argument, which contains `req` (the HTTP request object), `res` (the HTTP response object), `query` (route parameters), etc.
    `export async function getServerSideProps(context) { const userAgent = context.req.headers['user-agent']; ... }`
# What is Incremental Static Regeneration (ISR)? How do you enable it?
    **A:** ISR allows you to update static pages *after* the site has been built, without needing a full rebuild.
    Enable it by returning a `revalidate` property (in seconds) from `getStaticProps`:
    ```javascript
    export async function getStaticProps() {
      const data = await fetchData();
      return {
        props: { data },
        revalidate: 60, // Re-generate the page at most once every 60 seconds
      };
    }
    ```
    When a request comes in after the revalidation period, the cached page is served, and Next.js triggers a regeneration in the background. The next request gets the updated page.
# What is client-side data fetching in Next.js? When would you use it?
    **A:** Fetching data directly from the client-side (browser) using `useEffect` and `fetch`/`axios`, just like in a standard React SPA.
    When to use:
    *   For user-specific data that doesn't need to be pre-rendered for SEO (e.g., user dashboards, settings).
    *   When data changes frequently and real-time updates are not critical.
    *   To update parts of a page without a full navigation.
    Often used in combination with SSG/SSR for initial page content. Tools like SWR or React Query are excellent for client-side data fetching.
# What is SWR or React Query, and how can they be beneficial with Next.js?
    **A:** SWR ("stale-while-revalidate") and React Query are data fetching libraries for React.
    Benefits with Next.js:
    *   Simplify client-side data fetching.
    *   Provide caching, revalidation on focus/reconnect, pagination, optimistic updates.
    *   Work well with Next.js's pre-rendering strategies (e.g., pre-render initial data with `getStaticProps`, then use SWR/React Query for client-side updates).
    *   Reduce boilerplate for loading/error states.
# How can you pass data from `getStaticProps` or `getServerSideProps` to your page component?
    **A:** By returning an object with a `props` key from these functions. The value of this `props` key will be passed as props to your page component.
    ```javascript
    export async function getStaticProps() {
      const someData = await fetchMyData();
      return {
        props: { // This object is passed to the page component
          myData: someData,
          anotherProp: 'hello'
        },
      };
    }
    function MyPage({ myData, anotherProp }) { /* ... use props ... */ }
    ```

# What are React Server Components (RSCs) in Next.js?
    **A:** Server Components are React components that run on the server (either at build time or request time). They can directly access server-side resources (like databases or file systems) and do not send their JavaScript to the client by default, reducing bundle sizes. They are `async/await` compatible. Components in the `app` directory are Server Components by default.
# What are Client Components in the `app` directory? How do you define one?
    **A:** Client Components are components that run in the browser and can use React state, effects, and browser-only APIs.
    You define one by adding the `'use client'` directive at the top of the file.
    ```jsx
    'use client'; // This marks the component (and its imports) as Client Components
    import { useState, useEffect } from 'react';
    export default function Counter() { /* ... uses useState ... */ }
    ```
# Can Server Components use React Hooks like `useState` or `useEffect`?
    **A:** No. Server Components render entirely on the server and do not have the client-side interactivity or lifecycle that `useState` or `useEffect` manage. For interactivity, you use Client Components.
# How do Server Components fetch data in the `app` directory?
    **A:** Server Components can directly use `async/await` within the component function itself to fetch data. Next.js extends the native `fetch` API to provide automatic caching and revalidation.
    ```jsx
    // app/posts/page.js (Server Component by default)
    async function getPosts() {
      const res = await fetch('https://api.example.com/posts', { cache: 'no-store' }); // Or other caching options
      return res.json();
    }
    export default async function PostsPage() {
      const posts = await getPosts();
      return (
        <ul>{posts.map(post => <li key={post.id}>{post.title}</li>)}</ul>
      );
    }
    ```
# What are the caching options for `fetch` in Server Components?
    **A:** Next.js extends `fetch` with an options object:
    *   `cache: 'force-cache'` (default): Caches indefinitely or until manually revalidated. Good for SSG-like behavior.
    *   `cache: 'no-store'`: Fetches fresh data on every request. Good for SSR-like behavior.
    *   `next: { revalidate: number_in_seconds }`: Caches for a specified duration (ISR-like behavior).
    *   `next: { tags: ['tag1', 'tag2'] }`: Allows on-demand revalidation using `revalidateTag`.
# How does data fetching in Server Components compare to `getStaticProps`/`getServerSideProps`?
    **A:**
    *   **Colocation:** Data fetching logic lives directly within the component that needs it, rather than in separate data fetching functions.
    *   **Granularity:** Data fetching can happen at the component level, not just the page level.
    *   **Flexibility:** Caching and revalidation are more granular and controlled via `fetch` options or route segment configs.
    *   **No Prop Passing:** Server Components directly fetch their data; there's no equivalent of returning `props` from a special function.
    It offers a more integrated and component-centric approach to data fetching.
# Can Client Components directly fetch data using `async/await` in their function body?
    **A:** No. Client Components execute in the browser. Data fetching in Client Components is typically done within `useEffect` (for traditional fetch) or using libraries like SWR/React Query. Server Components can pass initial data as props to Client Components.
# How do Server and Client Components interact in the `app` directory?
    **A:**
    *   Server Components can import and render Client Components.
    *   Server Components can pass props (which must be serializable) to Client Components.
    *   Client Components *cannot* directly import Server Components (because Server Components might have server-only code). You can pass Server Components as `children` props to Client Components (this pattern is called "Server Components in Client Components holes").
# What are Route Handlers in the `app` directory? How do they replace API Routes from `pages`?
    **A:** Route Handlers allow you to create API endpoints by exporting functions named after HTTP methods (e.g., `GET`, `POST`, `PUT`, `DELETE`) from a `route.js` or `route.ts` file within a route segment.
    `app/api/users/route.js`
    ```javascript
    export async function GET(request) {
      // ... logic ...
      return Response.json({ message: 'Hello from GET' });
    }
    export async function POST(request) {
      const data = await request.json();
      // ... logic ...
      return Response.json({ received: data });
    }
    ```
    They use standard Web Request and Response APIs. They replace the need for the `pages/api` directory.
# What is Streaming in the context of Next.js `app` directory?
    **A:** Streaming allows the server to send parts of the HTML page to the client as they are rendered, instead of waiting for the entire page to be ready. This improves perceived performance, as users can see and interact with parts of the page sooner. The `app` directory with React Suspense and Server Components enables streaming by default. `loading.js` files integrate with this.
# What are Server Actions in Next.js (`app` directory)?
    **A:** Server Actions are asynchronous functions that run on the server and can be called directly from Client Components (e.g., in form submissions or event handlers) or invoked from Server Components. They simplify data mutations and form handling by allowing server-side logic to be executed without manually creating API endpoints. They are marked with `'use server'` directive if defined in a client component file, or are server-side by default if exported from a server component file.
    ```jsx
    // app/actions.js
    'use server';
    export async function createItem(formData) {
      const rawFormData = { name: formData.get('name') };
      // ... save to database ...
      // revalidatePath('/items'); // Optional: revalidate cached data
      return { message: 'Item created' };
    }

    // app/MyClientForm.js
    'use client';
    import { createItem } from './actions';
    export default function MyForm() {
      return <form action={createItem}><input name="name"/><button>Submit</button></form>;
    }
    ```
# How does caching work with Route Handlers in the `app` directory?
    **A:** By default, `GET` Route Handlers are cached similar to how pages are. You can control caching behavior using Route Segment Config options or by setting cache headers in the `Response` object. For `POST`, `PUT`, `DELETE`, etc., caching is typically not applied by default.
# What is `revalidatePath` and `revalidateTag` used for with `app` directory data fetching?
    **A:** These functions allow for on-demand revalidation of cached data:
    *   `revalidatePath(path)`: Invalidates the cache for a specific path, causing data to be re-fetched on the next request to that path.
    *   `revalidateTag(tag)`: Invalidates the cache for all data fetches that were associated with a specific tag (using `fetch(url, { next: { tags: ['myTag'] } })`).
    Often used in Server Actions or API routes after a data mutation.
# How do you handle forms that submit data to Server Actions?
    **A:** You can use the `action` prop on a `<form>` element in a Client Component, passing the Server Action function directly. Next.js handles the progressive enhancement (works with/without JS). You can also use `useTransition` and `startTransition` for pending UI states.
    ```jsx
    'use client';
    import { submitComment } from './actions'; // Server Action
    import { useTransition } from 'react';

    export default function CommentForm() {
      let [isPending, startTransition] = useTransition();
      async function handleSubmit(formData) {
        startTransition(async () => {
          await submitComment(formData);
        });
      }
      return (
        <form onSubmit={(e) => { e.preventDefault(); handleSubmit(new FormData(e.currentTarget)); }}>
          <textarea name="comment" />
          <button type="submit" disabled={isPending}>
            {isPending ? 'Submitting...' : 'Submit'}
          </button>
        </form>
      );
    }
    ```
# What is the difference between the Edge Runtime and Node.js Runtime for API Routes/Route Handlers/Middleware?
    **A:**
    *   **Node.js Runtime (default):** Full Node.js environment, access to all Node.js APIs and NPM packages. Runs on traditional servers or serverless functions with Node.js support.
    *   **Edge Runtime:** A lightweight JavaScript runtime based on web standards (like browser APIs: `fetch`, `Request`, `Response`). Designed to run on edge computing platforms (closer to users). Faster cold starts, lower latency, but limited API surface (no direct file system access, restricted Node.js APIs).
    You can configure the runtime for specific routes.

# How do you create an API route in the `pages` directory?
    **A:** Create a file inside the `pages/api` directory. The exported default function handles requests to that endpoint.
    `pages/api/hello.js`:
    ```javascript
    export default function handler(req, res) {
      if (req.method === 'GET') {
        res.status(200).json({ message: 'Hello from API!' });
      } else {
        res.setHeader('Allow', ['GET']);
        res.status(405).end(`Method ${req.method} Not Allowed`);
      }
    }
    ```
    This creates an endpoint at `/api/hello`.
# How do you handle different HTTP methods (GET, POST, etc.) in an API route (`pages` dir)?
    **A:** Check the `req.method` property within the handler function.
    ```javascript
    export default function userHandler(req, res) {
      const { id } = req.query;
      if (req.method === 'GET') { /* ... */ }
      else if (req.method === 'POST') { /* ... */ }
      else if (req.method === 'PUT') { /* ... */ }
      // ...
    }
    ```
# How do you access request body, query parameters, and route parameters in API routes (`pages` dir)?
    **A:**
    *   **Request Body (`req.body`):** Next.js automatically parses JSON, form data if `Content-Type` is set correctly.
    *   **Query Parameters (`req.query`):** For `GET /api/items?category=books`, `req.query.category` is `"books"`.
    *   **Route Parameters (`req.query`):** For dynamic API routes like `pages/api/users/[userId].js`, `req.query.userId` contains the ID.
# How do you send a JSON response from an API route (`pages` dir)?
    **A:** Use `res.status(code).json(object)`.
    ```javascript
    res.status(200).json({ success: true, data: users });
    ```
# How do Route Handlers in the `app` directory differ from API Routes in `pages/api`?
    **A:**
    *   **File Naming:** `route.js` vs. any file name in `pages/api`.
    *   **API:** Route Handlers use standard Web Request/Response APIs. API Routes use Node.js `req`/`res` objects (extended by Next.js).
    *   **Function Naming:** Export functions named `GET`, `POST`, etc. in `route.js` vs. a single default handler function in `pages/api`.
    *   **Colocation:** Route Handlers can be co-located with UI components in the `app` directory, while `pages/api` is a dedicated directory.
# Can you use middleware with API Routes/Route Handlers?
    **A:** Yes.
    *   **`pages` directory API Routes:** You can use Express-style middleware by wrapping your handler or using libraries that support the Node.js `req`/`res` pattern. Next.js also has its own Middleware feature (see below).
    *   **`app` directory Route Handlers:** Middleware in Next.js (using `middleware.js`) can run before Route Handlers. You can also structure your Route Handler code to use helper functions that act like middleware.
# How do you handle errors in API Routes/Route Handlers?
    **A:**
    *   Use `try...catch` blocks.
    *   Send appropriate HTTP status codes (e.g., `400` for bad request, `401` for unauthorized, `404` for not found, `500` for server error).
    *   Return a JSON response with an error message.
    ```javascript
    // app/api/example/route.js
    export async function GET(request) {
      try {
        // const data = await someAsyncOperationThatMightFail();
        // if (!data) return Response.json({ error: 'Not found' }, { status: 404 });
        return Response.json({ message: 'Success' });
      } catch (error) {
        console.error(error);
        return Response.json({ error: 'Internal Server Error' }, { status: 500 });
      }
    }
    ```
# How can you protect API Routes/Route Handlers (e.g., require authentication)?
    **A:**
    1.  Client sends an auth token (e.g., JWT) in the `Authorization` header.
    2.  In the API Route/Route Handler (or in Middleware), verify the token.
    3.  If invalid or missing, return a `401 Unauthorized` response.
    4.  If valid, proceed with the request.
    Libraries like `next-auth` simplify this, or you can implement custom token verification.
# What is Next.js Middleware? Where does it run?
    **A:** Next.js Middleware allows you to run code *before* a request is completed. Based on the incoming request, you can modify the response by rewriting, redirecting, adding headers, or streaming.
    *   Create a `middleware.js` (or `.ts`) file in the root of your project (or at the same level as `pages` or `app`).
    *   It runs on the Edge Runtime by default, but can be configured for Node.js runtime.
    *   Uses standard `Request` and `Response` objects.
    ```javascript
    // middleware.js
    import { NextResponse } from 'next/server';
    export function middleware(request) {
      if (request.nextUrl.pathname.startsWith('/dashboard')) {
        // Check for auth token in cookies or headers
        // If not authenticated, redirect to login
        // return NextResponse.redirect(new URL('/login', request.url));
      }
      return NextResponse.next(); // Continue processing
    }
    // Optional: specify which paths this middleware should run on
    export const config = {
      matcher: ['/dashboard/:path*', '/admin/:path*'],
    };
    ```
# Can you use Next.js API Routes/Route Handlers to interact with a database?
    **A:** Yes, absolutely. This is a primary use case. You would use a database client or ORM (like Prisma, Mongoose for MongoDB) within your API/Route Handler functions to perform CRUD operations, just like in a traditional backend application.

# What is the `next/image` component? What problems does it solve?
    **A:** `<Image>` (from `next/image`) is an extension of the HTML `<img>` element evolved for the modern web.
    Problems it solves/features:
    *   **Automatic Image Optimization:** Resizes, optimizes (e.g., converts to WebP), and serves images in modern formats.
    *   **Lazy Loading:** Images are loaded only when they enter the viewport.
    *   **Responsive Images:** Generates different image sizes for different viewports using `srcset`.
    *   **Prevent Cumulative Layout Shift (CLS):** Requires `width` and `height` props (or `fill` prop with a sized parent) to reserve space for the image before it loads.
    *   **Priority Loading:** `priority` prop for critical above-the-fold images.
# What are the required props for `next/image` for non-`fill` images?
    **A:** `src`, `width`, and `height` are required to prevent layout shift and allow for proper aspect ratio calculation during optimization. The `alt` prop is also crucial for accessibility.
# What is the `fill` prop in `next/image` used for?
    **A:** The `fill` prop makes the image expand to fill its parent container. When using `fill`, the parent element must have `position: relative`, `position: fixed`, or `position: absolute`. `width` and `height` props are not used with `fill`.
# What is the `next/script` component used for? What are its benefits?
    **A:** `<Script>` (from `next/script`) is an extension of the HTML `<script>` element that provides more control over loading and execution of third-party scripts.
    Benefits:
    *   **Loading Strategies:**
        *   `strategy="beforeInteractive"`: Load before page becomes interactive (for critical scripts).
        *   `strategy="afterInteractive"` (default): Load after page becomes interactive (for non-critical scripts).
        *   `strategy="lazyOnload"`: Load during browser idle time.
        *   `strategy="worker"` (experimental): Run script in a web worker.
    *   **Improves Performance:** By optimizing when and how scripts are loaded, it can improve metrics like FCP and TTI.
    *   **`onLoad` Callback:** Execute code after the script has loaded.
# What is `next/font`? How does it help with font optimization?
    **A:** `next/font` (introduced with `app` directory) allows you to self-host Google Fonts or local fonts efficiently.
    Benefits:
    *   **Automatic Self-Hosting:** Downloads fonts at build time and serves them from your own domain, improving privacy and performance (no extra request to Google).
    *   **No Layout Shift:** Helps prevent font-related layout shifts by preloading and optimizing font loading.
    *   **Subset Fonts:** Can automatically subset fonts to include only necessary characters.
    ```javascript
    // app/layout.js
    import { Inter } from 'next/font/google';
    const inter = Inter({ subsets: ['latin'] });
    export default function RootLayout({ children }) {
      return <html lang="en" className={inter.className}><body>{children}</body></html>;
    }
    ```
# What is Dynamic Importing in Next.js? How is it done?
    **A:** Dynamic importing (`next/dynamic` or `React.lazy` with Suspense) allows you to load JavaScript modules (including React components) on demand, rather than including them in the initial JavaScript bundle.
    ```jsx
    import dynamic from 'next/dynamic';
    const HeavyComponent = dynamic(() => import('../components/HeavyComponent'), {
      loading: () => <p>Loading component...</p>, // Optional loading indicator
      ssr: false // Optional: disable SSR for this component
    });
    function MyPage() {
      // HeavyComponent will only be loaded when MyPage is rendered
      return <div><HeavyComponent /></div>;
    }
    ```
    This reduces initial bundle size and improves load times.
# How can you analyze the bundle size of your Next.js application?
    **A:** Using the `@next/bundle-analyzer` package.
    1.  Install: `npm install --save-dev @next/bundle-analyzer`
    2.  Configure `next.config.js`:
        ```javascript
        const withBundleAnalyzer = require('@next/bundle-analyzer')({
          enabled: process.env.ANALYZE === 'true',
        });
        module.exports = withBundleAnalyzer({});
        ```
    3.  Run: `ANALYZE=true npm run build`. This will open an interactive treemap visualization of your bundles.
# What is "tree shaking" in the context of Next.js and Webpack?
    **A:** Tree shaking is a dead code elimination process. Next.js uses Webpack (or Turbopack) as its bundler, which analyzes `import` and `export` statements to detect which parts of your code and imported libraries are actually being used. Unused code is then "shaken off" and not included in the final production bundles, resulting in smaller file sizes.
# What is prefetching in Next.js? How does `<Link>` utilize it?
    **A:** Prefetching is the act of loading resources (like the JavaScript code for a page) in the background before a user actually navigates to it.
    The `<Link>` component automatically prefetches the code for the linked page when the link appears in the viewport. This makes subsequent navigation to that page feel almost instant. This behavior can be controlled with the `prefetch` prop.
# What are some general performance optimization tips for Next.js applications?
    **A:**
    *   Use SSG or ISR where possible over SSR for faster responses.
    *   Optimize images with `next/image`.
    *   Use `next/script` with appropriate strategies for third-party scripts.
    *   Use `next/font` for optimized font loading.
    *   Code split with `next/dynamic`.
    *   Analyze and reduce bundle sizes.
    *   Memoize React components (`React.memo`, `useMemo`, `useCallback`).
    *   Efficient data fetching patterns and caching.
    *   Minimize client-side JavaScript.
    *   Use Web Vitals to measure and improve performance.

# How do you configure environment variables in Next.js?
    **A:**
    *   Create `.env.local` (for local development, gitignored), `.env.development`, `.env.production` files in the root of your project.
    *   Variables prefixed with `NEXT_PUBLIC_` are exposed to the browser (client-side).
    *   Variables without the prefix are only available server-side (in `getServerSideProps`, `getStaticProps`, API routes, `app` dir Server Components).
    *   Access them via `process.env.VARIABLE_NAME`.
# What is Vercel? How is it related to Next.js?
    **A:** Vercel is a cloud platform for hosting and deploying frontend applications and serverless functions. It is created by the same team that develops Next.js. Vercel offers seamless, optimized deployment for Next.js projects, leveraging features like Edge Functions, Image Optimization, and global CDN.
# Can you deploy Next.js applications to platforms other than Vercel?
    **A:** Yes. You can deploy Next.js applications to:
    *   Node.js hosting environments (e.g., AWS EC2, DigitalOcean Droplets, Heroku) by running `next build` and `next start`.
    *   Serverless platforms (e.g., AWS Lambda, Google Cloud Functions) often requiring adapters.
    *   Static hosting providers (Netlify, GitHub Pages) if your app is fully SSG with no SSR or API routes needing a Node.js server (or using their serverless function equivalents).
    *   Docker containers.
# What is the `next.config.js` file used for?
    **A:** `next.config.js` is a Node.js module (not JSON) that allows you to customize the behavior of Next.js.
    Common configurations:
    *   Environment variables (though `.env` files are preferred for secrets).
    *   Redirects and rewrites.
    *   Custom Webpack configuration.
    *   Headers.
    *   Image optimization domains.
    *   Experimental features.
# What are rewrites and redirects in Next.js? How do you configure them?
    **A:** Configured in `next.config.js`:
    *   **Redirects:** Change the URL in the browser, sending the user to a different page (e.g., for changed URLs, enforcing trailing slashes). Returns a 3xx status code.
        ```javascript
        // next.config.js
        module.exports = {
          async redirects() {
            return [ { source: '/old-path', destination: '/new-path', permanent: true } ];
          },
        };
        ```
    *   **Rewrites:** Proxy a request to a different destination URL internally without changing the URL visible to the user. Useful for proxying to external services or structuring an application.
        ```javascript
        // next.config.js
        module.exports = {
          async rewrites() {
            return [ { source: '/api/external/:path*', destination: 'https://api.example.com/:path*' } ];
          },
        };
        ```
# What is Internationalization (i18n) routing in Next.js?
    **A:** Next.js has built-in support for i18n routing, allowing you to serve different language versions of your site based on URL prefixes (e.g., `/en/about`, `/fr/about`) or domain/subdomain mapping. Configured in `next.config.js` with `i18n: { locales: ['en', 'fr'], defaultLocale: 'en' }`. The `useRouter` hook provides locale information.
# What is Turbopack?
    **A:** Turbopack is a new, Rust-based incremental bundler for JavaScript and TypeScript, positioned as a successor to Webpack, developed by Vercel. It aims for significantly faster build times and development server performance. It's being integrated into Next.js (e.g., `next dev --turbo`).
# How can you create custom error pages (e.g., 500) in Next.js?
    **A:**
    *   **`pages` directory:** Create a `pages/_error.js` file. This component receives `statusCode` as a prop.
    *   **`app` directory:**
        *   `error.js`: For client-side errors within a segment.
        *   `global-error.js` (at the root of `app`): Catches errors in the root layout.
        *   For server errors that prevent rendering, the default Next.js error page is shown, or you might configure your server/hosting for custom 500 pages.
# What are Draft Mode (formerly Preview Mode) and On-Demand Revalidation?
    **A:**
    *   **Draft Mode:** Allows users to bypass statically generated pages and view a draft version of a page fetched on-demand (SSR-like behavior), typically from a headless CMS. Useful for previewing content changes before publishing. It uses cookies to enable this mode.
    *   **On-Demand Revalidation:** Allows you to manually trigger the regeneration of specific static pages (SSG or ISR) typically after content updates in a CMS. This is done by calling a specific API route (often secured) that uses `res.revalidate('/path-to-revalidate')` (for `pages` dir) or `revalidatePath` / `revalidateTag` (for `app` dir).
# What is the `Image` component's `loader` prop used for?
    **A:** The `loader` prop allows you to provide a custom function that resolves the image URL. This is useful if you are using a third-party image optimization service or a custom image backend, instead of Next.js's built-in image optimization.
# What are some considerations for SEO when building with Next.js?
    **A:**
    *   **Pre-rendering (SSG/SSR/ISR):** Ensures content is available for search engine crawlers.
    *   **`next/head`:** Use for setting `<title>`, `<meta description>`, canonical URLs, Open Graph tags, etc.
    *   **Semantic HTML:** Use appropriate HTML tags.
    *   **Structured Data (JSON-LD):** Add structured data to help search engines understand your content.
    *   **`robots.txt`:** Place in the `public` directory.
    *   **Sitemap Generation:** Create and submit a `sitemap.xml`.
    *   **Fast Load Times:** Optimize performance using Next.js features.
    *   **Mobile-Friendliness:** Ensure responsive design.
# How can you add custom headers to responses in Next.js?
    **A:** In `next.config.js` using the `headers` function:
    ```javascript
    module.exports = {
      async headers() {
        return [
          {
            source: '/:path*', // Apply to all routes
            headers: [
              { key: 'X-Frame-Options', value: 'SAMEORIGIN' },
              { key: 'Content-Security-Policy', value: "..." },
            ],
          },
        ];
      },
    };
    ```
    In API Routes/Route Handlers, you can set headers directly on the response object.
# How does Next.js handle TypeScript?
    **A:** Next.js has excellent built-in TypeScript support.
    *   If you create a `tsconfig.json` file in your project root (or use `create-next-app` with TypeScript template), Next.js will automatically configure it.
    *   You can use `.ts` and `.tsx` files for pages, components, API routes, etc.
    *   Next.js provides types for its core APIs (e.g., `GetStaticProps`, `NextApiRequest`).
# What is the `getLayout` pattern (more relevant for `pages` dir)?
    **A:** The `getLayout` pattern is a way to achieve per-page layouts in the `pages` directory. A page component can define a static `getLayout` function that receives the page component as an argument and wraps it in the desired layout. This is then used in `_app.js`.
    ```jsx
    // pages/about.js
    import SiteLayout from '../components/SiteLayout';
    export default function AboutPage() { /* ... */ }
    AboutPage.getLayout = function getLayout(page) {
      return <SiteLayout>{page}</SiteLayout>;
    };

    // pages/_app.js
    export default function MyApp({ Component, pageProps }) {
      const getLayout = Component.getLayout || ((page) => page);
      return getLayout(<Component {...pageProps} />);
    }
    ```
    The `app` directory has built-in support for layouts with `layout.js` files.
# What are some common pitfalls or challenges when working with Next.js?
    **A:**
    *   **Build Times:** For very large SSG sites, build times can become long (though ISR and on-demand revalidation mitigate this).
    *   **Understanding Rendering Strategies:** Choosing the right strategy (SSG, SSR, ISR, CSR, RSC) for each page/component requires careful consideration.
    *   **Client vs. Server Context:** Especially with the `app` directory, understanding what code runs where (Server Components vs. Client Components) is crucial.
    *   **Hydration Errors:** Mismatches between server-rendered HTML and client-side React rendering can cause hydration errors.
    *   **Configuration Complexity:** While Next.js provides good defaults, advanced customization in `next.config.js` or Webpack can be complex.

# What was the main motivation behind introducing the `app` directory?
    **A:**
    *   **React Server Components:** To natively support RSCs for better performance and developer experience.
    *   **Improved Layouts:** More flexible and intuitive layout system (`layout.js`, `template.js`).
    *   **Streaming:** Built-in support for streaming UI rendering.
    *   **Granular Data Fetching:** Co-locating data fetching with components.
    *   **Simplified Routing Primitives:** For features like loading states, error boundaries.
# Can you use both `pages` and `app` directories in the same Next.js project?
    **A:** Yes. Next.js allows for incremental adoption. You can have both directories, and routes defined in `app` will take precedence over `pages` for the same path. This facilitates gradual migration.
# How do you define metadata (e.g., `<title>`, `<meta description>`) in the `app` directory?
    **A:** By exporting a static `metadata` object or a dynamic `generateMetadata` function from `layout.js` or `page.js` files.
    ```javascript
    // app/some-page/page.js
    export const metadata = {
      title: 'My Page Title',
      description: 'Description of my page.',
    };
    // OR for dynamic metadata:
    // export async function generateMetadata({ params }) {
    //   const product = await getProduct(params.id);
    //   return { title: product.name };
    // }
    export default function SomePage() { /* ... */ }
    ```
    Next.js automatically handles merging metadata from different levels.
# What is the role of `page.js` in the `app` directory?
    **A:** `page.js` (or `page.tsx`) is the file that defines the UI unique to a specific route segment. It's the component that will be rendered for that URL path.
# How are search parameters (`?key=value`) accessed in `app` directory components?
    **A:** Page components receive a `searchParams` prop.
    ```jsx
    // app/search/page.js
    export default function SearchPage({ searchParams }) {
      // For URL /search?query=nextjs
      // searchParams will be { query: 'nextjs' }
      const query = searchParams.query;
      return <p>Searching for: {query}</p>;
    }
    ```
# How would you migrate a data fetching pattern from `getServerSideProps` (pages dir) to the `app` dir?
    **A:**
    *   The page component in the `app` directory becomes an `async` Server Component.
    *   The data fetching logic from `getServerSideProps` is moved directly into this `async` component using `await fetch(...)`.
    *   The `cache: 'no-store'` option for `fetch` mimics SSR behavior.
    *   Route parameters are accessed via the `params` prop, and search params via `searchParams` prop.
# How does error handling differ between a Server Component and a Client Component in the `app` dir?
    **A:**
    *   **Server Components:** Errors during data fetching or rendering on the server will be caught by the nearest `error.js` boundary above them in the tree. You can use `try/catch` within the Server Component for more granular handling.
    *   **Client Components:** Standard React error handling applies (e.g., `try/catch` in event handlers, effects). Rendering errors are also caught by `error.js`. The `error.js` file itself must be a Client Component.
# What is the `'use client'` directive and why is it important?
    **A:** The `'use client'` directive is placed at the top of a file. It marks that module and all its imported dependencies (that don't also have `'use server'`) as part of the client-side JavaScript bundle. This is necessary for components that use React state, effects, browser-only APIs, or event listeners. It creates a boundary between Server Components and Client Components.
# How can Server Components pass data to Client Components that they render?
    **A:** Server Components can pass data to Client Components via standard React props. The props must be serializable (i.e., can be converted to JSON), as they are sent from the server to the client. Functions or complex objects like Dates might need special handling or cannot be passed directly.
# What are some key benefits of React Server Components that drive their adoption in Next.js?
     **A:**
     *   **Reduced Client-Side JavaScript:** Only interactive parts (Client Components) send JS to the browser, leading to smaller bundles and faster TTI.
     *   **Direct Data Access:** Server Components can directly access databases, file systems, or internal services without needing to create intermediate API layers.
     *   **Improved Performance:** Rendering closer to the data source, and potential for better caching strategies.
     *   **Zero-Bundle Size Components:** Components that render static content or fetch data without interactivity don't add to the client bundle.
     *   **Enhanced Security:** Sensitive data fetching logic and keys can remain on the server.

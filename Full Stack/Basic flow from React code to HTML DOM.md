
**What is JSX compiled into?** `React.createElement()` calls, which return plain JS objects describing the UI — the virtual DOM.

---

**How do nested components compile?** Each component becomes a nested `createElement` call mirroring the JSX tree structure.

---

**How does React know whether to render a real DOM element or call a component function?** The type argument to `createElement` — a lowercase string like `"div"` means native HTML element, an uppercase function reference like `List` means call it and keep resolving.

---

**When are `{}` expressions inside JSX evaluated?** Immediately when the component function runs, as part of that render.

---

**Who calls `React.createElement`?** Your component functions do — they're just functions that return `createElement` calls. The reconciler calls your component functions, gets back elements, and keeps walking the tree until everything resolves to primitive HTML elements.

---

**Full chain of events from `npm run dev` to DOM:**

1. Vite reads config, finds entry point (`main.jsx`)
2. Traverses all imports, compiles `.jsx` files → plain JS (`createElement` calls) using Babel/esbuild
3. Bundles everything including `react` and `react-dom` into one JS file
4. Browser loads `index.html`, downloads and runs the bundle
5. `main.jsx` runs, calls `createRoot(...).render(<App />)`
6. This kicks off the React reconciler
7. Reconciler calls `App`, gets elements back, calls nested components, keeps going
8. Bottoms out at lowercase HTML elements, hands tree to ReactDOM
9. ReactDOM writes to the real DOM
10. After that, state changes trigger reconciler to re-run affected components and make minimal DOM updates

---

**Who does the JSX → JS compilation, Vite or React?** Vite (using Babel or esbuild) at build time. React reconciler only runs at runtime and never sees JSX — only compiled `createElement` calls. Completely separate concerns.

---

**How does Vite know to compile JSX?** File extension — `.jsx` or `.tsx` signals Vite to run the JSX transformer.

---

**How does Vite know to use a string vs function reference in `createElement`?** It checks the case of the tag name in JSX. Lowercase → string `"div"`, uppercase → function reference `List`. This is why components must start with uppercase — it affects both compilation and runtime behavior.

---

**Where does the reconciler live — is it external?** It's part of the `react-dom` package, bundled into your app by Vite. It runs in the browser like all other code — not a separate process.
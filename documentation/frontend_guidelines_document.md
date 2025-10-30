# Frontend Guideline Document

## 1. Frontend Architecture

**Framework and Libraries**
- **Next.js (App Router)**: Provides file-based routing, server-side rendering (SSR), static site generation (SSG) and API routes in one framework.  
- **React & TypeScript**: Enables a component-driven UI with static typing, reducing runtime errors and improving developer productivity.  
- **Tailwind CSS v4**: A utility-first CSS framework that accelerates styling and enforces consistency.  
- **shadcn/ui**: A curated set of accessible, headless UI components built on top of Tailwind CSS.  
- **Better Auth**: Handles secure sign-up, sign-in and session management.  

**Supporting Scalability, Maintainability, Performance**
- **Modular File Structure**: Separates pages (`/app`), components (`/components/ui`), utilities (`/lib`), and data schemas (`/db`), making it easy to locate and update code.  
- **Type Safety**: TypeScript and Drizzle ORM ensure compile-time checks for both frontend props and backend data models.  
- **Built-in Code Splitting**: Next.js automatically splits JavaScript bundles per route.  
- **API Routes**: Internal API endpoints under `/app/api` let you grow server logic without a separate backend.  
- **Containerization**: Docker ensures consistent environments from development to production, reducing "it works on my machine" issues.  

## 2. Design Principles

**Usability**
- Simple, clear interfaces with minimal distractions  
- Consistent button styles, form fields, error messages and feedback loops

**Accessibility**
- Semantic HTML and ARIA attributes in all components  
- Keyboard navigability across forms, modals and menus  
- Contrast ratios that meet WCAG AA standards

**Responsiveness**
- Mobile-first breakpoints cover common screen sizes (sm, md, lg, xl)  
- Flexible layouts using CSS grid and flexbox utilities  
- Touch-friendly touch targets and spacing

**Applying the Principles**
- All `shadcn/ui` components include default focus states and aria labels.  
- Design tokens (colors, spacing, typography) live centrally in `tailwind.config.js`, ensuring consistency.  
- Layouts use fluid containers and percentage widths to adapt to any viewport.  

## 3. Styling and Theming

**Styling Approach**
- **Utility-first with Tailwind CSS**: Write classes in JSX (`className="p-4 bg-primary"`) instead of custom CSS files.  
- **BEM-in-utility**: Tailwind’s naming (`bg-primary`, `text-secondary`) follows a predictable pattern similar to BEM.  

**Theming**
- **Light & Dark Modes**: Controlled via CSS media query or explicit toggle stored in local storage.  
- **Centralized Theme Configuration**: Colors and typography defined in `tailwind.config.js`.  

**Visual Style**
- **Design Style**: Modern flat design with subtle shadows and rounded corners.  
- **Glassmorphism Accent**: Occasional frosted-glass effect on modals and overlays (using `backdrop-filter`).

**Color Palette**
- Primary: #4F46E5 (indigo-600)  
- Secondary: #10B981 (emerald-500)  
- Accent: #F59E0B (amber-500)  
- Background Light: #FFFFFF  
- Background Dark: #111827  
- Text Light: #1F2937  
- Text Dark: #F9FAFB

**Typography**
- **Font Family**: Inter, fallbacks: system-ui, -apple-system, BlinkMacSystemFont  
- **Headings**: `font-semibold` with responsive sizes (e.g., `text-2xl md:text-3xl`).  
- **Body**: `font-normal` at `text-base` or `text-sm` for secondary information.

## 4. Component Structure

**Organization and Reuse**
- **`/components/ui`**: Houses all shared UI elements (Button, Input, Card).  
- **`/app/(protected)`**: Group of protected pages that share a dashboard layout (Header, Sidebar, Content).  
- **Feature Folders**: When a new feature grows, group its pages, hooks, and sub-components together.

**Benefits of Component-Based Architecture**
- **Reusability**: Write once, use everywhere—buttons and inputs maintain consistent behavior and style.  
- **Isolation**: Components encapsulate their markup, styles and logic, reducing side effects.  
- **Ease of Testing**: Small, focused units simplify writing unit tests.  
- **Scalability**: Teams can work on separate components or features without merge conflicts.

## 5. State Management

**Approach**
- **React Hooks**: `useState`, `useEffect` for local component state.  
- **Context API**: Provides global access to session and theme state (`AuthContext`, `ThemeContext`).  
- **Server Components & Fetching**: Leverages Next.js Server Components for data fetching—no need to manage loading states in many cases.

**Sharing State**
- Authentication status stored in a React context, accessed by any component needing user info.  
- Form state localized in page components and passed to API routes with `fetch`.

## 6. Routing and Navigation

**Routing Library**
- **Next.js App Router**: File-based routing under `/app`. Folders with `layout.tsx` define nested layouts.  

**Navigation Structure**
- **Public Routes**: `/sign-in`, `/sign-up`, `/verify` (if built in Next.js).  
- **Protected Routes**: Wrapped under a `ProtectedLayout`, only accessible after authentication.  
- **Dynamic Routes**: Use `[slug]` or `[...all]` for catch-all API handlers.

**User Flow**
1. Visitor lands on public page (e.g., verification form).  
2. Submits form → client-side JS calls `/app/api/auth` or `/verify` endpoint.  
3. On success, Next.js redirects to dashboard at `/dashboard`.

## 7. Performance Optimization

**Built-in Next.js Features**
- **SSR/SSG**: Choose server rendering or static generation per page for optimal load times.  
- **Image Optimization**: `<Image>` component automates resizing, lazy loading and modern formats.  

**Additional Strategies**
- **Code Splitting**: Use dynamic `import()` for heavy dependencies (e.g., charts).  
- **Lazy Loading**: Load non-critical components only when needed (e.g., modals).  
- **PurgeCSS**: Tailwind removes unused styles in production builds.  
- **Asset Compression**: Brotli/Gzip via Vercel or custom server.

## 8. Testing and Quality Assurance

**Unit Testing**
- **Jest** + **React Testing Library** for components, hooks and utility functions.  
- Mock API calls and context providers to isolate units.

**Integration Testing**
- Test page flows (sign-in → dashboard) using React Testing Library’s `render(<App />)` or a Next.js testing utility.

**End-to-End Testing**
- **Cypress** or **Playwright** for full UI workflows (form submission, navigation, error states).  

**Linting & Formatting**
- **ESLint** with a shared config for React, TypeScript and Next.js  
- **Prettier** for consistent code style  
- **husky** + **lint-staged** to run checks on pre-commit

## 9. Conclusion and Overall Frontend Summary

This guideline outlines a clear, scalable frontend setup that balances modern UX, strong developer ergonomics and high performance. By leveraging Next.js, TypeScript and Tailwind CSS, the project achieves:
- **Maintainability** through a modular file structure and type safety.  
- **Accessibility & Usability** via semantic components and adherence to WCAG guidelines.  
- **Performance** through built-in SSR/SSG, code splitting and image optimization.  
- **Consistency** with centralized theming, reusable UI components and enforced linting.

Unique aspects such as shadcn/ui’s headless components, Better Auth integration and an end-to-end Docker workflow differentiate this starter template. Following these guidelines ensures that anyone—even without a deep technical background—can understand, extend and maintain the frontend codebase with confidence.
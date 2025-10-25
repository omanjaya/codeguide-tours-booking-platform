# Frontend Guideline Document

This document explains, in everyday language, how the frontend of the **codeguide-tours-booking-platform** is put together. It covers the architecture, design principles, styling, components, state management, routing, performance, testing, and more. By the end, you’ll have a clear understanding of how everything fits and why.

---

## 1. Frontend Architecture

### 1.1 Overview
We’re using **Next.js 14** with the **App Router**. That gives us server-side rendering (SSR), static site generation (SSG) and dynamic client components all in one. React sits at the core for building interactive pieces, and **TypeScript** adds type safety to catch bugs early.

Alongside React/Next.js, we use:
- **Tailwind CSS** for utility-first styling.
- **shadcn/ui** (built on Radix UI) for accessible, pre-built components.
- **Framer Motion** for smooth, declarative animations.
- **React Query (TanStack Query)** for fetching and caching data from our backend.
- **Clerk** for user authentication.
- **Supabase** (as a temporary backend) for database and serverless functions.
- **Stripe** for payments.
- **OpenAI** to support future AI-powered features.

### 1.2 Scalability, Maintainability & Performance
- **Scalability:** Next.js routes and React Query hooks make it easy to add new pages and data sources. As you grow, you can swap Supabase for your own microservices without rewriting the UI.
- **Maintainability:** TypeScript plus clear folder structure (app/, components/, utils/, hooks/) helps new developers get up to speed.
- **Performance:** Automatic code splitting in Next.js, caching in React Query, image optimization, and SSR/SSG ensure the site loads fast.

---

## 2. Design Principles

### 2.1 Usability & Accessibility
- We follow **WCAG guidelines**: color contrast, keyboard navigation, and ARIA labels on custom components.
- shadcn/ui’s Radix base ensures accessible defaults.

### 2.2 Responsiveness
- All layouts use **mobile-first breakpoints** in Tailwind to look great from phones to desktops.
- Flexbox and CSS grid handle fluid layouts.

### 2.3 Consistency & Clarity
- Reusable components (buttons, inputs, cards) share consistent spacing, typography, and interaction patterns.
- Clear error messages, form hints, and loading states guide the user.

---

## 3. Styling and Theming

### 3.1 Styling Approach
- We use **Tailwind CSS** with a custom theme in `tailwind.config.js`.
- For complex cases, we layer on **BEM-style class names** when needed (e.g., `card__header`, `card__body`).

### 3.2 Theming
- Light and dark modes are supported via the `theme` plugin in Tailwind.
- A top-level Context Provider toggles the theme and persists the choice in localStorage.

### 3.3 Visual Style
- Overall style: **Modern flat design** with occasional **glassmorphism** (frosted, semi-transparent cards) for feature sections.

### 3.4 Color Palette
- **Primary Blue:** #0070f3
- **Secondary Orange:** #FF7A59
- **Success Green:** #28A745
- **Warning Yellow:** #FFC107
- **Error Red:** #DC3545
- **Background Light:** #F9FAFB
- **Background Dark:** #1F2937
- **Text Primary:** #111827
- **Text Secondary:** #6B7280

### 3.5 Typography
- **Font Family:** Inter, with system-font fallback.
- **Headings:** 600 weight.
- **Body Text:** 400 weight, 16px base.

---

## 4. Component Structure

### 4.1 Organization
- **app/**: Main Next.js folder for pages, layouts, and API routes.
- **components/**: Shared UI bits.
  - **ui/**: shadcn/ui primitives extended for our needs.
  - **feature-specific**: search bar, booking form, calendar.
- **hooks/**: Custom React hooks (e.g., `useBooking`, `useAuth`).
- **utils/**: API clients and helpers (`utils/supabase/`, `utils/stripe/`, `utils/api-client/`).
- **styles/**: Global CSS or Tailwind overrides.

### 4.2 Reusability
- Each component lives in its own folder with its `.tsx` file, styles, and tests.
- We follow a **“single responsibility”** rule: a component does one job well.

---

## 5. State Management

### 5.1 Server State
- **React Query** handles fetching, caching, and updating data from Supabase now and from your own APIs later.
- Queries and mutations live alongside their hooks in `hooks/`.

### 5.2 Client State
- **React Context** for theme, auth, and other global settings.
- Future plan: **Zustand** for complex state like multi-step booking flows.

### 5.3 Why It Works
- Decoupling UI from data fetching makes swapping backends painless.
- Caching and refetching policies in React Query keep UI snappy.

---

## 6. Routing and Navigation

### 6.1 Next.js App Router
- Routes map directly to files under **app/**.
- Layouts handle shared UI (navbars, footers).
- Nested routes support tabbed interfaces and sub-sections.

### 6.2 Navigation Structure
- **Public pages:** home, search, tour details.
- **Protected pages:** user profile, bookings, operator dashboard (protected by Clerk). 
- We use Next.js middleware with Clerk to guard routes based on roles.

---

## 7. Performance Optimization

### 7.1 Lazy Loading & Code Splitting
- Dynamic imports (`next/dynamic`) for heavy components (maps, calendars).
- Automatic splitting by Next.js for each page.

### 7.2 Asset Optimization
- Next/Image for responsive, optimized images.
- CDN delivery via Vercel (or your host).

### 7.3 Caching
- React Query caching for server data.
- Browser caching for static assets with proper headers.

---

## 8. Testing and Quality Assurance

### 8.1 Unit Tests
- **Jest** + **React Testing Library** for components and hooks.
- Snapshot tests for UI consistency.

### 8.2 Integration Tests
- Test pages with mocked API responses.

### 8.3 End-to-End Tests
- **Cypress** (or Playwright) to cover user flows: search → select → booking.

### 8.4 Accessibility & Performance Audits
- **Axe** and **Lighthouse** integrated into CI.

---

## 9. Conclusion and Overall Frontend Summary

This frontend setup combines the power of Next.js, React Query, Tailwind CSS, and shadcn/ui to deliver a **scalable**, **maintainable**, and **high-performance** tours booking platform. Key takeaways:

- A **modular architecture** that grows with your microservices backend.
- **Clear design principles** ensuring usability and accessibility.
- A **consistent look and feel** powered by Tailwind theming and a modern flat style.
- **Robust state management** and routing with React Query and Next.js.
- **Performance best practices** like lazy loading and caching.
- **Comprehensive testing** for reliability and quality.

With this guide, anyone on your team can understand and extend the frontend, ensuring a smooth development process as you build out the full “GetYourGuide Clone.”

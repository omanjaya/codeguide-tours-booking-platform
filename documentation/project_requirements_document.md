# Project Requirements Document (PRD)

## 1. Project Overview

This project is a web-based tours booking platform modeled closely on GetYourGuide. Travelers can browse, search, and book guided tours and experiences around the world. Tour operators can register, list their offerings, manage availability, and track bookings. Under the hood, the frontend is built with Next.js (App Router) and React, while the backend follows a microservices approach (NestJS or Laravel) exposed via a unified API Gateway.

We are building this platform to provide a scalable, performant, and extensible foundation for bookings and payments. Our goals are to deliver a polished user experience (search, calendar availability, secure checkout), enable tour operators to manage offerings easily, and ensure the system can grow—both in traffic and in features (AI recommendations, advanced analytics) without needing major rewrites.

### Success Criteria
- A fully functional minimum-viable product (MVP) where users can sign up, search tours, view details, and complete Stripe-processed bookings.
- A supplier dashboard for adding/editing tours, monitoring availability, and viewing bookings.
- Page load times under 2 seconds, search responses under 200 ms, and reliable payment webhooks.
- Code architecture that cleanly separates frontend components and backend microservices, allowing future features to be slotted in with minimal friction.

---

## 2. In-Scope vs. Out-of-Scope

### In-Scope (Version 1.0)
- User authentication and profile management (powered by Clerk).
- Public catalog of tours: browse, filter (location, date, group size), and paginate.
- Tour detail pages with images, descriptions, pricing, and availability calendar.
- Booking flow: select a date, fill in traveler info, process payment via Stripe, confirmation email.
- Supplier dashboard: sign up as operator, create and edit tours, view booking list.
- Basic AI-powered ‘recommended tours’ based on user search/query (using OpenAI GPT-4).
- Stripe webhook listener (BFF pattern) that records successful payments in the booking service.
- Admin panel (read-only) to view all users, bookings, and tours.
- CI/CD pipeline: automated tests (unit, integration, E2E) and deployments via GitHub Actions.

### Out-of-Scope (Planned for Later Phases)
- Multi-language support (i18n) beyond English.
- Reviews, ratings, and user comments on tours.
- In-app messaging between travelers and operators.
- Mobile-native apps (React Native or SwiftUI).
- Advanced analytics dashboards for operators.
- Real-time notifications (webhooks > WebSockets).
- Loyalty programs, gift cards, or voucher codes.

---

## 3. User Flow

A new traveler lands on the homepage and sees a hero search bar. They enter destination, travel dates, and number of guests, then click ‘Search’. The system shows a list of matching tours with thumbnails, short descriptions, and prices. The traveler filters results by rating or price, clicks a tour to view detailed info (images gallery, full description, calendar availability). They choose an available date, enter traveler details, and proceed to payment. After Stripe payment is successful, they see a confirmation page and receive an email with booking details.

A tour operator visits the platform, clicks ‘Become a Host’, and signs up through Clerk’s registration flow. Once logged in, they land on their supplier dashboard showing their current tours and upcoming bookings. From here they click ‘Add New Tour’, fill out title, description, images, pricing, and availability slots. They save the new tour, which immediately appears in the public catalog. The operator can return at any time to edit tour details or view a list of paid bookings.

---

## 4. Core Features

- **Authentication & User Management**: Clerk-powered sign up/in, social logins, email verification, password reset, roles (traveler vs. operator).
- **Tour Catalog & Search**: Full-text search, faceted filters (location, date range, party size), pagination, React Query for data fetching and caching.
- **Tour Detail Page**: Image carousel, rich text description, pricing tiers, dynamic availability calendar, ‘Book Now’ CTA.
- **Booking & Payment**: Multi-step form (traveler info, guest count), Stripe Checkout integration, secure handling of payment data, redirect to confirmation.
- **Stripe Webhooks**: BFF endpoint that listens for payment success/failure, updates booking status in the booking microservice, triggers confirmation email.
- **Supplier (Operator) Dashboard**: CRUD interface for tours, availability management, view bookings, export booking data (CSV).
- **Admin Panel (Read-Only)**: Global view of users, tours, bookings; search and filter across entities.
- **AI Recommendations**: On tour detail pages and search results, suggest relevant tours or add-ons using OpenAI GPT-4 prompts.
- **CI/CD & Testing**: Unit tests (Jest/RTL), integration tests, E2E tests (Cypress), GitHub Actions pipeline with lint, build, test, deploy.

---

## 5. Tech Stack & Tools

**Frontend**
- Next.js 14 (App Router) & React 18
- TypeScript for type safety
- Tailwind CSS, shadcn/ui & Radix UI for design system
- Framer Motion for animations
- React Query (TanStack Query) for server-state management

**Backend**
- Microservices framework: NestJS (Node.js/TypeScript) or Laravel (PHP)
- API Gateway (e.g., API Gateway in AWS or Kong) for routing to services
- PostgreSQL for transactional data, Elasticsearch for search indexing
- Stripe for payment processing and webhooks
- Clerk for authentication service

**AI & Integrations**
- OpenAI GPT-4 via openai npm SDK for recommendations
- Potential for Vector DB (e.g., Pinecone) in future phases

**Tooling & Workflow**
- GitHub Actions for CI/CD
- Jest & React Testing Library for unit/integration tests
- Cypress for end-to-end tests
- Sentry or LogRocket for error monitoring
- VS Code with Windsurf & Cursor plugins for dev productivity

---

## 6. Non-Functional Requirements

- **Performance**: First contentful paint <2 s, TTFB <500 ms, search API <200 ms under typical load.
- **Scalability**: Stateless microservices, horizontal scaling with container orchestration (e.g., Kubernetes or ECS).
- **Security**: HTTPS everywhere, OWASP Top 10 mitigation, input validation (Zod), CSRF protection, Stripe PCI DSS compliance.
- **Accessibility**: WCAG 2.1 AA compliance, semantic HTML, keyboard navigation, ARIA attributes where needed.
- **Reliability**: 99.9% uptime SLA, automatic retries for transient failures, health-check endpoints.
- **Maintainability**: ESLint, Prettier, type-safe contracts (OpenAPI codegen for frontend clients).

---

## 7. Constraints & Assumptions

- We assume access to Stripe and OpenAI GPT-4 APIs with sufficient rate limits.
- Elasticsearch cluster is available for indexing tours; otherwise search falls back to database.
- Clerk authentication service is online and configured with required OAuth providers.
- Environment variables (.env) for API keys, DB connections, and third-party secrets are securely stored.
- React Query hooks abstract data source so swapping from Supabase to microservices is a simple endpoint change.
- API Gateway routes must be defined before frontend integration to avoid CORS or endpoint mismatches.

---

## 8. Known Issues & Potential Pitfalls

- **API Contract Drift**: Without a shared OpenAPI/Swagger spec, frontend and backend models can get out of sync. Mitigate by generating client types from the API schema.
- **Stripe Webhook Testing Locally**: Local dev can’t receive webhooks without tunneling (ngrok). Document the setup in README.
- **Elasticsearch Indexing Delays**: Newly created tours may not appear instantly in search. Use a short polling delay or notify users of indexing time.
- **Rate Limits (OpenAI, Stripe)**: Implement exponential backoff and fallback UI for degraded AI features.
- **CORS & Authentication Tokens**: Ensure the API Gateway is configured with correct CORS headers and JWT verification to prevent 401/403 errors.

This PRD provides a clear, unambiguous blueprint for the AI model (and developers) to generate all necessary technical artifacts—file structures, API specs, UI guidelines, and CI/CD configurations—without missing any critical detail.
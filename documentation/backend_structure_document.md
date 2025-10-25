# Backend Structure Document

This document outlines the backend setup for the tours booking platform. It covers the overall architecture, databases, APIs, hosting, infrastructure, security, monitoring, and maintenance. The goal is to provide a clear, step-by-step overview for both technical and non-technical readers.

## 1. Backend Architecture

The backend evolves in two phases:

1.  **MVP Phase (Serverless with Supabase):**
    -  Uses Supabase (PostgreSQL + serverless functions) as a quick, managed backend.
    -  Clerk handles authentication and user management.
    -  Stripe webhooks run in Next.js API routes as a simple Backend-for-Frontend (BFF) layer.

2.  **Microservices Phase (Custom Services):**
    -  Services built in NestJS or Laravel, each in its own container or serverless function.
    -  An **API Gateway** (e.g., AWS API Gateway or Kong) routes requests to individual services:
       •  Auth Service
       •  Tours Service
       •  Booking Service
       •  Inventory Service
       •  Payment Service
       •  Search Service
    -  **Event bus** (RabbitMQ or Kafka) connects payment webhooks to booking workflows.
    -  **BFF Layer:** Next.js API routes continue to aggregate data as needed for the frontend.

How it supports key goals:
-  **Scalability:** Each microservice scales independently based on load.
-  **Maintainability:** Clear separation of concerns makes updates, testing, and debugging easier.
-  **Performance:** Lightweight services communicate over fast protocols; critical paths can be cached.

## 2. Database Management

**Technologies Used:**
-  PostgreSQL (initially via Supabase, later via managed RDS)
-  Elasticsearch for advanced search indexing
-  Redis for caching frequent queries and session data
-  (Optional) MongoDB for unstructured data, if needed by specific services

**Data Practices:**
-  **ORM & Migrations:** Use Prisma (for Node/NestJS) or Eloquent (for Laravel) to define models and run schema migrations.
-  **Backups & Replication:** Enable nightly backups and a read-replica for analytics or search indexing.
-  **Connection Pooling:** Use PgBouncer or a managed pool to optimize database connections.
-  **Data Access Patterns:** Each service connects only to its own database schemas or tables to minimize cross-service coupling.

## 3. Database Schema

Below is a simplified, human-readable view of our relational schema. Tables represent core concepts: Users, Tours, Bookings, and Payments.

**Users**
-  user_id (PK)
-  email
-  hashed_password
-  full_name
-  role (traveler or operator)
-  created_at, updated_at

**Tours**
-  tour_id (PK)
-  operator_id (FK → Users)
-  title, description
-  location
-  price_per_person
-  total_capacity
-  created_at, updated_at

**Bookings**
-  booking_id (PK)
-  tour_id (FK → Tours)
-  user_id (FK → Users)
-  seats_booked
-  status (pending, confirmed, canceled)
-  created_at, updated_at

**Payments**
-  payment_id (PK)
-  booking_id (FK → Bookings)
-  stripe_charge_id
-  amount
-  currency
-  status (succeeded, failed)
-  created_at, updated_at

**Search Index** (in Elasticsearch)
-  index: `tours`
-  fields: title, location, price_per_person, availability, tags

**SQL Definition Example (PostgreSQL)**
```sql
CREATE TABLE users (
  user_id UUID PRIMARY KEY,
  email TEXT UNIQUE NOT NULL,
  hashed_password TEXT,
  full_name TEXT NOT NULL,
  role TEXT NOT NULL,
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE tours (
  tour_id UUID PRIMARY KEY,
  operator_id UUID REFERENCES users(user_id),
  title TEXT NOT NULL,
  description TEXT,
  location TEXT,
  price_per_person NUMERIC(10,2),
  total_capacity INT,
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE bookings (
  booking_id UUID PRIMARY KEY,
  tour_id UUID REFERENCES tours(tour_id),
  user_id UUID REFERENCES users(user_id),
  seats_booked INT,
  status TEXT,
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE payments (
  payment_id UUID PRIMARY KEY,
  booking_id UUID REFERENCES bookings(booking_id),
  stripe_charge_id TEXT,
  amount NUMERIC(10,2),
  currency TEXT,
  status TEXT,
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);
```  

## 4. API Design and Endpoints

We follow a RESTful approach, versioned under `/api/v1/`. Key endpoints include:

**Authentication (Auth Service)**
-  POST `/api/v1/auth/register` → Create account
-  POST `/api/v1/auth/login` → Issue JWT
-  POST `/api/v1/auth/logout` → Revoke token
-  GET `/api/v1/auth/me` → Get current user profile

**Tours (Tours Service)**
-  GET `/api/v1/tours` → List or search tours
-  GET `/api/v1/tours/{tour_id}` → Tour details
-  POST `/api/v1/tours` → Create new tour (operator only)
-  PUT `/api/v1/tours/{tour_id}` → Update tour
-  DELETE `/api/v1/tours/{tour_id}` → Remove tour

**Bookings (Booking Service)**
-  POST `/api/v1/bookings` → Create booking
-  GET `/api/v1/bookings/{booking_id}` → Booking details
-  PUT `/api/v1/bookings/{booking_id}/cancel` → Cancel booking

**Payments (Payment Service)**
-  POST `/api/v1/payments/checkout` → Start Stripe checkout
-  POST `/api/v1/webhooks/stripe` → Handle Stripe events

**Search (Search Service)**
-  GET `/api/v1/search/tours?q={query}` → Full-text search

All endpoints require an `Authorization: Bearer <token>` header (except public GETs). Responses use standard HTTP status codes and JSON bodies.

## 5. Hosting Solutions

**Cloud Provider:** AWS (preferred) or GCP/Azure

**Components:**
-  **API Gateway:** AWS API Gateway or Kong for routing and auth validation
-  **Containers:** AWS ECS or EKS (Kubernetes) for microservices
-  **Serverless (Initial Phase):** Supabase functions and Next.js API routes on Vercel or Netlify
-  **Databases:** RDS for PostgreSQL, ElastiCache for Redis, Elasticsearch Service

**Benefits:**
-  **Reliability:** Managed services with multi-AZ failover
-  **Scalability:** Auto-scaling at service and database levels
-  **Cost-effectiveness:** Pay-as-you-go and the ability to right-size resources

## 6. Infrastructure Components

-  **Load Balancer:** AWS ALB distributes traffic across service instances
-  **API Gateway:** Central entry point for routing, throttling, and authentication
-  **Caching:** Redis for session storage and query results
-  **CDN:** CloudFront (or equivalent) to cache static assets and speed up API responses
-  **Message Broker:** RabbitMQ or Kafka for event-driven workflows (payment → booking)
-  **Search Cluster:** Elasticsearch cluster for faceted and full-text search

These components work together to handle high traffic, reduce latency, and keep the user experience fast and responsive.

## 7. Security Measures

-  **Authentication:** JWT tokens issued by Auth Service (Clerk in MVP)
-  **Authorization:** Role-based checks in each service (traveler vs. operator)
-  **Encryption:** TLS for in-transit data; AES-256 encryption at rest in databases
-  **Secret Management:** AWS Secrets Manager or Vault for API keys, DB credentials
-  **Input Validation & Sanitization:** Use Zod (Node) or built-in validators (Laravel)
-  **Rate Limiting & Throttling:** Enforced at API Gateway to prevent abuse
-  **Compliance:** Stripe integration follows PCI-DSS standards; GDPR data handling practices

## 8. Monitoring and Maintenance

-  **Logging:** Centralized logs in ELK stack (Elasticsearch, Logstash, Kibana) or AWS CloudWatch Logs
-  **Metrics & Alerts:** Prometheus + Grafana or AWS CloudWatch Metrics with threshold-based alarms
-  **Error Tracking:** Sentry or Rollbar to capture unhandled exceptions
-  **Health Checks:** Kubernetes or ECS health probes with auto-restart on failure
-  **Maintenance Strategy:**
    •  Regular dependency updates and security patching
    •  Automated backups and restore drills
    •  Blue/Green or Canary deployments for zero-downtime releases

## 9. Conclusion and Overall Backend Summary

The backend for this tours booking platform starts with a **serverless MVP** using Supabase, Clerk, and Stripe, then transitions to a **robust microservices architecture**. Key highlights:

-  **Modular Services:** Independent microservices for auth, tours, bookings, payments, and search.
-  **Scalable Data Layers:** PostgreSQL for core data, Elasticsearch for search, Redis for caching.
-  **Secure & Compliant:** JWT auth, role-based access, encryption, PCI-compliant payment handling.
-  **High Performance:** Caching layers, CDN, load balancing, and auto-scaling.
-  **Event-Driven Workflows:** Reliable message broker for asynchronous tasks.
-  **Comprehensive Monitoring:** Centralized logging, metrics, and alerts to ensure health and uptime.

This setup meets the goals of **scalability, maintainability, and performance**, while providing a clear path for phased development from a quick MVP to a production-grade microservices system.
# Business Requirements Document (BRD)

## Project Overview
**Project Name:** E-Commerce Full-Stack Web Application (Restore)  
**Description:** A robust full-stack e-commerce web application featuring an ASP.NET Core Web API backend and a React (TypeScript) frontend. The platform provides comprehensive online shopping capabilities including product catalog management, detailed item inspection, shopping basket management, and robust error handling.

---

## 1. Executive Summary & Objectives
The objective of this project is to deliver a scalable, responsive, and secure e-commerce application. Key goals include:
- Providing an intuitive user experience for browsing, searching, and purchasing products.
- Establishing a reliable, high-performance RESTful API backend using ASP.NET Core 8 and Entity Framework Core with SQLite.
- Ensuring strong client-side type safety and modern UI/UX using React and TypeScript.
- Implementing robust error handling, validation, and session tracking.

---

## 2. Scope of Functionality

### 2.1 Backend API Features (`API/`)
- **Product Management (`ProductsController`):**
  - Retrieve a complete list of available products in the catalog.
  - Retrieve detailed information for a specific product by ID (`GET /api/products/{id}`).
- **Shopping Cart / Basket Management (`BasketController`):**
  - Retrieve the current user's shopping basket via cookie-based buyer identification (`GET /api/basket`).
  - Add items to the basket with specified quantities (`POST /api/basket?productId={id}&quantity={qty}`).
  - Automatically generate and persist `buyerId` cookies for anonymous and persistent cart sessions.
- **Error Handling & Diagnostics (`BuggyController` & Middleware):**
  - Global exception middleware (`ExceptionMiddleware`) to gracefully handle server errors and return structured problem details.
  - Test endpoints for validating BadRequest, Unauthorized, NotFound, ServerError, and validation errors.

### 2.2 Frontend Client Features (`client/src/`)
- **Routing & Navigation (`Routes.tsx`, `Header.tsx`, `App.tsx`):**
  - React Router configuration supporting seamless navigation across Home, Catalog, Product Details, About, Contact, and Error pages.
  - Responsive navigation header with active link highlighting and cart summary indicators.
- **Catalog Feature (`features/catalog/`):**
  - Grid-based product listing (`ProductList`, `ProductCard`) with rich visual cards.
  - Detailed product view (`ProductDetails`) displaying description, brand, type, stock quantity, and "Add to Basket" actions.
- **Static & Informational Pages:**
  - Home Page (`HomePage`) featuring promotional banners/carousels.
  - About Page (`AboutPage`) for company/project background and error testing triggers.
  - Contact Page (`ContactPage`) for customer support inquiries.
- **Error & Loading States:**
  - Dedicated NotFound (404) and ServerError (500) fallback components.
  - Loading indicators (`LoadingComponent`) for asynchronous data fetching.

---

## 3. System Architecture & Technical Stack

### 3.1 Architecture Diagram / Component Flow
```
[ React + TypeScript Client ] 
          │  (HTTP / Axios)
          ▼
[ ASP.NET Core 8 Web API ] 
          │  (Entity Framework Core)
          ▼
[ SQLite Database (store.db) ]
```

### 3.2 Technology Stack
- **Backend:** C#, .NET 8, ASP.NET Core Web API, Entity Framework Core, SQLite.
- **Frontend:** React, TypeScript, React Router, Axios, Material UI / custom CSS.
- **Development & Testing Tools:** Swagger / OpenAPI, EF Core Migrations.

---

## 4. User Personas & Workflows

### 4.1 Shopper / Customer
1. **Browsing Products:** Navigates to the catalog, views product cards with images, pricing, and titles.
2. **Viewing Product Details:** Clicks a product to inspect full specifications, description, stock status, and brand/type.
3. **Managing Shopping Basket:** Adds items to the cart, updates quantities, and reviews basket subtotal.
4. **Navigating Site:** Uses top navigation bar to switch between Home, Catalog, About, and Contact pages.

### 4.2 System Administrator / Developer
1. **API Exploration:** Uses Swagger UI (`/swagger`) to inspect endpoints, test request/response payloads, and verify database state.
2. **Error Testing:** Triggers simulated exceptions via the Buggy API controller to verify middleware and error boundary behavior.

---

## 5. Non-Functional Requirements
- **Performance:** Fast API response times with efficient database querying using EF Core.
- **Reliability:** Global exception handling ensuring graceful recovery and descriptive error responses.
- **Maintainability:** Modular architecture separating Controllers, DTOs, Entities, and React feature modules.
- **Security:** Secure cookie management for guest/buyer session tracking (`buyerId`).

---

## 6. Future Enhancements
- User authentication and authorization with JWT tokens and registration/login workflows.
- Secure payment integration using Stripe.
- Order history tracking and checkout workflow completion.
- Advanced product search, filtering, and pagination.

# Product Requirements Document (PRD): ReStore E-Commerce Application

## 1. Document Overview & Metadata
- **Project Name:** ReStore Full-Stack E-Commerce Platform
- **Document Version:** 1.0.0
- **Status:** Approved / Active
- **Target Audience:** Product Managers, Developers, QA Engineers, Stakeholders
- **Base References:** Business Requirements Document ([Business_Requirements_Document.md](/docs/Business_Requirements_Document.md)), Backend API codebase (`API/`), and Frontend Client codebase (`client/src/`).

---

## 2. Introduction & Product Vision
ReStore is a modern, high-performance, full-stack e-commerce web application built using ASP.NET Core 8 Web API on the backend and React with TypeScript on the frontend. The platform aims to provide a seamless online shopping experience, featuring intuitive product catalog browsing, detailed item inspection, persistent shopping cart/basket management, and comprehensive error handling and diagnostics.

---

## 3. Product Scope & Functional Requirements

### 3.1 Backend API Module (`API/`)
The ASP.NET Core 8 Web API backend provides secure, robust RESTful endpoints backed by Entity Framework Core and SQLite.

#### 3.1.1 Product Catalog Management (`ProductsController`)
- **Get All Products (`GET /api/products`):**
  - Returns a list of all products available in the store catalog.
  - Returns HTTP 200 OK with JSON array of `Product` entities.
- **Get Product Details (`GET /api/products/{id}`):**
  - Accepts a product ID (`int`).
  - Returns HTTP 200 OK with product details if found.
  - Returns HTTP 404 Not Found if the product does not exist.

#### 3.1.2 Shopping Basket Management (`BasketController`)
- **Retrieve Basket (`GET /api/basket`):**
  - Identifies the user/session via an encrypted HTTP cookie (`buyerId`).
  - Returns HTTP 200 OK with `BasketDto` including item breakdown (ID, Name, Price, PictureUrl, Type, Brand, Quantity) and subtotal.
  - Returns HTTP 404 Not Found if no active basket is associated with the cookie.
- **Add Item to Basket (`POST /api/basket?productId={id}&quantity={qty}`):**
  - Adds a specified quantity of a product to the user's basket.
  - Automatically generates a unique `buyerId` and sets a 30-day essential cookie if one does not exist.
  - Returns HTTP 201 Created on successful addition.
  - Returns HTTP 400 Bad Request or HTTP 404 Not Found if validation or product lookup fails.
- **Remove Item from Basket (`DELETE /api/basket`):** *(Endpoint placeholder / extensible for quantity adjustment)*

#### 3.1.3 Error Handling & Diagnostics (`BuggyController` & Middleware)
- **Global Exception Middleware (`ExceptionMiddleware`):**
  - Intercepts unhandled exceptions in development/production, logging errors and returning standard ASP.NET Problem Details JSON responses with status 500.
- **Testing Endpoints (`BuggyController`):**
  - `/api/buggy/bad-request`: Tests `BadRequest` responses.
  - `/api/buggy/unauthorised`: Tests `Unauthorized` responses.
  - `/api/buggy/not-found`: Tests `NotFound` responses.
  - `/api/buggy/server-error`: Tests unhandled exception handling.
  - `/api/buggy/validation-error`: Tests ModelState validation error handling.

---

### 3.2 Frontend Client Module (`client/src/`)
The React application is built with TypeScript, React Router, Axios, and Material-UI styling.

#### 3.2.1 Navigation & Layout (`App.tsx`, `Header.tsx`, `Routes.tsx`)
- **Responsive Navigation Bar:**
  - Displays brand logo, links to Catalog, About, and Contact pages, and error testing triggers.
  - Displays shopping cart summary badge.
- **Routing:**
  - Client-side routing with React Router supporting `/`, `/catalog`, `/catalog/:id`, `/about`, `/contact`, `/server-error`, and wildcard 404 NotFound routing.

#### 3.2.2 Catalog Feature (`features/catalog/`)
- **Product Listing (`Catalog`, `ProductList`, `ProductCard`):**
  - Asynchronously fetches and displays products in a responsive grid.
  - Each card shows product image, title, price, brand, and type.
- **Product Details (`ProductDetails`):**
  - Detailed view showing full description, brand, type, available stock, and quantity input with "Add to Basket" action.

#### 3.2.3 Static & Informational Pages
- **Home Page (`HomePage`):** Landing page with hero image carousel/banners.
- **About Page (`AboutPage`):** Informational page containing buttons to test various API error codes (400, 401, 404, 500, validation errors) for QA and error boundary validation.
- **Contact Page (`ContactPage`):** Customer support inquiries placeholder.

#### 3.2.4 Error & State Management
- **Axios Interceptors (`agent.ts`):**
  - Handles response errors globally, showing toast notifications for 400/401 errors, redirecting to the server error page for 500 errors, and flattening model validation state errors.
- **Loading Component (`LoadingComponent`):** Displays a global loading overlay during asynchronous API calls.

---

## 4. System Architecture & Data Model

### 4.1 Architecture Stack
```
┌────────────────────────────────────────────────────────┐
│               React + TypeScript Client                │
│     (React Router, Axios, Toastify, Material UI)       │
└──────────────────────────┬─────────────────────────────┘
                           │ HTTP / JSON (CORS Enabled)
                           ▼
┌────────────────────────────────────────────────────────┐
│                ASP.NET Core 8 Web API                  │
│       (Controllers, DTOs, Exception Middleware)        │
└──────────────────────────┬─────────────────────────────┘
                           │ Entity Framework Core
                           ▼
┌────────────────────────────────────────────────────────┐
│                 SQLite Database (store.db)             │
│            (Products, Baskets, BasketItems)            │
└────────────────────────────────────────────────────────┘
```

### 4.2 Core Data Entities
1. **Product:**
   - `Id` (int, PK)
   - `Name` (string)
   - `Description` (string)
   - `Price` (long / decimal representation)
   - `PictureUrl` (string)
   - `Type` (string)
   - `Brand` (string)
   - `QuantityInStock` (int)
2. **Basket:**
   - `Id` (int, PK)
   - `BuyerId` (string)
   - `Items` (List<BasketItem>)
3. **BasketItem:**
   - `Id` (int, PK)
   - `Quantity` (int)
   - `ProductId` (int, FK)
   - `Product` (Product)
   - `BasketId` (int, FK)
   - `Basket` (Basket)

---

## 5. Non-Functional Requirements (NFRs)

- **Performance & Scalability:**
  - Async/await pattern used across all controller actions and database queries to maximize throughput.
  - Efficient lazy/eager loading via Entity Framework Core (`.Include()` and `.ThenInclude()`).
- **Security:**
  - CORS configured securely to allow credentials and communication with the trusted frontend origin (`http://localhost:3000` / `3001`).
  - Secure cookie handling (`buyerId` with `IsEssential = true` and 30-day expiration).
- **Reliability & Error Handling:**
  - Standardized Problem Details responses for client consumption.
  - Graceful toast notifications and error boundary redirection on the client.
- **Maintainability:**
  - Clear separation of concerns (Controllers, DTOs, Entities, Data, Middleware, and Client Feature Modules).

---

## 6. Future Roadmap & Enhancements
1. **User Authentication & Authorization:** Implement ASP.NET Core Identity with JWT tokens for user registration, login, and profile management.
2. **Checkout & Payment Integration:** Add checkout workflow and payment processing via Stripe.
3. **Order Management:** Track customer orders, order statuses, and order history.
4. **Advanced Search & Filtering:** Add pagination, sorting, search queries, and product filtering by brand and type on the catalog page.

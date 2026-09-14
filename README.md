# STC Student Portal

A modern, highly optimized single-page React portal designed for students appearing for placement drives.

The platform combines placement analytics, verified placement reports, mock online assessment practice, and an administrative control center into a single student-focused portal.

---

## Overview

The STC Student Portal provides students with a centralized platform to explore placement-related information, review experiences shared by placed seniors, practice online assessment questions, and access relevant placement data efficiently.

The application also includes an administrative interface for managing placement reports, companies, mock questions, and admin authorizations.

---

## Key Features

### Interactive Dashboard

The dashboard provides:

* Dynamic analytics
* Recruiter statistics
* Tier-based company distribution
* Real-time news ticker

This gives students a centralized view of placement-related information.

### Verified Placement Reports

Students can access detailed placement reports submitted by placed seniors.

Each report can contain:

* Interview rounds
* CTC information
* Eligibility criteria
* Placement experience details

Submitted reports are reviewed by administrators before being made available.

### Mock OA Practice

The portal provides online assessment practice with:

* Real online assessment questions
* Instant evaluation
* Feedback
* Answer revelation

This allows students to practice and evaluate their performance.

### Admin Control Center

Administrators can manage important portal content through the admin panel.

Available controls include:

* Approving placement reports
* Rejecting placement reports
* Adding companies
* Submitting new mock questions
* Managing admin authorizations

### Premium User Interface

The interface focuses on a modern and polished visual experience with:

* Custom dark and light modes
* Premium typography
* Glassmorphism card layouts
* Subtle animations

---

# Caching Architecture

The application uses a four-layer caching architecture designed to reduce unnecessary data requests and improve navigation performance.

```text
                    STC Student Portal
                           |
                           v
                  Layer 1: Zustand
                  In-Memory Cache
                           |
                           v
                 Layer 2: localStorage
                    Local Cache
                           |
                           v
                Layer 3: Service Worker
                 Progressive Cache
                           |
                           v
                Layer 4: Supabase
                  Backend / Source
                    of Truth
```

## Layer 1 — Zustand

Zustand acts as the in-memory cache.

Its purpose is to provide instant navigation between screens during the same browser session.

**Technology:** Zustand

---

## Layer 2 — localStorage

The second layer stores application data locally using browser `localStorage`.

It provides:

* Full company directories
* Placement reports
* Mock questions
* Infinite TTL local caching
* Automatic invalidation on schema/version mismatch

This reduces the need to repeatedly retrieve the same data.

---

## Layer 3 — Service Worker

The service worker provides progressive caching by intercepting network requests.

It uses a stale-while-revalidate approach:

1. Cached resources can be served immediately.
2. The service worker requests updated resources in the background.
3. The cache can then be synchronized with newer resources.

**File:** `public/sw.js`

---

## Layer 4 — Supabase Backend

Supabase acts as the backend and source of truth.

The application requests data from Supabase when:

* Lower cache levels do not contain the required data
* Data invalidation is triggered

Supabase provides:

* PostgreSQL database
* Google OAuth authentication

---

# Data Flow

The application follows the following general data retrieval flow:

```text
User Request
     |
     v
Zustand Cache
     |
     | Cache Miss
     v
localStorage
     |
     | Cache Miss / Invalid Data
     v
Service Worker
     |
     | Required Data Not Available
     v
Supabase Backend
     |
     v
Updated Data
     |
     v
Application
```

This layered approach allows frequently accessed information to remain available closer to the application while Supabase remains the backend source of truth.

---

# Authentication

Authentication is handled through Supabase.

**Authentication Provider:**

* Google OAuth

Authentication-related functionality is implemented through:

```text
src/hooks/useAuth.js
```

and the Supabase configuration is maintained in:

```text
src/lib/supabase.js
```

---

# Database

The application uses Supabase PostgreSQL as its database.

Supabase functions as the backend source of truth for the portal's data.

The application retrieves information from the backend when required after checking the available cache layers.

---

# Technology Stack

| Category          | Technology                    |
| ----------------- | ----------------------------- |
| Frontend          | React 17                      |
| Styling           | Vanilla CSS inside JavaScript |
| Styling Compiler  | Sass                          |
| State Management  | Zustand                       |
| Database          | Supabase PostgreSQL           |
| Authentication    | Supabase Google OAuth         |
| Progressive Cache | Service Worker                |
| Local Cache       | localStorage                  |
| In-Memory Cache   | Zustand                       |
| Backend           | Supabase                      |
| Hosting / Serving | Serve                         |

---

# Project Structure

```text
user-code-main/
├── public/
│   ├── index.html
│   ├── sw.js
│   └── site.webmanifest
│
├── src/
│   ├── components/
│   │   └── AdminPanel.jsx
│   │
│   ├── hooks/
│   │   ├── useAuth.js
│   │   ├── useDirectory.js
│   │   ├── useQuestions.js
│   │   └── useReport.js
│   │
│   ├── lib/
│   │   ├── localCache.js
│   │   └── supabase.js
│   │
│   ├── store/
│   │   └── portalStore.js
│   │
│   ├── STC_KIET_Portal.jsx
│   ├── index.js
│   ├── index.scss
│   └── _common.scss
│
├── .env
├── .gitignore
├── package.json
└── README.md
```

---

# Directory Breakdown

## `public/`

Contains public application files and the service worker.

### `index.html`

Main HTML entry point for the React application.

### `sw.js`

Service worker responsible for the Layer-3 progressive caching architecture.

### `site.webmanifest`

Web application manifest file.

---

## `src/components/`

Contains reusable application components.

### `AdminPanel.jsx`

Provides the administrative control interface.

---

## `src/hooks/`

Contains application-specific React hooks.

### `useAuth.js`

Handles authentication-related functionality.

### `useDirectory.js`

Handles company directory data.

### `useQuestions.js`

Handles mock assessment question data.

### `useReport.js`

Handles placement report data.

---

## `src/lib/`

Contains supporting application utilities and external service configuration.

### `localCache.js`

Handles Layer-2 local caching through `localStorage`.

### `supabase.js`

Contains the Supabase configuration and connection.

---

## `src/store/`

Contains application state management.

### `portalStore.js`

Defines the Zustand store used for Layer-1 in-memory caching and application state.

---

## Main Application Files

### `STC_KIET_Portal.jsx`

Main React portal application.

### `index.js`

React application entry point.

### `index.scss`

Main stylesheet.

### `_common.scss`

Shared Sass styles.

---

# Local Development

## 1. Environment Configuration

Create a `.env` file in the project root.

```env
REACT_APP_SUPABASE_URL=your_supabase_url
REACT_APP_SUPABASE_ANON_KEY=your_supabase_anon_key
```

Replace the placeholder values with the corresponding Supabase project credentials.

---

## 2. Install Dependencies

Run:

```bash
npm install
```

---

## 3. Start the Application

Run:

```bash
npm start
```

---

## 4. Create a Production Build

Run:

```bash
npm run build
```

---

# Application Architecture

The application is organized around several primary areas:

```text
STC Student Portal
│
├── Student Dashboard
│   ├── Analytics
│   ├── Recruiter Statistics
│   ├── Company Distribution
│   └── News Ticker
│
├── Placement Reports
│   ├── Interview Rounds
│   ├── CTC
│   └── Eligibility Criteria
│
├── Mock OA Practice
│   ├── Questions
│   ├── Evaluation
│   ├── Feedback
│   └── Answers
│
└── Admin Control Center
    ├── Report Management
    ├── Company Management
    ├── Question Management
    └── Admin Authorization
```

---

# Performance Approach

The portal uses multiple caching layers to reduce unnecessary backend requests and improve the overall navigation experience.

The caching hierarchy is:

```text
Zustand
   ↓
localStorage
   ↓
Service Worker
   ↓
Supabase
```

Each layer has a specific responsibility:

| Layer   | Purpose                                                  |
| ------- | -------------------------------------------------------- |
| Layer 1 | Instant in-memory access during the browser session      |
| Layer 2 | Persistent local caching through localStorage            |
| Layer 3 | Progressive network-level caching through Service Worker |
| Layer 4 | Backend data and source of truth through Supabase        |

---

# Core Objectives

The STC Student Portal is designed to provide students with a centralized placement preparation and information platform.

Its primary objectives are:

* Provide placement-related analytics
* Provide verified placement reports
* Allow students to practice online assessment questions
* Provide instant evaluation and feedback
* Centralize company-related placement information
* Provide administrative controls for managing portal content
* Improve navigation through a multi-layer caching architecture

---

# Project Information

**Project:** STC Student Portal

**Application Type:** Student Placement Portal

**Frontend:** React 17

**Database:** Supabase PostgreSQL

**Authentication:** Google OAuth through Supabase

**State Management:** Zustand

**Caching:** Zustand, localStorage, Service Worker

**Serving:** Serve

---

# Summary

The STC Student Portal brings together placement analytics, verified placement reports, mock OA practice, and administrative management in a single React-based application.

Its four-layer caching architecture combines Zustand, localStorage, Service Worker caching, and Supabase to provide a structured approach to data access and application performance.

The result is a student-focused placement portal with a modern interface, centralized placement information, practice functionality, and dedicated administrative controls.

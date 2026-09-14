# 🎓 STC Student Portal

> A modern, highly optimized single-page React portal designed for students appearing for placement drives.

**STC Student Portal** brings placement preparation resources into one centralized platform, combining verified company placement experiences, round-by-round interview information, mock Online Assessment practice, placement analytics, and an administrative management center.

---

## 📌 Overview

Preparing for campus placements often requires students to collect information about companies, eligibility criteria, CTC, interview rounds, Online Assessments, and previous placement experiences.

The **STC Student Portal** provides a centralized platform where this placement-related information can be accessed and managed through a modern single-page application.

The platform consists of two primary sides:

* **Student-facing placement and preparation features**
* **Administrator controls for managing portal content**

The application is built with a strong focus on performance through a **four-layer caching architecture**, designed to provide fast navigation, reduce unnecessary Supabase reads, and support offline accessibility.

---

# ✨ Features

## 📊 Interactive Dashboard

The dashboard provides students with an overview of placement-related information through dynamic data and analytics.

### Includes

* Dynamic placement analytics
* Recruiter statistics
* Tier-based company distribution
* Real-time news ticker
* Centralized placement information

The dashboard acts as the primary entry point for students accessing the portal.

---

## 🏢 Verified Placement Reports

The portal provides detailed placement experiences submitted by placed seniors and reviewed by administrators.

Instead of providing only basic company information, placement reports contain a structured breakdown of the recruitment process.

### Report Information

Depending on the report, students can access:

* Company information
* CTC
* Eligibility criteria
* Number of interview rounds
* Round-by-round interview logs
* Interview experiences
* Placement-related details

### Verification Flow

```text
Placement Experience Submitted
              │
              ▼
        Admin Review
          /       \
       Reject     Approve
         │           │
         ▼           ▼
    Not Published   Available
                    to Students
```

This administrative review process allows submitted placement experiences to be reviewed before becoming available to students.

---

# 🧠 Mock Online Assessment Practice

The portal includes a dedicated **Mock OA Practice** feature for placement preparation.

Students can practice Online Assessment questions and receive immediate results.

### Features

* Online Assessment questions
* Interactive question practice
* Instant evaluation
* Feedback
* Answer revelation
* Cached question bank

The question system is integrated with the application's multi-layer caching architecture to provide fast access to previously loaded questions.

---

# 🛠️ Admin Control Center

The **Admin Control Center** provides administrative functionality for managing portal content.

Administrators can:

### Placement Reports

* Review submitted reports
* Approve reports
* Reject reports

### Companies

* Add companies
* Maintain company-related information

### Mock OA

* Submit new mock questions
* Manage the question content used for practice

### Administrator Authorization

* Manage administrator authorizations

The administrative functionality provides centralized control over the information displayed to students.

---

# 🎨 Premium User Interface

The portal uses a modern visual design rather than a traditional static portal interface.

### Design Features

* Dark mode
* Light mode
* Premium typography
* Glassmorphism card layouts
* Subtle animations
* Modern dashboard presentation
* Interactive placement-focused interface

The interface is designed to keep placement information organized while maintaining a polished visual experience.

---

# ⚡ Four-Layer Caching Architecture

Performance is a core part of the application's architecture.

The portal uses **four caching layers** between the application and the Supabase backend.

```text
┌─────────────────────────────┐
│      React Application      │
└──────────────┬──────────────┘
               │
               ▼
┌─────────────────────────────┐
│ Layer 1                     │
│ Zustand Memory Cache        │
└──────────────┬──────────────┘
               │ Cache Miss
               ▼
┌─────────────────────────────┐
│ Layer 2                     │
│ localStorage Cache          │
└──────────────┬──────────────┘
               │ Cache Miss
               ▼
┌─────────────────────────────┐
│ Layer 3                     │
│ Service Worker Cache        │
└──────────────┬──────────────┘
               │
               ▼
┌─────────────────────────────┐
│ Layer 4                     │
│ Supabase Backend            │
│ Source of Truth             │
└─────────────────────────────┘
```

The four layers are designed to provide:

* Fast page and data access
* Reduced repeated Supabase read calls
* Better navigation performance
* Offline accessibility for cached resources
* Persistent local data between page refreshes

---

## 1️⃣ Layer 1 — Zustand Memory Cache

The first caching layer uses **Zustand**.

This is an in-memory cache available while the application is running.

### Purpose

* Instant access to already-loaded data
* Fast navigation between screens
* Avoid repeated data retrieval during the same browser session
* Centralized application state

The Zustand store is located at:

```text
src/store/portalStore.js
```

---

## 2️⃣ Layer 2 — localStorage Cache

The second layer uses the browser's **localStorage**.

The application stores cached data locally, including:

* Full company directories
* Placement reports
* Mock OA questions

The local cache uses a schema/version mechanism.

When a schema or version mismatch occurs, the existing cached data can be invalidated so that the application can obtain updated data.

The local cache implementation is located at:

```text
src/lib/localCache.js
```

---

## 3️⃣ Layer 3 — Service Worker

The third layer uses a **Service Worker** to intercept network requests and serve cached resources.

The Service Worker follows a **stale-while-revalidate** approach.

Conceptually:

```text
Network Request
       │
       ▼
Cached Resource Available?
      / \
    Yes  No
     │    │
     ▼    ▼
 Return  Network
 Cached   Request
 Resource    │
             ▼
       Update Cache
```

The Service Worker is located at:

```text
public/sw.js
```

This layer helps provide cached resource access and offline capability.

---

## 4️⃣ Layer 4 — Supabase Backend

**Supabase** acts as the backend and source of truth.

The application requests data from Supabase when:

* Lower cache layers do not contain the required data
* Cache invalidation requires fresh data

Supabase provides:

* PostgreSQL database
* Authentication
* Backend data access

The Supabase client is initialized in:

```text
src/lib/supabase.js
```

---

# 🔄 Data Flow

The portal follows a cache-first approach for frequently accessed data.

For example, when accessing company directory data:

```text
Student requests company data
             │
             ▼
      Zustand Cache?
        /         \
      YES          NO
       │            │
       ▼            ▼
  Return Data   localStorage?
                  /       \
                YES        NO
                 │          │
                 ▼          ▼
            Return Data   Network
                              │
                              ▼
                          Supabase
                              │
                              ▼
                       Store in Cache
                              │
                              ▼
                         Return Data
```

The same four-layer caching approach is used for:

* Company directory and statistics
* Mock OA questions
* Placement reports

---

# 🔐 Authentication

Authentication is handled using **Supabase Authentication**.

The portal uses:

* Supabase Auth
* Google OAuth
* Session handling

Authentication and session-related logic is contained in:

```text
src/hooks/useAuth.js
```

---

# 🗄️ Database

The project uses **Supabase PostgreSQL** as its database.

Supabase serves as the persistent backend data source for the portal.

The application communicates with Supabase through its configured client:

```text
src/lib/supabase.js
```

The lower cache layers are used to reduce repeated reads from the backend while Supabase remains the source of truth.

---

# 🧩 Technology Stack

| Technology                | Purpose                                   |
| ------------------------- | ----------------------------------------- |
| **React 17**              | Frontend single-page application          |
| **Vanilla CSS inside JS** | Injected layout design                    |
| **Sass / SCSS**           | Styling and shared styles                 |
| **Zustand**               | State management and Layer-1 memory cache |
| **Supabase**              | PostgreSQL database and backend           |
| **Supabase Auth**         | Authentication                            |
| **Google OAuth**          | Authentication provider                   |
| **localStorage**          | Layer-2 persistent cache                  |
| **Service Worker**        | Layer-3 progressive/network caching       |
| **Serve**                 | Static Node server                        |

---

# 📁 Project Structure

```text
user-code-main/
│
├── public/
│   ├── index.html
│   ├── sw.js
│   └── site.webmanifest
│
├── src/
│   │
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

# 🗂️ Directory Breakdown

## `public/`

Contains the application's public/static resources.

### `index.html`

Main HTML entry point of the React application.

### `sw.js`

Service Worker responsible for Layer-3 caching.

### `site.webmanifest`

Contains web application manifest metadata.

---

## `src/components/`

Contains reusable application components.

### `AdminPanel.jsx`

Contains the controls and interface for the administrative management center.

---

## `src/hooks/`

Contains application-specific React hooks.

### `useAuth.js`

Handles:

* Authentication checks
* Session handling

### `useDirectory.js`

Handles:

* Company directory
* Statistics
* Four-layer caching

### `useQuestions.js`

Handles:

* Mock OA questions
* Four-layer caching

### `useReport.js`

Handles:

* Placement reports
* Four-layer caching

---

## `src/lib/`

Contains supporting application libraries.

### `localCache.js`

Provides the localStorage cache wrapper.

### `supabase.js`

Initializes the Supabase database client.

---

## `src/store/`

Contains global application state.

### `portalStore.js`

Contains the global Zustand memory store used as the first cache layer.

---

## Main Application

### `STC_KIET_Portal.jsx`

The main client application component containing the primary UI and sub-components.

### `index.js`

React application entry point.

### `index.scss`

Base application styles.

### `_common.scss`

Shared SCSS variables.

---

# 📋 Local Development

## Prerequisites

The project requires:

* Node.js
* npm
* A configured Supabase project

---

## 1. Configure Environment Variables

Create a `.env` file in the root directory.

```env
REACT_APP_SUPABASE_URL=your_supabase_url
REACT_APP_SUPABASE_ANON_KEY=your_supabase_anon_key
```

Example structure:

```env
REACT_APP_SUPABASE_URL=your_supabase_url
REACT_APP_SUPABASE_ANON_KEY=your_supabase_anon_key
```

The `.env` file contains the Supabase configuration used by the application.

---

## 2. Install Dependencies

From the project root, run:

```bash
npm install
```

---

## 3. Start the Application

Run:

```bash
npm start
```

This starts the React application for local development.

---

## 4. Build for Production

Create the production build using:

```bash
npm run build
```

---

# 🔒 Environment Configuration

The project uses environment variables for the Supabase configuration.

The environment file is:

```text
.env
```

Required variables:

```text
REACT_APP_SUPABASE_URL
REACT_APP_SUPABASE_ANON_KEY
```

The `.env` file should remain outside the committed source code when configured as an ignored environment file.

---

# 🧭 Application Architecture

The overall application can be represented as:

```text
                    STC STUDENT PORTAL
                           │
          ┌────────────────┴────────────────┐
          │                                 │
          ▼                                 ▼
   Student Features                  Admin Features
          │                                 │
   ┌──────┼───────┐                 ┌───────┼────────┐
   │      │       │                 │       │        │
   ▼      ▼       ▼                 ▼       ▼        ▼
Dashboard Reports  Mock OA        Reports Companies Questions
   │      │       │                 │       │        │
   └──────┴───────┘                 └───────┴────────┘
          │                                 │
          └──────────────┬──────────────────┘
                         ▼
                 Caching Architecture
                         │
        ┌────────────────┼────────────────┐
        ▼                ▼                ▼
     Zustand        localStorage    Service Worker
        │                │                │
        └────────────────┼────────────────┘
                         ▼
                    Supabase
                         │
                         ▼
                 PostgreSQL + Auth
```

---

# 🎯 Core Objectives

The portal focuses on three major areas:

### 1. Placement Information

Provide structured company and placement information to students.

### 2. Placement Preparation

Provide verified placement experiences and mock Online Assessment practice.

### 3. Content Management

Provide administrators with centralized controls for reviewing and managing placement-related content.

---

# 📈 Performance Approach

The application is designed around minimizing unnecessary repeated data retrieval.

Frequently accessed information can be available through the lower caching layers before the application needs to request it from Supabase.

The architecture therefore follows:

```text
Fastest
   │
   ▼
Zustand
   │
   ▼
localStorage
   │
   ▼
Service Worker / Network Cache
   │
   ▼
Supabase
   │
   ▼
Source of Truth
```

This four-layer approach is used to support:

* Instant access to cached information
* Persistent browser-side data
* Cached network resources
* Reduced Supabase read calls
* Offline accessibility

---

# 📝 Summary

**STC Student Portal** is a single-page React portal focused on placement-drive preparation.

It combines:

* 📊 Interactive placement analytics
* 🏢 Company placement information
* 📝 Verified placement experiences
* 🎯 Round-by-round interview logs
* 🧠 Mock Online Assessment practice
* 🛠️ Administrative management
* 🔐 Supabase authentication
* ⚡ Four-layer caching
* 🎨 Modern dark/light interface

The architecture combines **React, Zustand, localStorage, Service Worker caching, and Supabase** to create a centralized placement portal with a strong focus on fast data access and organized placement information.

---

## 📄 Project Information

**Project:** STC Student Portal
**Application Type:** Single-Page React Portal
**Primary Purpose:** Placement Drive Preparation
**Database:** Supabase PostgreSQL
**Authentication:** Supabase Auth + Google OAuth
**State Management:** Zustand
**Caching:** Zustand + localStorage + Service Worker + Supabase
**Styling:** CSS / SCSS
**Serving:** Serve

---

<p align="center">
  <strong>STC Student Portal</strong><br>
  Placement Information • Preparation • Management
</p>

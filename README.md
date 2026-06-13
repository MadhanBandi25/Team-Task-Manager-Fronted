# 🎨 Astra – Team Task Manager · Frontend

> A fully responsive, single-file vanilla HTML/CSS/JavaScript frontend for the Astra Team Task Manager. No build tools, no frameworks — just open in a browser and it works.

---

## 📋 Table of Contents

- [Overview](#-overview)
- [Tech Stack](#-tech-stack)
- [UI Architecture](#-ui-architecture)
- [Page & View Structure](#-page--view-structure)
- [Features](#-features)
- [API Integration](#-api-integration)
- [Authentication Flow (Frontend)](#-authentication-flow-frontend)
- [Theme System](#-theme-system)
- [Setup & Running](#-setup--running)
- [File Structure](#-file-structure)
- [Component Reference](#-component-reference)
- [Known Limitations & Future Improvements](#-known-limitations--future-improvements)

---

## 🌟 Overview

The Astra frontend is a **single HTML file** (`index.html`) that communicates with the Spring Boot backend over REST. It uses a page-based routing system built in vanilla JavaScript — no React, no Vue, no build step required.

```
┌─────────────────────────────────────────────────────┐
│               ASTRA FRONTEND (index.html)           │
│                                                     │
│  ┌──────────┐  ┌─────────┐  ┌────────────────────┐  │
│  │  HTML    │  │   CSS   │  │    JavaScript      │  │
│  │ Markup   │  │ (Inline)│  │    (Inline)        │  │
│  │ (Pages)  │  │ Theming │  │  API + Logic       │  │
│  └──────────┘  └─────────┘  └────────────────────┘  │
│                                                     │
│         Single file · No dependencies · No build    │
└─────────────────────────────────────────────────────┘
                          │
                          │  fetch() REST calls
                          ▼
              Spring Boot API (localhost:8080)
```

---

## 🛠 Tech Stack

| Layer | Technology |
|-------|-----------|
| Markup | HTML5 |
| Styling | CSS3 (CSS Variables, Flexbox, Grid) |
| Scripting | Vanilla JavaScript (ES6+) |
| Fonts | Google Fonts — Nunito, Plus Jakarta Sans |
| HTTP Client | Fetch API (native browser) |
| Storage | `localStorage` (token + user session) |
| No frameworks | Zero dependencies, zero build tools |

---

## 🏗 UI Architecture

### Page System

The frontend uses a **CSS display toggle** system for routing. Every top-level section is a `.page` div. Only the `.active` page is visible at any time.

```
body
 ├── #pg-signup        → Signup form
 ├── #pg-otp           → OTP verification
 ├── #pg-login         → Login form
 ├── #pg-forgot        → Forgot password
 ├── #pg-reset         → Reset password
 └── #pg-dashboard     → Main app shell
       ├── .sidebar    → Navigation sidebar
       └── .main
             ├── .topbar
             └── .content
                   ├── #view-dashboard   → Home overview
                   ├── #view-projects    → All projects table
                   ├── #view-tasks       → All tasks table
                   ├── #view-my-tasks    → Current user's tasks
                   ├── #view-activity    → Activity feed
                   ├── #view-members     → Admin: user management
                   ├── #view-deleted     → Admin: restore deleted items
                   └── #view-settings    → Profile & preferences
```

### State Management

All application state lives in plain JavaScript variables:

```javascript
let authToken = '';        // JWT token (also in localStorage)
let currentUser = null;    // Logged-in user object
let allProjects = [];      // Cached projects list
let allTasks = [];         // Cached tasks list
let allUsers = [];         // Cached users list (admin)
let allMyTasks = [];       // Cached personal tasks
let activityLog = [];      // In-memory activity feed
```

---

## 📄 Page & View Structure

### Auth Pages (pre-login)

| Page ID | Route equivalent | Purpose |
|---------|-----------------|---------|
| `#pg-signup` | `/signup` | New user registration |
| `#pg-otp` | `/verify` | Email OTP verification |
| `#pg-login` | `/login` | User login |
| `#pg-forgot` | `/forgot` | Request password reset OTP |
| `#pg-reset` | `/reset` | Enter OTP + new password |

All auth pages share the same two-panel card layout:
- **Left panel** — Purple gradient with brand illustration
- **Right panel** — White form area

### Dashboard Views (post-login)

| View ID | Sidebar Item | Purpose |
|---------|-------------|---------|
| `#view-dashboard` | 🏠 Dashboard | Stats, Kanban, quick lists |
| `#view-projects` | 📁 Projects | Full projects table with search |
| `#view-tasks` | ✅ Tasks | All tasks with filters |
| `#view-my-tasks` | 👤 My Tasks | Personal task list |
| `#view-activity` | 📊 Activity | In-session activity log |
| `#view-members` | 👥 All Users | Admin only — user grid |
| `#view-deleted` | 🗑️ Deleted Items | Admin only — restore items |
| `#view-settings` | ⚙️ Settings | Profile edit + preferences |

---

## ✨ Features

### Authentication
- **Signup** with name, email, password (with live password strength rules)
- **OTP verification** via 6-box visual input with 5-minute countdown timer
- **Login** with email + password, JWT stored in `localStorage`
- **Forgot Password** — sends OTP to email
- **Reset Password** — OTP + new password form
- **Logout** — clears token and resets all state

### Dashboard
- **Stats cards** — Projects count, Total tasks, In Progress, Completed
- **My Projects** list with progress bars and member avatars
- **Kanban board** — 4-column view (To Do / In Progress / Review / Completed)
- **My Tasks** preview with quick complete toggle
- **Global search** — instant dropdown results for tasks and projects

### Projects
- **Create project** via modal (name + description)
- **Edit project** (owner or admin only)
- **Delete project** (soft delete — owner or admin only)
- **Restore deleted project** (admin, from Deleted Items tab)
- **View project details** — members, tasks, progress in modal
- **Add/Remove project members** with role assignment (Member / Project Admin)
- **Progress bar** per project based on completed vs total tasks

### Tasks
- **Create task** — title, description, project, assignee, priority, due date
- **Edit task** — all fields including status
- **Delete task** (soft delete)
- **Restore deleted task**
- **Update status** via quick PATCH (done button, kanban, or task detail panel)
- **Task detail panel** — slides in from right with full info and status dropdown
- **Filters** — search by title/project/assignee + filter by status + filter by priority

### My Tasks
- Shows only tasks assigned to the logged-in user
- Quick "✓ Done" button per row
- Separate search + status filter

### Admin Features (ADMIN role only)
- **All Users** grid with role badges and delete button
- **Deleted Items** tabbed view for Projects, Tasks, Users
- **Restore** any soft-deleted entity

### Settings
- **Edit display name** (saved to `localStorage`)
- **Dark mode toggle**
- **Email / Desktop notification toggles** (UI only)

### UX Extras
- **Toast notifications** (success/error) for every action
- **Confirmation modal** before any destructive action
- **Loading spinners** while fetching data
- **Empty states** with icons when lists are empty
- **Dark / Light theme** with CSS variables, persisted in `localStorage`
- **Responsive grid** — collapses stats and kanban on smaller screens

---

## 🔌 API Integration

All API calls go through a single `api()` helper:

```javascript
async function api(method, path, body) {
  const opts = {
    method,
    headers: { 'Content-Type': 'application/json' }
  };
  if (authToken) opts.headers['Authorization'] = 'Bearer ' + authToken;
  if (body)      opts.body = JSON.stringify(body);

  const res  = await fetch(API + path, opts);
  const data = await res.json().catch(() => ({}));
  if (!res.ok) throw new Error(data.message || data.error || 'Request failed');
  return data;
}
```

### Base URL

```javascript
const API = 'http://localhost:8080/api';
```

To point at a different backend, change this one constant.

### Endpoints Used

| Action | Method | Path |
|--------|--------|------|
| Signup | POST | `/auth/signup` |
| Verify OTP | POST | `/auth/verify` |
| Login | POST | `/auth/login` |
| Forgot Password | POST | `/auth/forgot` |
| Reset Password | POST | `/auth/reset` |
| Get all projects | GET | `/projects` |
| Create project | POST | `/projects/{userId}` |
| Update project | PUT | `/projects/{id}` |
| Delete project | DELETE | `/projects/{id}` |
| Restore project | PUT | `/projects/restore/{id}` |
| Get project by ID | GET | `/projects/{id}` |
| Add member | POST | `/projects/{id}/members` |
| Remove member | DELETE | `/projects/{id}/members/{userId}` |
| Get all tasks | GET | `/tasks` |
| Get task by ID | GET | `/tasks/{id}` |
| Create task | POST | `/tasks/{userId}` |
| Update task | PUT | `/tasks/{id}` |
| Update task status | PATCH | `/tasks/{id}/status` |
| Delete task | DELETE | `/tasks/{id}` |
| Restore task | PUT | `/tasks/restore/{id}` |
| Get tasks by user | GET | `/tasks/user/{userId}` |
| Get all users | GET | `/users` |
| Delete user | DELETE | `/users/{id}` |
| Restore user | PUT | `/users/restore/{id}` |
| Dashboard stats | GET | `/dashboard` |

---

## 🔐 Authentication Flow (Frontend)

```
FIRST VISIT
    │
    ▼
Check localStorage for 'astra_token' + 'astra_user'
    │
    ├── Found → initDashboard() → go('pg-dashboard')
    │
    └── Not found → go('pg-signup')


SIGNUP
    │
    ├── POST /auth/signup
    ├── On success → save otpEmail → go('pg-otp') → start 5-min timer
    └── On error   → toast error message


OTP VERIFY
    │
    ├── POST /auth/verify
    ├── On success → go('pg-login')
    └── On error   → toast error message


LOGIN
    │
    ├── POST /auth/login
    ├── On success → save token + user to localStorage → initDashboard()
    └── On error   → toast error message


LOGOUT
    │
    ├── Clear localStorage ('astra_token', 'astra_user')
    ├── Reset all state variables
    └── go('pg-login')
```

### Token Usage

After login, every API call includes:
```
Authorization: Bearer eyJhbGciOiJIUzI1NiJ9...
```

The token is read from `authToken` (in-memory) and `localStorage.getItem('astra_token')` on page reload.

---

## 🎨 Theme System

The entire UI is driven by CSS custom properties (variables) defined on `:root`. Switching between light and dark mode is a single attribute change on `<html>`.

### Light Mode (default)

```css
:root {
  --p: #7C3AED;          /* primary purple */
  --bg: #F5F3FF;         /* page background */
  --surface: #fff;       /* card background */
  --text: #0F0A1E;       /* primary text */
  --muted: #7C7499;      /* secondary text */
  --border: #E5E7EB;     /* dividers */
  /* ... */
}
```

### Dark Mode

```css
[data-theme="dark"] {
  --bg: #0D0B1A;
  --surface: #17132B;
  --text: #F0EDFF;
  --muted: #6B6385;
  --border: #2D2650;
  /* ... */
}
```

### Toggling

```javascript
function applyTheme(dark) {
  document.documentElement.setAttribute('data-theme', dark ? 'dark' : 'light');
  localStorage.setItem('astra_dark', dark ? '1' : '0');
}
function toggleTheme() {
  applyTheme(document.documentElement.getAttribute('data-theme') !== 'dark');
}
```

Theme preference is persisted in `localStorage` under the key `astra_dark`.

---

## ⚙️ Setup & Running

### Prerequisites

```
✅ A modern browser (Chrome, Firefox, Edge, Safari)
✅ The Spring Boot backend running at localhost:8080
✅ A local HTTP server (VS Code Live Server, or similar)
```

> **Why a local server?** The browser blocks `fetch()` calls from `file://` URLs due to CORS. You need to serve the file over HTTP.

### Option 1: VS Code Live Server (Recommended)

1. Install the **Live Server** extension in VS Code
2. Right-click `index.html` → **Open with Live Server**
3. Browser opens at `http://127.0.0.1:5500`

### Option 2: Python HTTP Server

```bash
# Python 3
python -m http.server 5500

# Then open: http://localhost:5500
```

### Option 3: Node.js serve

```bash
npx serve . -p 5500
# Then open: http://localhost:5500
```

### Backend CORS

The Spring Boot backend is pre-configured to allow:
- `http://127.0.0.1:5500`
- `http://localhost:5500`

If you serve the frontend on a different port, add it to `SecurityConfig.java`:
```java
config.setAllowedOrigins(List.of(
    "http://127.0.0.1:5500",
    "http://localhost:5500",
    "http://localhost:YOUR_PORT"  // add here
));
```

---

## 📁 File Structure

```
frontend/
└── index.html          ← Entire frontend (HTML + CSS + JS in one file)
```

That's it. The entire application — all pages, styles, and logic — lives in a single file.

### Internal Structure of index.html

```
index.html
│
├── <head>
│   ├── Google Fonts (Nunito, Plus Jakarta Sans)
│   └── <style> ─────────────────────────────────── ~600 lines of CSS
│         ├── CSS Variables (light + dark theme)
│         ├── Base / reset styles
│         ├── Auth page styles (.auth-wrap, .auth-card, .auth-left, .auth-right)
│         ├── Dashboard layout (.sidebar, .main, .topbar, .content)
│         ├── Component styles (cards, tables, badges, modals, etc.)
│         └── Responsive breakpoints
│
├── <body>
│   ├── #toast                        ← Global toast notification
│   │
│   ├── Auth Pages
│   │   ├── #pg-signup
│   │   ├── #pg-otp
│   │   ├── #pg-login
│   │   ├── #pg-forgot
│   │   └── #pg-reset
│   │
│   ├── #pg-dashboard
│   │   ├── .sidebar
│   │   └── .main
│   │       ├── .topbar (+ #search-results dropdown)
│   │       └── .content
│   │           ├── #view-dashboard
│   │           ├── #view-projects
│   │           ├── #view-tasks
│   │           ├── #view-my-tasks
│   │           ├── #view-activity
│   │           ├── #view-members
│   │           ├── #view-deleted
│   │           └── #view-settings
│   │
│   ├── Task Detail Panel (#task-panel)
│   │
│   ├── Modals
│   │   ├── #modal-project         ← Create project
│   │   ├── #modal-edit-project    ← Edit project
│   │   ├── #modal-task            ← Create task
│   │   ├── #modal-edit-task       ← Edit task
│   │   ├── #modal-add-member      ← Add project member
│   │   ├── #modal-project-detail  ← View project details
│   │   └── #modal-confirm         ← Confirmation dialog
│   │
│   └── <script> ────────────────────────────────── ~700 lines of JS
│         ├── CONFIG & STATE         (constants, state vars)
│         ├── THEME                  (applyTheme, toggleTheme)
│         ├── INIT                   (window.onload)
│         ├── NAV                    (go, setNav, setNavByName)
│         ├── TOAST                  (toast)
│         ├── HELPERS                (toggleVis, chkRules, esc, statusBadge, ...)
│         ├── API                    (api)
│         ├── AUTH                   (doSignup, doLogin, doLogout, ...)
│         ├── DASHBOARD              (initDashboard, loadDashboardData, ...)
│         ├── ALL PROJECTS           (loadAllProjects, renderProjectsTable, ...)
│         ├── PROJECT DETAIL         (viewProjectDetail)
│         ├── ALL TASKS              (loadAllTasks, renderTasksTable, ...)
│         ├── MY TASKS               (loadMyTasks, renderMyTasksTable, ...)
│         ├── MEMBERS (Admin)        (loadMembers, renderMembersGrid, ...)
│         ├── DELETED ITEMS (Admin)  (loadDeletedItems, switchDeletedTab, ...)
│         ├── ACTIVITY               (renderActivity, addActivity, ...)
│         ├── SETTINGS               (populateSettings, saveProfile)
│         ├── TASK DETAIL PANEL      (openTaskPanel, closeTaskPanel, ...)
│         ├── CRUD PROJECTS          (createProject, updateProject, deleteProject, ...)
│         ├── CRUD TASKS             (createTask, updateTask, deleteTask, ...)
│         ├── PROJECT MEMBERS        (openAddMemberModal, addProjectMember, ...)
│         ├── POPULATE SELECTS       (populateProjectSelect, populateUserSelect)
│         ├── GLOBAL SEARCH          (globalSearch)
│         └── MODAL HELPERS          (openModal, closeModal)
```

---

## 🧩 Component Reference

### CSS Classes Quick Reference

| Class | Purpose |
|-------|---------|
| `.page` / `.page.active` | Page visibility toggle |
| `.auth-card` | Two-panel auth card wrapper |
| `.auth-left` / `.auth-right` | Auth card panels |
| `.sidebar` | Left navigation sidebar |
| `.main` | Main content area (right of sidebar) |
| `.topbar` | Top navigation bar |
| `.content` | Scrollable content area |
| `.stats-grid` | 4-column stats card grid |
| `.stat-card` | Individual stat card |
| `.kanban-grid` | 4-column Kanban board |
| `.kanban-col` | Single Kanban column |
| `.kanban-task` | Task card inside Kanban |
| `.two-col` | Two-column layout (projects + tasks) |
| `.card` | Generic white surface card |
| `.data-table` | Styled HTML table |
| `.modal-overlay` / `.modal-overlay.open` | Modal backdrop + visibility |
| `.modal` | Modal dialog box |
| `.detail-panel` / `.detail-panel.open` | Slide-in task detail panel |
| `.filter-bar` | Search + filter controls row |
| `.badge` | Inline status/role badge |
| `.action-btn` | Table row action button |
| `.tab-bar` / `.tab-btn` | Tab switcher (used in Deleted Items) |
| `.members-grid` | Responsive user card grid |
| `.toast` | Top-center notification |
| `.spin` / `.spin2` | CSS loading spinners |
| `.empty-state` | Centered empty placeholder |

### JavaScript Functions Quick Reference

| Function | Purpose |
|----------|---------|
| `go(pageId)` | Navigate to a `.page` by ID |
| `setNav(el, view)` | Switch dashboard view + update sidebar |
| `toast(msg, type)` | Show success/error notification |
| `api(method, path, body)` | Make an authenticated API call |
| `initDashboard()` | Bootstrap dashboard after login |
| `loadDashboardData()` | Fetch projects + tasks + stats |
| `openModal(id)` / `closeModal(id)` | Show/hide modals |
| `openTaskPanel(taskId)` | Load + open task detail panel |
| `confirm2(title, msg, cb)` | Show confirmation dialog |
| `globalSearch(q)` | Search projects + tasks inline |
| `toggleTheme()` | Switch light/dark mode |
| `addActivity(action, detail)` | Add entry to in-memory activity log |
| `renderKanban(tasks)` | Render 4-column Kanban board |
| `statusBadge(s)` / `priorityBadge(p)` | Return HTML badge string |
| `canEdit(project)` | Returns true if user can edit a project |
| `isAdmin()` | Returns true if logged-in user is ADMIN |

---

## ⚠️ Known Limitations & Future Improvements

### Current Limitations

| Area | Limitation |
|------|-----------|
| **Activity log** | In-memory only — resets on page refresh |
| **Deleted items** | Only tracks items deleted in the current session (not fetched from DB) |
| **Profile edit** | Only updates the name in `localStorage`; no API call to update the backend |
| **Social login** | Google, Apple, Facebook buttons are UI-only placeholders |
| **Notifications** | Bell icon and notification toggles are UI-only (no real push notifications) |
| **Offline** | No service worker or offline support |
| **Pagination** | All lists load in full — no pagination for large datasets |
| **Real-time** | No WebSocket; data is only fresh on page/view load |

### Suggested Improvements

- Add `PATCH /users/{id}` endpoint and wire up the Settings save button
- Persist deleted items by adding a `GET /projects?deleted=true` style endpoint
- Add pagination to tasks and projects tables
- Replace in-memory activity log with a backend `/api/activity` endpoint
- Add WebSocket support for real-time task updates across team members
- Extract CSS into a separate `styles.css` file as the project grows
- Add a proper router (hash-based or History API) for bookmarkable URLs

---

## 🌐 Browser Compatibility

| Browser | Support |
|---------|---------|
| Chrome 90+ | ✅ Full support |
| Firefox 88+ | ✅ Full support |
| Edge 90+ | ✅ Full support |
| Safari 14+ | ✅ Full support |
| IE 11 | ❌ Not supported (no ES6+) |

---

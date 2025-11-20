# 🎬 Call Sheet v1.0 - Production Management Platform

A professional production management platform for film and video teams to coordinate shoots, manage scenes, and track production progress with **real-time scene timers**.

![Status](https://img.shields.io/badge/status-active-success)
![Version](https://img.shields.io/badge/version-1.0.0-blue)

## ✨ Features

### 🔐 Authentication & User Management
- **JWT-based authentication** with secure password hashing (bcryptjs)
- **Role-based access control** with 3 roles:
  - **Admin**: Full system access, user management, create shows/scenes
  - **Assistant Director (AD)**: Manage scenes, control timers, view shows
  - **Crew**: View-only access to shows and scenes
- User signup/login flows
- Admin user management interface

### 🎞️ Show Management
- Create, view, update, and delete productions
- Track show status: Pre-Production, Shooting, Wrapped
- Detailed show information with start/end dates
- Scene count tracking per show
- Beautiful card-based interface

### 🎬 Scene Management with Real-Time Timers (⭐ Core Feature)
- **Real-time scene timer functionality** - the key differentiator
  - **Start/Stop/Pause/Resume** capabilities
  - **Live elapsed time display** in HH:MM:SS format
  - **Accurate time tracking** with millisecond precision
  - **Automatic duration calculation** when timer is stopped
  - **Timer state persistence** across page refreshes
  - **Visual indicators** for scene status:
    - 🔵 Gray = Unshot
    - 🟠 Orange = In Progress (timer may be running)
    - 🟢 Green = Complete (timer stopped, final duration recorded)
- Scene details including:
  - Scene number, title, description
  - Location and scheduled time
  - Notes and metadata
  - Assigned actors and crew (JSON fields)
- **Timer history logs** tracking all START, PAUSE, RESUME, STOP actions
- **Role-based permissions**: Only Admins and ADs can control timers

### 📊 Dashboard
- **Real-time production statistics**:
  - Total shows, active shows, wrapped shows
  - Total scenes, unshot, in progress, complete
  - Active timer count
- **Scene overview** with status breakdown
- **Active scene timers** widget showing currently running timers
- **Recent shows** quick access
- Role-appropriate dashboard views

### 🎨 Design
- **Cinematic dark mode theme** with professional aesthetics
- **Custom color palette**:
  - Gold (#eab308) for primary actions and branding
  - Blue for informational elements
  - Emerald for success states
  - Orange for in-progress indicators
- Fully responsive layout for mobile, tablet, and desktop
- Smooth animations and transitions
- Clean, modern interface with Tailwind CSS

## 🚀 Getting Started

### Prerequisites
- Node.js 18+ and Yarn
- PostgreSQL database (auto-provisioned in this environment)

### Installation

```bash
# Install dependencies
yarn install

# Push database schema
yarn db:push

# Seed database with test data
yarn db:seed
```

### Running the Application

#### Development Mode (Recommended)

```bash
# Start backend server (Terminal 1)
yarn dev:server

# Start frontend dev server (Terminal 2)
yarn dev:client

# Or run both concurrently
yarn dev
```

The application will be available at:
- **Frontend**: http://localhost:3000
- **Backend API**: http://localhost:3001
- **tRPC Endpoint**: http://localhost:3001/api/trpc

### Test Credentials

The application comes with pre-seeded demo data:

**Admin Account:**
- Email: `john@doe.com`
- Password: `johndoe123`
- Access: Full system access

**Assistant Director Account:**
- Email: `sarah@example.com`
- Password: `password123`
- Access: Scene management and timer control

**Crew Account:**
- Email: `mike@example.com`
- Password: `password123`
- Access: View-only

### Demo Data

The seeded database includes:
- **2 shows**:
  - "The Last Stand" (Shooting) - 4 scenes
  - "Midnight Dreams" (Pre-Production) - 1 scene
- **5 scenes** with various statuses
- **1 active timer** on Scene 2 to demonstrate the real-time feature
- **Timer logs** showing action history

## 🏗️ Technical Stack

### Frontend
- **React 18** with TypeScript
- **TanStack Router** for file-based routing
- **TanStack Query** for data fetching and caching
- **Tailwind CSS 3** for styling
- **Lucide React** for icons
- **Zustand** with persistence for client-side state management
- **React Hot Toast** for notifications
- **Vite** for fast development and building

### Backend
- **tRPC** for type-safe API with zero codegen
- **Prisma ORM** for database management
- **PostgreSQL** database
- **Express.js** for HTTP server
- **JWT** (jsonwebtoken) for authentication
- **bcryptjs** for password hashing
- **Zod** for runtime schema validation
- **SuperJSON** for serialization

## 📁 Project Structure

```
call_sheet_platform/
├── prisma/
│   └── schema.prisma          # Database schema
├── server/
│   ├── trpc/
│   │   ├── context.ts         # tRPC context with auth
│   │   ├── trpc.ts            # tRPC setup and procedures
│   │   ├── root.ts            # Main router
│   │   └── routers/           # Feature-based routers
│   │       ├── auth.ts        # Authentication
│   │       ├── users.ts       # User management
│   │       ├── shows.ts       # Show management
│   │       ├── scenes.ts      # Scene management
│   │       ├── timers.ts      # Timer control (⭐ Core)
│   │       └── dashboard.ts   # Dashboard stats
│   ├── utils/
│   │   └── auth.ts            # Auth utilities
│   ├── db.ts                  # Prisma client
│   └── index.ts               # Express server
├── src/
│   ├── components/            # React components
│   │   ├── Sidebar.tsx        # Navigation sidebar
│   │   ├── DashboardLayout.tsx
│   │   ├── AuthLayout.tsx
│   │   ├── SceneTimer.tsx     # ⭐ Real-time timer component
│   │   ├── StatsCard.tsx
│   │   └── Loading.tsx
│   ├── routes/                # TanStack Router pages
│   │   ├── __root.tsx
│   │   ├── index.tsx          # Dashboard
│   │   ├── login.tsx
│   │   ├── signup.tsx
│   │   ├── shows.tsx          # Shows list
│   │   ├── shows.$id.tsx      # Show detail
│   │   ├── scenes.$id.tsx     # Scene detail with timer
│   │   └── users.tsx          # User management (Admin only)
│   ├── stores/
│   │   └── authStore.ts       # Zustand auth state
│   ├── lib/
│   │   ├── trpc.ts            # tRPC client setup
│   │   └── utils.ts           # Utility functions
│   ├── styles.css             # Global styles
│   └── main.tsx               # App entry point
├── scripts/
│   └── seed.ts                # Database seeding script
└── package.json
```

## 🔑 Key Features Explained

### Real-Time Timer System (Core Differentiator)

The timer system is the **most important feature** of Call Sheet v1.0:

**Architecture:**
1. **Database fields** (`Scene` model):
   - `timerStart`: Timestamp when timer started/resumed
   - `timerPausedAt`: Timestamp when timer was paused
   - `timerAccumulatedMs`: Total milliseconds accumulated during pauses
   - `isTimerRunning`: Boolean flag for current state
   - `durationSeconds`: Final duration when timer is stopped

2. **API Endpoints** (timers router):
   - `timers.start`: Start a new timer or resume from pause
   - `timers.pause`: Pause the timer and accumulate elapsed time
   - `timers.stop`: Stop timer, mark scene complete, record final duration
   - `timers.reset`: Reset timer completely
   - `timers.getCurrentTime`: Get current elapsed time (real-time polling)
   - `timers.getLogs`: Get timer action history

3. **Frontend Component** (`SceneTimer.tsx`):
   - Uses `refetchInterval: 1000` when timer is running for real-time updates
   - Displays formatted time (HH:MM:SS)
   - Shows pulsing animation when active
   - Permission-based controls (Admin/AD only)

**Usage:**
- Navigate to a scene detail page
- Click "Start" to begin timing
- Timer updates every second automatically
- Use "Pause" to temporarily stop (time is preserved)
- Use "Stop" to complete the scene and record final duration
- All actions are logged with timestamps and user info

### Role-Based Access Control

**Permissions Matrix:**

| Feature | Admin | AD | Crew |
|---------|-------|----|----|
| View Dashboard | ✅ | ✅ | ✅ |
| View Shows | ✅ | ✅ | ✅ |
| Create/Edit Shows | ✅ | ✅ | ❌ |
| Delete Shows | ✅ | ✅ | ❌ |
| View Scenes | ✅ | ✅ | ✅ |
| Create/Edit Scenes | ✅ | ✅ | ❌ |
| Delete Scenes | ✅ | ✅ | ❌ |
| Control Timers | ✅ | ✅ | ❌ |
| User Management | ✅ | ❌ | ❌ |

## 🔧 Database Schema

Key models:

- **User**: Authentication and role management
- **Show**: Production information
- **Scene**: Scene details with timer fields
- **TimerLog**: Audit trail for timer actions

See `prisma/schema.prisma` for full schema.

## 🧪 Testing

The application has been tested with:
- ✅ User authentication (signup/login)
- ✅ Dashboard statistics loading
- ✅ Show CRUD operations
- ✅ Scene CRUD operations
- ✅ Real-time timer start/pause/stop/reset
- ✅ Timer persistence and accuracy
- ✅ Role-based access control
- ✅ User management (Admin only)

## 📝 API Examples

### Authentication

```typescript
// Login
const { token, user } = await trpc.auth.login.mutate({
  email: 'john@doe.com',
  password: 'johndoe123',
});

// Get current user
const user = await trpc.auth.me.query();
```

### Timer Control

```typescript
// Start timer
await trpc.timers.start.mutate({ sceneId: 1 });

// Get current time (polls every second when running)
const time = await trpc.timers.getCurrentTime.query({ sceneId: 1 });
// Returns: { totalSeconds, hours, minutes, seconds, isRunning, formatted }

// Stop timer
await trpc.timers.stop.mutate({ sceneId: 1 });
```

## 🚀 Future Enhancements

Potential v2.0 features:
- **Multi-workspace** support for production companies
- **WebSocket/Server-Sent Events** for real-time updates without polling
- **Advanced roles**: Director, Actor, Production, Developer
- **Call sheet PDF generation**
- **Calendar view** with drag-and-drop scheduling
- **Automated reports** and analytics
- **Email/SMS notifications**
- **Mobile app** for on-set access
- **Equipment tracking**
- **Budget management**

## 📄 License

Proprietary - Call Sheet v1.0

## 👥 Credits

Built with ❤️ for film production teams

---

**Call Sheet v1.0** - Simplifying production management, one scene at a time 🎬
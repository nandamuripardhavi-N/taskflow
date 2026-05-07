# TaskFlow — Team Task Manager

A full-stack team task management application with role-based access control, Kanban boards, and real-time dashboards.

## 🚀 Live Demo

- **Frontend:** `https://taskflow-frontend.railway.app`
- **Backend API:** `https://taskflow-backend.railway.app`
- **Demo Admin:** admin@taskflow.com / password123
- **Demo Member:** member@taskflow.com / password123

## ✨ Features

### Authentication
- JWT-based signup and login
- Secure password hashing (bcrypt)
- Protected routes with token validation
- Persistent sessions via localStorage

### Projects
- Create and manage multiple projects
- Custom colors and due dates
- Progress tracking (task completion %)
- Invite team members by email

### Role-Based Access Control
| Feature | Admin | Member |
|---------|-------|--------|
| Create project | ✅ | ✅ |
| Edit/delete project | ✅ | ❌ |
| Add/remove members | ✅ | ❌ |
| Change member roles | ✅ | ❌ |
| Create tasks | ✅ | ✅ |
| Edit any task | ✅ | Own only |
| Delete any task | ✅ | Own only |

### Task Management (Kanban Board)
- 4 status columns: To Do, In Progress, In Review, Done
- Priority levels: Low, Medium, High, Urgent
- Task assignment to team members
- Due dates with overdue indicators
- Quick status transitions
- Search and filter by assignee/priority
- Task comments

### Dashboard
- Personal task summary
- Stats: total, in progress, completed, overdue
- My assigned tasks with priority + due dates
- Recent activity feed across all projects

## 🛠 Tech Stack

**Frontend**
- React 18 + Vite
- React Router v6 (SPA routing)
- Axios (API client)
- Pure CSS-in-JS (no UI library dependency)

**Backend**
- Node.js + Express 4
- PostgreSQL with raw SQL (pg driver)
- JWT authentication
- express-validator for input validation
- bcryptjs for password hashing

## 📁 Project Structure

```
taskflow/
├── backend/
│   ├── src/
│   │   ├── config/
│   │   │   ├── database.js       # pg Pool setup
│   │   │   ├── migrate.js        # DB schema creation
│   │   │   └── seed.js           # Demo data
│   │   ├── controllers/
│   │   │   ├── authController.js
│   │   │   ├── projectController.js
│   │   │   └── taskController.js
│   │   ├── middleware/
│   │   │   └── auth.js           # JWT + role checks
│   │   ├── routes/
│   │   │   ├── auth.js
│   │   │   ├── projects.js
│   │   │   └── tasks.js
│   │   └── server.js
│   ├── package.json
│   └── .env.example
└── frontend/
    ├── src/
    │   ├── api/index.js           # Axios client + all API calls
    │   ├── context/AuthContext.jsx
    │   ├── components/
    │   │   ├── auth/              # Login, Signup
    │   │   ├── dashboard/         # Dashboard with stats
    │   │   ├── layout/            # Sidebar layout
    │   │   ├── projects/          # Projects list + detail
    │   │   └── tasks/             # Kanban board
    │   ├── App.jsx                # Router setup
    │   └── main.jsx
    ├── package.json
    └── vite.config.js
```

## 🗄 Database Schema

```sql
users           -- id, name, email, password, avatar_color
projects        -- id, name, description, status, color, owner_id, due_date
project_members -- project_id, user_id, role (admin/member)
tasks           -- id, title, description, status, priority, project_id, assignee_id, due_date
task_comments   -- id, task_id, user_id, content
activity_log    -- user_id, project_id, task_id, action, details (JSONB)
```

## 🚂 Deployment on Railway (Step-by-Step)

### Prerequisites
- [Railway account](https://railway.app) (free tier works)
- GitHub account with this repo pushed

### Step 1: Push to GitHub

```bash
git init
git add .
git commit -m "Initial commit"
git remote add origin https://github.com/YOUR_USERNAME/taskflow.git
git push -u origin main
```

### Step 2: Deploy Backend

1. Go to [railway.app](https://railway.app) → New Project → Deploy from GitHub
2. Select your repo → **Add service → Database → PostgreSQL**
3. Add another service → **GitHub Repo** (select your repo)
4. Set **Root Directory** to `backend`
5. Set **Start Command**: `npm start`
6. Add environment variables (Settings → Variables):
   ```
   DATABASE_URL     = (copy from PostgreSQL service → Connect tab)
   JWT_SECRET       = your-very-secret-key-change-this
   NODE_ENV         = production
   FRONTEND_URL     = https://taskflow-frontend.railway.app
   ```
7. After first deploy, run migrations via Railway CLI or add a **Deploy Command**:
   ```
   npm run migrate && npm run seed && npm start
   ```
   > ⚠️ Change deploy command back to `npm start` after first run to avoid re-seeding

### Step 3: Deploy Frontend

1. In Railway project → **Add service → GitHub Repo**
2. Set **Root Directory** to `frontend`
3. Set **Build Command**: `npm run build`
4. Set **Start Command**: `npx serve dist -p $PORT`
5. Add environment variables:
   ```
   VITE_API_URL = https://YOUR-BACKEND-SERVICE.railway.app/api
   ```
   > Get backend URL from: backend service → Settings → Networking → Public Domain

### Step 4: Verify

- Visit your frontend Railway URL
- Login with: admin@taskflow.com / password123
- Create a project, add tasks, invite a member!

## 🏠 Local Development

### Prerequisites
- Node.js 18+
- PostgreSQL 14+

### Backend Setup

```bash
cd backend
npm install
cp .env.example .env
# Edit .env with your local DATABASE_URL and a JWT_SECRET
psql -d your_db -f /dev/null   # create db if needed
npm run migrate   # create tables
npm run seed      # optional demo data
npm run dev       # starts on port 3001
```

### Frontend Setup

```bash
cd frontend
npm install
cp .env.example .env
# Set VITE_API_URL=http://localhost:3001/api (or leave blank for proxy)
npm run dev       # starts on port 5173
```

## 🔌 API Reference

### Auth
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | /api/auth/signup | Register new user |
| POST | /api/auth/login | Login |
| GET | /api/auth/me | Get current user |
| PATCH | /api/auth/me | Update profile |

### Projects
| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| GET | /api/projects | ✅ | List user's projects |
| POST | /api/projects | ✅ | Create project |
| GET | /api/projects/:id | ✅ Member | Get project + members |
| PATCH | /api/projects/:id | ✅ Admin | Update project |
| DELETE | /api/projects/:id | ✅ Owner | Delete project |
| POST | /api/projects/:id/members | ✅ Admin | Add member |
| PATCH | /api/projects/:id/members/:uid | ✅ Admin | Change role |
| DELETE | /api/projects/:id/members/:uid | ✅ Admin | Remove member |

### Tasks
| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| GET | /api/tasks/dashboard | ✅ | Dashboard stats + tasks |
| GET | /api/tasks/project/:id | ✅ Member | Get project tasks |
| POST | /api/tasks/project/:id | ✅ Member | Create task |
| GET | /api/tasks/:id | ✅ | Get task |
| PATCH | /api/tasks/:id | ✅ | Update task |
| DELETE | /api/tasks/:id | ✅ | Delete task |
| POST | /api/tasks/:id/comments | ✅ | Add comment |

## 📋 Validations

- **Users**: name ≥ 2 chars, valid email, password ≥ 6 chars
- **Projects**: name ≥ 2 chars
- **Tasks**: title required, enum validation for status/priority
- **Auth**: JWT expiry (7 days), bcrypt rounds: 12
- **DB constraints**: FK relationships, status/priority enums

## 🎥 Demo Video Script

1. Show login page → login as Admin
2. Dashboard: explain stats + activity
3. Projects page → create new project
4. Project detail → add member (invite by email)
5. Task board → create tasks across statuses
6. Move tasks between columns
7. Login as Member → show restricted access (no admin actions visible)
8. Show role badge differences

---

Built with ❤️ using Node.js, Express, PostgreSQL, and React

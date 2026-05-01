# TaskFlow — Team Task Manager

> A full-stack **Team Task Management Web Application** built with Spring Boot, MySQL, and Vanilla JS.
> Think of it as a simplified Trello/Asana — where teams create projects, assign tasks, and track progress in real time.

---

## 🔗 Links

| | |
|---|---|
| 🌐 **Live App** | [https://taskflow-production-f99f.up.railway.app](https://taskflow-production-f99f.up.railway.app) |
| 📁 **GitHub Repo** | [https://github.com/divya323-great/TaskFlow](https://github.com/divya323-great/TaskFlow) |

---

## ✨ Features

- **JWT Authentication** — Secure signup & login with token-based sessions
- **Project Management** — Create projects; creator auto-becomes Admin; add/remove members
- **Task Management** — Full CRUD with title, description, due date, priority, status & assignee
- **Dashboard** — Real-time stats: total tasks, by status, by priority, overdue tasks, per-user breakdown
- **Role-Based Access Control** — Admin manages everything; Members update only their assigned tasks
- **Validation & Error Handling** — Input validation with meaningful HTTP error responses
- **Clean Dark UI** — Sidebar navigation, modals, charts, and responsive tables

---

## 🛠 Tech Stack

| Layer | Technology |
|---|---|
| Backend | Spring Boot 3.2, Java 17 |
| Authentication | JWT (jjwt 0.11.5) |
| Database | MySQL 8+ |
| ORM | Spring Data JPA / Hibernate |
| Security | Spring Security |
| Frontend | HTML5, CSS3, Vanilla JavaScript |
| Build Tool | Maven |
| Deployment | Railway |

---

## 🗄 Database Schema

```
users           — id, name, email, password
roles           — id, name (ADMIN / MEMBER)
projects        — id, name, description, admin_id → users
project_members — project_id → projects, user_id → users  (many-to-many)
tasks           — id, title, description, due_date, priority, status,
                  assigned_to_id → users, project_id → projects
```

---

## ⚙️ Local Setup

### Prerequisites

- Java 17+
- Maven 3.6+
- MySQL 8+

### 1. Clone the repository

```bash
git clone https://github.com/divya323-great/TaskFlow.git
cd TaskFlow
```

### 2. Create MySQL database

```sql
CREATE DATABASE taskdb;
```

### 3. Configure database credentials

Edit `src/main/resources/application.properties`:

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/taskdb?createDatabaseIfNotExist=true&useSSL=false&allowPublicKeyRetrieval=true&serverTimezone=UTC
spring.datasource.username=root
spring.datasource.password=YOUR_MYSQL_PASSWORD

spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true

jwt.secret=bXlfc3VwZXJfc2VjdXJlX2p3dF9zZWNyZXRfa2V5X2Zvcl90ZWFtdGFza19hcHBfMjAyNA==
jwt.expiration=86400000
```

### 4. Build and run

```bash
# macOS / Linux
./mvnw spring-boot:run

# Windows
mvnw.cmd spring-boot:run
```

### 5. Open in browser

```
http://localhost:8080
```

> **Note:** The first registered user automatically becomes **Admin**.

---

## 📡 API Reference

All endpoints (except auth) require the header:

```
Authorization: Bearer <your-jwt-token>
```

---

### 🔐 Auth

| Method | Endpoint | Body | Description |
|---|---|---|---|
| POST | `/api/auth/signup` | `{name, email, password}` | Register a new user |
| POST | `/api/auth/login` | `{email, password}` | Login and receive JWT |
| GET | `/api/auth/me` | — | Get the currently logged-in user |

---

### 📂 Projects

| Method | Endpoint | Role | Description |
|---|---|---|---|
| POST | `/api/projects` | Any | Create project (creator becomes Admin) |
| GET | `/api/projects` | Any | List all accessible projects |
| GET | `/api/projects/{id}` | Any | Get a project by ID |
| PUT | `/api/projects/{id}` | Admin | Update project details |
| DELETE | `/api/projects/{id}` | Admin | Delete project |
| POST | `/api/projects/{id}/members/{userId}` | Admin | Add a member to the project |
| DELETE | `/api/projects/{id}/members/{userId}` | Admin | Remove a member from the project |
| GET | `/api/projects/{id}/members` | Any | List all project members |

---

### ✅ Tasks

| Method | Endpoint | Role | Description |
|---|---|---|---|
| POST | `/api/tasks` | Admin | Create a new task |
| GET | `/api/tasks` | Any | List tasks (filtered by role) |
| GET | `/api/tasks/{id}` | Any | Get a task by ID |
| GET | `/api/tasks/project/{id}` | Any | List tasks under a specific project |
| PUT | `/api/tasks/{id}` | Admin / Assignee | Update all task fields |
| PATCH | `/api/tasks/{id}/status` | Admin / Assignee | Update task status only |
| DELETE | `/api/tasks/{id}` | Admin | Delete a task |

---

### 📊 Dashboard

| Method | Endpoint | Description |
|---|---|---|
| GET | `/api/dashboard` | Totals, tasks by status, overdue tasks, per-user stats |

> Members see stats for their own assigned tasks only. Admins see full team stats.

---

### 👤 Users *(Admin only)*

| Method | Endpoint | Description |
|---|---|---|
| GET | `/api/users` | List all users |
| GET | `/api/users/{id}` | Get a user by ID |
| PATCH | `/api/users/{id}/role` | Update a user's role |
| DELETE | `/api/users/{id}` | Delete a user |

---

## 🔑 Role-Based Access Control

| Action | Admin | Member |
|---|:---:|:---:|
| Create project | ✅ | ✅ |
| Manage project members | ✅ | ❌ |
| Delete project | ✅ | ❌ |
| Create task | ✅ | ❌ |
| Edit task (all fields) | ✅ | ❌ |
| Update own task status | ✅ | ✅ |
| View all tasks | ✅ | ❌ |
| View assigned tasks | ✅ | ✅ |
| Manage users | ✅ | ❌ |
| View full dashboard | ✅ | ✅* |

*\*Members see only their own task stats.*

---

## 🧪 Postman Testing Guide

**Base URL:** `https://taskflow-production-f99f.up.railway.app`

> ⚠️ **Avoid 403 Errors:** Always login first and use a fresh token. In Postman go to **Authorization tab → Bearer Token** and paste your token there. Use the first registered user (auto-Admin) for full access. If you get 403, login again to get a new token.

---

### Step 1 — Signup *(first user becomes Admin)*

```http
POST https://taskflow-production-f99f.up.railway.app/api/auth/signup
Content-Type: application/json

{
  "name": "John Admin",
  "email": "admin@test.com",
  "password": "password123"
}
```

---

### Step 2 — Login & save the token

```http
POST https://taskflow-production-f99f.up.railway.app/api/auth/login
Content-Type: application/json

{
  "email": "admin@test.com",
  "password": "password123"
}
```

**Response:**

```json
{
  "token": "eyJhbGciOiJIUzI1NiJ9...",
  "type": "Bearer"
}
```

> Copy the `token` value and use it in all requests below as: `Authorization: Bearer <token>`

---

### Step 3 — Get current user

```http
GET https://taskflow-production-f99f.up.railway.app/api/auth/me
Authorization: Bearer <your-token>
```

---

### Step 4 — Create a project

```http
POST https://taskflow-production-f99f.up.railway.app/api/projects
Authorization: Bearer <your-token>
Content-Type: application/json

{
  "name": "My First Project",
  "description": "Testing the API"
}
```

> Note the `id` from the response — you will need it as `projectId` when creating tasks.

---

### Step 5 — List all projects

```http
GET https://taskflow-production-f99f.up.railway.app/api/projects
Authorization: Bearer <your-token>
```

---

### Step 6 — Add a member to a project

```http
POST https://taskflow-production-f99f.up.railway.app/api/projects/1/members/2
Authorization: Bearer <your-token>
```

> Replace `1` with your project ID and `2` with the user ID to add.

---

### Step 7 — Create a task *(Admin only)*

```http
POST https://taskflow-production-f99f.up.railway.app/api/tasks
Authorization: Bearer <your-token>
Content-Type: application/json

{
  "title": "Design homepage",
  "description": "Create wireframes for the landing page",
  "priority": "HIGH",
  "status": "TODO",
  "dueDate": "2025-12-31",
  "projectId": 1,
  "assignedToId": 1
}
```

> `priority` values: `LOW` · `MEDIUM` · `HIGH`
>
> `status` values: `TODO` · `IN_PROGRESS` · `DONE`

---

### Step 8 — List all tasks

```http
GET https://taskflow-production-f99f.up.railway.app/api/tasks
Authorization: Bearer <your-token>
```

---

### Step 9 — Update task status *(Admin or Assignee)*

```http
PATCH https://taskflow-production-f99f.up.railway.app/api/tasks/1/status
Authorization: Bearer <your-token>
Content-Type: application/json

{
  "status": "IN_PROGRESS"
}
```

---

### Step 10 — View dashboard

```http
GET https://taskflow-production-f99f.up.railway.app/api/dashboard
Authorization: Bearer <your-token>
```

---

### Step 11 — List all users *(Admin only)*

```http
GET https://taskflow-production-f99f.up.railway.app/api/users
Authorization: Bearer <your-token>
```

---

## 🚀 Deployment on Railway

### 1. Push to GitHub

```bash
git init
git add .
git commit -m "Initial commit"
git remote add origin https://github.com/divya323-great/TaskFlow.git
git push -u origin main
```

### 2. Create a Railway project

1. Go to [railway.app](https://railway.app) → **New Project**
2. Add a **MySQL** service (Railway provisions it automatically)
3. Add a **GitHub Repo** service pointing to your repository

### 3. Set environment variables

```
SPRING_DATASOURCE_URL=jdbc:mysql://${{MySQL.MYSQL_HOST}}:${{MySQL.MYSQL_PORT}}/${{MySQL.MYSQL_DATABASE}}?useSSL=false&allowPublicKeyRetrieval=true&serverTimezone=UTC
SPRING_DATASOURCE_USERNAME=${{MySQL.MYSQL_USER}}
SPRING_DATASOURCE_PASSWORD=${{MySQL.MYSQL_PASSWORD}}
JWT_SECRET=bXlfc3VwZXJfc2VjdXJlX2p3dF9zZWNyZXRfa2V5X2Zvcl90ZWFtdGFza19hcHBfMjAyNA==
JWT_EXPIRATION=86400000
```

### 4. Deploy

Railway auto-deploys on every `git push`. App is live at:

```
https://taskflow-production-f99f.up.railway.app
```

---

## 📁 Project Structure

```
TaskFlow/
├── src/
│   ├── main/
│   │   ├── java/com/taskmanager/
│   │   │   ├── controller/      # REST Controllers
│   │   │   ├── model/           # JPA Entities
│   │   │   ├── repository/      # Spring Data Repositories
│   │   │   ├── service/         # Business Logic
│   │   │   ├── security/        # JWT & Spring Security Config
│   │   │   └── dto/             # Request/Response DTOs
│   │   └── resources/
│   │       └── application.properties
├── pom.xml
└── README.md
```

---

## 📝 Submission Checklist

- [x] Live application URL (Railway)
- [x] GitHub repository with full source code
- [x] README with setup and deployment steps

---

*Built with Spring Boot · MySQL · Vanilla JS · Deployed on Railway*

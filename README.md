TaskFlow — Team Task Manager

A full-stack Team Task Management Web Application built with Spring Boot, MySQL, and Vanilla JS.
Think of it as a simplified Trello/Asana — where teams create projects, assign tasks, and track progress in real time.


🔗 Links
🌐 Live Apphttps://taskflow-production-f99f.up.railway.app📁 GitHub Repohttps://github.com/divya323-great/TaskFlow

✨ Features

JWT Authentication — Secure signup & login with token-based sessions
Project Management — Create projects; creator auto-becomes Admin; add/remove members
Task Management — Full CRUD with title, description, due date, priority, status & assignee
Dashboard — Real-time stats: total tasks, by status, by priority, overdue tasks, per-user breakdown
Role-Based Access Control — Admin manages everything; Members update only their assigned tasks
Validation & Error Handling — Input validation with meaningful HTTP error responses
Clean Dark UI — Sidebar navigation, modals, charts, and responsive tables


🛠 Tech Stack
LayerTechnologyBackendSpring Boot 3.2, Java 17AuthenticationJWT (jjwt 0.11.5)DatabaseMySQL 8+ORMSpring Data JPA / HibernateSecuritySpring SecurityFrontendHTML5, CSS3, Vanilla JavaScriptBuild ToolMavenDeploymentRailway

🗄 Database Schema
users           — id, name, email, password
roles           — id, name (ADMIN / MEMBER)
projects        — id, name, description, admin_id → users
project_members — project_id → projects, user_id → users  (many-to-many)
tasks           — id, title, description, due_date, priority, status,
                  assigned_to_id → users, project_id → projects

⚙️ Local Setup
Prerequisites

Java 17+
Maven 3.6+
MySQL 8+

1. Clone the repository
bashgit clone https://github.com/divya323-great/TaskFlow.git
cd TaskFlow
2. Create MySQL database
sqlCREATE DATABASE taskdb;
3. Configure database credentials
Edit src/main/resources/application.properties:
propertiesspring.datasource.url=jdbc:mysql://localhost:3306/taskdb?createDatabaseIfNotExist=true&useSSL=false&allowPublicKeyRetrieval=true&serverTimezone=UTC
spring.datasource.username=root
spring.datasource.password=YOUR_MYSQL_PASSWORD

spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true

jwt.secret=bXlfc3VwZXJfc2VjdXJlX2p3dF9zZWNyZXRfa2V5X2Zvcl90ZWFtdGFza19hcHBfMjAyNA==
jwt.expiration=86400000
4. Build and run
bash# macOS / Linux
./mvnw spring-boot:run

# Windows
mvnw.cmd spring-boot:run
5. Open in browser
http://localhost:8080

Note: The first registered user automatically becomes Admin.


📡 API Reference
All endpoints (except auth) require the header:
Authorization: Bearer <your-jwt-token>

🔐 Auth
MethodEndpointBodyDescriptionPOST/api/auth/signup{name, email, password}Register a new userPOST/api/auth/login{email, password}Login and receive JWTGET/api/auth/me—Get the currently logged-in user

📂 Projects
MethodEndpointRoleDescriptionPOST/api/projectsAnyCreate project (creator becomes Admin)GET/api/projectsAnyList all accessible projectsGET/api/projects/{id}AnyGet a project by IDPUT/api/projects/{id}AdminUpdate project detailsDELETE/api/projects/{id}AdminDelete projectPOST/api/projects/{id}/members/{userId}AdminAdd a member to the projectDELETE/api/projects/{id}/members/{userId}AdminRemove a member from the projectGET/api/projects/{id}/membersAnyList all project members

✅ Tasks
MethodEndpointRoleDescriptionPOST/api/tasksAdminCreate a new taskGET/api/tasksAnyList tasks (filtered by role)GET/api/tasks/{id}AnyGet a task by IDGET/api/tasks/project/{id}AnyList tasks under a specific projectPUT/api/tasks/{id}Admin / AssigneeUpdate all task fieldsPATCH/api/tasks/{id}/statusAdmin / AssigneeUpdate task status onlyDELETE/api/tasks/{id}AdminDelete a task

📊 Dashboard
MethodEndpointDescriptionGET/api/dashboardReturns totals, tasks by status, overdue tasks, and per-user stats

Members see stats for their own assigned tasks only. Admins see full team stats.


👤 Users (Admin only)
MethodEndpointDescriptionGET/api/usersList all usersGET/api/users/{id}Get a user by IDPATCH/api/users/{id}/roleUpdate a user's roleDELETE/api/users/{id}Delete a user

🔑 Role-Based Access Control
ActionAdminMemberCreate project✅✅Manage project members✅❌Delete project✅❌Create task✅❌Edit task (all fields)✅❌Update own task status✅✅View all tasks✅❌View assigned tasks✅✅Manage users✅❌View full dashboard✅✅*
*Members see only their own task stats.

🧪 Postman Testing Guide
Base URL: https://taskflow-production-f99f.up.railway.app

⚠️ Important — Avoid 403 Errors:

Always login first and use a fresh token before calling any protected endpoint
In Postman, go to Authorization tab → Type: Bearer Token → paste your token
Make sure there is a space between Bearer and the token value
Use the first registered user (auto-Admin) for full access
If you get 403, your token may have expired — login again to get a new one



Step 1 — Signup (first user becomes Admin)
httpPOST https://taskflow-production-f99f.up.railway.app/api/auth/signup
Content-Type: application/json

{
  "name": "John Admin",
  "email": "admin@test.com",
  "password": "password123"
}

Step 2 — Login & save the token
httpPOST https://taskflow-production-f99f.up.railway.app/api/auth/login
Content-Type: application/json

{
  "email": "admin@test.com",
  "password": "password123"
}
Response:
json{
  "token": "eyJhbGciOiJIUzI1NiJ9...",
  "type": "Bearer"
}

Copy the token value and use it in all requests below as:
Authorization: Bearer eyJhbGci...


Step 3 — Get current user
httpGET https://taskflow-production-f99f.up.railway.app/api/auth/me
Authorization: Bearer <your-token>

Step 4 — Create a project
httpPOST https://taskflow-production-f99f.up.railway.app/api/projects
Authorization: Bearer <your-token>
Content-Type: application/json

{
  "name": "My First Project",
  "description": "Testing the API"
}

Note the id from the response — you'll need it as projectId when creating tasks.


Step 5 — List all projects
httpGET https://taskflow-production-f99f.up.railway.app/api/projects
Authorization: Bearer <your-token>

Step 6 — Add a member to a project
httpPOST https://taskflow-production-f99f.up.railway.app/api/projects/1/members/2
Authorization: Bearer <your-token>

Replace 1 with your project ID and 2 with the user ID to add.


Step 7 — Create a task (Admin only)
httpPOST https://taskflow-production-f99f.up.railway.app/api/tasks
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

priority values: LOW · MEDIUM · HIGH
status values: TODO · IN_PROGRESS · DONE


Step 8 — List all tasks
httpGET https://taskflow-production-f99f.up.railway.app/api/tasks
Authorization: Bearer <your-token>

Step 9 — Update task status (Admin or Assignee)
httpPATCH https://taskflow-production-f99f.up.railway.app/api/tasks/1/status
Authorization: Bearer <your-token>
Content-Type: application/json

{
  "status": "IN_PROGRESS"
}

Step 10 — View dashboard
httpGET https://taskflow-production-f99f.up.railway.app/api/dashboard
Authorization: Bearer <your-token>

Step 11 — List all users (Admin only)
httpGET https://taskflow-production-f99f.up.railway.app/api/users
Authorization: Bearer <your-token>

🚀 Deployment on Railway
1. Push to GitHub
bashgit init
git add .
git commit -m "Initial commit"
git remote add origin https://github.com/divya323-great/TaskFlow.git
git push -u origin main
2. Create a Railway project

Go to railway.app → New Project
Add a MySQL service (Railway provisions it automatically)
Add a GitHub Repo service pointing to your repository

3. Set environment variables
In your Railway service settings, add:
SPRING_DATASOURCE_URL=jdbc:mysql://${{MySQL.MYSQL_HOST}}:${{MySQL.MYSQL_PORT}}/${{MySQL.MYSQL_DATABASE}}?useSSL=false&allowPublicKeyRetrieval=true&serverTimezone=UTC
SPRING_DATASOURCE_USERNAME=${{MySQL.MYSQL_USER}}
SPRING_DATASOURCE_PASSWORD=${{MySQL.MYSQL_PASSWORD}}
JWT_SECRET=bXlfc3VwZXJfc2VjdXJlX2p3dF9zZWNyZXRfa2V5X2Zvcl90ZWFtdGFza19hcHBfMjAyNA==
JWT_EXPIRATION=86400000
4. Deploy
Railway auto-deploys on every git push. Your app is live at:
https://taskflow-production-f99f.up.railway.app

📁 Project Structure
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

📝 Submission Checklist

 Live application URL (Railway)
 GitHub repository with full source code
 README with setup and deployment steps


Built with Spring Boot · MySQL · Vanilla JS · Deployed on Railway

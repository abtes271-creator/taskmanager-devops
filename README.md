# Task Manager — Phase 1: The App

A simple REST API for managing tasks. This is the foundation for the full DevOps
journey — every later phase (Docker, CI/CD, Kubernetes, etc.) packages and deploys
*this exact application* without changing its code.

**Stack:** Java 17, Spring Boot 3.3, PostgreSQL, Maven

---

## 1. Install prerequisites (Windows)

Skip anything you already have installed — check first with the verify command.

### Java 17 (JDK)
1. Download the JDK 17 installer (Temurin/Adoptium is a good free build): https://adoptium.net/
2. Run the installer, keep defaults (make sure "Set JAVA_HOME" is checked).
3. Verify in PowerShell:
   ```powershell
   java -version
   ```
   Should print something like `openjdk version "17..."`.

### Maven
1. Download the binary zip from https://maven.apache.org/download.cgi
2. Extract to e.g. `C:\Program Files\Apache\maven`
3. Add `C:\Program Files\Apache\maven\bin` to your `Path` environment variable
   (Settings → System → About → Advanced system settings → Environment Variables).
4. Verify:
   ```powershell
   mvn -version
   ```

### PostgreSQL
1. Download the installer: https://www.postgresql.org/download/windows/
2. Run it — remember the password you set for the `postgres` superuser.
3. Keep the default port `5432`.
4. Once installed, open **pgAdmin** (installed alongside) or `psql` and create the
   database and a dedicated user for this app:
   ```sql
   CREATE DATABASE taskmanager;
   CREATE USER taskmanager_user WITH ENCRYPTED PASSWORD 'changeme';
   GRANT ALL PRIVILEGES ON DATABASE taskmanager TO taskmanager_user;
   ```
   Since you already use pgAdmin, this will feel familiar — open pgAdmin, connect
   to your local server, right-click "Databases" → Create, or just run the SQL
   above in the Query Tool.

### An IDE
You likely already have IntelliJ IDEA — that's fine, open this folder as a Maven project.

---

## 2. Configure the app

Database credentials are read from environment variables, with local defaults
already matching the SQL above (`taskmanager` / `taskmanager_user` / `changeme`).
If you used different values, either update `src/main/resources/application.properties`
directly, or set environment variables before running:

```powershell
$env:DB_NAME="taskmanager"
$env:DB_USER="taskmanager_user"
$env:DB_PASSWORD="changeme"
```

## 3. Run it

```powershell
mvn spring-boot:run
```

On first run, Hibernate will auto-create the `tasks` table (`ddl-auto=update`).
The app starts on **http://localhost:8080**.

Health check: http://localhost:8080/actuator/health should return `{"status":"UP"}`.

## 4. Try the API

Create a task:
```powershell
curl -X POST http://localhost:8080/api/tasks `
  -H "Content-Type: application/json" `
  -d '{"title":"Learn Docker","description":"Finish Phase 4","dueDate":"2026-08-20"}'
```

List all tasks:
```powershell
curl http://localhost:8080/api/tasks
```

Get one task:
```powershell
curl http://localhost:8080/api/tasks/1
```

Update a task:
```powershell
curl -X PUT http://localhost:8080/api/tasks/1 `
  -H "Content-Type: application/json" `
  -d '{"title":"Learn Docker","status":"IN_PROGRESS","dueDate":"2026-08-20"}'
```

Filter by status:
```powershell
curl "http://localhost:8080/api/tasks?status=IN_PROGRESS"
```

Search by title:
```powershell
curl "http://localhost:8080/api/tasks?search=docker"
```

Delete a task:
```powershell
curl -X DELETE http://localhost:8080/api/tasks/1
```

(Or just use Postman/Insomnia if you prefer a GUI — whatever you're already comfortable with.)

## API summary

| Method | Endpoint | Description |
|---|---|---|
| GET | `/api/tasks` | List all tasks (optional `?status=` or `?search=`) |
| GET | `/api/tasks/{id}` | Get one task |
| POST | `/api/tasks` | Create a task |
| PUT | `/api/tasks/{id}` | Update a task |
| DELETE | `/api/tasks/{id}` | Delete a task |
| GET | `/actuator/health` | Health check (used later for Docker/K8s probes & monitoring) |

---

## What's next

Once you've confirmed this runs locally and you can hit every endpoint, we move to
**Phase 2: Git + GitHub** — turning this into a proper versioned repo with a clean
commit history, then push it up. Come back and say "ready for phase 2" whenever you are.

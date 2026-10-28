# CRM — Local Development Setup

This repository is the entry point for the CRM project. It contains the shared Docker Compose configuration, project documentation, and Git submodule references to the backend and frontend repositories.

## Repository Structure

```text
dot-net-crm/
├── backend/                    # .NET backend (Git submodule)
├── frontend/                   # Vite + TypeScript frontend (Git submodule)
├── docs/                       # Project documentation
├── .env                        # Local environment variables (not committed)
├── .env.example                # Environment template
├── .gitmodules                 # Git submodule configuration
└── docker-compose.local.yml    # Local development stack
```

### Repositories

- **Root:** https://github.com/Suchank2059/dot-net-crm
- **Backend:** https://github.com/Suchank2059/dot-net-crm-backend
- **Frontend:** https://github.com/Suchank2059/dot-net-crm-frontend

The root repository pins specific commits of the backend and frontend. This ensures that developers and CI/CD workflows can use a consistent combination of application versions.

## Prerequisites

Install the following before starting:

- Git
- Docker Desktop
- A GitHub account with access to all three repositories

You do not need to install .NET, Node.js, or MySQL locally to run the Docker-based development stack.

## 1. Clone the Repository

Clone the root repository and initialize both submodules:

```bash
git clone --recurse-submodules https://github.com/Suchank2059/dot-net-crm.git
cd dot-net-crm
```

If you already cloned the root repository without submodules, run:

```bash
git submodule update --init --recursive
```

**Access requirement:** The backend and frontend repositories must also be accessible to your GitHub account. If cloning fails with an authentication or permission error, verify your repository access.

## 2. Configure Environment Variables

Create your local `.env` file from the template:

```bash
cp .env.example .env
```

Update the values in `.env` as required.

Example development configuration:

```dotenv
MYSQL_ROOT_PASSWORD=crm_local_root_password
MYSQL_DATABASE=crm
MYSQL_USER=crm_user
MYSQL_PASSWORD=crm_local_password

MYSQL_PORT=3307
PHPMYADMIN_PORT=8082

ConnectionStrings__DefaultConnection=Server=mysql;Port=3306;Database=crm;User=crm_user;Password=crm_local_password;
```

These credentials are for local development only. Do not commit `.env` or use these example passwords in production.

### Environment Variable Notes

| Variable | Description |
|---|---|
| `MYSQL_ROOT_PASSWORD` | MySQL root password |
| `MYSQL_DATABASE` | Database created for the CRM |
| `MYSQL_USER` | Application database user |
| `MYSQL_PASSWORD` | Application database password |
| `MYSQL_PORT` | MySQL port exposed on the host |
| `PHPMYADMIN_PORT` | phpMyAdmin web interface port |
| `ConnectionStrings__DefaultConnection` | .NET database connection string |

The connection string uses `mysql:3306` because the backend connects to MySQL over the Docker network. Use `localhost:3307` only when connecting to MySQL directly from your host machine.

## 3. Start the Local Stack

From the root repository directory, run:

```bash
docker compose -f docker-compose.local.yml up --build
```

To run the stack in the background:

```bash
docker compose -f docker-compose.local.yml up --build -d
```

Docker will build and start the backend, frontend, MySQL, and phpMyAdmin services.

## 4. Access the Applications

| Service | URL |
|---|---|
| Frontend | http://localhost:5001 |
| Backend API | http://localhost:5000 |
| phpMyAdmin | http://localhost:8082 |
| MySQL from host | `127.0.0.1:3307` |

Use the database name, username, and password configured in `.env` to log in to phpMyAdmin.

## 5. Common Docker Commands

Run these commands from the root repository directory.

**Check running services:**

```bash
docker compose -f docker-compose.local.yml ps
```

**View all service logs:**

```bash
docker compose -f docker-compose.local.yml logs -f
```

**View backend logs:**

```bash
docker compose -f docker-compose.local.yml logs -f backend
```

**View frontend logs:**

```bash
docker compose -f docker-compose.local.yml logs -f frontend
```

**Restart the stack:**

```bash
docker compose -f docker-compose.local.yml restart
```

**Rebuild and restart after Dockerfile or dependency changes:**

```bash
docker compose -f docker-compose.local.yml up --build -d
```

**Stop the stack:**

```bash
docker compose -f docker-compose.local.yml down
```

Stopping the stack preserves the MySQL data volume.

**Reset the local database:**

```bash
docker compose -f docker-compose.local.yml down --volumes
docker compose -f docker-compose.local.yml up --build -d
```

Warning: removing volumes deletes the local MySQL data. Use this only when you intentionally want a fresh database.

## 6. Working with Git Submodules

The backend and frontend are independent Git repositories. Make code changes and commit them inside their respective directories.

### Update Backend or Frontend Code

For example, to update the backend:

```bash
cd backend

git switch master
git pull origin master
```

Make your changes, then commit and push them:

```bash
git add .
git commit -m "Describe the backend change"
git push origin master
```

Return to the root and record the updated backend commit:

```bash
cd ..
git add backend
git commit -m "Update backend revision"
git push origin master
```

Follow the same process for the frontend by replacing `backend` with `frontend`.

**Important:** The root repository records the exact commit of each submodule. Pushing changes to a child repository does not automatically update the commit pinned by the root. The root pointer must be committed and pushed separately.

When switching to a branch or commit recorded by the root, run:

```bash
git submodule update --init --recursive
```

Avoid using `git submodule update --remote` for reproducible deployments unless you intentionally want to advance a submodule to a newer remote revision.

## 7. Troubleshooting

### Submodules are empty

Run:

```bash
git submodule update --init --recursive
```

### Docker services fail to start

Check the resolved Compose configuration:

```bash
docker compose -f docker-compose.local.yml config
```

Check service status and logs:

```bash
docker compose -f docker-compose.local.yml ps
docker compose -f docker-compose.local.yml logs
```

### Port already in use

If ports `3307`, `5000`, `5001`, or `8082` are already occupied, update the corresponding host port in `.env` or the relevant Compose configuration. Keep the internal container ports unchanged unless you also update the application configuration.

### Backend cannot connect to MySQL

Verify that the backend uses:

```text
Server=mysql;Port=3306
```

The backend must connect to MySQL through the shared Docker network, using the MySQL service name and internal port.

### Environment changes do not take effect

Recreate the affected services:

```bash
docker compose -f docker-compose.local.yml up -d
```

If the Docker image or dependencies changed, rebuild:

```bash
docker compose -f docker-compose.local.yml up --build -d
```

## Development Notes

- Keep secrets in `.env`, not in Git.
- Commit application changes inside the appropriate child repository.
- Update the root submodule pointer whenever a new backend or frontend revision should become part of the shared project state.
- Use the root repository's pinned commits when testing or preparing a deployment.
- Do not remove Docker volumes unless you intend to delete local database data.

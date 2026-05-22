# PresalesAssistantInfra

Infrastructure for local Presales Assistant deployment.

## Prepare Secrets

From the repository root, enter the compose directory:

```powershell
cd infra
```

Create compose path config and edit it if backend/frontend repositories are in different locations:

```powershell
Copy-Item .env.example .env
```

Open `infra/.env` and set `BACKEND_DIR` and `FRONTEND_DIR`. Paths are resolved from the `infra/` directory.

Create real secret `.env` files from examples in `secrets/` and edit values if needed:

```powershell
cd secrets

Copy-Item ai_service.env.example ai_service.env
Copy-Item auth_service.env.example auth_service.env
Copy-Item db_auth.env.example db_auth.env
Copy-Item db_presale.env.example db_presale.env
Copy-Item frontend.env.example frontend.env
Copy-Item minio.env.example minio.env
Copy-Item presale_service.env.example presale_service.env

cd ..
```

Real `.env`, `secrets/*.env`, and `secrets/keys/` are ignored by git. Only `*.env.example` files should be committed.

## Generate Keys

Create the keys directory:

```powershell
New-Item -ItemType Directory -Force secrets\keys
```

Generate RS256 private/public keys:

```powershell
openssl genrsa -out secrets\keys\jwtRS256.key 2048
openssl rsa -in secrets\keys\jwtRS256.key -pubout -out secrets\keys\jwtRS256.key.pub
```

The `secrets/keys/` directory is ignored by git.

## Run

From `infra/`:

```bash
docker compose up -d --build
```

## Dev Run

For hot reload:

```bash
docker compose -f docker-compose.yml -f docker-compose.dev.yml up --build
```

Dev compose uses hot reload for frontend and backend, but exposes the app through nginx only.

Services:

- Frontend: `http://localhost:8088`
- Auth API: `http://localhost:8088/auth_service/docs`
- Presale API: `http://localhost:8088/presale_service/docs`
- AI API: `http://localhost:8088/ai_service/docs`
- Auth DB: `localhost:15433`
- Presale DB: `localhost:15434`
- MinIO API: `http://localhost:19000`
- MinIO Console: `http://localhost:19001`

MinIO credentials:

```text
MINIO_ROOT_USER=minioadmin
MINIO_ROOT_PASSWORD=minioadmin
```

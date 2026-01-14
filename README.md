# Kong Gateway (OSS) + Postgres — GitHub Codespaces-ready

This repo runs **Kong Gateway (open-source image)** backed by **PostgreSQL** using Docker Compose — designed to work cleanly in **GitHub Codespaces**.

> Notes
> - Kong proxy: `8000` (HTTP) / `8443` (HTTPS)
> - Kong Admin API: `8001` (HTTP) / `8444` (HTTPS) — **keep these ports private** in Codespaces.
> - Postgres is exposed on `5432` only for convenience (you can comment it out in `docker-compose.yml`).

## Quick start (Codespaces)

1. Create a Codespace for this repo.
2. In the terminal, start everything:

```bash
docker compose up -d
```

3. Verify Kong is up:

```bash
curl -s http://localhost:8001/status | head
```

If you see JSON output with version / status, you're good.

## Create a sample upstream + route

This will route `/httpbin` to `https://httpbin.org`.

```bash
# Create a Service
curl -sS -X POST http://localhost:8001/services   --data name=httpbin   --data url=https://httpbin.org   | jq .

# Create a Route
curl -sS -X POST http://localhost:8001/services/httpbin/routes   --data name=httpbin-route   --data paths[]=/httpbin   | jq .
```

Test through Kong's proxy:

```bash
curl -i http://localhost:8000/httpbin/get
```

## Stop / reset

Stop containers:

```bash
docker compose down
```

Stop and delete data (fresh start):

```bash
docker compose down -v
```

## Version pins

This repo pins Kong to `kong:3.13` and Postgres to `postgres:16` in `docker-compose.yml`.
If you want to use a different Kong tag, update both `kong` and `kong-migrations` services.

## Troubleshooting

### "migrations" failed
Reset and try again:

```bash
docker compose down -v
docker compose up -d
docker compose logs -f kong-migrations
```

### Admin API exposure in Codespaces
In the **Ports** tab, set `8001` and `8444` visibility to **Private**.
Your proxy port `8000` can be Public if you want to demo externally.

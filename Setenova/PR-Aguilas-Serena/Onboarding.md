## 1. Propósito

Es una **plataforma municipal de analítica de datos**, actualmente orientada a casos como Águilas y Mojácar. Reúne:

- dashboards de indicadores y datos turísticos;
- autenticación, usuarios, roles, permisos y licencias;
- consultas sobre datos mediante un agente SQL;
- ingesta batch desde AEMET, INE, DataGob, DataEstur y GA4;
- ingesta en tiempo real de sensores Wi‑Fi y parking;
- almacenamiento histórico, alertas y monitorización.

Es un **monorepo políglota**: Next.js/TypeScript, Python/FastAPI, SQL, Docker y Terraform (`docs/plan-reorganizacion-estructura.md:15`).

## 2. Arquitectura resumida

```text
Fuentes externas
 ├─ AEMET / INE / DataGob / DataEstur / GA4
 ├─ Google Maps
 ├─ Galgus por webhook
 └─ Cámaras de parking por MQTT
          │
          ▼
 Ingesta
 ├─ backend/ingest/batch
 ├─ backend/ingest/streaming
 └─ backend/ingest/oneshot
          │
          ▼
 Persistencia
 ├─ PostgreSQL: batch, autenticación, auditoría
 ├─ TimescaleDB: series temporales
 ├─ MongoDB: Google Maps y restos Galgus
 └─ Redis: JWT revocable, rate limiting y caché
          │
          ▼
 API FastAPI
 ├─ Autenticación/RBAC
 ├─ KPIs y verticales
 ├─ Alertas y exportación
 └─ SQL/Data Agents
          │
          ▼
 Frontend Next.js
 ├─ Dashboards y gráficos
 ├─ Administración
 ├─ PWA e i18n
 └─ Proxy server-side hacia la API
```

## 3. Componentes principales

| Ruta | Responsabilidad |
|---|---|
| `frontend/` | Aplicación Next.js, dashboards, gráficos, mapas, i18n y PWA |
| `backend/api/` | API REST FastAPI, autenticación, permisos, KPIs y agentes |
| `backend/ingest/batch/pipelines/` | Pipelines Python periodicos y carga en PostgreSQL |
| `backend/ingest/streaming/` | Webhooks Galgus y consumidor MQTT |
| `backend/ingest/oneshot/` | Scraping de Google Maps |
| `backend/storage/postgres/` | Esquemas, migraciones, autenticación, auditoría y vistas |
| `backend/storage/timescaledb/` | Series temporales, retención, compresión y vistas |
| `infra/` | Terraform, máquinas virtuales, HAProxy, backups y monitoring |
| `dev/` | Simuladores y pruebas de interoperabilidad |
| `raspberry/` | Software para displays de parking |
| `docs/` | Operación, despliegue, modelos de datos y auditorías |

La API se crea en `backend/api/src/main.py:24`. Sus routers cubren administración, datos municipales, alertas y agentes (`backend/api/src/routers/__init__.py:9`).

## 4. Flujo principal de una petición web

1. El usuario entra en Next.js.
2. NextAuth valida sus credenciales contra FastAPI.
3. FastAPI verifica la contraseña y emite un JWT.
4. El JWT backend queda cifrado en la cookie de sesión.
5. El navegador llama a `/api/backend/...`.
6. Un proxy server-side descifra y reenvía el JWT a FastAPI.
7. La API aplica autenticación, RBAC y rate limiting.
8. Se consultan PostgreSQL o TimescaleDB.
9. El dashboard recibe y representa los datos.

Entradas principales:

- `frontend/src/auth.ts:7`
- `frontend/src/app/api/backend/[...path]/route.ts:48`
- `backend/api/src/middleware/JWTMiddleware.py:51`
- `backend/api/src/main.py:77`

## 5. Flujos de ingesta

### Batch

```text
supercronic
  → entry point pipe-*
  → lockfile y run_id
  → descarga
  → transformación
  → PostgreSQL
  → vistas materializadas
  → auditoría, métricas y alertas
```

El runner común controla concurrencia, timeout, estados, métricas y códigos de salida en `backend/ingest/batch/pipelines/common/runner.py:72`.

Los entry points están en `pyproject.toml:22`:

```text
pipe-aemet-rt       pipe-aemet-hist
pipe-datagob        pipe-dataine
pipe-dataine-local  pipe-dataestur
pipe-ga4            pipe-matviews
pipe-status         pipe-schedule
pipe-bootstrap      pipe-interop-aemet
```

La programación se encuentra en `crontab:8`.

### Tiempo real

- **Galgus:** recibe un webhook, verifica HMAC, timestamp y dispositivos, y persiste en TimescaleDB (`backend/ingest/streaming/webhook_galgus/src/routers/endpoints.py:144`).
- **Parking MQTT:** consume eventos, valida tópicos, procesa lotes y actualiza vistas materializadas (`backend/ingest/streaming/mqtt_to_timescale/main.py:278`).

## 6. Cómo ejecutarlo

### Preparación

```bash
cp .env.example .env
mkdir -p secrets
```

### Pipelines batch con Docker

```bash
make net
make build
make up
make migrate-db
make bootstrap
```

Para ejecutar un pipeline concreto:

```bash
make run-pipeline PIPE=ga4
make run-pipeline PIPE=ga4 ARGS="--period yesterday"
```

Operación:

```bash
make status
make status-problems
make schedule
make audit
make failures
make logs-json
```

Referencias: `Makefile:18`, `Makefile:36`, `docs/operaciones.md:59`.

### API y frontend

```bash
docker compose -f docker-compose.api_frontend.yaml up -d --build
```

Frontend local:

```bash
cd frontend
npm ci
npm run dev
```

API local:

```bash
cd backend/api
pip install -r requirements.txt -r requirements-dev.txt
```

La imagen ejecuta:

```bash
uvicorn main:app --host 0.0.0.0 --port 80 --workers 2
```

Referencia: `backend/api/Dockerfile:55`.

### Streaming

```bash
docker compose -f docker-compose.timescale_webhook_mqtt.yaml up -d
docker compose -f docker-compose.mongo_gm.yaml up -d
```

## 7. Calidad y comprobaciones

```bash
# Pipelines
make lint
make type
make test

# Frontend
cd frontend
npm run lint
npm run typecheck
npm test

# API
cd backend/api
python -m pytest
```

Los pipelines son la parte mejor cubierta: unitarias, integración con PostgreSQL mediante testcontainers, idempotencia y cargas (`backend/ingest/batch/pipelines/tests/conftest.py:18`).

La API sólo ejecuta por defecto pruebas unitarias sin servicios reales (`backend/api/pytest.ini:1`).

El frontend tiene Vitest y Playwright, pero su umbral de cobertura es solamente **7–9%** (`frontend/vitest.config.ts:24`).

## 8. Riesgos principales

### Riesgos altos

1. **Complejidad operativa:** cuatro stacks Docker, varias bases de datos, secretos compartidos y múltiples máquinas.
2. **Ausencia de despliegue automatizado:** infraestructura y despliegues son manuales; sólo existen workflows de calidad.
3. **Cobertura frontend baja:** 7–9%, sin E2E autenticado ni build como gate obligatorio.
4. **Dependencia de APIs externas:** AEMET, INE, DataGob, DataEstur, GA4, Google Maps y MQTT pueden cambiar sus contratos.
5. **Scraper frágil de Google Maps:** depende de selectores específicos de la interfaz (`backend/ingest/oneshot/googlemaps/gm_mojacar.py:174`).

### Inconsistencias confirmadas

- `datarta` aparece en documentación y operación, pero ya no tiene entry point (`pyproject.toml:22`).
- El bootstrap no ejecuta todos los pipelines anunciados (`backend/ingest/batch/pipelines/ops/bootstrap.py:38`).
- El comentario del `crontab` habla de 11 tareas, pero sólo contiene cinco jobs activos (`crontab:1`).
- El README del frontend anuncia Next 14/React 18, pero el manifiesto usa Next 15/React 19 (`frontend/README.md:1`, `frontend/package.json:61`).
- El frontend documenta `type-check`, pero el script real es `typecheck` (`frontend/README.md:378`, `frontend/package.json:5`).
- Los pipelines exigen Python 3.12, pero parte de CI prueba con 3.11 (`pyproject.toml:5`, `.github/workflows/pipelines-ci.yml:47`).
- Hay una posible colisión de puerto `5011` entre la API y PostgreSQL de desarrollo (`docs/operaciones.md:199`).
- La configuración Mongo del webhook no coincide con la del Compose (`backend/ingest/streaming/webhook_galgus/src/db/db.py:9`, `.env.example:64`).
- La configuración activa embeddings semánticos, pero la imagen API no instala las dependencias opcionales; el sistema cae silenciosamente a keywords (`.env.example:125`, `backend/api/Dockerfile:29`).

## 9. Orden recomendado para estudiarlo

1. `README.md`
2. `docs/operaciones.md`
3. `docker-compose.api_frontend.yaml`
4. `backend/api/src/main.py`
5. `frontend/src/app/api/backend/[...path]/route.ts`
6. `backend/ingest/batch/pipelines/common/runner.py`
7. `crontab`
8. `backend/storage/postgres/`
9. `backend/storage/timescaledb/`
10. `infra/`

En resumen: **es una plataforma de datos bastante completa, con una capa batch madura, pero con operación distribuida, pruebas frontend débiles y documentación/configuración que necesita alinearse con el código real.**

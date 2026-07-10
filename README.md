# Innovatech

Plataforma de gestión de ventas y despachos compuesta por un frontend, dos microservicios backend (Spring Boot) y una base de datos relacional MySQL. El ciclo de integración y despliegue está automatizado con GitHub Actions hacia un clúster Amazon EKS.

## Arquitectura

```
Usuario → LoadBalancer (svc-frontend) → Nginx (Frontend)
                                           ├── proxy /api/v1/ventas     → svc-ventas (ClusterIP)     → api-ventas (Spring Boot)
                                           └── proxy /api/v1/despachos  → svc-despachos (ClusterIP)  → api-despachos (Spring Boot)

api-ventas / api-despachos → tienda-db (ClusterIP, headless) → MySQL 8.0 (namespace: tienda)
```

## Componentes

| Componente | Tecnología | Puerto |
|---|---|---|
| Frontend | React + Vite, servido con Nginx | 8080 |
| Backend Ventas | Spring Boot 3 / Java 17 | 8080 |
| Backend Despachos | Spring Boot 3 / Java 17 | 8080 |
| Base de datos | MySQL 8.0 | 3306 |

## Desarrollo local

Levantar todo el entorno localmente con Docker Compose:

```bash
docker-compose up --build
```

- Frontend: http://localhost:8082
- API Ventas: http://localhost:8080
- API Despachos: http://localhost:8081
- MySQL: localhost:3306

> Nota: en local, el proxy de Nginx del frontend está configurado para resolver los nombres DNS internos de Kubernetes (`svc-ventas.tienda.svc.cluster.local`), por lo que **no** enruta las llamadas API en `docker-compose`; este modo es únicamente para build y pruebas unitarias de cada componente por separado. La integración end-to-end se valida en el clúster EKS.

## Despliegue en AWS EKS

El despliegue a producción es 100% automatizado vía GitHub Actions (`.github/workflows/deploy.yml`) en cada `push` a `main`:

1. Build de las 3 imágenes Docker (frontend, backend-ventas, backend-despachos).
2. Push a Amazon ECR.
3. Actualización del kubeconfig del clúster EKS.
4. Aplicación de los manifiestos en `K8s/` sobre el namespace `tienda`.
5. `rollout restart` de los 3 Deployments para forzar el pull de las imágenes nuevas.

### Secrets requeridos en GitHub Actions

| Secret | Descripción |
|---|---|
| `AWS_ACCESS_KEY_ID` | Access key temporal de AWS Academy |
| `AWS_SECRET_ACCESS_KEY` | Secret key temporal de AWS Academy |
| `AWS_SESSION_TOKEN` | Session token temporal (expira cada 4h en Academy) |
| `AWS_REGION` | Región de despliegue (`us-east-1`) |
| `EKS_CLUSTER_NAME` | Nombre del clúster EKS |

## Estructura del repositorio

```
├── Frontend/            # React + Vite + Nginx (reverse proxy interno)
├── backend-ventas/      # Microservicio Spring Boot - Ventas
├── backend-despachos/   # Microservicio Spring Boot - Despachos
├── K8s/                 # Manifiestos de Kubernetes (Deployments, Services, HPA, Secrets)
├── docker-compose.yml   # Orquestación local para desarrollo
└── .github/workflows/   # Pipeline CI/CD (GitHub Actions)
```

## Seguridad

- Todas las imágenes corren con usuario no-root (`appuser` en los backends, `nginx-unprivileged` en el frontend).
- Imágenes base minimalistas (`alpine`, `jre-alpine`).
- Credenciales de base de datos gestionadas vía Kubernetes Secrets, nunca hardcodeadas.
- Credenciales de AWS gestionadas vía GitHub Secrets, nunca expuestas en el código.

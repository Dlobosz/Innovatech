# 🚀 Proyecto Innovatech Chile - [Nombre del Módulo, ej: Frontend Despachos]

Este repositorio contiene la implementación de [Frontend / Backend Ventas / Backend Despachos] para la Evaluación Parcial N°2.

## 🛠️ Arquitectura y Tecnologías
* **Contenedorización:** Docker (Multi-stage build para optimización y mínimo privilegio).
* **CI/CD:** GitHub Actions (Build, Push a Docker Hub y Deploy automatizado en AWS EC2).
* **Infraestructura:** Desplegado en Amazon Web Services (AWS) sobre instancias EC2 con Amazon Linux 2023.

## ⚙️ Cómo ejecutar localmente
1. Clonar el repositorio.
2. Asegurarse de tener Docker y Docker Compose instalados.
3. Ejecutar el comando: `docker-compose up -d` (o `docker build -t nombre-app .` según corresponda).

## 🔒 Variables de Entorno (Secrets configurados en el Pipeline)
* `DOCKER_USERNAME` / `DOCKER_PASSWORD`
* `EC2_HOST` / `EC2_USER` / `EC2_SSH_KEY`

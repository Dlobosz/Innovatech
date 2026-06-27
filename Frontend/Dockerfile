# ETAPA 1: Construcción (Build)
FROM node:18-alpine AS build
WORKDIR /app
# Copiamos package.json e instalamos dependencias
COPY package*.json ./
RUN npm install
# Copiamos el resto del código y construimos el proyecto
COPY . .
RUN npm run build

# ETAPA 2: Ejecución (Run) con mínimo privilegio
# Usamos una imagen de Nginx que ya viene configurada sin permisos root por seguridad
FROM nginxinc/nginx-unprivileged:alpine
# Copiamos los archivos compilados de la Etapa 1 a la carpeta que lee Nginx
COPY --from=build /app/dist /usr/share/nginx/html
# (Ojo: si usas React puro o Angular, la carpeta generada puede llamarse /app/build o /app/dist/nombre-app. Verifica esto).

EXPOSE 8080
CMD ["nginx", "-g", "daemon off;"]
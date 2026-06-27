# ETAPA 1: Construcción (Build)
FROM maven:3.9.6-eclipse-temurin-17-alpine AS build
WORKDIR /app
# Copiamos el archivo de dependencias y descargamos (optimiza caché)
COPY pom.xml .
RUN mvn dependency:go-offline
# Copiamos el código fuente y compilamos
COPY src ./src
RUN mvn clean package -DskipTests

# ETAPA 2: Ejecución (Run)
FROM eclipse-temurin:17-jre-alpine
WORKDIR /app

# Creación de usuario 'no root' por seguridad
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser

# Copiamos el archivo .jar generado en la Etapa 1
COPY --from=build /app/target/*.jar app.jar

EXPOSE 8080
# (Si tu backend corre en otro puerto, cámbialo arriba)

CMD ["java", "-jar", "app.jar"]
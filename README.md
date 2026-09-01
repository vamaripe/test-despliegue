# test-despliegue


```
docker volume create datos_aplicacion
```

```
docker volume ls
```

## Frontend

### Variables de entorno 

Ip de nuestra maquina invitada.

```
EXPO_PUBLIC_API_URL=http://10.0.2.15:8080
```

### Dockerfile

```
# =========================
# Etapa 1: Build del frontend
# =========================
FROM node:22-alpine AS builder

WORKDIR /app

COPY package*.json ./

RUN npm ci

COPY . .

ARG EXPO_PUBLIC_API_URL
ENV EXPO_PUBLIC_API_URL=$EXPO_PUBLIC_API_URL

RUN npx expo export --platform web


# =========================
# Etapa 2: Servir con Nginx
# =========================
FROM nginx:alpine

COPY --from=builder /app/dist /usr/share/nginx/html

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

### .dockerignore

```
node_modules
.expo
.git
dist
npm-debug.log
Dockerfile
.dockerignore
```
## Backend

### Variables de entorno 

```
# ─── BASE DE DATOS ───────────────────────────────────────────
DB_URL=jdbc:postgresql://postgres:5432/facelit-db
DB_USERNAME=facelit_user
DB_PASSWORD=facelit_password

# ─── DOCKER / POSTGRES ───────────────────────────────────────
POSTGRES_DB=facelit-db
POSTGRES_USER=facelit_user
POSTGRES_PASSWORD=facelit_password
POSTGRES_PORT=5439

# ─── JWT ─────────────────────────────────────────────────────
JWT_SECRET=FaceLit2025$ClaveSecretaSuperSegura#SENA@Backend!

# ─── GMAIL ───────────────────────────────────────────────────
MAIL_USERNAME=facelit.system@gmail.com
MAIL_PASSWORD=pgkyeljftwhclykr
```

### Dockerfile 

```
# =========================
# Etapa 1: Compilación
# =========================
FROM eclipse-temurin:17-jdk-alpine AS builder

WORKDIR /app

COPY .mvn .mvn
COPY mvnw pom.xml ./

RUN chmod +x mvnw

RUN ./mvnw dependency:go-offline -DskipTests

COPY src src

RUN ./mvnw clean package -DskipTests


# =========================
# Etapa 2: Ejecución
# =========================
FROM eclipse-temurin:17-jre-alpine

WORKDIR /app

COPY --from=builder /app/target/*.jar app.jar

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "app.jar"]
```

### .dockerignore

```
.git
.gitignore
.vscode
target
*.jar
README.md
docs
```

## BD

### Variables de entorno
```
POSTGRES_DB=facelit-db
POSTGRES_USER=facelit_user
POSTGRES_PASSWORD=facelit_password
POSTGRES_PORT=5439
```

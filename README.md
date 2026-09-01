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

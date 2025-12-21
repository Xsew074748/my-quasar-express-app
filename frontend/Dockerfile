# Stage 1: Builder
FROM node:20-alpine AS builder
WORKDIR /app

# install dependencies (cached if package.json doesn't change)
COPY package*.json ./
RUN npm ci --legacy-peer-deps

# copy source and build
COPY . .
RUN npm run build

# Stage 2: Nginx
FROM nginx:1.27-alpine
COPY --from=builder /app/dist/spa /usr/share/nginx/html
# Stage 1: Builder
FROM node:20-alpine AS builder
WORKDIR /app

# copy everything first
COPY . .

# install dependencies
RUN npm ci --legacy-peer-deps

# build project
RUN npm run build


# Stage 2: Nginx
FROM nginx:1.27-alpine
COPY --from=builder /app/dist/spa /usr/share/nginx/html
########################
# Stage 1 — Build React
########################
FROM public.ecr.aws/docker/library/node:18-alpine AS builder
# FROM node:18-alpine AS builder

# Create app directory
WORKDIR /app

# Copy package files first (for better caching)
COPY package*.json ./

# Install dependencies (clean install)
RUN npm ci --no-audit --no-fund

# Copy source code
COPY . .

# Build production version
RUN npm run build


########################
# Stage 2 — Production (Nginx)
########################
FROM public.ecr.aws/nginx/nginx:1.28.2-alpine
# FROM nginx:1.28.2-alpine

# Metadata labels
LABEL org.opencontainers.image.title="RealWorld Frontend"
LABEL org.opencontainers.image.description="React frontend served by Nginx"

# Remove default nginx config and static files (one layer)
RUN rm /etc/nginx/conf.d/default.conf && \
    rm -rf /usr/share/nginx/html/*

# Copy nginx template for runtime env substitution
COPY nginx.conf.template /etc/nginx/templates/default.conf.template

# Copy build output from builder
COPY --from=builder /app/build /usr/share/nginx/html

# Expose application port
EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]

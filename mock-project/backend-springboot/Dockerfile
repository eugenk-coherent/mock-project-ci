########################
# Stage 1 — Build
########################
FROM public.ecr.aws/docker/library/eclipse-temurin:17-jdk-alpine AS builder
# FROM eclipse-temurin:17-jdk-alpine AS builder

WORKDIR /app

# Copy Gradle wrapper and build files first (for caching)
COPY gradlew .
COPY gradle gradle
COPY build.gradle .

# Make wrapper executable and download dependencies (single layer)
RUN chmod +x gradlew && \
    ./gradlew dependencies --no-daemon

# Copy source code
COPY src src

# Build application (skip tests for faster build)
RUN ./gradlew build -x test --no-daemon

########################
# Stage 2 — Runtime
########################
FROM public.ecr.aws/docker/library/eclipse-temurin:17-jre-alpine
# FROM eclipse-temurin:17-jre-alpine

# Metadata labels
LABEL org.opencontainers.image.title="RealWorld Backend"
LABEL org.opencontainers.image.description="Spring Boot backend for RealWorld Conduit"

# Create app directory
WORKDIR /app

# Create non-root user
RUN addgroup -S appgroup && adduser -S appuser -G appgroup

# Copy jar with correct ownership
COPY --from=builder --chown=appuser:appgroup /app/build/libs/*.jar app.jar

# Switch to non-root user
USER appuser

# Expose application port
EXPOSE 8080

# Health check
HEALTHCHECK --interval=30s --timeout=5s --start-period=20s --retries=3 \
  CMD wget -qO- http://localhost:8080/api/tags || exit 1

ENTRYPOINT ["java", "-jar", "app.jar"]

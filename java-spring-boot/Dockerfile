# syntax=docker/dockerfile:1.7

FROM maven:3.9-eclipse-temurin-17-alpine AS build

WORKDIR /workspace

COPY pom.xml .

RUN --mount=type=cache,target=/root/.m2 \
    mvn -B -ntp dependency:go-offline

COPY src ./src

ARG SKIP_TESTS=true
RUN --mount=type=cache,target=/root/.m2 \
    mvn -B -ntp clean package -DskipTests=${SKIP_TESTS}

FROM eclipse-temurin:17-jre-alpine AS runtime

LABEL org.opencontainers.image.title="spring-boot-template" \
      org.opencontainers.image.description="Secure multi-stage Spring Boot Docker template"

WORKDIR /app

RUN addgroup -S spring && \
    adduser -S spring -G spring && \
    mkdir -p /app && \
    chown -R spring:spring /app

USER spring:spring

COPY --from=build --chown=spring:spring /workspace/target/*.jar /app/app.jar

EXPOSE 8080

ENV JAVA_TOOL_OPTIONS="-XX:MaxRAMPercentage=75.0 -XX:+UseContainerSupport"

ENTRYPOINT ["java", "-jar", "/app/app.jar"]

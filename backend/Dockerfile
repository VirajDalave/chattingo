FROM maven:3.9.6-eclipse-temurin-17 AS maven
WORKDIR /app
COPY pom.xml .
COPY src ./src
RUN mvn -B -DskipTests package

FROM eclipse-temurin:17-jdk AS builder
WORKDIR /app
COPY --from=maven /app/target/*.jar app.jar

# ===== Stage 3: Distroless JRE runtime =====
FROM gcr.io/distroless/java17-debian12:nonroot AS runtime
WORKDIR /app
COPY --from=builder /app/app.jar app.jar
EXPOSE 8080
# Distroless has no shell. Entrypoint must be array form.
ENTRYPOINT ["java","-jar","app.jar"]


FROM openjdk:13-jdk-alpine
MAINTAINER techsophy
RUN mkdir -p /app/bin
RUN addgroup -S spring && adduser -S spring -G spring
USER spring:spring
ARG JAR_FILE=build/libs/*.jar
ARG TELEMETRY=src/main/jib/opentelemetry-javaagent-all.jar
COPY ${JAR_FILE} /app/bin/app.jar
COPY ${TELEMETRY} /app/bin
ENTRYPOINT ["java", "-Dotel.exporter=jaeger", "-Dotel.exporter.jaeger.endpoint=192.168.1.71:14250", "-Dotel.exporter.jaeger.service.name=cloud-gateway", "-Dapplication.name=cloud-gateway", "-javaagent:/app/bin/opentelemetry-javaagent-all.jar", "-jar","/app/bin/app.jar"]
#Debug
# ENTRYPOINT ["java", "-Dotel.javaagent.debug=true", "-Dotel.exporter=jaeger", "-Dotel.exporter.jaeger.endpoint=192.168.1.71:14250", "-Dotel.exporter.jaeger.service.name=service-1", "-Dapplication.name=service-1", "-javaagent:/app/bin/opentelemetry-javaagent-all.jar", "-jar","/app/bin/app.jar"]
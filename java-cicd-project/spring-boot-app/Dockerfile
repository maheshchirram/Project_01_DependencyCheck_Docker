FROM eclipse-temurin:11-jre

WORKDIR /opt/app

COPY target/*.jar app.jar

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "app.jar"]

FROM eclipse-temurin:21-jdk

WORKDIR /app

COPY target/ClaimRequestService-0.0.1-SNAPSHOT.jar app.jar

EXPOSE 2001

ENTRYPOINT ["java","-jar","app.jar"]
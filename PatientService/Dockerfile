FROM eclipse-temurin:21-jdk

WORKDIR /app

COPY target/PatientService-0.0.1-SNAPSHOT.jar app.jar

EXPOSE 2002

ENTRYPOINT ["java","-jar","app.jar"]
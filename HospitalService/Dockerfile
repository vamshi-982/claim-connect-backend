FROM eclipse-temurin:21-jdk

WORKDIR /app

COPY target/HospitalService-0.0.1-SNAPSHOT.jar app.jar

EXPOSE 2000

ENTRYPOINT ["java","-jar","app.jar"]
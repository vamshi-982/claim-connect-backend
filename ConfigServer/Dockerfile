FROM eclipse-temurin:21-jdk

WORKDIR /app

COPY target/ConfigServer-0.0.1-SNAPSHOT.jar app.jar

EXPOSE 1234

ENTRYPOINT ["java","-jar","app.jar"]
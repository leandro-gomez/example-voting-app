FROM maven:3.6.1-jdk-8-alpine

WORKDIR /app

COPY pom.xml pom.xml
COPY . /app
RUN mvn package -DskipTests

CMD ["java", "-jar", "target/worker-jar-with-dependencies.jar"]

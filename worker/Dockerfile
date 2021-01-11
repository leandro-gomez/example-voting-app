FROM maven:3.6.1-jdk-8-alpine

WORKDIR /app

COPY pom.xml pom.xml
RUN mvn package -DskipTests
COPY . /app

CMD ["java", "-jar", "target/worker-jar-with-dependencies.jar"]

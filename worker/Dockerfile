FROM baugereau/worker-base:latest AS build
WORKDIR /code
COPY . . 
RUN mvn package -DskiptTests

FROM openjdk:8-jre-alpine AS package
WORKDIR /run
COPY --from=build /code/target/worker-jar-with-dependencies.jar worker.jar
CMD java -jar worker.jar

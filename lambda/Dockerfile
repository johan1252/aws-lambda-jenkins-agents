FROM alpine as base

RUN apk add openjdk11

# configure the build environment
FROM base as build
RUN apk add maven
WORKDIR /src

# cache and copy dependencies
ADD pom.xml .
RUN mvn dependency:go-offline dependency:copy-dependencies

# compile the function
ADD . .
RUN mvn package 

# copy the function artifact and dependencies onto a clean base
FROM base
WORKDIR /function

COPY --from=build /src/target/dependency/*.jar ./
COPY --from=build /src/target/*.jar ./

#RUN apk add jq

# configure the runtime startup as main
ENTRYPOINT [ "/usr/bin/java", "-cp", ".:./*:/opt/java/lib/*", "com.amazonaws.services.lambda.runtime.api.client.AWSLambda" ]
# pass the name of the function handler as an argument to the runtime
CMD [ "io.jenkins.agent.aws.lambda.AgentHandler::handleRequest" ]
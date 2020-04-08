# syntax=docker/dockerfile:experimental

## WARNING This build requires experimental features (native build and buildkit)
## run with the following command
## COMPOSE_DOCKER_CLI_BUILD=1 DOCKER_BUILDKIT=1 docker-compose up --build

## If needed invalidate the cache with
#docker builder prune --filter type=exec.cachemount

FROM maven:3.6.3-jdk-11-openj9 as maven
WORKDIR /build
COPY . /build

COPY CI/my-settings.xml $HOME/.m2/settings.xml


# If in CI server (Travis), fixes version.
RUN if [[ "${TRAVIS_BRANCH}" == release-* ]]; then
    QUALIFIER=-release-${TRAVIS_BUILD_NUMBER}
  elif [[ "${TRAVIS_BRANCH}" == "develop" ]]; then
    QUALIFIER=-develop-${TRAVIS_BUILD_NUMBER}
  elif [[ "${TRAVIS_BRANCH}" == v[0-9]* ]]; then
    QUALIFIER=""
  else
    QUALIFIER=-${TRAVIS_BRANCH}-${TRAVIS_BUILD_NUMBER}
  fi && \
  mvn build-helper:parse-version \
    versions:set -DgenerateBackupPoms=false \
    -DnewVersion='${parsedVersion.majorVersion}.${parsedVersion.minorVersion}.${parsedVersion.incrementalVersion}'${QUALIFIER}

# Compile and test
RUN --mount=type=cache,target=$HOME/.m2 \
    mvn -B -e -T 1C \
        deploy

#FROM adoptopenjdk:11-jre-hotspot
#ENTRYPOINT exec java $JAVA_OPTS -jar /app/my-app.jar
#ENTRYPOINT ["java","-jar","/app/my-app.jar"]
#COPY --from=maven /build/target/*jar-with-dependencies.jar /app/my-app.jar


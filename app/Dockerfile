FROM curlimages/curl:8.2.1 AS download
ARG OTEL_AGENT_VERSION="2.10.0"
RUN curl --silent --fail -L "https://github.com/open-telemetry/opentelemetry-java-instrumentation/releases/download/v${OTEL_AGENT_VERSION}/opentelemetry-javaagent.jar" \
    -o "$HOME/opentelemetry-javaagent.jar"

FROM maven:3.8.6-openjdk-8 AS build
ADD . /build
RUN cd /build && mvn package --quiet -DskipTests

FROM openjdk:8-jre-slim
COPY --from=build /build/target/*.jar /app.jar
COPY --from=download /home/curl_user/opentelemetry-javaagent.jar /opentelemetry-javaagent.jar


ENV OTEL_EXPORTER_OTLP_HEADERS="lightstep-access-token=y5mu3HlztSMnsZWPoWlN7K6vj/nO9gvjwjyilS7M9oPFU5nMdlXfW38SfoJnRvunjG4hwq7h8fCYNw6lRPj2stz3oTAXkL2wLd/uOSel"

ENTRYPOINT ["java", \
  "-javaagent:/opentelemetry-javaagent.jar", \
  "-Dotel.logs.exporter=logging,otlp", \
  "-Dotel.traces.exporter=logging,otlp", \
  "-Dotel.metrics.exporter=logging,otlp", \
  "-Dotel.exporter.otlp.protocol=grpc", \
  "-Dotel.exporter.otlp.endpoint=https://ingest.lightstep.com:443", \
  "-jar", "/app.jar" \
  ]

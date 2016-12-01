FROM cassandra:3.9

ENV CASSANDRA_CONFIG /etc/cassandra

RUN apt-get update && apt-get install -y wget

RUN wget -O ${CASSANDRA_CONFIG}/jmx_prometheus_javaagent-0.7.jar \
  http://central.maven.org/maven2/io/prometheus/jmx/jmx_prometheus_javaagent/0.7/jmx_prometheus_javaagent-0.7.jar

RUN chmod +r ${CASSANDRA_CONFIG}/jmx_prometheus_javaagent-0.7.jar

ADD prometheus-cassandra.yml ${CASSANDRA_CONFIG}/prometheus-cassandra.yml

RUN echo 'JVM_OPTS="$JVM_OPTS -javaagent:${CASSANDRA_CONFIG}/jmx_prometheus_javaagent-0.7.jar=${PROMETHEUS_PORT:-7070}:${CASSANDRA_CONFIG}/prometheus-cassandra.yml"' >> ${CASSANDRA_CONFIG}/cassandra-env.sh

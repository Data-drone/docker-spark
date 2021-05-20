FROM nvidia/cuda:11.0.3-devel-ubuntu20.04

LABEL maintainer="Gezim Sejdiu <g.sejdiu@gmail.com>, Giannis Mouchakis <gmouchakis@gmail.com>"

ENV ENABLE_INIT_DAEMON true
ENV INIT_DAEMON_BASE_URI http://identifier/init-daemon
ENV INIT_DAEMON_STEP spark_master_init

ENV SPARK_VERSION=3.1.1
ENV HADOOP_VERSION=3.2

COPY wait-for-step.sh /
COPY execute-step.sh /
COPY finish-step.sh /

#COPY bde-spark.css /css/org/apache/spark/ui/static/timeline-view.css

RUN apt-get update && DEBIAN_FRONTEND="noninteractive" apt-get install -y curl bash openjdk-8-jdk python3-pip libnss3 libc6 wget \
      && ln -s /lib64/ld-linux-x86-64.so.2 /lib/ld-linux-x86-64.so.2 \
      && chmod +x *.sh \
      && wget https://archive.apache.org/dist/spark/spark-${SPARK_VERSION}/spark-${SPARK_VERSION}-bin-hadoop${HADOOP_VERSION}.tgz \
      && tar -xvzf spark-${SPARK_VERSION}-bin-hadoop${HADOOP_VERSION}.tgz \
      && mv spark-${SPARK_VERSION}-bin-hadoop${HADOOP_VERSION} spark \
      && rm spark-${SPARK_VERSION}-bin-hadoop${HADOOP_VERSION}.tgz \
      #&& cd /css \
      #&& jar uf /spark/jars/spark-core_2.11-${SPARK_VERSION}.jar org/apache/spark/ui/static/timeline-view.css \
      && cd /

# Add repids 0.5.0
RUN mkdir /opt/sparkRapidsPlugin
RUN wget -O /opt/sparkRapidsPlugin/rapids-4-spark_2.12-0.5.0.jar https://repo1.maven.org/maven2/com/nvidia/rapids-4-spark_2.12/0.5.0/rapids-4-spark_2.12-0.5.0.jar
RUN wget -O /opt/sparkRapidsPlugin/cudf-0.19.2-cuda11.jar https://repo1.maven.org/maven2/ai/rapids/cudf/0.19.2/cudf-0.19.2-cuda11.jar
RUN wget -O /opt/sparkRapidsPlugin/getGpusResources.sh https://raw.githubusercontent.com/apache/spark/master/examples/src/main/scripts/getGpusResources.sh

RUN chmod u+x /opt/sparkRapidsPlugin/getGpusResources.sh \
      && chmod u+x /opt/sparkRapidsPlugin/rapids-4-spark_2.12-0.5.0.jar \
      && chmod u+x /opt/sparkRapidsPlugin/cudf-0.19.2-cuda11.jar

#Give permission to execute scripts
RUN chmod +x /wait-for-step.sh && chmod +x /execute-step.sh && chmod +x /finish-step.sh

# Fix the value of PYTHONHASHSEED
# Note: this is needed when you use Python 3.3 or greater
ENV PYTHONHASHSEED 1

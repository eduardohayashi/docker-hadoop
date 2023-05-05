FROM debian:stretch-slim

MAINTAINER Eduardo Hayashi <eduardo@suisse.com>


CMD ["bash"]
#Update stretch repositories
RUN sed -i s/deb.debian.org/archive.debian.org/g /etc/apt/sources.list
RUN sed -i 's|security.debian.org|archive.debian.org/|g' /etc/apt/sources.list
RUN sed -i '/stretch-updates/d' /etc/apt/sources.list

RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends openjdk-8-jdk net-tools curl netcat gnupg && rm -rf /var/lib/apt/lists/*
ENV JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64/
RUN curl -O https://dist.apache.org/repos/dist/release/hadoop/common/KEYS
RUN gpg --import KEYS

ENV HADOOP_VERSION=3.3.5
ENV HADOOP_HOME=/opt/hadoop-3.3.5
ENV HADOOP_CONF_DIR=/etc/hadoop
ENV HADOOP_URL=https://www.apache.org/dist/hadoop/common/hadoop-3.3.5/hadoop-3.3.5.tar.gz
ENV MULTIHOMED_NETWORK=1
ENV USER=root
ENV PATH=/opt/hadoop-$HADOOP_VERSION/bin/:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
ENV HDFS_CONF_dfs_namenode_name_dir=file:///hadoop/dfs/name

RUN /bin/sh -c set -x     && curl -fSL "$HADOOP_URL" -o /tmp/hadoop.tar.gz     && curl -fSL "$HADOOP_URL.asc" -o /tmp/hadoop.tar.gz.asc     && gpg --verify /tmp/hadoop.tar.gz.asc     && tar -xvf /tmp/hadoop.tar.gz -C /opt/     && rm /tmp/hadoop.tar.gz*

ADD entrypoint.sh /
RUN chmod a+x /entrypoint.sh

ADD run.sh /
RUN chmod a+x /run.sh


RUN  ln -s /opt/hadoop-$HADOOP_VERSION/etc/hadoop /etc/hadoop
RUN  mkdir -p /opt/hadoop-$HADOOP_VERSION/logs
RUN  mkdir /hadoop-data
RUN  mkdir -p /hadoop/dfs/name

VOLUME [/hadoop/dfs/name]



HEALTHCHECK --interval=30s --timeout=3s \
  CMD curl -f http://localhost:9870 || exit 1
EXPOSE 9870

ENTRYPOINT ["/entrypoint.sh"]
CMD ["/run.sh"]

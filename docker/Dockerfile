FROM ubuntu:22.04
ENV ZK_USER=zookeeper \
ZK_DATA_DIR=/var/lib/zookeeper/data \
ZK_DATA_LOG_DIR=/var/lib/zookeeper/log \
ZK_LOG_DIR=/var/log/zookeeper \
JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64

ARG GPG_KEY=BBE7232D7991050B54C8EA0ADC08637CA615D22C
ARG ZK_DIST=zookeeper-3.8.1
RUN set -x \
    && sed -i 's@//ports.ubuntu.com@//mirrors.ustc.edu.cn@g' /etc/apt/sources.list \
    && apt-get update \
    && apt-get install -y openjdk-8-jre-headless wget netcat-openbsd gpg\
       && wget -q "http://www.apache.org/dist/zookeeper/$ZK_DIST/apache-$ZK_DIST-bin.tar.gz" \
    && wget -q "http://www.apache.org/dist/zookeeper/$ZK_DIST/apache-$ZK_DIST-bin.tar.gz.asc" \
    && export GNUPGHOME="$(mktemp -d)" \
    && gpg --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys "$GPG_KEY" \
    && gpg --batch --verify "apache-$ZK_DIST-bin.tar.gz.asc" "apache-$ZK_DIST-bin.tar.gz" \
    && tar -xzf "apache-$ZK_DIST-bin.tar.gz" -C /opt \
    && rm -r "$GNUPGHOME" "apache-$ZK_DIST-bin.tar.gz" "apache-$ZK_DIST-bin.tar.gz.asc" \
    && ln -s /opt/apache-$ZK_DIST-bin /opt/zookeeper \
    && rm -rf /opt/zookeeper/CHANGES.txt \
    /opt/zookeeper/README.txt \
    /opt/zookeeper/NOTICE.txt \
    /opt/zookeeper/CHANGES.txt \
    /opt/zookeeper/README_packaging.txt \
    /opt/zookeeper/build.xml \
    /opt/zookeeper/config \
    /opt/zookeeper/contrib \
    /opt/zookeeper/dist-maven \
    /opt/zookeeper/docs \
    /opt/zookeeper/ivy.xml \
    /opt/zookeeper/ivysettings.xml \
    /opt/zookeeper/recipes \
    /opt/zookeeper/src \
    /opt/zookeeper/apache-$ZK_DIST.jar.asc \
    /opt/zookeeper/apache-$ZK_DIST.jar.md5 \
    /opt/zookeeper/apache-$ZK_DIST.jar.sha1 \
	&& apt-get autoremove -y wget \
	&& rm -rf /var/lib/apt/lists/*

#Copy configuration generator script to bin
COPY scripts /opt/zookeeper/bin/

# Create a user for the zookeeper process and configure file system ownership 
# for nessecary directories and symlink the distribution as a user executable
RUN set -x \
	&& useradd $ZK_USER \
    && [ `id -u $ZK_USER` -eq 1000 ] \
    && [ `id -g $ZK_USER` -eq 1000 ] \
    && mkdir -p $ZK_DATA_DIR $ZK_DATA_LOG_DIR $ZK_LOG_DIR /usr/share/zookeeper /tmp/zookeeper /usr/etc/ \
    && chown -R "$ZK_USER:$ZK_USER" /opt/apache-$ZK_DIST-bin $ZK_DATA_DIR $ZK_LOG_DIR $ZK_DATA_LOG_DIR /tmp/zookeeper \
	&& ln -s /opt/zookeeper/conf/ /usr/etc/zookeeper \
	&& ln -s /opt/zookeeper/bin/* /usr/bin \
	&& ln -s /opt/zookeeper/lib/* /usr/share/zookeeper 

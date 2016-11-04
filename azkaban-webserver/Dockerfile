# VERSION 1.0
# AUTHOR: Matthieu "Puckel_" Roisil
# DOCKER HUB: https://hub.docker.com/u/puckel/
# DESCRIPTION: Simple Debian image for azkaban-webserver based on official java:8
# BUILD: docker build --rm -t puckel/azkaban-webserver .
# SOURCE: https://github.com/puckel/docker-azkaban

FROM java:8
MAINTAINER Puckel_

# Never prompts the user for choices on installation/configuration of packages
ENV DEBIAN_FRONTEND noninteractive
ENV TERM linux
# Work around initramfs-tools running on kernel 'upgrade': <http://bugs.debian.org/cgi-bin/bugreport.cgi?bug=594189>
ENV INITRD No

ENV AZK_VERSION 3.6.0
#ENV MYSQL_JDBC_VERSION 5.1.40

WORKDIR /root

COPY bin/azkaban-web-server-$AZK_VERSION.tar.gz /root/azkaban-web-server-$AZK_VERSION.tar.gz

RUN apt-get update -yqq \
    && apt-get install -yqq \
    curl \
    netcat \
    mysql-client \
    && tar xfz azkaban-web-server-$AZK_VERSION.tar.gz \
    #&& curl -sLk https://s3-eu-west-1.amazonaws.com/ci-rtlambda/dist/azkaban/azkaban-web-server-$AZK_VERSION.tar.gz | tar xz \    
    #&& curl -sLk http://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-$MYSQL_JDBC_VERSION.tar.gz| tar xz \
    #&& mv mysql-connector-java-$MYSQL_JDBC_VERSION/mysql-connector-java-$MYSQL_JDBC_VERSION-bin.jar azkaban-web-server-$AZK_VERSION/lib/ \
    #&& rm -rf mysql-connector-java-$MYSQL_JDBC_VERSION \
    && mkdir azkaban-web-server-$AZK_VERSION/logs/ \
    # Work around to run container as a daemon
    && sed -i "s/&//" azkaban-web-server-$AZK_VERSION/bin/azkaban-web-start.sh \
    && apt-get clean \
    && rm -rf \
    /var/lib/apt/lists/* \
    /tmp/* \
    /var/tmp/* \
    /usr/share/man \
    /usr/share/doc \
    /usr/share/doc-base

ADD keystore azkaban-web-server-$AZK_VERSION/keystore
ADD conf/azkaban.properties azkaban-web-server-$AZK_VERSION/conf/azkaban.properties
ADD bin/create-all-sql-$AZK_VERSION.sql /root/create-all-sql-$AZK_VERSION.sql  

# Azkaban web server port
EXPOSE 8443

# Define default workdir
WORKDIR /root/azkaban-web-server-$AZK_VERSION
ADD script/run.sh run.sh
RUN chmod +x run.sh

CMD ["./run.sh"]

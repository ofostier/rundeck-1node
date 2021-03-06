# Dockerfile for rundeck
# https://github.com/jjethwa/rundeck

FROM debian:jessie

MAINTAINER ofostier <ofostier@gmail.com>

ENV DEBIAN_FRONTEND=noninteractive \
    SERVER_URL=${SERVER_URL} \
    RUNDECK_STORAGE_PROVIDER=file \
    RUNDECK_PROJECT_STORAGE_TYPE=file \
    NO_LOCAL_MYSQL=false \
    LOGIN_MODULE=RDpropertyfilelogin \
    JAAS_CONF_FILE=jaas-loginmodule.conf \
    KEYSTORE_PASS=adminadmin \
    TRUSTSTORE_PASS=adminadmin

RUN echo "deb http://ftp.debian.org/debian jessie-backports main" >> /etc/apt/sources.list && \
    apt-get -qq update && \
    apt-get -qqy install -t jessie-backports --no-install-recommends bash openjdk-8-jre-headless ca-certificates-java supervisor procps sudo ca-certificates openssh-client mysql-server mysql-client pwgen curl git uuid-runtime parallel vim && \
    cd /tmp/ && \
    curl -Lo /tmp/rundeck.deb http://dl.bintray.com/rundeck/rundeck-deb/rundeck-2.8.0-1-GA.deb && \
    echo '791802dd432048eb296c68d0e7c4ea945b3b67cec356bde32f6ba94da2cfa1ea  rundeck.deb' > /tmp/rundeck.sig && \
    shasum -a256 -c /tmp/rundeck.sig && \
    curl -Lo /tmp/rundeck-cli.deb https://github.com/rundeck/rundeck-cli/releases/download/v1.0.9/rundeck-cli_1.0.9-1_all.deb && \
    echo 'f311bed15c69c8bec4b7981daf75df091210304ec4fbcb506b2f76cad094f343  rundeck-cli.deb' > /tmp/rundeck-cli.sig && \
    shasum -a256 -c /tmp/rundeck-cli.sig && \
    cd - && \
    dpkg -i /tmp/rundeck*.deb && rm /tmp/rundeck*.deb && \
    chown rundeck:rundeck /tmp/rundeck && \
    mkdir -p /var/lib/rundeck/.ssh && \
    chown rundeck:rundeck /var/lib/rundeck/.ssh && \
    sed -i "s/export RDECK_JVM=\"/export RDECK_JVM=\"\${RDECK_JVM} /" /etc/rundeck/profile && \
    curl -Lo /var/lib/rundeck/libext/rundeck-slack-incoming-webhook-plugin-0.6.jar https://github.com/higanworks/rundeck-slack-incoming-webhook-plugin/releases/download/v0.6.dev/rundeck-slack-incoming-webhook-plugin-0.6.jar && \
    echo 'd23b31ec4791dff1a7051f1f012725f20a1e3e9f85f64a874115e46df77e00b5  rundeck-slack-incoming-webhook-plugin-0.6.jar' > /tmp/rundeck-slack-plugin.sig && \
    cd /var/lib/rundeck/libext/ && \
    shasum -a256 -c /tmp/rundeck-slack-plugin.sig && \
    cd - && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

ADD content/ /
RUN chmod u+x /opt/run && \
    mkdir -p /var/log/supervisor && mkdir -p /opt/supervisor && \
    chmod u+x /opt/supervisor/rundeck && chmod u+x /opt/supervisor/mysql_supervisor

RUN useradd -ms /bin/bash ftpdata
RUN echo ftpdata:ydfnsjdvr | chpasswd

#RUN KEY=$(cat /var/lib/rundeck/.ssh/id_rsa.pub) ; ssh ftpdata@oracle-java8 echo $KEY >> /ftpdata/.ssh/authorized_keys
#ssh ftpdata@oracle-java8 -i /var/lib/rundeck/.ssh/id_rsa

EXPOSE 4440 4443 22

VOLUME  ["/etc/rundeck", "/var/rundeck", "/var/lib/rundeck", "/var/lib/mysql", "/var/log/rundeck", "/opt/rundeck-plugins", "/var/lib/rundeck/logs", "/var/lib/rundeck/var/storage"]

ENTRYPOINT ["/opt/run"]
CMD ["-e", ""]

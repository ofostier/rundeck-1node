FROM ubuntu:16.04

MAINTAINER ofostier <ofostier@gmail.com>

RUN apt-get -qq update && \
    apt-get -qqy install bash sudo ca-certificates openssh-client pwgen git uuid-runtime parallel vim curl && \
    apt-get clean

RUN useradd -ms /bin/bash ftpdata
RUN echo ftpdata:ydfnsjdvr | chpasswd

ADD assets /assets
RUN /assets/setup.sh

EXPOSE 22
EXPOSE 1521
EXPOSE 8080

CMD /usr/sbin/startup.sh && /usr/sbin/sshd -D

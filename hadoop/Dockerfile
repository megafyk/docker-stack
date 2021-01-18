FROM alpine

RUN sed -i 's/https/http/' /etc/apk/repositories

RUN apk add --no-cache bash

RUN apk update && apk add ca-certificates \
    && rm -rf /var/cache/apk/*
COPY ./myCA.crt /usr/local/share/ca-certificates
RUN update-ca-certificates
# add alternatives packages and clean
RUN apk add --update openssh \
    && rm -rf /tmp/* /var/cache/apk/*
#
RUN apk add curl vim sudo openjdk8  \
    && rm -rf /tmp/* /var/cache/apk/*
# add entrypoint script
ADD docker-entrypoint.sh /usr/local/bin
#make sure we get fresh keys
RUN rm -rf /etc/ssh/ssh_host_rsa_key /etc/ssh/ssh_host_dsa_key
#exec entrypoint file
RUN chmod 777 /usr/local/bin/docker-entrypoint.sh \
    && ln -s /usr/local/bin/docker-entrypoint.sh
#
RUN addgroup hadoop && adduser -D -s /bin/bash -G hadoop hadoop && passwd -u hadoop
#
RUN echo "hadoop ALL=(ALL) ALL" >> /etc/sudoers
#
ADD hosts tmp/
#
WORKDIR /home/hadoop
#
ADD ./hadoop-3.2.2 /home/hadoop/hadoop
#
ADD ./etc /home/hadoop/hadoop/etc
#
RUN echo "export JAVA_HOME=/usr/lib/jvm/java-8-openjdk/jre" >> /home/hadoop/hadoop/etc/hadoop/hadoop-env.sh
#
COPY ./.profile /home/hadoop/.profile
#
COPY ./.bashrc /home/hadoop/.bashrc
#
RUN chown -R hadoop:hadoop /home/hadoop
#
EXPOSE 22
#
ENTRYPOINT ["docker-entrypoint.sh"]
CMD ["/usr/sbin/sshd","-D"]
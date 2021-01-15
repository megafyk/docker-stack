FROM alpine

RUN sed -i 's/https/http/' /etc/apk/repositories

RUN apk add --no-cache bash

RUN apk update && apk add ca-certificates \
&& rm -rf /var/cache/apk/*

COPY ./myCA.crt /usr/local/share/ca-certificates

RUN update-ca-certificates

# add openssh and clean
RUN apk add --update openssh \
&& rm  -rf /tmp/* /var/cache/apk/*

# add alternatives packages
RUN apk add curl vim sudo openjdk8

# add entrypoint script
ADD docker-entrypoint.sh /usr/local/bin

#make sure we get fresh keys
RUN rm -rf /etc/ssh/ssh_host_rsa_key /etc/ssh/ssh_host_dsa_key

RUN chmod 777 /usr/local/bin/docker-entrypoint.sh \
    && ln -s /usr/local/bin/docker-entrypoint.sh

RUN adduser hadoop

EXPOSE 22
ENTRYPOINT ["docker-entrypoint.sh"]
CMD ["/usr/sbin/sshd","-D"]
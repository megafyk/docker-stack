FROM centos

EXPOSE 22

# This containers function is specifically to run/test ssh, ignore hints
# about having openssh in a Dockerfile.
# "WARNING: installing SSH in a container is not recommended.
# Do you really need SSH in this image?"
# dockerfile_lint - ignore
RUN yum -y erase vim-minimal iputils libss && \
    yum -y install sudo openssh openssh-server openssh-clients && \
    yum -y clean all

ENV container docker
RUN (cd /lib/systemd/system/sysinit.target.wants/; for i in *; do [ $i == \
systemd-tmpfiles-setup.service ] || rm -f $i; done); \
rm -f /lib/systemd/system/multi-user.target.wants/*;\
rm -f /etc/systemd/system/*.wants/*;\
rm -f /lib/systemd/system/local-fs.target.wants/*; \
rm -f /lib/systemd/system/sockets.target.wants/*udev*; \
rm -f /lib/systemd/system/sockets.target.wants/*initctl*; \
rm -f /lib/systemd/system/basic.target.wants/*;\
rm -f /lib/systemd/system/anaconda.target.wants/*;
VOLUME [ "/sys/fs/cgroup" ]
CMD ["/usr/sbin/init"]

RUN useradd -mU hadoop && (echo 'hadoop:0' | chpasswd)
RUN echo "hadoop ALL=(ALL) ALL" >> /etc/sudoers
WORKDIR /home/hadoop

ADD hosts /tmp/
ADD docker-entrypoint.sh /usr/local/bin
RUN chmod +x /usr/local/bin/docker-entrypoint.sh
ENTRYPOINT [ "docker-entrypoint.sh" ]
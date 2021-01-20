FROM centos/systemd

# Install packages
RUN yum -y install openssh openssh-clients openssh-server sudo vim epel-release openssl certmonger; systemctl enable sshd.service

# Add ssl certificate
COPY myCA.crt /etc/pki/ca-trust/source/anchors/
RUN update-ca-trust

# Enable hadoop accounts
RUN useradd -mU hadoop && \
	echo 'hadoop:0' | chpasswd
	
# Configure SSHD
RUN mkdir -p /var/run/sshd ; chmod -rx /var/run/sshd
# http://stackoverflow.com/questions/2419412/ssh-connection-stop-at-debug1-ssh2-msg-kexinit-sent
RUN ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key
# Bad security, add a user and sudo instead!
RUN sed -ri 's/#PermitRootLogin yes/PermitRootLogin yes/g' /etc/ssh/sshd_config
# http://stackoverflow.com/questions/18173889/cannot-access-centos-sshd-on-docker
RUN sed -ri 's/UsePAM yes/#UsePAM yes/g' /etc/ssh/sshd_config
RUN sed -ri 's/#UsePAM no/UsePAM no/g' /etc/ssh/sshd_config
RUN echo "AllowUsers hadoop" >> /etc/ssh/sshd_config
# Configure sudoer hadoop
RUN echo "hadoop ALL=(ALL) ALL" >> /etc/sudoers

WORKDIR /home/hadoop
ADD hadoop-3.2.2 /home/hadoop/hadoop
ADD etc /home/hadoop/hadoop/etc
ADD hosts /tmp/
ADD jdk-8u281-linux-x64.rpm /home/hadoop
RUN chmod +x /home/hadoop/jdk-8u281-linux-x64.rpm
RUN yum install -y /home/hadoop/jdk-8u281-linux-x64.rpm
RUN rm -f /home/hadoop/jdk-8u281-linux-x64.rpm
RUN echo "export JAVA_HOME=/usr/java/jdk1.8.0_281-amd64/jre" >> /home/hadoop/hadoop/etc/hadoop/hadoop-env.sh
ADD .profile /home/hadoop
ADD .bashrc /home/hadoop
RUN chown -R hadoop:hadoop /home/hadoop

EXPOSE 22

CMD ["/usr/sbin/init"]
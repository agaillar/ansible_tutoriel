FROM amazonlinux:latest

RUN yum install -y openssh-server sudo && \
  mkdir /var/run/sshd && \
  useradd devops -s /bin/bash && \
  mkdir -p /home/devops/.ssh/ && \
  chmod 0700 /home/devops/.ssh/

COPY ssh_config /home/devops/.ssh/config
# COPY ansible /home/devops/.ssh/id_rsa
COPY ansible.pub /home/devops/.ssh/id_rsa.pub
COPY ansible.pub /home/devops/.ssh/authorized_keys

RUN chown -R ansible:ansible /home/devops/ && \
  echo "ansible ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers && \
  touch /etc/sysconfig/network

ENV container docker

EXPOSE 22
CMD ["/sbin/init"]
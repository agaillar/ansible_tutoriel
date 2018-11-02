FROM centos:centos7
LABEL maintainer="arnaud.gaillard@sogeti.com"
LABEL version="0.0.1"
LABEL description="Création d'une image docker configurée pour ansible"

#################################
# Le necessaire pour compiler python
#################################
# Install pygments (for syntax highlighting)
RUN yum update -y \
        && yum install -y zlib-devel bzip2-devel openssl-devel ncurses-devel gcc altinstall make wget git libselinux-python

# Installation Python
ARG PYTHON_VERSION=2.7.15
ENV PYTHON_BINARY Python-${PYTHON_VERSION}
ADD https://www.python.org/ftp/python/${PYTHON_VERSION}/${PYTHON_BINARY}.tgz /tmp/python.tgz
RUN mkdir -p  /opt/${PYTHON_BINARY} \
        && cd /tmp && tar xvfz python.tgz && cd ${PYTHON_BINARY} \
        && ./configure --prefix=/opt/${PYTHON_BINARY} --enable-shared \
        && make && make altinstall \
        && rm -rf /tmp/python.tgz /tmp/${PYTHON_BINARY}
ENV LD_LIBRARY_PATH=/opt/${PYTHON_BINARY}/lib:$LD_LIBRARY_PATH
ENV PATH=/opt/${PYTHON_BINARY}/bin:$PATH

# Installation ansible et pre-requis
ARG ANSIBLE_VERSION=2.7.0
RUN wget https://bootstrap.pypa.io/ez_setup.py -O - | python \
        && easy_install pip \
        && pip install ecdsa pycrypto paramiko pywinrm boto ansible==${ANSIBLE_VERSION}

# Ajout du user ansible
ENV HOME /home/ansible
RUN groupadd -g 10000 ansible
RUN useradd -c "Ansible user" -d $HOME -u 10000 -g 10000 -m ansible
WORKDIR /home/ansible
RUN mkdir -p /home/ansible/.ssh/
RUN chmod 0700 /home/ansible/.ssh/

COPY ./key_ansible /home/ansible/.ssh/id_rsa
COPY ./key_ansible.pub /home/ansible/.ssh/id_rsa.pub
COPY ./key_ansible.pub /home/ansible/.ssh/authorized_keys

RUN chown -R ansible:ansible /home/ansible/ && \
  echo "ansible ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

# Configuration de l'environnement
ENV ANSIBLE_CONFIG=/home/ansible/ansible-configuration/ansible.cfg
COPY ./ansible.cfg /home/ansible/ansible-configuration \
RUN mkdir /etc/ansible/
COPY hosts /etc/ansible/hosts

EXPOSE 22
CMD ["/sbin/init"]
FROM centos:7

###
# Update the system. Install stuff.
###

ADD kubectl.repo /tmp/

RUN cat /tmp/kubectl.repo >> /etc/yum.repos.d/kubernetes.repo && \
    rm /tmp/kubectl.repo

RUN yum install -y vim telnet openssh openssh-clients git vi less nano wget \
    unzip kubectl openssl sudo

###
# python
###

RUN yum install -y python3

###
# Terraform
###

ENV TERRAFORM_VERSION=0.12.26

RUN  wget https://releases.hashicorp.com/terraform/${TERRAFORM_VERSION}/terraform_${TERRAFORM_VERSION}_linux_amd64.zip && \
    unzip terraform_${TERRAFORM_VERSION}_linux_amd64.zip -d /usr/local/bin/

###
# Set up openstack account and create bin directory
###

RUN useradd -ms /bin/bash openstack

RUN echo "openstack ALL=NOPASSWD: ALL" >> /etc/sudoers

RUN echo 'openstack:docker' | chpasswd

ENV HOME /home/openstack

RUN mkdir -p $HOME/bin

ENV PATH=$HOME/bin:$PATH

###
# Clone kubespray
###

RUN git clone https://github.com/zonca/jetstream_kubespray \
    $HOME/jetstream_kubespray -b branch_v2.13.1

RUN git clone https://github.com/zonca/jupyterhub-deploy-kubernetes-jetstream  \
    $HOME/jupyterhub-deploy-kubernetes-jetstream

RUN pip3 install -r $HOME/jetstream_kubespray/requirements.txt

RUN pip3 install --upgrade openstackclient --ignore-installed

RUN mkdir $HOME/.kube

###
# Install helm
###

ENV HELM_VERSION=3.2.4

RUN cd /home \
	&& curl https://get.helm.sh/helm-v${HELM_VERSION}-linux-amd64.tar.gz -o helm.tar.gz \
	&& tar -xvf helm.tar.gz \
	&& rm helm.tar.gz \
	&& mv linux-amd64/helm /usr/local/bin/helm \
	&& chmod +x /usr/local/bin/helm \
	&& cd

###
# Add various utilities
###

ADD bin/boot.sh bin/mount.sh bin/secgroup.sh bin/teardown.sh \
   bin/unidata-secgroups.sh bin/wrangler-jetstream.sh bin/wrangler-secgroup.sh \
   bin/swap-vm.sh bin/kube-setup.sh bin/swap-vm.sh bin/kube-setup2.sh $HOME/bin/

RUN chmod +x $HOME/bin/*.sh

ADD .bashrc $HOME/

###
# Finalize
###

RUN chown -R openstack:openstack $HOME/

WORKDIR $HOME

USER openstack

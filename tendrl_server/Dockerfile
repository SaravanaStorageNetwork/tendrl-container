FROM centos:7.3.1611

MAINTAINER Saravanakumar

ENV container docker

LABEL architecture="x86_64" \
      name="tendrl-server" \
      vendor="Red Hat, Inc" \
      summary="This image is running tendlr server"

RUN yum --setopt=tsflags=nodocs -y update; yum clean all; \
(cd /lib/systemd/system/sysinit.target.wants/; for i in *; do [ $i == systemd-tmpfiles-setup.service ] || rm -f $i; done); \
rm -f /lib/systemd/system/multi-user.target.wants/*; \
rm -f /etc/systemd/system/*.wants/*; \
rm -f /lib/systemd/system/local-fs.target.wants/*; \
rm -f /lib/systemd/system/sockets.target.wants/*udev*; \
rm -f /lib/systemd/system/sockets.target.wants/*initctl*; \
rm -f /lib/systemd/system/basic.target.wants/*; \
rm -f /lib/systemd/system/anaconda.target.wants/*; \
yum install sudo -y; \
sed -i '/Defaults    requiretty/c\#Defaults    requiretty' /etc/sudoers;

RUN yum install --setopt=tsflags=nodocs -y carbon-selinux tendrl-grafana-selinux tendrl-selinux ntp;

ADD tendrl-release-epel-7.repo /etc/yum.repos.d/tendrl-release-epel-7.repo
ADD tendrl-dependencies-epel-7.repo /etc/yum.repos.d/tendrl-dependencies-epel-7.repo
RUN yum install --setopt=tsflags=nodocs -y epel-release

ADD grafana.repo /etc/yum.repos.d/grafana.repo
RUN yum install --setopt=tsflags=nodocs -y  grafana-4.5.2-1.x86_64

RUN yum install --setopt=tsflags=nodocs -y  etcd tendrl-node-agent tendrl-api tendrl-ui tendrl-monitoring-integration tendrl-notifier
RUN yum clean all

VOLUME [ "/sys/fs/cgroup" ]

CMD ["/usr/sbin/init"]

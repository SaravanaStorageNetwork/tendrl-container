FROM centos:7.3.1611

MAINTAINER Saravanakumar

ENV container docker

LABEL architecture="x86_64" \
      name="tendrl-server" \
      vendor="Red Hat, Inc" \
      summary="This image is running tendlr server"

RUN yum install --setopt=tsflags=nodocs -y carbon-selinux tendrl-grafana-selinux tendrl-selinux ntp;

ADD tendrl-release-epel-7.repo /etc/yum.repos.d/tendrl-release-epel-7.repo
ADD tendrl-dependencies-epel-7.repo /etc/yum.repos.d/tendrl-dependencies-epel-7.repo
RUN yum install --setopt=tsflags=nodocs -y epel-release

ADD grafana.repo /etc/yum.repos.d/grafana.repo
RUN yum install --setopt=tsflags=nodocs -y  grafana-4.5.2-1.x86_64

RUN yum install --setopt=tsflags=nodocs -y  etcd tendrl-node-agent tendrl-api tendrl-ui tendrl-monitoring-integration tendrl-notifier
RUN yum clean all

RUN yum --setopt=tsflags=nodocs -y update; yum clean all; \
(cd /lib/systemd/system/sysinit.target.wants/; for i in *; do [ $i == systemd-tmpfiles-setup.service ] || rm -f $i; done); \
rm -f /lib/systemd/system/multi-user.target.wants/*; \
rm -f /etc/systemd/system/*.wants/*; \
rm -f /lib/systemd/system/local-fs.target.wants/*; \
rm -f /lib/systemd/system/sockets.target.wants/*udev*; \
rm -f /lib/systemd/system/sockets.target.wants/*initctl*; \
rm -f /lib/systemd/system/basic.target.wants/*; \
rm -f /lib/systemd/system/anaconda.target.wants/*; \
yum --setopt=tsflags=nodocs -y install sudo; \
sed -i '/Defaults    requiretty/c\#Defaults    requiretty' /etc/sudoers; \
sed -i 's/ENV{DM_UDEV_DISABLE_OTHER_RULES_FLAG}=="1", ENV{SYSTEMD_READY}="0"/ENV{DM_UDEV_DISABLE_OTHER_RULES_FLAG}=="1", GOTO="systemd_end"/g' /usr/lib/udev/rules.d/99-systemd.rules;

VOLUME [ "/sys/fs/cgroup" ]

RUN iptables --flush; \
systemctl mask getty.target; \
systemctl enable ntpd.service; \
systemctl enable etcd; \
systemctl enable tendrl-node-agent; \
systemctl enable tendrl-api

#sed -i.bak '/^ETCD_LISTEN_CLIENT_URLS=/s/=.*/='\"http://gant:2379\"'/' /etc/etcd/etcd.conf
#sed -i.bak '/^ETCD_ADVERTISE_CLIENT_URLS=/s/=.*/='\"http://gant:2379\"'/' /etc/etcd/etcd.conf

RUN /usr/lib/python2.7/site-packages/graphite/manage.py syncdb --noinput; \
chown apache:apache /var/lib/graphite-web/graphite.db

RUN systemctl enable carbon-cache; \
systemctl daemon-reload; \
systemctl enable grafana-server.service; \
systemctl enable tendrl-monitoring-integration; \
systemctl enable tendrl-notifier; \
systemctl enable httpd

EXPOSE 8080 80

CMD ["/usr/sbin/init"]

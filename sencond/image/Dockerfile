FROM rhel7

#RUN yum install epel-release -y
COPY epel-7.repo  /etc/yum.repos.d/epel.repo

RUN yum install redis hostname -y ; yum clean all && \
    mkdir /data/

COPY update-node.sh /data/
RUN chmod 777 /data/update-node.sh && \
    ls -tral /data/
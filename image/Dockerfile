FROM rhel7

#RUN yum install epel-release -y
COPY epel-7.repo  /etc/yum.repos.d/epel.repo

RUN yum install redis hostname -y ; yum clean all && \
    yum -y install wget

COPY peer-finder /bin/
COPY initial_peers /conf/
RUN chmod 777 /bin/peer-finder && \
    chmod 777 /conf/initial_peers

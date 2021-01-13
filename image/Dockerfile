FROM rhel7

#RUN yum install epel-release -y
COPY epel-7.repo  /etc/yum.repos.d/epel.repo

RUN yum install redis hostname -y ; yum clean all && \
    yum -y install wget

COPY peer-finder /bin/peer-finder
RUN chmod u+x /bin/peer-finder

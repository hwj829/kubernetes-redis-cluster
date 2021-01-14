FROM rhel7

#RUN yum install epel-release -y
COPY epel-7.repo  /etc/yum.repos.d/epel.repo

RUN yum install redis:5.0.1-alpine hostname -y ; yum clean all && \
    cp /usr/local/etc/redis/redis.conf /conf/ && \
    mkdir /data/

COPY update-node.sh /data/
RUN chmod 777 /data/update-node.sh /conf/ && \
    ls -tral /data/ /conf/ /usr/local/etc/redis/
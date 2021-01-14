FROM rhel7

#RUN yum install epel-release -y
COPY epel-7.repo  /etc/yum.repos.d/epel.repo

RUN yum install redis hostname -y ; yum clean all && \
    mkdir -p /data/conf/

COPY redis.conf /data/conf/

RUN chmod -R 777 /data/

#RUN sed -i -e "/myself/ s/[0-9]\{1,3\}\.[0-9]\{1,3\}\.[0-9]\{1,3\}\.[0-9]\{1,3\}/${POD_IP}/" /data/conf/redis.conf

#设置端口
EXPOSE 6379

#启动
ENTRYPOINT ["redis-server","/data/conf/redis.conf"]




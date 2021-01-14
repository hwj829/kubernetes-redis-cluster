FROM rhel7

#RUN yum install epel-release -y
COPY epel-7.repo  /etc/yum.repos.d/epel.repo

RUN yum install redis hostname -y ; yum clean all && \
    mkdir -p /conf /data/redis

COPY redis.conf /conf/

RUN chmod -R 777 /conf /data/redis && \
    echo '######' >> /conf/redis.conf

#RUN sed -i -e "/myself/ s/[0-9]\{1,3\}\.[0-9]\{1,3\}\.[0-9]\{1,3\}\.[0-9]\{1,3\}/${POD_IP}/" /conf/redis.conf

#设置端口
EXPOSE 6379

#启动
ENTRYPOINT ["redis-server","/conf/redis.conf"]




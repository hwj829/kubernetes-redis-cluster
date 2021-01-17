
## 步骤一：创建Service,configMap,StatefulSet
oc create -f .\kubernetes-redis-cluster\third\redis-sts.yaml

oc create -f .\kubernetes-redis-cluster\third\redis-svc.yaml

## 步骤二：部署RedisCluster
#### 方案一
oc exec -it redis-cluster-0 -- redis-cli --cluster create --cluster-replicas 1 $(oc get pods -l app=redis-cluster -o jsonpath='{range.items[*]}{.status.podIP}:6379 ')
######方案一执行失败，使用方案二

#### 方案二 
##### 输出redis-cluster.json
oc get pods -l app=redis-cluster -o json > redis-cluster.json
###### 从redis-cluster.json文件中取status.podIP值（IP）
oc exec -it redis-cluster-0 -- redis-cli --cluster create --cluster-replicas 1 xx.xx.237.218:6379 xx.xx.12.145:6379 xx.xx.171.219:6379 xx.xx.237.199:6379 xx.xx.12.146:6379 xx.xx.237.201:6379 
###### 输出信息
C:\workspace\software\oc>oc exec -it redis-cluster-0 -- redis-cli --cluster create --cluster-replicas 1 xx.xx.237.229:6379 xx.xx.12.178:6379 xx.xx.171.244:6379 xx.xx.237.220:6379 xx.xx.12.135:6379 xx.xx.237.215:6379
>>> Performing hash slots allocation on 6 nodes...
Master[0] -> Slots 0 - 5460
Master[1] -> Slots 5461 - 10922
Master[2] -> Slots 10923 - 16383
Adding replica xx.xx.237.220:6379 to xx.xx.237.229:6379
Adding replica xx.xx.12.135:6379 to xx.xx.12.178:6379
Adding replica xx.xx.237.215:6379 to xx.xx.171.244:6379
M: 25d87bc1f4f398b25f447e4db7b8e2fb6e1de0fb xx.xx.237.229:6379
   slots:[0-5460] (5461 slots) master
M: 73f9d4c6222e716b60b60ab006d3fd502b72c1ca xx.xx.12.178:6379
   slots:[5461-10922] (5462 slots) master
M: 2cbe7a551d4e594ceb8bce5710514d4fc964fcee xx.xx.171.244:6379
   slots:[10923-16383] (5461 slots) master
S: 65f736afa31a7a5d9916afc72975a7e3ebadb23c xx.xx.237.220:6379
   replicates 25d87bc1f4f398b25f447e4db7b8e2fb6e1de0fb
S: f052c717684c86efb271bbd3fbae26a6ea7a4d05 xx.xx.12.135:6379
   replicates 73f9d4c6222e716b60b60ab006d3fd502b72c1ca
S: 64342427388f2f84ae39fc8b9515b7770015b116 xx.xx.237.215:6379
   replicates 2cbe7a551d4e594ceb8bce5710514d4fc964fcee
Can I set the above configuration? (type 'yes' to accept): yess
>>> Nodes configuration updated
>>> Assign a different config epoch to each node
>>> Sending CLUSTER MEET messages to join the cluster
Waiting for the cluster to join
..
>>> Performing Cluster Check (using node xx.xx.237.229:6379)
M: 25d87bc1f4f398b25f447e4db7b8e2fb6e1de0fb xx.xx.237.229:6379
   slots:[0-5460] (5461 slots) master
   1 additional replica(s)
M: 2cbe7a551d4e594ceb8bce5710514d4fc964fcee xx.xx.171.244:6379
   slots:[10923-16383] (5461 slots) master
   1 additional replica(s)
M: 73f9d4c6222e716b60b60ab006d3fd502b72c1ca xx.xx.12.178:6379
   slots:[5461-10922] (5462 slots) master
   1 additional replica(s)
S: f052c717684c86efb271bbd3fbae26a6ea7a4d05 xx.xx.12.135:6379
   slots: (0 slots) slave
   replicates 73f9d4c6222e716b60b60ab006d3fd502b72c1ca
S: 65f736afa31a7a5d9916afc72975a7e3ebadb23c xx.xx.237.220:6379
   slots: (0 slots) slave
   replicates 25d87bc1f4f398b25f447e4db7b8e2fb6e1de0fb
S: 64342427388f2f84ae39fc8b9515b7770015b116 xx.xx.237.215:6379
   slots: (0 slots) slave
   replicates 2cbe7a551d4e594ceb8bce5710514d4fc964fcee
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.

## 步骤三：检查cluster status
oc exec -it redis-cluster-0 -- redis-cli cluster info
oc exec -it redis-cluster-0 -- redis-cli role


for x in $(seq 0 5); do echo "redis-cluster-$x"; oc exec redis-cluster-$x -- redis-cli role; echo; done

###### Reference --> https://rancher.com/blog/2019/deploying-redis-cluster/

## 步骤五：openshift service映射本地端口16379
oc port-forward --address 0.0.0.0 service/redis-cluster 16379:6379 -n mdc


## 步骤六：Spring boot redis cluster client
###### git: https://github.com/hwj829/spring-boot-redis-client.git
oc port-forward --address 0.0.0.0 service/spring-boot-redis-client 18080:8080 -n mdc


## 问题处理
#### 1. [ERR] Node xx.xx.237.240:6379 is not empty. Either the node already knows other nodes (check with CLUSTER NODES) or contains some key in database 0.
######处理方案1：
    oc exec -it redis-cluster-4 -- rm /data/appendonly.aof
    
    oc exec -it redis-cluster-4 -- rm /data/nodes.conf
    
    oc exec -it redis-cluster-4 -- rm /data/dump.rdb
    
    oc exec -it redis-cluster-0 -- redis-cli flushdb      
######处理方案2：重新创建集群（新环境），重新创建集群前，检查原PVC是否删除，原PVC已经停留部分集群信息，不删除的话，问题会重现

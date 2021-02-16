##步骤一：创建Service,StatefulSet
   oc create -f .\\kubernetes-redis-cluster\\standalone\\redis-standalone-sts.yaml

   oc create -f .\\kubernetes-redis-cluster\\standalone\\redis-standalone-svc.yaml
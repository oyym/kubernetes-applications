# 1. Start a Zookeeper server instance
    docker run --name zookeeper --restart always -d oyymmw/zookeeper:3.5.5

# 2. running Apache Zookeeper on Kubernetes using StatefulSets
## 2.1 get zookeeper yaml file
    wget https://github.com/oyym/kubernetes-applications/blob/master/zookeeper/zookeeper.yml
## 2.2 Modify the file according to actual requirements, e.g.
    storageClassName: "storageclases_name"  --> storageClassName: "sc-glusterfs"
## 2.3 Run a zookeeper cluster of 3 replicas
    kubectl apply -f zookeeper.yml
    kubectl get pods -l app=zookeeper
    NAME          READY   STATUS    RESTARTS   AGE
    zookeeper-0   1/1     Running   0          1m
    zookeeper-1   1/1     Running   0          1m
    zookeeper-2   1/1     Running   0          1m

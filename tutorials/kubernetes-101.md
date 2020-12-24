
---

  

title: Kubernetes

  

description: Kubernetes

  

---



























---
### Operators

**Operator overview**

**What is an Operators** An Operator is a way to package, run, and maintain a Kubernetes application. It builds on Kubernetes abstractions to automate the entire lifecycle of the software it manages. Manage means an Operator can perform: scaling a complex app, application version upgrades, or even managing kernel modules for nodes in a computational cluster with specialized hardware.

**How Operators use Kubernetes resources** Operators is a custom Kubernetes controller and work by extending the Kubernetes control plane and API. In its simplest form, an Operator adds an endpoint to the Kubernetes API, called a Custom Resource (CR). It then continuously monitors and maintains resources of this new type of resource. This Operator can then take action based on the resource’s state. What actions the Operator takes in response to changes in the CR is specific to the application the Operator manages.

**Kubernetes CRs** CRs are the API extension mechanism in Kubernetes. A custom resource definition (CRD) defines a CR; it’s analogous to a schema for the CR data. Unlike members of the official API, a given CRD doesn’t exist on every Kubernetes cluster. CRDs extend the API of the particular cluster where they are defined. A cluster user can interact with CRs with kubectl or another Kubernetes client, just like any other API resource.

The diagram shows what the operator does.

![Operator_overview](_images/Operator_overview.png)

The coming sections give 2 practical examples on how Operators are deployed and work-

**Lab- Etcd Operator**

The etcd Operator creates and maintains highly-available etcd clusters on Kubernetes, allowing engineers to easily deploy and manage etcd clusters for their applications.

- Create and Destroy
- Resize
- Failover
- Rolling Upgrade
- Backup and Recovery

**etcd Architectural Flow**

To create etcd-cluster instance user needs to deploy etcd operator on Kubernetes. etcd-cluster gets created when we apply custom resource definition on operator. User can now create users for etcd-cluster and external application can access etcd-cluster by providing credentials.
Here is how an etcd Operator will work on Kubernetes.


![Operator_overview](_images/Operator.png)

#### Execute below command to create directory for this operator


```execute
cd && mkdir ${WORKING_DIR}/etcd-operator
cd ${WORKING_DIR}/etcd-operator
```

**Step 1:** Execute below command to install the operator:

```execute
kubectl create -f https://operatorhub.io/install/etcd.yaml
```

This Operator will be installed in the "my-etcd" namespace and will be usable from all namespaces in the cluster.

```
--- 
apiVersion: v1
kind: Namespace
metadata: 
  apiVersion: operators.coreos.com/v1alpha2
  kind: OperatorGroup
  metadata: 
    name: operatorgroup
    namespace: my-etcd
    spec: 
      apiVersion: operators.coreos.com/v1alpha1
      kind: Subscription
      metadata: 
        name: my-etcd
        namespace: my-etcd
        spec: 
          channel: singlenamespace-alpha
          name: etcd
          source: operatorhubio-catalog
          sourceNamespace: olm
      targetNamespaces: my-etcd
  name: my-etcd
```

You will get output similar below:

```
namespace/my-etcd created
operatorgroup.operators.coreos.com/operatorgroup created
subscription.operators.coreos.com/my-etcd created
```

**Step 2:** After install, Execute below command to watch your operator come up .

```execute
kubectl get csv -n my-etcd | egrep -i "name|etcd"
```

Sample output:

```
NAME                             DISPLAY                       VERSION       REPLACES                   PHASE
elastic-cloud-eck.v1.0.0-beta1   Elastic Cloud on Kubernetes   1.0.0-beta1   elastic-cloud-eck.v0.9.0   Succeeded
etcdoperator.v0.9.4              etcd                          0.9.4         etcdoperator.v0.9.2        Succeeded

```

**Note - Please wait till `PHASE` will be `Succeeded` and then proceed further.**

**Step 3:** etcd-Operator Instance can be created using the Custom Resource Definition YAML files.

Create a custom resource YAML file

```execute
cat <<'EOF' >etcd-cluster.yaml
apiVersion: etcd.database.coreos.com/v1beta2
kind: EtcdCluster
metadata:
  name: example
spec:
  size: 3
  version: 3.2.13
EOF
```

**Step 4:** Create etcd-cluster in the same namespace of operator.

```execute
kubectl create -f etcd-cluster.yaml -n my-etcd
```

Sample output:

```
etcdcluster.etcd.database.coreos.com/example created
```

**Step 5:** Check etcd-cluster pods.

```execute
kubectl get pods -n my-etcd
```

You will see output similar like below:

```
NAME                             READY   STATUS     RESTARTS   AGE
etcd-operator-546468f574-78vxg   3/3     Running    0          11m
example-7m2dq8mk5d               1/1     Running    0          45s
example-xdsgpp9c6s               0/1     Init:0/1   0          24s
```

It may take up to a few minutes until all the resources are created and the cluster is ready for use.

```
NAME                             READY   STATUS    RESTARTS   AGE
etcd-operator-546468f574-78vxg   3/3     Running   0          30m
example-7m2dq8mk5d               1/1     Running   0          20m
example-vfbs98thmq               1/1     Running   0          19m
example-xdsgpp9c6s               1/1     Running   0          19m
```

**Note - Please wait till `Status` will be `Running` and `READY` should be 1/1 , and then proceed further.**

**Step 6:** Accessing etcd-cluster from within a pod/container
Get the cluster-service

```execute
kubectl get svc -n my-etcd
```

You will see output similar below:

```
NAME                    TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)             AGE
etcd-restore-operator   ClusterIP   10.96.179.156    <none>        19999/TCP           13m
example                 ClusterIP   None             <none>        2379/TCP,2380/TCP   2m38s
example-client          ClusterIP   10.107.108.163   <none>        2379/TCP            2m38s
```

To access etcd cluster externally, lets first update service to use NodePort:

#Execute below command to use NodePort:
```execute
kubectl get service example-client --output yaml -n my-etcd > /tmp/my-etcd.yaml
sed -i "s/type: .*/type: NodePort/g" /tmp/my-etcd.yaml
kubectl patch svc example-client -p "$(cat /tmp/my-etcd.yaml)" --namespace my-etcd
```

#Execute below command to update NodePort to 32379:
```execute
kubectl get service example-client --output yaml -n my-etcd > /tmp/my-etcd.yaml
sed -i "s/nodePort: .*/nodePort: 32379/g" /tmp/my-etcd.yaml
kubectl patch svc example-client -p "$(cat /tmp/my-etcd.yaml)" --namespace my-etcd
```

**Step 7:** Connect to etcd-cluster from etcd-client container.

```execute
kubectl run --rm -i --tty etcd-client --image quay.io/coreos/etcd --restart=Never  --env ClusterIP=##SSH.host##  -- /bin/sh
```

You will see output similar below:

```
If you don't see a command prompt, try pressing enter.
/ #
```

**Step 8:** Put sample key-value pair into etcd-cluster database

Execute below command to put key-value pair.

```execute
ETCDCTL_API=3 etcdctl --endpoints http://##SSH.host##:32379 put cluster admin
```

Sample output:

```
OK
```

**Step 9:** Retrieving the value by key

Execute below command to retrive value of key.

```execute
ETCDCTL_API=3 etcdctl --endpoints http://##SSH.host##:32379 get cluster 
```

Sample Output:

```
cluster
admin
```

**Step 10:** Create user for etcd-cluster. 

Execute below command to add user for etcd-cluster . It will prompt for password which you need to enter and confirm. 

```execute
ETCDCTL_API=3 etcdctl --endpoints http://##SSH.host##:32379 user add Openlabs
```

Sample output:

```
Password of openlabs:
Type password of openlabs again for confirmation:
User openlabs created
```

**Step 11:** Checking the user creating is successful or not

Execute below command to check created user.

```execute
ETCDCTL_API=3 etcdctl --endpoints http://##SSH.host##:32379 user list
```

Sample output:

```
openlabs
```


Execute below command to exit out of terminal:

```execute
exit
```

---

**Lab- Elastic Cloud Operator**

Elastic Cloud on Kubernetes automates the deployment, provisioning, management, and orchestration of Elasticsearch, Kibana and the APM Server on Kubernetes.

**Current features:**

- Elasticsearch, Kibana and APM Server deployments
- TLS Certificates management
- Safe Elasticsearch cluster configuration & topology changes
- Persistent volumes usage
- Custom node configuration and attributes
- Secure settings keystore updates



#Execute below command to create directory for this operator



```execute
mkdir ${WORKING_DIR}/Elastic-Cloud-Operator
cd ${WORKING_DIR}/Elastic-Cloud-Operator
```

**Step 1:** Execute below command to install the operator:

```execute
kubectl create -f https://operatorhub.io/install/elastic-cloud-eck.yaml
```

This Operator will be installed in the "operators" namespace and will be usable from all namespaces in the cluster.

```
--- 
apiVersion: operators.coreos.com/v1alpha1
kind: Subscription
metadata: 
  name: my-elastic-cloud-eck
  namespace: operators
  spec: 
    channel: beta
    name: elastic-cloud-eck
    source: operatorhubio-catalog
    sourceNamespace: olm
```

You will find output similar below:

```
subscription.operators.coreos.com/my-elastic-cloud-eck created
```

**Step 2:** Execute below command to watach the operator come up

```execute
kubectl get csv -n operators | egrep -i "name|elastic-cloud"
```

```
NAME                             DISPLAY                       VERSION       REPLACES                   PHASE
elastic-cloud-eck.v1.0.0-beta1   Elastic Cloud on Kubernetes   1.0.0-beta1   elastic-cloud-eck.v0.9.0   Succeeded
```

**Note - Please wait till `PHASE` status will be `Succeeded` and then proceed further.**

**Step 3:** Create the custom resource file

```execute
cat <<'EOF' >>elasticsearch_cluster.yaml
apiVersion: elasticsearch.k8s.elastic.co/v1beta1
kind: Elasticsearch
metadata:
  name: elasticsearch
spec:
  version: 7.4.2
  nodeSets:
  - name: default
    count: 1
    config:
      node.master: true
      node.data: true
      node.ingest: true
      node.store.allow_mmap: false
EOF
```

This small specification causes the operator to deploy a single node Elasticsearch cluster named ElasticSearch . The cluster will automatically have all its communications secured using Transport Layer Security (TLS)

#Execute below command to create cluster

```execute
kubectl create -f elasticsearch_cluster.yaml -n operators
```

```
elasticsearch.elasticsearch.k8s.elastic.co/elasticsearch created
```

The operator automatically creates and manages Kubernetes resources to achieve the desired state of the Elasticsearch cluster. 

**Step 4:** Get the status of pods

```execute
kubectl get pods -n operators | egrep -i "name|elasticsearch"
```

Sample output:

```
NAME                         READY   STATUS     RESTARTS   AGE
elasticsearch-es-default-0   0/1     Init:0/1   0          27s
```

It may take up to a few minutes until all the resources are created and the cluster is ready for use. Status should be `running` and READY should be `1/1` .

```
NAME                         READY   STATUS    RESTARTS   AGE
elasticsearch-es-default-0   1/1     Running   0          3m23s
```

**Step 5:** Check the health of current ElasticSearch Cluster

```execute
kubectl get elasticsearch -n operators
```

```
NAME            HEALTH   NODES   VERSION   PHASE   AGE
elasticsearch   green    1       7.4.2     Ready   45s
```

When you create the cluster, there is no HEALTH status and the PHASE is empty. After a while, the PHASE turns into Ready, and HEALTH becomes green. Health will turn into green only when all pods of that cluster are in `READY` state.

You can see that one Pod is in the process of being started:

```execute
kubectl get pods -n operators --selector='elasticsearch.k8s.elastic.co/cluster-name=elasticsearch'
```

You will find output similar below:

```
NAME                         READY   STATUS    RESTARTS   AGE
elasticsearch-es-default-0   1/1     Running   0          65s
```

**Note - Please wait till `Status` should be `Running` and `READY` should be 1/1 , and then proceed further.**

**Step 6:** To check the logs of pods

```execute
kubectl logs -f elasticsearch-es-default-0 -n operators | more
```

You will find output simlar below :

```
OpenJDK 64-Bit Server VM warning: Option UseConcMarkSweepGC was deprecated in version 9.0 and will likely be removed in a future release.
{"type": "server", "timestamp": "2019-11-27T09:34:44,928Z", "level": "INFO", "component": "o.e.e.NodeEnvironment", "cluster.name": "elasticsearch", "node.name": "elasticsearch-es-default-0", "message": "using [1] data paths, mounts [[/usr/share/elasticsearch/data (/dev/xvda2)]], net usable_space [76.5gb], net total_space [98gb], types [ext3]" }
{"type": "server", "timestamp": "2019-11-27T09:34:44,935Z", "level": "INFO", "component": "o.e.e.NodeEnvironment", "cluster.name": "elasticsearch", "node.name": "elasticsearch-es-default-0", "message": "heap size [1015.6mb], compressed ordinary object pointers [true]" }
{"type": "server", "timestamp": "2019-11-27T09:34:44,939Z", "level": "INFO", "component": "o.e.n.Node", "cluster.name": "elasticsearch", "node.name": "elasticsearch-es-default-0", "message": "node name [elasticsearch-es-default-0], node ID [tavAWXP7Qe63rhMJHfYaZQ], cluster name [elasticsearch]" }
```

**Step 7:** Get the credentials

A default user named elastic is automatically created with the password stored in a Kubernetes secret:

```execute
PASSWORD=$(kubectl get secret elasticsearch-es-elastic-user -n operators -o=jsonpath='{.data.elastic}' | base64 --decode)
echo $PASSWORD
```
You will find output similar below:

```
fdqxb7j68pfvj....
```

**Step 8:** Request the Elasticsearch endpoint

From inside the Kubernetes cluster:

```execute
ClusterIP=`kubectl get service -n operators | grep -i  elasticsearch | awk 'NR==2 {print $3}'`
echo $ClusterIP
curl -u "elastic:$PASSWORD" -k "https://$ClusterIP:9200"
```

You will find output similar below:

```
{
  "name" : "elasticsearch-es-default-0",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "9cJ5aKa_R92jXw1A8tWznA",
  "version" : {
    "number" : "7.4.2",
    "build_flavor" : "default",
    "build_type" : "docker",
    "build_hash" : "2f90bbf7b93631e52bafb59b3b049cb44ec25e96",
    "build_date" : "2019-10-28T20:40:44.881551Z",
    "build_snapshot" : false,
    "lucene_version" : "8.2.0",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}

```

**Step 9:** Specify a Kibana instance and associate it with your Elasticsearch cluster:

```execute
cat <<'EOF' > kibana_instance.yaml
apiVersion: kibana.k8s.elastic.co/v1beta1
kind: Kibana
metadata:
  name: kibanainstance
spec:
  version: 7.4.2
  count: 1
  elasticsearchRef:
    name: elasticsearch
EOF
```

#Execute below command to create kibana instance

```execute
kubectl create -f kibana_instance.yaml -n operators
```

You will find output similar below:

```
kibana.kibana.k8s.elastic.co/kibanainstance created
```

**Step 10:** Monitor Kibana health and instance creation progress.

Similar to Elasticsearch, you can retrieve details about Kibana instances:

```execute
kubectl get kibana -n operators
```

You will find output similar below:

```
NAME             HEALTH   NODES   VERSION   AGE
kibanainstance   green    1       7.4.2     62s
```

And the associated Pods:

```execute
kubectl get pod -n operators --selector='kibana.k8s.elastic.co/name=kibanainstance'
```

Sample output:

```
NAME                                 READY   STATUS    RESTARTS   AGE
kibanainstance-kb-66f574459f-vvbx8   1/1     Running   0          91s
```
**Note - Please wait till `Status` should be `Running` and `READY` should be 1/1 , and then proceed further.**

**Step 11:** Access Kibana.

To access Kibana service externally, update it to use NodePort:

```execute
kubectl get service kibanainstance-kb-http -n operators --output yaml > /tmp/kb_test.yaml
sed -i "s/type: .*/type: NodePort/g" /tmp/kb_test.yaml
kubectl patch svc kibanainstance-kb-http -n operators -p "$(cat /tmp/kb_test.yaml)"
```

**Step 12:** Update the service nodeport to 30449

```execute
kubectl get service kibanainstance-kb-http -n operators --output yaml > /tmp/kb_test1.yaml
sed -i "s/nodePort: .*/nodePort: 30449/g" /tmp/kb_test1.yaml
kubectl patch svc kibanainstance-kb-http -n operators -p "$(cat /tmp/kb_test1.yaml)"
```
Execute below command to get Kibana Service details:

```execute
kubectl get service kibanainstance-kb-http -n operators
```

Sample output:
```
NAME                     TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
kibanainstance-kb-http   NodePort   10.96.67.171   <none>        5601:30449/TCP   7m42s
```


**Step 13:** Use the password  obtained from above Step 7 to login to Kibana
Click on the <a href="https://##SSH.host##:30449" target="_blank">https://##SSH.host##:30449</a> to access Kibana Dashboard

You will see login page like below:

![](_images/kibana_localhost.png)

Once you logged in , you will see dashboard like below:

![](_images/kibana_login.png)


---

### What You Learned

**Summary**


**What you learned in this lab**

In this lab, you gained a basic understanding of Kubernetes and the components in the Kubernetes cluster.

1. How to create and manage kubernetes resources.

2. How to create, run and scale applications in a cluster

3. How to upgrade and rollback without application downtime

4. What are operators and kubernetes resources used in an operator

![kubernetes_summary](_images/kubernetes_summary.PNG "kubernetes_summary")

Now you can easily use the kubectl commands to build, deploy and manage applications and deploy Operators using Kubernetes.

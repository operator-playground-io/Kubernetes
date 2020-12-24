
---

  

title: Kubernetes

  

description: Kubernetes

  

---

### Introduction

---

**Kuberntes Basics**
****

Kubernetes is a powerful open-source system, initially developed by Google, for managing containerized applications in a clustered environment. It aims to provide better ways of managing related, distributed components and services across varied infrastructure. It is a platform designed to completely manage the life cycle of containerized applications and services using methods that provide predictability, scalability, and high availability.

<strong>Features of Kubernetes</strong>:

- <strong>Horizontal Scaling</strong>:- It can scale up or down by adding or removing containers as the demand changes.
- <strong>Self-Healing</strong>:- It can launch new containers on different machines if something fails.
- <strong>Automated Rollout</strong>:- Kubernetes supports rollouts and rollbacks for the desired state of containerized application.
- <strong>Service Discovery & Load balancing</strong>:- It can distribute load between the containers.
- <strong>Secrets & Config Management</strong>:- ConfigMaps are Kubernetes objects that can draw configuration information from other sources such as directories or files. While secrets contain sensitive piece of information. ConfigMaps and Secrets are added to virtual directories called Volumes, which are mounted filesystems that share the lifetime of a Pod which encloses it.
- <strong>Self-monitoring</strong>:- Kubernetes constantly checks the health of nodes and containers.
- <strong>Operators</strong>:- Operators are software extensions to Kubernetes that make use of custom resources to manage applications and their components. Operators follow Kubernetes principles, notably the control loop.


![Kubernetes_Introduction](_images/Kubernetes_Introduction.png "Kubernetes_Introduction")

<br/>

**Kubernetes Architecture**
****

Kubernetes follows a client-server architecture. Master is the controlling machine and has components which operate as the main management contact point for users. Nodes are where the containerized apps run. 

Main  components of Kubernetes are explained below:

![Kubernetes_architechture](_images/kubernetes_kubernetes_architechture.png "Kubernetes architecture")


**Master Components**
#

Below are the main components found on the master node:

**- ETCD Cluster** – It is a distributed key value storage which is used to store the Kubernetes cluster data (such as number of pods, their state, namespace,etc). It is a database for Kubernetes data.

**- Kube-API-Server** - Kubernetes API server is the main management point that receives all REST requests for modifications (to pods, services, replication sets/controllers and others). This is the only component that communicates with the etcd cluster, making sure data is stored in etcd. It is the front-end of Kubernetes Cluster.

**- Controller-Manager**-  It is responsible for regulating the state of the cluster and managing the controller processes. For example: The replication controller ensures that the number of replicas defined for a service matches the number currently deployed on the cluster.

**- Scheduler**– It assigns workloads to the nodes as follows- Reads the workload's operating requirements, Analyzes the current infrastructure environment, Places the workload on an acceptable node(s).

**Node Components**
#

The following are the key components of Node server which are necessary to communicate with Kubernetes master.

**- Docker** - The first requirement of each node is Docker, which is used to run the containers.

**- Kubelet Service** - Kubelet is a service which receives instructions from master component to execute.

**- Kubernetes Proxy Service** - This is a proxy service which runs on each node and helps in making services available to the external host. It is used for maintaining network rules and performing connection forwarding.

**- Pod** - A pod is the most basic unit in Kubernetes. Pods consist of containers that operate closely together. They share a life cycle, and should always be scheduled on the same node. They are managed entirely as a unit and share their environment, volumes, and IP space.

<br/>

**Lab- Prerequisite**
****

For this labs you will need access to a kubernetes cluster. There is already kubernetes setup in this lab environment. You can use it or you can use your own kubernetes cluster by following procedure given below.

**#Provision a Kubernetes Cluster**

**Note: Execute below steps if you have Kubernetes cluster on the IBM Cloud else skip this section.  Steps of this lab will be executed by default on the Kubernetes setup installed on the attached terminal.**

Login to IBM Cloud and create a Kubernetes cluster if you haven't already. Follow <a href="https://cloud.ibm.com/docs/containers?topic=containers-cs_cluster_tutorial#cs_cluster_tutorial_lesson1" target="_blank">this</a> link for creating a cluster.

Log in to the IBM Cloud CLI . You can either login with Option 1 or Option 2 (If your id is federated): 

**Option 1:** Use this for normal login:

```execute
ibmcloud login
```
Enter your IBM Cloud credentials when prompted. You will find output similar like below. As an example we have used openlabs account details , you need to enter your own registered email id and password:

```

API endpoint: cloud​.ibm.com

Email> openlabs@ibm.com

Password> 
Authenticating...
OK

Targeted account Openlabs's Account (80d297103c56406aa783749878e0e465b9bf)

Targeted resource group Default


Select a region (or press enter to skip):
1. au-syd
2. jp-tok
3. kr-seo
4. eu-de
5. eu-gb
6. us-south
7. us-east
Enter a number> 6
Targeted region us-south

                      
API endpoint:      https​://cloud.ibm.com   
Region:            us-south   
User:              openlabs@ibm.com 
Account:           Openlabs's Account (80d297103c56406aa783749878e0e465b9bf)  
Resource group:    Default   
CF API endpoint:      
Org:                  
Space:                

Tip: If you are managing Cloud Foundry applications and services
- Use 'ibmcloud target --cf' to target Cloud Foundry org/space interactively, or use 'ibmcloud target --cf-api ENDPOINT -o ORG -s SPACE' to target the org/space.
- Use 'ibmcloud cf' if you want to run the Cloud Foundry CLI with current IBM Cloud CLI context.


New version 0.20.0 is available.
Release notes: https​://github.com/IBM-Cloud/ibm-cloud-cli-release/releases/tag/v0.20.0
TIP: use 'ibmcloud config --check-version=false' to disable update check.

```

**Option 2:** 

**Note:** 
If you have a federated ID, use `"ibmcloud login --sso"` to log in to the IBM Cloud CLI. Use the provided URL in your CLI output to retrieve your one-time passcode. You have a federated ID if login fails without the `--sso` and succeeds with the `--sso` option.

```execute
ibmcloud login --sso
```
Using --sso you will see output similar like below: In this you need to select account and region if you have access to multiple account and regions. As an example we have used openlabs account details , you need to use your own registered email id and password to get `One Time Code`:

```
ibmcloud login --sso
API endpoint: https​://cloud.ibm.com

Get One Time Code from https​://identity-3.us-south.iam.cloud.ibm.com/identity/passcode to proceed.
Open the URL in the default browser? [Y/n]> y
One Time Code > 
Authenticating...
OK

Select an account:
1. IBM (80d297103c56406aa783749878e0e465b9bf)
2. IBM (3a4766a7bcab032d4ffc980d360fbf23) <-> 338150
Enter a number> 1
Targeted account IBM (80d297103c56406aa783749878e0e465b9bf)

Targeted resource group Default


Select a region (or press enter to skip):
1. au-syd
2. jp-osa
3. jp-tok
4. kr-seo
5. eu-de
6. eu-gb
7. us-south
8. us-south-test
9. us-east
Enter a number> 7
Targeted region us-south

                      
API endpoint:      https​://cloud.ibm.com   
Region:            us-south   
User:              openlabs@ibm.com   
Account:           IBM (80d297103c56406aa783749878e0e465b9bf)   
Resource group:    Default   
CF API endpoint:      
Org:                  
Space:                

Tip: If you are managing Cloud Foundry applications and services
- Use 'ibmcloud target --cf' to target Cloud Foundry org/space interactively, or use 'ibmcloud target --cf-api ENDPOINT -o ORG -s SPACE' to target the org/space.
- Use 'ibmcloud cf' if you want to run the Cloud Foundry CLI with current IBM Cloud CLI context.


New version 0.20.0 is available.
Release notes: https​://github.com/IBM-Cloud/ibm-cloud-cli-release/releases/tag/v0.20.0
TIP: use 'ibmcloud config --check-version=false' to disable update check.
```

If you have a api key then, use `"ibmcloud login --apikey <api_key>"` to log in to the IBM Cloud CLI

#Copy below command and replace <​CLUSTER.name> with your cluster name and execute it to get your cluster details.

```copycommand
ibmcloud ks cluster ls | grep  <​CLUSTER.name>
```

You should see output similar to the following. Wait until `state` value becomes `normal`

```
Name         ID                     State    Created          Workers   Location   Version       Resource Group Name   Provider
<​CLUSTER.name>   <​CLUSTER.id>   normal   41 minutes ago   1         <​CLUSTER.dataCenter>      1.14.8_1536   Default               classic
```

Once the cluster is provisioned, the kubernetes client CLI `kubectl` needs to be configured to talk to the provisioned cluster.

#Copy below command and replcase <​CLUSTER.name> to your cluster name and execute it to get value `KUBECONFIG`

```execute
ibmcloud ks cluster config --cluster <​CLUSTER.name>
```

You should see output similar to the following.

```
OK
The configuration for <​CLUSTER.name> was downloaded successfully.
Export environment variables to start using Kubernetes.
export KUBECONFIG=/home/student/.bluemix/plugins/container-service/clusters/<​CLUSTER.name>/kube-config-<​CLUSTER.dataCenter>-<​CLUSTER.name>.yml
```

Copy below command and replace <​CLUSTER.name> with you cluster name and <​CLUSTER.dataCenter> with your cluster data center and execute to set the `KUBECONFIG` environment variable based on the output of the command. 

This will make your `kubectl` client point to your new Kubernetes cluster.

```execute
export KUBECONFIG=/home/student/.bluemix/plugins/container-service/clusters/<​CLUSTER.name>/kube-config-<​CLUSTER.dataCenter>-<​CLUSTER.name>.yml
```

Once your client is configured, you are ready to deploy your application


---

### Create and Manage Kubernetes Resources

---

**Objects Overview**
***

Kubernetes objects are entities provided by Kubernetes for deploying, maintaining, and scaling applications either on cloud or on-premise infrastructure. In this section, we’ll be discussing the most common Kubernetes objects that you’ll use in your deployments.

- <strong>Namespace</strong>: Namespaces are used to organize objects in a Kubernetes cluster. They enable you to group resources together and perform actions on those resources. One of the use cases can be the creation of different environments (development and production) for your deployed application.
- <strong>Pods</strong>: A pod is the most basic unit of the Kubernetes cluster. It usually contains one or more running containers. Pods are designed to be ephemeral in nature which means that they can be destroyed at any time.
- <strong>ReplicaSet</strong>: The ReplicaSet is used to create multiple copies of the same pod in a Kubernetes cluster. It helps ensure that at any given time, the desired number of pods specified are in the running state.
- <strong>Deployment</strong>: Deployments are Kubernetes objects that are used for managing pods. The first thing a Deployment does when it’s created is to create a replicaset. The replicaset creates pods according to the number specified in the replica option. 
- <strong>Services</strong>: Service is a way of management and grouping of related pods running on cluster. It can be defined as an abstraction on the top of the pod which provides a single IP address and DNS name by which pods can be accessed. With Service, it is very easy to manage load balancing configuration. It helps pods to scale very easily.
- <strong>Volumes</strong>: In Kubernetes, a volume can be thought of as a mounted directory. It is needed to make the pod stateful. If data needs to persist, volumes are needed.
- <strong>ConfigMaps</strong>: ConfigMaps are used to separate configuration data from containers in your Kubernetes application. They offer you the ability to dynamically change data for containers at runtime.
- <strong>Secrets</strong>: Kubernetes Secrets let you store and manage sensitive information, such as passwords, Auth tokens, and ssh keys. Storing confidential information in a Secret is safer and more flexible than putting it in a Pod definition or in a container image. Secrets are base64 encoded and saved.

Most of the kubernetes objects consists of four top-level required fields, which are:

- <strong>apiVersion</strong>: This refers to the version of Kubernetes. There are several versions, and several objects are introduced with each version. Some common ones are v1, apps/v1, and extensions/v1beta1. You can check out the official Kubernetes documentation for the list of versions.

- <strong>Kind</strong>: This is the type of Kubernetes object. (example: *Pod*, *ReplicaSet*, *Deployment*)

- <strong>Metadata</strong>: The metadata houses information that describes the object briefly. The information in the metadata usually contains the name you want to give the object (pod in our case), the labels, and the annotation. For the labels, you can define as many labels as you want, and you aren’t restricted to words to use as labels.

- <strong>Spec</strong>: The spec section is where you define the desired state of your object. In the case of a pod, it’s where you describe the state of your container.

<strong>*kubectl:*</strong> kubectl is a command line tool which controls the Kubernetes cluster manager. In this lab you are going to use kubectl tool to create and manage kubernetes resources.

General syntax for *kubectl* commands is a followed:

```
kubectl [COMMAND] [TYPE] [NAME] [flags]
```

where:

- COMMAND is the operation to be applied on object. For example: create, get, apply, describe, delete, logs.
- TYPE specifies object type, like Namespace, Pod, Deployment, Service. Resource types are case-insensitive and you can specify the singular, plural, or abbreviated forms.
- *flags* specifies optional arguments. For example, you can use the -s or --server flags to specify the address and port of the Kubernetes API server.

Basic kubectl commands are given below:

- <strong>create:</strong> Create a resource from a file or from stdin.

  *#Note:* JSON and YAML formats are accepted.

  Examples:
  
  *# Create a pod using the data in pod.yaml*
  ```
  kubectl create -f ./pod.json
  ```
  
- <strong>get:</strong> List one or more resources and prints a table of the most important information about the specified resources.
  
  Examples:
  
  *# List all pods in ps output format.*
  ```
  kubectl get pods
  ```

  *# List all pods in ps output format with more information (such as node name).*
  ```
  kubectl get pods -o wide
  ```

  *# List a single pod with specified NAME in ps output format.*
  ```
  kubectl get pod mypod
  ```

- <strong>describe:</strong> Print a detailed description of the selected resources, including related resources such as events or controllers. You may select a single object by name, all objects of that type, provide a name prefix, or label selector.
  
  Examples:

  *# Describe a pod*
  ```
  kubectl describe pods nginx
  ```

  *# Describe a pod identified by type and name in "pod.yaml"*
  ```
  kubectl describe -f pod.yaml
  ```

  *# Describe all pods*
  ```
  kubectl describe pods
  ```

  *# Describe pods by label name=myLabel*
  ```
  kubectl describe po -l name=myLabel
  ```

- <strong>delete:</strong> Deletes a resource.
  
  Examples:
  
  *# Delete a pod using the type and name specified in pod.yaml.*
  ```
  kubectl delete -f ./pod.yaml
  ```

  *# Delete resources from a directory containing yamls*
  ```
  kubectl delete -k dir
  ```
  
  *# Delete pod with name "foo"*
  ```
  kubectl delete pod foo
  ```

  *# Delete pods and services with same names "baz" and "foo"*
  ```
  kubectl delete pod,service baz foo
  ```

- <strong>exec:</strong> Execute a command against a container in a pod. If the pod has only one container, the container name is
optional.
  
  Examples:
  
  *# Get output from running 'date' command from pod mypod, using the first container by default*
  ```
  kubectl exec mypod date
  ```

  *# Get output from running 'date' command in ruby-container from pod mypod*
  ```
  kubectl exec mypod -c ruby-container date
  ```

  *# Get a shell to container from pod mypod*
  ```
  kubectl exec mypod -c ruby-container -it -- /bin/bash
  ```

- <strong>logs:</strong> Print the logs for a container in a pod or specified resource. If the pod has only one container, the container name is
optional.

  Examples:
  
  *# Return snapshot logs from pod nginx with only one container*
  ```
  kubectl logs nginx
  ```

  *# Return snapshot logs from pod nginx with multi containers*
  ```
  kubectl logs nginx --all-containers=true
  ```

*#Note:* Kubernetes object definitions can be defined in YAML or JSON formats. The file extension .yaml, .yml for YAML and .json for JSON can be used. In this lab you will use YAML format because it is clean and easy to understand compared to JSON format.


<br/>

**Lab- Namespace**
#

Namespaces are intended for use in environments with many users spread across multiple teams, or projects. For clusters with a few to tens of users, you should not need to create or think about namespaces at all. Start using namespaces when you need the features they provide.

Namespaces provide a scope for names. Names of resources need to be unique within a namespace, but not across namespaces. Namespaces can not be nested inside one another and each Kubernetes resource can only be in one namespace.

Namespaces are a way to divide cluster resources between multiple users.

Create namespace using a YAML definition:

Below is the yaml definition to create a namespace with name *my-openlabs-namespace*. Execute below command to create namespace manifest file:

```execute
cat <<'EOF'>namespace.yaml
apiVersion: v1
kind: Namespace
metadata:
  # Name for namespace
  name: my-openlabs-namespace
EOF
```

*# You can run the following command to create a new namespace using yaml definition.*

```execute
kubectl create -f namespace.yaml
```

Output:

```namespace/my-openlabs-namespace created```

*# Delete namespace by executing below command:*

```execute
kubectl delete namespace my-openlabs-namespace
```

Following commands are used to manage the namespace.

1. ```$ kubectl create –f namespace.yaml```            # Create namespace using yaml definition

2. ```$ kubectl get namespace```                       # List all the namespaces present on cluster

3. ```$ kubectl get namespace <​Namespace name>```      # List namespace having a particular name

4. ```$ kubectl describe namespace <​Namespace name>``` # Describe information about particular namespace

5. ```$ kubectl delete namespace <​Namespace name>```   # Delete a namespace

Alternatively, you can also create namespace using kubectl command without yaml file, you just have to pass namespace name like below:

```
kubectl create namespace <​insert-namespace-name-here>
```

*#Note:* 
- By default kubernetes object are created in *default* namespace. To create object in particular namespace you must pass *namespace: <​namespace-name>* in object *metadata*. 
- Object created inside a particular namespace cannot be accessed/used by objects created in another namespace.
- To get manage object in particular namespace, *-n <​namespace>* argument is passed in kubectl command. e.g. to get pod from a particular namespace, command will be like ```kubectl get pods -n <​namespace>```



---
### Operators

**Operator overview**

**What is an Operators** An Operator is a way to package, run, and maintain a Kubernetes application. It builds on Kubernetes abstractions to automate the entire lifecycle of the software it manages. Manage means an Operator can perform: scaling a complex app, application version upgrades, or even managing kernel modules for nodes in a computational cluster with specialized hardware.

**How Operators use Kubernetes resources** Operators is a custom Kubernetes controller and work by extending the Kubernetes control plane and API. In its simplest form, an Operator adds an endpoint to the Kubernetes API, called a Custom Resource (CR). It then continuously monitors and maintains resources of this new type of resource. This Operator can then take action based on the resource’s state. What actions the Operator takes in response to changes in the CR is specific to the application the Operator manages.

**Kubernetes CRs** CRs are the API extension mechanism in Kubernetes. A custom resource definition (CRD) defines a CR; it’s analogous to a schema for the CR data. Unlike members of the official API, a given CRD doesn’t exist on every Kubernetes cluster. CRDs extend the API of the particular cluster where they are defined. A cluster user can interact with CRs with kubectl or another Kubernetes client, just like any other API resource.

The diagram shows what the operator does.

![Operator_overview](_images/Operator_overview.png)

The coming sections give 2 practical examples on how Operators are deployed and work-

---

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

#Execute below command to create directory for this operator


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

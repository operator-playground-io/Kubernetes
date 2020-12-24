
---

  

title: Kubernetes

  

description: Kubernetes

  

---

### Introduction


**Kuberntes Basics**
#

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
#

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


### Create and Manage Kubernetes Resources


**Objects Overview**
#

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

**Lab- Pod**
#

In simple words a *Pod:* is an Atomic Unit of Scheduling in Kubernetes. Containers are encapsulated inside a pod.

To deploy a pod user generally write a pod manifest file which consists of the container images that user wants to deploy on kubernetes node. These manifests are submitted on API Server on master node, then API Server and scheduler components deploys the pod on a worker node.

This process can be illustrated by the below diagram.

![pod](_images/pod.png "pod")

Now coming to pod definition, in this section user will create a pod to deploy a nginx container image from docker hub.

Execute below command to create a pod manifest file *nginx-pod.yaml*.

```execute
cat <<'EOF'>nginx-pod.yaml
# apiVersion of Pod resource is "v1"
apiVersion: v1
kind: Pod
metadata:
  # Name of pod
  name: nginx-pod
  # Labels for pod management
  labels:
    app: nginx
spec:
  # Containers specifications
  containers:
  - name: nginx-container
    image: nginx:1.17
    ports:
    - containerPort: 80
EOF
```

Execute below command to create pod object:

```execute
kubectl create -f nginx-pod.yaml
```

Output: ```pod/nginx created```

To verify that pod is running, execute below command:

```execute
kubectl get pod
```

Output: 


![pod_1](_images/pod_1.png "pod_1")

To get the IP Address of the pod, execute the below *kubectl get* command with wide option:

```execute
kubectl get pod -o wide
```

Output:

```
NAME        READY   STATUS    RESTARTS   AGE   IP               NODE            NOMINATED NODE   READINESS GATES
nginx-pod   1/1     Running   0          37s   172.30.200.199   10.221.78.188   <none>           <none>
```

To print all the details of pod, execute the below *kubectl describe* command:

```execute
kubectl describe pod nginx-pod
```

In the output this will print all the information including the events of the pod. This information comes handy during troubleshooting.

To get inside the pod using interactive shell, execute below *kubectl exec* command:

```execute
kubectl exec -it nginx-pod -- /bin/bash
```

In above command *-it* refers to *interactive shell* and type of shell is bash.

After getting inside the pod, execute below command to see hostname of the pod:

```execute
hostname
```

Output:  ```nginx-pod```

To exit from pod interactive shell, execute below command:

```execute
exit
```

Delete the pod by executing below command:

```execute
kubectl delete -f nginx-pod.yaml
```

Output: ```pod "nginx-pod" deleted```


**Lab- Replicase**
#

ReplicaSet is a controller which ensures that a specified number of pods are running at any time to ensure high availability, load balancing and failure tolerance:

1. New pods are launched when they get failed, get deleted or terminated unexpectedly.

2. If there are excess Pods, they get killed and vice versa.

ReplicaSet and Pods are associated with the help of *"labels"* and *"selectors"*:

1. *Labels* are key-value pairs generally attached to Pods. It is a kind of tag given to one or set of related pods together. Label helps to display and manage related pods as a set.
	
2. Controllers and Services manage the collection of Pods using *Selectors*. (Pod Labels goes inside Selectors)

Now coming to ReplicaSet definition, in this section you will create a ReplicaSet to deploy and manage pods with a nginx container image from docker hub.

Execute the below command to create a ReplicaSet manifest file:

```execute
cat <<'EOF'>nginx-replicaset.yaml
# apiVersion of ReplicaSet is "apps/v1"
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  # Name of ReplicaSet
  name: nginx-replicaset
spec:
  # replicas is the number of pod to be deployed and managed
  replicas: 3
  # ReplicaSet uses selector to filter out related pods to be managed
  selector:
    matchLabels:
      app: nginx-app
  # Pod definition geos inside template
  template:
    metadata:
      name: nginx-pod
      # labels associated with pod
      labels:
        app: nginx-app
    spec:
      # pod container specs
      containers:
      - name: nginx-container
        image: nginx:1.17
        ports:
        - containerPort: 80
EOF
```

To deploy ReplicaSet, execute below command:

```execute
kubectl create -f nginx-replicaset.yaml
```

Output: ```replicaset.apps/nginx-replicaset created```

Execute below command to check status of replicaset:

```execute
kubectl get rs nginx-replicaset
```

*#Note*: in command *rs* is the abbreviation of replicaset

Output:

![](_images/replicaset_0.png)

From the output you can see that all the pod replicas are in ready state

Execute below command to get list of pods:

```execute
kubectl get pods
```

Output:

![](_images_/replicaset_1.png)

Notice that there are three pods running with name *nginx-replicaset* appended with a random alphanumeric string, this is done to create unique pod names.

Assume that there are hundreds of other pods also running. To list out pods with label as *app=nginx-app*, execute the below command:

```execute
kubectl get pod -l app=nginx-app
```

*Note:* *-l* flag supports '=', '==', and '!='. You can pass multiple labels also (e.g. *-l* key1=value1, key2=value2, key3!=value3)

To get detailed information of replication, execute below command:

```execute
kubectl describe rs nginx-replicaset
```

Output will show information and events for all the pods managed by replicaset.

It's time to perform various test cases on ReplicaSet to understand its advantages.

To test scheduling function of ReplicaSet, delete one of the pod by following below steps:

Copy name of one of the pod from the output of *kubectl get pod -l app=nginx-app* and replace with the <pod-name> in the below command, then past the command in terminal.

```
kubectl delete pod <​pod-name>
```

Now ReplicaSet should launch a new pod to maintain the replica count.

You can verify that new pod is created by executing below command and checking the *Age* column of the output, there will be a pod that's created recently:

```execute
kubectl get pod
```

For example, if command ```kubectl delete pod nginx-replicaset-6jgh8``` is executed:

Sample output is shown below:

![](_images/replicaset_2.png)

There will be one new pod that is created recently.

Suppose that the traffic to your application is increased and 2 extra pod are required to manage traffic. To scale up the application instances from 3 to 5, execute below *kubectl scale* command:

```execute
kubectl scale rs nginx-replicaset --replicas=5
```

To see the pods, execute below command:

```execute
kubectl get pod
```

There should be 2 new pod created and 5 pods in total.

Sample output is shown below:

![](_images/replicaset_3.png)

If traffic to your application is low and only 2 application instances are required to manage traffic. Then scale down the application instances to 2 by executing below command:

```execute
kubectl scale rs nginx-replicaset --replicas=2
```

To see the pods, execute below command:

```execute
kubectl get pod
```

Now there should be 2 pods in Running state like below output: 

![](_images/replicaset_4.png)

Execute below command to delete replicaset:

```execute
kubectl delete -f nginx-replicaset.yaml
```

Output: ```replicaset.apps "nginx-replicaset" deleted```


**Lab- Deployment**
#

Deployments are upgraded and higher version of replication controller. They manage the deployment of replica sets which is also an upgraded version of the replication controller. They have the capability to update the replica set and are also capable of rolling back to the previous version.

<strong>Architecture diagram of Deployment is given below</strong>:

![](_images/deployment-img.png)

You can manage all three objects using once single Deployment manifest file. Deployment manifest file contains pod definition, number of pod replicas and you also specify preferred upgrade strategy.

<strong>Features of Deployment</strong>:

1. Multiple Replicas: Ensures high availability, load balancing and failure tolerance. If user did not specify replicas count, then by default it is set to 1.

2. Upgrades: Update application instances with no downtime.

3. Rollbacks: If somethings go wrong with current upgrade, then deployment controller allows you to rollback to previous stable version.

4. Scale up or down: You can increase or decrease the application instances based on the load.

5. Pause and Resume: You can pause or resume the deployment process in progress as and when needed. Helps to test and validate new version of the application.

Now coming to Deployment definition, in this section you will create a Deployment to deploy and manage pods with a nginx container image from docker hub.

Execute the below command to create a Deployment manifest file:

```execute
cat <<'EOF'>nginx-deployment.yaml
# apiVersion of Deployment is "apps/v1"
apiVersion: apps/v1
kind: Deployment
metadata:
  # Name of Deployment
  name: nginx-deployment
spec:
  # replicas is the number of pod to be deployed and managed
  replicas: 3
  # Deployment uses selector to filter out related pods to be managed
  selector:
    matchLabels:
      app: nginx-app
  # Pod definition geos inside template
  template:
    metadata:
      name: nginx-pod
      # labels associated with pod
      labels:
        app: nginx-app
    spec:
      # pod container specs
      containers:
      - name: nginx-container
        image: nginx:1.17
        ports:
        - containerPort: 80
EOF
```

*#Note:*If you notice the content of Deployment manifest file then it will look similar to the manifest file of ReplicaSet with only one change and that is *kind* which is *Deployment* in this case.

To create Deployment object, execute below command:

```execute
kubectl create -f nginx-deployment.yaml
```

Output: ```deployment.apps/nginx-deployment created```

Execute below command to check status of deployment:

```execute
kubectl get deployment nginx-deployment
```

Output:

![](_images/deployment_1.png)

From the output you can see that all the pod replicas are running and available.

To list the pods which are part of this deployment execute the below command:

```execute
kubectl get pods -l app=nginx-app
```

Output:

![](_images/deployment_2.png)

Notice that there are three pods running with name *nginx-deployment* appended with a random alphanumeric string, this is done to create unique pod names.

When you create deployment object it will automatically create ReplicaSet object in backend. You can get it by executing below command:

```execute
kubectl get rs -l app=nginx-app
```

To print detailed output of deployment, execute *kubectl describe* command given below:

```execute
kubectl describe deployment nginx-deployment
```

Notice that there is a field *StrategyType* which is the field for updateStrategy. As it is not mentioned in deployment manifest file, by default it is set *RollingUpdate*. Also, you can see the list of events at the end.

<strong>Test the use cases of Deployment:</strong>

<strong>Update Strategies:</strong>

Suppose you are updating application from version A to version B, then there are different types of deployment strategies you can use:

1. <strong>Recreate</strong>: Shuts down version A and then deploy version B, using this strategy there will be downtime of the service.

2. <strong>RollingUpdate</strong>: This is the *default* updating strategy in kubernetes. It rolls out the update of application by replacing instances one after the other until all the updated instances are successfully rolled out.

3. <strong>Canary</strong>: This deployment strategy consists of gradually shifting production traffic from version A to version B. Suppose there are 5 instances of version A running inside your cluster and you do not want to upgrade all at one because you want to test version B. So, you upgrade 2 instances of version A to version B and after testing new version you completely replace version A to version B.

4. <strong>Blue / Green</strong>: Using this strategy version B which is *Green* is deployed alongside the version A which is *Blue* with exactly same number of instances. After testing the new version of deployment, traffic is switched from version A to version B at loadbalancer level. Advantage is instant rollout and rollback.

<strong>Update Deployment:</strong>

Currently the version of container image is 1.17. You can update the container image version to 1.18 by executing below *kubectl set* command with *--record* option to checkpoint the current state of deployment, this is useful if rollback is needed later due to any problem:

```execute
kubectl set image deployment nginx-deployment nginx-container=nginx:1.18 --record
```

This will set image field of nginx-container in the deployment nginx-deployment and rollout the update.

Output: ```deployment.extensions/nginx-deployment image updated```

Check the rollout status of deployment by executing below command:

```execute
kubectl rollout status deployment/nginx-deployment
```

Output: ```deployment "nginx-deployment" successfully rolled out```

Check the status of deployment by executing below command:

```execute
kubectl get deployment nginx-deployment
```

Get detailed information about the deployment by executing below command:

```execute
kubectl describe deployment nginx-deployment
```

In the output you can see that the image version is updated to 1.18 which was 1.17 previously

Check the updated pods by executing below command:

```execute
kubectl get pods -l app=nginx-app
```

<strong>Rollback Deployment:</strong>

Suppose that for some reason the update did not meet expectations and you want to rollback to previous deployment and because you had set the *--record* option which maintain history of deployment updates, you can execute the given *kubectl rollout undo* command to get back to previous deployment:

```execute
kubectl rollout undo deployment nginx-deployment
```

Verify the rollback by executing below command:

```execute
kubectl describe deployment nginx-deployment
```

Notice that the image version of nginx-container is now changed to 1.17 as before.

<strong>Delete Deployment</strong>

Execute below command to delete deployment.

```execute
kubectl delete deployment nginx-deployment
```

Output: ```deployment.apps "nginx-deployment" deleted```

To verify that all pod associated with deployment are deleted, execute below command:

```execute
kubectl get pods -l app=nginx-app
```

Output:  ```No resource found```



**Lab- Services**
#

Service is a way of management and grouping of related pods running on cluster. It can be defined as an abstraction on the top of the pod which provides a single IP address and DNS name by which pods can be accessed. With Service, it is very easy to manage load balancing configuration. It helps pods to scale very easily.

A service is a REST object in Kubernetes whose definition can be posted to Kubernetes apiServer on the Kubernetes master to create a new instance.

Services discover their respective pods with the help of labels and selectors.

<strong>Types of Services</strong>:

1. <strong>ClusterIP Service</strong>: It is default type of service in kubernetes. This helps in restricting the service within the cluster. Exposes the Service on a cluster-internal IP. Choosing this value makes the Service only reachable from within the cluster

2. <strong>NodePort Service</strong>: It will expose the service on a static port (nodePort) on the deployed node. The service can be accessed from outside the cluster using the NodeIP:nodePort.

3. <strong>Load Balancer</strong>: It helps to manage traffic across nodes. NodePort and ClusterIP services are created automatically to which the external load balancer will route.

A diagram is shown below to explain the working of service:

![](_images/service-architecture.png)

In the above diagram there are two types of application instances *frontend* and *backend*. Frontend pods with label *app: frontend* are grouped using a NodePort type of service because the need to be exposed to outside world. On the other hand, backend pods *app: backend* are grouped using ClusterIP type of service because they do not need to be directly exposed to outside work due to security reasons. In this example there is only one node, if there are multiple nodes then you need to group them using Load Balancer type of service.

In this lab you will learn how to create a service:

First, Execute the below command to create the deployment using the manifest which was created in previous section:

```execute
kubectl create -f nginx-deployment.yaml
```

To group the pods created by deployment, create the service object manifest by executing command given below:

```execute
cat <<'EOF'>nginx-service-nodeport.yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  # service will route network requests to the pods with labels same as selector below
  selector:
    app: nginx-app
  type: NodePort
  ports:
  - name: service-nodeport
    port: 80
    targetPort: 80
EOF
```

Below is the description of *ports* field used in the service definition.

<strong>port: </strong>
Expose the service on the specified port internally within the cluster. That is, the service becomes visible on this port, and will send requests made to this port to the pods selected by the service.

<strong>targetPort: </strong>
This is the port on the pod that the request gets sent to. Your application needs to be listening for network requests on this port for the service to work.

Execute below command to create the service using the manifest file:

```execute
kubectl create -f nginx-service-nodeport.yaml
```

To get list of all the services, execute below command:

```execute
kubectl get service
```

To detailed information of the service, execute below command:

```execute
kubectl describe svc nginx-service
```

Use the nodeport number alongwith **##SSH.host##** in the browser to access the application. Example if your nodeport number is **32145**, the URL will be **##SSH.host##:32145**.

Output must be the welcome page of the Nginx server.
![](_images/nginx-output.png)

To delete service, execute below command:

```execute
kubectl delete service nginx-service
```

Output: ```service "nginx-service" deleted```

To delete deployment, execute below command:

```execute
kubectl delete deployment nginx-deployment
```

Output: ```deployment.apps "nginx-deployment" deleted```


**Lab- Volumes**
#

In Kubernetes, a volume can be thought of as a directory which is accessible to the containers in a pod. A volume can be thought of as a mounted directory. It is needed to make the pod stateful. If data needs to persist, volumes are needed. We have different types of volumes in Kubernetes and the type defines how the volume is created and its content.

The concept of volume was present with Docker, however the only issue was that the volume was very much limited to a particular pod. As soon as the life of a pod ended, the volume was also lost. Also, when running Containers together in a Pod it is often necessary to share files between those Containers. The Kubernetes Volume abstraction solves both of these problems.

To use a volume, you have to specify what volumes to provide for the Pod ( in the ```.spec.volumes``` field) and where to mount those into Containers (in the ```.spec.containers[*].volumeMounts``` field).

In this exercise, you will create a hostPath PersistentVolume. Kubernetes supports hostPath for development and testing on a single-node cluster. A hostPath PersistentVolume uses a file or directory on the Node to emulate network-attached storage.

In a production cluster, you would not use hostPath. Instead, a cluster administrator would provision a network resource like a Google Compute Engine persistent disk, an NFS share, or an Amazon Elastic Block Store volume. Cluster administrators can also use StorageClasses to set up dynamic provisioning.

<strong>Create a PersistentVolume:</strong>

Execute the below command to create a PersistentVolume manifest file:

```execute
cat <<'EOF'>persistent-volume.yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-volume
  labels:
    type: local
spec:
  storageClassName: manual
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mnt/data"
EOF
```

The configuration file specifies that the volume is at /mnt/data on the cluster’s Node. The configuration also specifies a size of 1 gibibytes and an access mode of ReadWriteOnce, which means the volume can be mounted as read-write by a single Node. It defines the StorageClass name manual for the PersistentVolume, which will be used to bind PersistentVolumeClaim requests to this PersistentVolume.

Create the PersistentVolume object by executing below command:

```execute
kubectl create -f persistent-volume.yaml
```

View information about the PersistentVolume by executing below command:

```execute
kubectl get pv pv-volume
```

The output shows that the PersistentVolume has a STATUS of Available. This means it has not yet been bound to a PersistentVolumeClaim.

```
NAME        CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE
pv-volume   1Gi        RWO            Retain           Available           manual                  9s
```

<strong>Create a PersistentVolumeClaim:</strong>

The next step is to create a PersistentVolumeClaim. Pods use PersistentVolumeClaims to request physical storage. In this exercise, you will create a PersistentVolumeClaim that requests a volume of at least 1 gibibytes that can provide accessMode of ReadWriteOnce.

Execute the below command to create a PersistentVolumeClaim manifest file:

```execute
cat <<'EOF'>persistent-volume-claim.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pv-claim
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
EOF
```

Create the PersistentVolumeClaim by executing below command:

```execute
kubectl create -f persistent-volume-claim.yaml
```

After you create the PersistentVolumeClaim, the Kubernetes control plane looks for a PersistentVolume that satisfies the claim’s requirements. If the control plane finds a suitable PersistentVolume with the same StorageClass, it binds the claim to the volume.

Look again at the PersistentVolume (PV) by executing below command:

```execute
kubectl get pv pv-volume
```

Now the output shows a STATUS of Bound.

```
NAME        CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM              STORAGECLASS   REASON   AGE
pv-volume   1Gi        RWO            Retain           Bound    default/pv-claim   manual                  51s
```

Look at the PersistentVolumeClaim (PVC) by executing below command:

```execute
kubectl get pvc pv-claim
```

The output shows that the PersistentVolumeClaim is bound to your PersistentVolume, task-pv-volume.

```
NAME       STATUS   VOLUME      CAPACITY   ACCESS MODES   STORAGECLASS   AGE
pv-claim   Bound    pv-volume   1Gi        RWO            manual         33s
```

Create a Pod
The next step is to create a Pod that uses your PersistentVolumeClaim as a volume.

Here is the configuration file for the Pod:

```execute
cat <<'EOF'>pod-with-pv.yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-pv
spec:
  volumes:
  - name: pv-storage
    persistentVolumeClaim:
      claimName: pv-claim
  containers:
    - name: pv-container
      image: nginx:1.17
      ports:
      - containerPort: 80
        name: "http-server"
      volumeMounts:
      - name: pv-storage
        mountPath: "/test-mount-path/"          
EOF
```

Notice that the Pod’s configuration file specifies a PersistentVolumeClaim, but it does not specify a PersistentVolume. From the Pod’s point of view, the claim is a volume.

Create the Pod by executing below command:

```execute
kubectl create -f pod-with-pv.yaml
```

Verify that the container in the Pod is running;

```execute
kubectl get pod pod-with-pv
```

Get in interactive shell to the container running in your Pod:

```execute
kubectl exec -it pod-with-pv -- /bin/bash
```	

Execute below command to create a sample text file named *"hello1.txt"* at mountPath */test-mount-path/*:

```execute
cat <<'EOF'>/test-mount-path/hello1.txt
Hello from Pod 1
EOF
```

Execute below command get *exit* from container shell:

```execute
exit
```

To check if data persist or not, delete the pod by executing below command and then create new pod using the next command:

```execute
kubectl delete pod pod-with-pv
```

```execute
kubectl create -f pod-with-pv.yaml
```

Get in interactive shell to the container running in your Pod:

```execute
kubectl exec -it pod-with-pv -- /bin/bash
```	

Execute below command to change directory to mountPath:

```execute
cd test-mount-path/
```

Execute below command to list files inside the directory:

```execute
ls
```

Output: ```hello1.txt```

View content of file:

```execute
cat hello1.txt
```

Output: ```Hello from Pod 1```

This proves that data persist at mounted path on persistent volume even if pod gets terminated due to any reason.

Execute below command get *exit* from container shell:

```execute
exit
```

<strong>Delete created resources:</strong>

Delete Pod by executing below command:

```execute
kubectl delete pod pod-with-pv
```

Output: ```pod "pod-with-pv" deleted```

Delete PersistentVolumeClaim (PVC) by executing below command:

```execute
kubectl delete pvc pv-claim
```

Output: ```persistentvolumeclaim "pv-claim" deleted```

Delete PersistentVolume (PV) by executing below command:

```execute
kubectl delete pv pv-volume
```

Output: ```persistentvolume "pv-volume" deleted```

**Lab- ConfigMaps**
#

ConfigMap is a kubernetes object which allows you to separate cofigurations from pod and components. As a result, it keeps container portable and easier to manage by preventing harcoding of configuration data into pod definition.

ConfigMaps store cofiguration data as key-value pairs. It is similar to Secrets but don't contain sensitive information.

ConfigMap helps to keep the application and the configuration loosely coupled. Each time there is a change in the configuration, the pod need not be upgraded or redeployed.

*#Note:* You must create a ConfigMap before referencing it into a Pod definition.

You can write a Pod spec that refers to a ConfigMap and configures the container(s) in that Pod based on the data in the ConfigMap. The Pod and the ConfigMap must be in the same namespace.

ConfigMaps can be consumed as *Environment Variables* or as *Volumes*. In this lab you are going to use configmap to create environment variables.

Here’s an example ConfigMap that has some keys with single values, and other keys where the value looks like a fragment of a configuration file format.

```execute
cat <<'EOF'>demo-configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: game-demo
data:
  # property-like keys; each key maps to a simple value
  player_initial_lives: "3"
  
  # file-like keys
  game.properties: |
    enemy.types=aliens,monsters
    player.maximum-lives=5
    color.good=green
    color.bad=red
EOF
```

Create ConfigMap object by executing below command:

```execute
kubectl create -f demo-configmap.yaml
```

Below an example Pod with nginx image that uses values from game-demo configMap for pod configurations:

Execute below command to create pod manifest file:

```execute
cat <<'EOF'>demo-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: configmap-demo-pod
spec:
  containers:
    - name: demo
      image: nginx:1.17
      env:
        # Define environment variables
		
        - name: PLAYER_INITIAL_LIVES # Notice that the case is different here
                                     # from the key name in the ConfigMap. This can be any name.
          valueFrom:
            configMapKeyRef:
              name: game-demo           # The ConfigMap this value comes from.
              key: player_initial_lives # The key to fetch.

        - name: game_properties
          valueFrom:
            configMapKeyRef:
              name: game-demo
              key: game.properties
      # Path to mount configmap
      volumeMounts:
      - name: config
        mountPath: "/config"
        readOnly: true
  volumes:
    # You set volumes at the Pod level, then mount them into containers inside that Pod
    - name: config
      configMap:
        # Provide the name of the ConfigMap you want to mount.
        name: game-demo
EOF
```

Create Pod object by executing below command:

```execute
kubectl create -f demo-pod.yaml
```

Execute below command to list config files created inside the mountPath directory:

```execute
kubectl exec configmap-demo-pod ls /config
```

A ConfigMap doesn’t differentiate between single line property values and multi-line file-like values. What matters how Pods and other objects consume those values. For this example, defining a volume and mounting it inside the demo container as */config* creates two files:

1. player_initial_lives

2. game.properties

Execute below command to print the value of environment *PLAYER_INITIAL_LIVES*:

```execute
kubectl exec configmap-demo-pod printenv PLAYER_INITIAL_LIVES
```

Output: ```3```

Execute below command to print the value of environment *game_properties*:

```execute
kubectl exec configmap-demo-pod printenv game_properties
```

Output: 

```
enemy.types=aliens,monsters
player.maximum-lives=5
color.good=green
color.bad=red
```

Delete the ConfigMap by executing below command:

```execute
kubectl delete configmap game-demo
```

Delete the Pod by executing below command:

```execute
kubectl delete pod configmap-demo-pod
```

**Lab- Secrets**
#

Kubernetes Secrets let you store and manage sensitive information, such as passwords, Auth tokens, and ssh keys. Storing confidential information in a Secret is safer and more flexible than putting it in a Pod definition or in a container image.

*#Note:* You must create a Secret before referencing it into a Pod definition. Maximum size limit of each secret is 1MB.

Secrets get stored inside ETCD database on Kubernetes Master. 

Secrets are consumed in two ways inside Pod definition -

1. Mount as Volumes

2. As Environment Variables

<strong>Create Secret:</strong>

First encrypt the data, usually base64 encoding is used.

Execute below commands to encode username and password details:

```execute
echo -n "admin" | base64
```

Output: ```YWRtaW4=```

```execute
echo -n "P@ssw0rd" | base64
```

Output: ```UEBzc3cwcmQ=```

Execute below command to create Secret manifest file with encoded base64 data of username and password data:

```execute
cat <<'EOF'>my-secret.yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-secret
type: Opaque
data:
  username: YWRtaW4=
  password: UEBzc3cwcmQ=
EOF
```

Execute below command to create secret object using manifest file:

```execute
kubectl create -f my-secret.yaml
```

Output: ```secret/my-secret created```

Below is the pod manifest file to consume the created secret from *Volume*, execute below command to create the manifest file:

```execute
cat <<'EOF'>mysecret-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: mysecret-pod
spec:
  containers:
  - name: secret-container
    image: nginx:1.17
    volumeMounts:
    - name: secret-mount
      mountPath: "/etc/secret-mount"
      readOnly: true
  volumes:
  - name: secret-mount
    secret:
      secretName: my-secret
EOF
```

Execute below command to create pod:

```execute
kubectl create -f mysecret-pod.yaml
```

Output: ```pod/mysecret-pod created```

Execute below command to list the files inside the mountPath */etc/secret-mount*:

```execute
kubectl exec mysecret-pod ls /etc/secret-mount 
```

Output: There are two files created for secrets *password* and *username*.

```
password
username
```

To display the content of secrets where they are mounted, execute below commands:

```execute
kubectl exec mysecret-pod cat /etc/secret-mount/username
```

Output: ```admin```

```execute
kubectl exec mysecret-pod cat /etc/secret-mount/password
```

Output: ```P@ssw0rd```

Notice that secrets are in decrypted form, because secrets are decrypted when they are consumed inside the pod.

Execute below command to delete pod:

```execute
kubectl delete pod mysecret-pod
```

Below is the pod manifest file to consume the created secret from *Environment Variables*, execute below command to create the manifest file:

```execute
cat <<'EOF'>mysecret-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: mysecret-pod
spec:
  containers:
  - name: my-container
    image: nginx:1.17
    env:
    # Map "username" key to environment variable SECRET_USERNAME
    - name: SECRET_USERNAME
      valueFrom:
        secretKeyRef:
          name: my-secret
          key: username
    # Map "password" key to environment variable SECRET_PASSWORD
    - name: SECRET_PASSWORD
      valueFrom:
        secretKeyRef:
          name: my-secret
          key: password
EOF
```

Execute below command to create pod:

```execute
kubectl create -f mysecret-pod.yaml
```

Execute below command to check status of pod:

```execute
kubectl get pod
```

To list created environment variables, execute below command:

```execute
kubectl exec mysecret-pod env | grep SECRET
```

Output:

```
SECRET_PASSWORD=P@ssw0rd
SECRET_USERNAME=admin
```

Secrets are now set as environment variables inside the pod.

To delete the pod, execute below command:

```execute
kubectl delete pod mysecret-pod
```

To delete the secret, execute below command:

```execute
kubectl delete secret my-secret
```


**Lab- Deploying Application on Kubernetes**
#

In this section, let's take a look at how Kubernetes resources are put in combination to deploy an application so that the users are able to use it. Generally, a developer needs to perform the following steps while deploying an application.

![kubernetes-overview](_images/kubernetes_kubernetes-overview.png "kubernetes_kubernetes-overview")

1. Create the application with the desired framework and tools.
2. Create a Dockerfile for the application.
3. Build the Dockerfile which produces a Docker container image.
4. The image is pushed to the docker registry.
5. Create the necessary resources required for the application like Deployment, Service, Volumes etc onto the cluster.
6. Expose the Service.

After this, the users are able to access your application. Let's deploy a simple application onto the Kubernetes application.

For hands-on purpose, let's use an already built application image; it is actually a simple **Helloworld Flask application** and deploy this application onto the cluster. For deploying this application, you will need a **Deployment** resource and a **Service** resource to access this application from outside the cluster. Let's create them one by one.

Before starting, let's create a working directory for this application where all the files will be stored.

```execute
mkdir ${WORKING_DIR}/Flask-app
cd ${WORKING_DIR}/Flask-app
```

* Firstly create a namespace using the following command.
```execute
kubectl create namespace flask-app
```
****
* Create a **Deployment** with the application image.
```execute
cat <<'EOF'>flask-app-deployment.yaml
# apiVersion of Deployment is "apps/v1"
apiVersion: apps/v1
kind: Deployment
metadata:
  # Name of Deployment
  name: flask-app-deployment
spec:
  # replicas is the number of pod to be deployed and managed
  replicas: 3
  # Deployment uses selector to filter out related pods to be managed
  selector:
    matchLabels:
      app: flask-app
  # Pod definition geos inside template
  template:
    metadata:
      name: flask-app-pod
      # labels associated with pod
      labels:
        app: flask-app
    spec:
      # pod container specs
      containers:
      - name: flask-app-container
        image: quay.io/opentestlabs/flask-app:latest
        ports:
        - containerPort: 5000
EOF
```
Let's take a look at the code.
```execute
cat flask-app-deployment.yaml
```
The **Deployment** consists of one container with **image** field having the name of the image to be deployed and a **containerPort** whose value is **5000**. The Pods will listen on this port.

Create this object in `flask-app` namespace.
```execute
kubectl create -f flask-app-deployment.yaml -n flask-app
```
****
* Now create a service to access this application from outside the cluster. To access the application from outside, **NodePort** type to **Service** needs to be created.
```execute
cat <<'EOF'>flask-app-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: flask-service
spec:
  # service will route network requests to the pods with labels same as selector below
  selector:
    app: flask-app
  type: NodePort
  ports:
  - name: service-nodeport
    port: 8000
    targetPort: 5000
EOF
```
Let's take a look at the code.
```execute
cat flask-app-service.yaml
```
The **targetPort** field has value of **5000**. This is the port on the pod on which request gets sent. The **Deployment** had a **containerPort** whose value is **5000**. So the requests are sent to **5000** port number. Now let's create the **Service** object. The **selector** field forwards the requests to those Deployments, which has labels as `app: my-flask`.
Create the Service object.
```execute
kubectl create -f flask-app-service.yaml -n flask-app
```
To list the services in the **flask-app** namespace, use the following command.
```execute
kubectl get svc -n flask-app
```
To get a detailed information about the service which you created,
```execute
kubectl describe svc flask-service -n flask-app
```
Note the **IP** field from the output. You can use this IP address alongwith the assigned nodeport number to access your application. To access this application, use **##SSH.host##** alongwith the nodeport number. If your nodeport number is **32115**, the URL must look like - **##SSH.host##:32115**. Paste the URL into the browser.

Output must be like :

![](_images/flask-output.png)

Now that you have learnt how to deploy an application on Kubernetes, let's cleanup all the resources you created.

* Delete the service.
```execute
kubectl delete -f flask-app-service.yaml -n flask-app
```
* Delete the deployment.
```execute
kubectl delete -f flask-app-deployment.yaml -n flask-app
```
* Delete the namespace.
```execute
kubectl delete namespace flask-app
```
* Delete all the files and folder you created.
```execute
cd ..
rm -rf Flask-app
```

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

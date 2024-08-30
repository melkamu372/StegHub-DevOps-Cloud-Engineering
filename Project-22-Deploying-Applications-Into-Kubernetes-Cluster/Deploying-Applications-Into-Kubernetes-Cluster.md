### Deploying a random Pod

Lets see what it looks like to have a Pod running in a k8s cluster. This section is just to illustrate and get you to familiarise with how the object's fields work. Lets deploy a basic Nginx container to run inside a Pod.

- **apiVersion is v1**
- **kind is Pod**
- **metatdata has a name which is set to nginx-pod**
- The **spec section** has further information about the Pod. Where to find the image to run the container - (This defaults to Docker Hub), the port and protocol.
The structure is similar for any Kubernetes objects, and you will get to see them all as we progress.
**Prerequisites you need to meet before you can deploy a Pod in a Kubernetes**
1. Set Up Your Kubernetes Environment
2. Set Up a Kubernetes Cluster
3. Make sure kubectl can communicate with your cluster

1. Create a [Pod yaml](https://kubernetes.io/docs/concepts/workloads/pods/) manifest on your master node

```
sudo cat <<EOF | sudo tee ./nginx-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
spec:
  containers:
  - image: nginx:latest
    name: nginx-pod
    ports:
    - containerPort: 80
      protocol: TCP
EOF
```
![image](https://github.com/user-attachments/assets/00ff1a50-8eee-43ad-a23b-0227dc0e5377)

2. Apply the manifest with the help of kubectl
```
kubectl apply -f nginx-pod.yaml
```

**Output**:
```
pod/nginx-pod created
```
![image](https://github.com/user-attachments/assets/6445b0a5-d2d1-4518-bc38-11ccb7476c6f)

3. Get an output of the pods running in the cluster
```
kubectl get pods
```

Output:
```
NAME        READY   STATUS    RESTARTS   AGE
nginx-pod   1/1     Running   0          19m
```
![image](https://github.com/user-attachments/assets/805a5d50-f9aa-46e3-a54c-46f884e4d771)

4. If the Pods were not ready for any reason, for example if there are no worker nodes, you will see something like the below output.
```
NAME        READY   STATUS    RESTARTS   AGE
nginx-pod   0/1     Pending   0          111s
```
5. To see other fields introduced by kubernetes after you have deployed the resource, simply run below command, and examine the output. You will see other fields that kubernetes updates from time to time to represent the state of the resource within the cluster. -o simply means the output format.

```
kubectl get pod nginx-pod -o yaml
```

![image](https://github.com/user-attachments/assets/2667abfd-a4ae-4c0c-a0fb-6500097b0c37)
![image](https://github.com/user-attachments/assets/51fa44cb-1137-42be-813c-f967faa904ac)
or
```
kubectl describe pod nginx-pod
```
![image](https://github.com/user-attachments/assets/48814735-a2c5-4ed6-9f55-aa4c6b3905f2)
![image](https://github.com/user-attachments/assets/febc0784-1758-4f35-9431-a9dceda512d6)

### Accessing the app from the browser

**Now you have a running Pod. What's next?**

The ultimate goal of any solution is to access it either through a web portal or some application (e.g., mobile app). We have a Pod with Nginx container, so we need to access it from the browser. But all you have is a running Pod that has its own IP address which cannot be accessed through the browser. To achieve this, we need another Kubernetes object called [Service](https://kubernetes.io/docs/concepts/services-networking/service/) to accept our request and pass it on to the Pod.

A service is an object that accepts requests on behalf of the Pods and forwards it to the Pod's IP address. If you run the command below, you will be able to see the Pod's IP address. But there is no way to reach it directly from the outside world.
```
kubectl get pod nginx-pod  -o wide 
```
Output:
```
NAME        READY   STATUS    RESTARTS   AGE    IP               NODE                                              NOMINATED NODE   READINESS GATES
nginx-pod   1/1     Running   0          138m   172.50.202.214   ip-172-50-202-161.eu-central-1.compute.internal   <none>           <none>
```
![image](https://github.com/user-attachments/assets/84cc24f8-2f63-4406-8c58-11c8986ef321)

Let us try to access the Pod through its IP address from within the K8s cluster. To do this,

1. We need an image that already has curl software installed. You can check it out [here](https://hub.docker.com/r/dareyregistry/curl)
```
dareyregistry/curl
```
Retrieve the IP Address
```
kubectl get pod nginx-pod -o wide
```
![image](https://github.com/user-attachments/assets/4c58169e-f059-4187-a5ee-34b14eb56d33)

2. Run kubectl to connect inside the container
```
kubectl run curl --image=dareyregistry/curl -i --tty
```
or you can Run  Temporary Pod
```
kubectl run curl-pod --image=appropriate/curl --restart=Never -i --tty -- /bin/sh
```
3. Run curl and point to the IP address of the Nginx Pod (Use the IP address of your own Pod)
```
 curl 10.244.0.3:80
```
Output:
```
> GET / HTTP/1.1
> User-Agent: curl/7.35.0
> Host: 172.50.202.214
> Accept: */*
> 
< HTTP/1.1 200 OK
< Server: nginx/1.21.0
< Date: Sat, 12 Jun 2021 21:12:56 GMT
< Content-Type: text/html
< Content-Length: 612
< Last-Modified: Tue, 25 May 2021 12:28:56 GMT
< Connection: keep-alive
< ETag: "60aced88-264"
< Accept-Ranges: bytes
< 
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```
![image](https://github.com/user-attachments/assets/adadfa81-9562-49aa-bd0a-13ddf7f02731)

**Clean Up**
```
kubectl delete pod curl-pod
```
If the use case for your solution is required for internal use ONLY, without public Internet requirement. Then, this should be OK. But in most cases, it is NOT!
Assuming that your requirement is to access the Nginx Pod internally, using the Pod's IP address directly as above is not a reliable choice because Pods are ephemeral. They are not designed to run forever. When they die and another Pod is brought back up, the IP address will change and any application that is using the previous IP address directly will break.

To solve this problem, kubernetes uses Service - An object that abstracts the underlining IP addresses of Pods. A service can serve as a load balancer, and a reverse proxy which basically takes the request using a human readable DNS name, resolves to a Pod IP that is running and forwards the request to it. This way, you do not need to use an IP address. Rather, you can simply refer to the service name directly.

Let us create a service to access the **Nginx Pod**

1. Create a Service yaml manifest file:
```
sudo cat <<EOF | sudo tee ./nginx-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx-pod 
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
EOF
```
![image](https://github.com/user-attachments/assets/f48c1b93-35c4-4bde-9f7b-d2e9848919b8)

2. Create a nginx-service resource by applying your manifest
```
kubectl apply -f nginx-service.yaml
```
output:
```
service/nginx-service created
```
Check the created service
```
kubectl get service
```
output:
```
NAME            TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
kubernetes      ClusterIP   10.100.0.1      <none>        443/TCP   68d
nginx-service   ClusterIP   10.100.71.130   <none>        80/TCP    85s
```
![image](https://github.com/user-attachments/assets/40f201c6-fe9b-47c9-9c68-a5b392ef2580)

Observation:

The TYPE column in the output shows that there are [different service types](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types).

- ClusterIP
- NodePort
- LoadBalancer &
- Headless Service
  
Since we did not specify any type, it is obvious that the default type is ClusterIP

Now that we have a service created, how can we access the app? Since there is no public IP address, we can leverage kubectl's port-forward functionality.
```
kubectl  port-forward svc/nginx-service 8089:80
```
**8089** is an arbitrary port number on your laptop or client PC, and we want to tunnel traffic through it to the port number of the nginx-service 80.

![image](https://github.com/user-attachments/assets/b9f34457-ac42-4717-bf21-7a65da7080b7)

Unfortunately, this will not work quite yet. Because there is no way the service will be able to select the actual Pod it is meant to route traffic to. If there are hundreds of Pods running, there must be a way to ensure that the service only forwards requests to the specific Pod it is intended for.

To make this work, you must reconfigure the Pod manifest and introduce labels to match the selectors key in the field section of the service manifest.

Update the Pod manifest with the below and apply the manifest:
```
sudo cat <<EOF | sudo tee ./nginx-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    app: nginx-pod  
spec:
  containers:
  - image: nginx:latest
    name: nginx-pod
    ports:
    - containerPort: 80
      protocol: TCP
EOF
```
**Notice** that under the metadata section, we have now introduced labels with a key field called app and its value nginx-pod. This matches exactly the selector key in the service manifest.

The key/value pairs can be anything you specify. These are not Kubernetes specific keywords. As long as it matches the selector, the service object will be able to route traffic to the Pod.

Apply the manifest with:
```
kubectl apply -f nginx-pod.yaml
```
2. Run kubectl port-forward command again
```
kubectl  port-forward svc/nginx-service 8089:80
```
output:
```
kubectl  port-forward svc/nginx-service 8089:80
Forwarding from 127.0.0.1:8089 -> 80
Forwarding from [::1]:8089 -> 80
```
![image](https://github.com/user-attachments/assets/6e17eafd-62dc-4d33-ae42-f9c9d1858aaf)

Then go to your web browser and enter localhost:8089 - You should now be able to see the nginx page in the browser.

![image](https://github.com/user-attachments/assets/51f55a5b-9bdc-4d4f-beb3-8812e67af4b0)


Let us try to understand a bit more about how the service object is able to route traffic to the Pod.

If you run the below command:
```
kubectl get service nginx-service -o wide
```
You will get the output similar to this:
```
NAME            TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE   SELECTOR
nginx-service   ClusterIP   10.107.171.86   <none>        80/TCP    4d    app=nginx-pod
```
![image](https://github.com/user-attachments/assets/67118101-018d-45ea-88da-9dd0f8b1ee25)

As you already know, the service's type is ClusterIP, and in the above output, it has the IP address of  10.107.171.86 - This IP works just like an internal loadbalancer. It accepts requests and forwards it to an IP address of any Pod that has the respective selector label. In this case, it is app=nginx-pod. If there is more than one Pod with that label, service will distribute the traffic to all theese pofs in a [Round Robin](https://en.wikipedia.org/wiki/Round-robin_scheduling) fashion.

Now, let us have a look at what the Pod looks like:
```
kubectl get pod nginx-pod --show-labels
```
Output:

```
NAME        READY   STATUS    RESTARTS   AGE   LABELS
nginx-pod   1/1     Running   0          31m   app=nginx-pod
```
![image](https://github.com/user-attachments/assets/420b3764-9c04-48dd-bb18-b527dacbed4b)

**Notice** that the IP address of the Pod, is NOT the IP address of the server it is running on. Kubernetes, through the implementation of network plugins assigns virtual IP adrresses to each Pod.
```
kubectl get pod nginx-pod -o wide
```
Output:
```
NAME        READY   STATUS    RESTARTS   AGE   IP               NODE                                              NOMINATED NODE   READINESS GATES
nginx-pod   1/1     Running   0          57m   172.50.197.236   ip-172-50-197-215.eu-central-1.compute.internal   <none>           <none>
```
![image](https://github.com/user-attachments/assets/82b0faa9-5008-4e57-bcd2-479da08f488f)

Therefore, Service with IP 10.107.171.86 takes request and forwards to Pod with IP 10.244.0.3

### Self Side Task:
1. Build the Tooling app Dockerfile and push it to Dockerhub registry
2. Write a Pod and a Service manifests, ensure that you can access the Tooling app's frontend using port-forwarding feature.

**Expose a Service on a server's public IP address & static port**

Sometimes, it may be needed to directly access the application using the public IP of the server (when we speak of a K8s cluster we can replace 'server' with 'node') the Pod is running on. This is when the [NodePort service](https://kubernetes.io/docs/concepts/services-networking/service/#nodeport) type comes in handy.

A Node port service type exposes the service on a static port on the node's IP address. NodePorts are in the 30000-32767 range by default, which means a NodePort is unlikely to match a service’s intended port (for example, 80 may be exposed as 30080).

Update the nginx-service yaml to use a NodePort Service.
```
sudo cat <<EOF | sudo tee ./nginx-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: NodePort
  selector:
    app: nginx-pod
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 30080
EOF
```
**What has changed is:**

1. Specified the type of service (Nodeport)
2. Specified the NodePort number to use.
![image](https://github.com/user-attachments/assets/be00e468-3965-494b-ae1b-15c5e48482e8)

**To access the service, you must**:

1. Allow the inbound traffic in your EC2's Security Group to the NodePort range 30000-32767
2. Get the public IP address of the node the Pod is running on, append the nodeport and access the app through the browser.

You must understand that the port number 30080 is a port on the node in which the Pod is scheduled to run. If the Pod ever gets rescheduled elsewhere, that the same port number will be used on the new node it is running on. So, if you have multiple Pods running on several nodes at the same time - they all will be exposed on respective nodes' IP addresses with a static port number.
![image](https://github.com/user-attachments/assets/6da717a8-e42e-4c80-8ca7-317dacf9e317)


Read some more information regarding Services in Kubernetes in [this article](https://medium.com/avmconsulting-blog/service-types-in-kubernetes-24a1587677d6).

### How Kubernetes ensures desired number of Pods is always running?

When we define a Pod manifest and appy it - we create a Pod that is running until it's terminated for some reason (e.g., error, Node reboot or some other reason), but what if we want to declare that we always need at least 3 replicas of the same Pod running at all times? Then we must use an [ResplicaSet (RS)](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/) object - it's purpose is to maintain a stable set of Pod replicas running at any given time. As such, it is often used to guarantee the availability of a specified number of identical Pods.

> **Note**: In some older books or documents you might find the old version of a similar object - [ReplicationController (RC)](https://kubernetes.io/docs/concepts/workloads/controllers/replicationcontroller/), it had similar purpose, but did not support [set-base label selectors](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#set-based-requirement) and it is now recommended to use ReplicaSets instead, since it is the next-generation RC.

Let us delete our nginx-pod Pod:
```
kubectl delete -f nginx-pod.yaml
```

Output:
![image](https://github.com/user-attachments/assets/599c89f3-9854-46e9-9465-51879eae2a85)

**Create a ReplicaSet**

Let us create a rs.yaml manifest for a ReplicaSet object:
```
cat <<EOF > rs.yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-rs
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx-pod
  template:
    metadata:
      labels:
        app: nginx-pod
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
          protocol: TCP
EOF

```
```
kubectl apply -f rs.yaml
```
![image](https://github.com/user-attachments/assets/de97f176-1fc7-458d-b7b4-4036d57c142f)


The manifest file of ReplicaSet consist of the following fields:

- **apiVersion**: This field specifies the version of kubernetes Api to which the object belongs. ReplicaSet belongs to apps/v1 apiVersion.
- **kind**: This field specify the type of object for which the manifest belongs to. Here, it is ReplicaSet.
- **metadata**: This field includes the metadata for the object. It mainly includes two fields: name and labels of the ReplicaSet.
- **spec** : This field specifies the label selector to be used to select the Pods, number of replicas of the Pod to be run and the container or list of containers which the Pod will run. In the above example, we are running 3 replicas of nginx container.

Let us check what Pods have been created:
```
kubectl get pods
```
![image](https://github.com/user-attachments/assets/1d5d59ea-6970-4c79-ad63-dbc9361fc469)

Here we see three ngix-pods with some random suffixes (e.g., -j784r) - it means, that these Pods were created and named automatically by some other object (higher level of abstraction) such as ReplicaSet.

Try to delete one of the Pods:
```
kubectl delete po nginx-rs-9ddtp
```
Output:
```
pod "nginx-pod-j784r" deleted
❯ kubectl get pods
NAME              READY   STATUS    RESTARTS   AGE
nginx-rc-7xt8z   1/1     Running   0          22s
nginx-rc-kg7v6   1/1     Running   0          34m
nginx-rc-ntbn4   1/1     Running   0          34m
```
![image](https://github.com/user-attachments/assets/23574b61-1d88-4f09-8d22-ab9a7e6a26c3)

You can see, that we still have all 3 Pods, but one has been recreated (can you differentiate the new one?)

Explore the ReplicaSet created:
```
kubectl get rs -o wide
```
Output:
```
NAME        DESIRED   CURRENT   READY   AGE   CONTAINERS   IMAGES         SELECTOR
nginx-rs   3         3         3       34m   nginx-pod    nginx:latest   app=nginx-pod
```
![image](https://github.com/user-attachments/assets/6dc12d44-f78b-41c9-a25e-72f7603c8394)

Notice, that ReplicaSet understands which Pods to create by using SELECTOR key-value pair.

### Get detailed information of a ReplicaSet

To display detailed information about any Kubernetes object, you can use 2 differen commands:

- kubectl describe %object_type% %object_name% (e.g. kubectl describe rs nginx-rs)

![image](https://github.com/user-attachments/assets/c459e45d-c678-4dba-81ec-82aaa313625b)

- kubectl get %object_type% %object_name% -o yaml (e.g. kubectl describe rs nginx-rs -o yaml)
![image](https://github.com/user-attachments/assets/9db26e86-2d03-49cc-8d8f-e792f6e7fcdc)

Try both commands in action and see the difference. Also try get with -o json instead of -o yaml and decide for yourself which output option is more readable for you.
**Get the ReplicaSet in JSON format**
```
kubectl get rs nginx-rs -o json
```
![image](https://github.com/user-attachments/assets/77084a42-8b6a-4fb0-b885-356c9b124091)

**Scale ReplicaSet up and down**:

In general, there are 2 approaches of [Kubernetes Object Management](https://kubernetes.io/docs/concepts/overview/working-with-objects/object-management/): _imperative_ and _declarative_.

Let us see how we can use both to scale our Replicaset up and down:

_**Imperative**_:

We can easily scale our ReplicaSet up by specifying the desired number of replicas in an imperative command, like this:
```
kubectl scale rs nginx-rs --replicas=5
```
```
kubectl get pods
```

![image](https://github.com/user-attachments/assets/fa3257a3-9052-44bb-94bd-29e9396b6e2c)

Scaling down will work the same way, so scale it down to 3 replicas.
```
kubectl scale rs nginx-rs --replicas=3
```
![image](https://github.com/user-attachments/assets/deb135b0-6558-49d3-a735-b008b390ddd5)


_**Declarative**_:

Declarative way would be to open our rs.yaml manifest, change desired number of replicas in respective section
```
spec:
  replicas: 3
```
![image](https://github.com/user-attachments/assets/294f54a9-7f4c-4b6f-8533-a32c36d22088)

and applying the updated manifest:
```
kubectl apply -f rs.yaml

```
There is another method - 'ad-hoc', it is definitely not the best practice and we do not recommend using it, but you can edit an existing ReplicaSet with following command:
```
kubectl edit -f rs.yaml
```
**Advanced label matching**

As Kubernetes mature as a technology, so does its features and improvements to k8s objects. ReplicationControllers do not meet certain complex business requirements when it comes to using selectors. Imagine if you need to select Pods with multiple lables that represents things like:

- **Application tier**: such as Frontend, or Backend
- **Environment**: such as Dev, SIT, QA, Preprod, or Prod

So far, we used a simple selector that just matches a key-value pair and check only 'equality':
```
  selector:
    app: nginx-pod
```
But in some cases, we want ReplicaSet to manage our existing containers that match certain criteria, we can use the same simple label matching or we can use some more complex conditions, such as:
```
 - in
 - not in
 - not equal
 - etc...
```
Let us look at the following manifest file:
```
apiVersion: apps/v1
kind: ReplicaSet
metadata: 
  name: nginx-rs
spec:
  replicas: 3
  selector:
    matchLabels:
      env: prod
    matchExpressions:
    - { key: tier, operator: In, values: [frontend] }
  template:
    metadata:
      name: nginx
      labels: 
        env: prod
        tier: frontend
    spec:
      containers:
      - name: nginx-container
        image: nginx:latest
        ports:
        - containerPort: 80
          protocol: TCP
```

In the above spec file, under the selector, matchLabels and matchExpression are used to specify the key-value pair. The matchLabel works exactly the same way as the equality-based selector, and the matchExpression is used to specify the set based selectors. This feature is the main differentiator between ReplicaSet and previously mentioned obsolete ReplicationController.

Get the replication set:
```
 kubectl get rs nginx-rs -o wide
```
```
NAME       DESIRED   CURRENT   READY   AGE     CONTAINERS        IMAGES         SELECTOR
nginx-rs   3         3         3       5m34s   nginx-container   nginx:latest   env=prod,tier in (frontend)
```
![image](https://github.com/user-attachments/assets/5b968823-286c-4faf-a559-6794d6330c7f)

### Using AWS Load Balancer to access your service in Kubernetes.

**Note**: You will only be able to test this using AWS EKS. You don not have to set this up in current project yet. In the next project, you will update your Terraform code to build an EKS cluster.

You have previously accessed the Nginx service through ClusterIP, and NodeIP, but there is another service type - [Loadbalancer](https://kubernetes.io/docs/concepts/services-networking/service/#loadbalancer). This type of service does not only create a Service object in K8s, but also provisions a real external Load Balancer (e.g. [Elastic Load Balancer - ELB](https://aws.amazon.com/elasticloadbalancing/) in AWS)


> **Since we were working locally using Minikube. now we need  to create EKS Cluster on aws and connect AWS to our local machine, please refer to this guideline:  [EKS Cluster Creation on AWS Cloud](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/blob/main/Project-22-Deploying-Applications-Into-Kubernetes-Cluster/eks-cluster-creation-on-aws.md)**

To get the experience of this service type, update your service manifest and use the LoadBalancer type. Also, ensure that the selector references the Pods in the replica set.
```
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: LoadBalancer
  selector:
    tier: frontend
  ports:
    - protocol: TCP
      port: 80 # This is the port the Loadbalancer is listening at
      targetPort: 80 # This is the port the container is listening at
```
Apply the configuration:
```
kubectl apply -f nginx-service.yaml
```
Get the newly created service :
```
kubectl get service nginx-service
```


![image](https://github.com/user-attachments/assets/3bec40df-329f-4cb2-9e41-ee8b6d5a311f)

An ELB resource will be created in your AWS console.

![image](https://github.com/user-attachments/assets/f5a6e466-3dfe-4fc3-8bda-e46c8c59aa1b)

A Kubernetes component in the control plane called  [Cloud-controller-manager](https://kubernetes.io/docs/concepts/architecture/cloud-controller/) is responsible for triggeriong this action. It connects to your specific cloud provider's (AWS) APIs and create resources such as Load balancers. It will ensure that the resource is appropriately tagged:


Get the output of the entire yaml for the service. You will some additional information about this service in which you did not define them in the yaml manifest. Kubernetes did this for you.
```
kubectl get service nginx-service -o yaml
```
![image](https://github.com/user-attachments/assets/100e98f1-007e-4e14-9708-96efaf51a3bd)

1. A clusterIP key is updated in the manifest and assigned an IP address. Even though you have specified a Loadbalancer service type, internally it still requires a clusterIP to route the external traffic through.
2. In the ports section, nodePort is still used. This is because Kubernetes still needs to use a dedicated port on the worker node to route the traffic through. Ensure that port range 30000-32767 is opened in your inbound Security Group configuration.
3. More information about the provisioned balancer is also published in the .status.loadBalancer field.
```
status:
  loadBalancer:
    ingress:
    - hostname: aabbdf1d6593740cbb6de71706d9bfb2-166682067.us-east-1.elb.amazonaws.com
```
Copy and paste the load balancer's address to the browser, and you will access the Nginx service
![image](https://github.com/user-attachments/assets/45c0fcd0-88fb-4df7-aa78-4c54b483c7b8)


**Do not Use Replication Controllers - Use Deployment Controllers Instead**

Kubernetes is loaded with a lot of features, and with its vibrant open source community, these features are constantly evolving and adding up.

Previously, you have seen the improvements from ReplicationControllers (RC), to ReplicaSets (RS). In this section you will see another K8s object which is highly recommended over Replication objects (RC and RS).

A [Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) is another layer above ReplicaSets and Pods, newer and more advanced level concept than ReplicaSets. It manages the deployment of ReplicaSets and allows for easy updating of a ReplicaSet as well as the ability to roll back to a previous version of deployment. It is declarative and can be used for rolling updates of micro-services, ensuring there is no downtime.

Officially, it is highly recommended to use Deplyments to manage replica sets rather than using replica sets directly.

**Let us see Deployment in action.**

1. Delete the ReplicaSet
 ```
 kubectl delete rs nginx-rs
 ```
2. Understand the layout of the deployment.yaml manifest below. Lets go through the 3 separated sections:
```
# Section 1 - This is the part that defines the deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    tier: frontend

# Section 2 - This is the Replica set layer controlled by the deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      tier: frontend

# Section 3 - This is the Pod section controlled by the deployment and selected by the replica set in section 2.
  template:
    metadata:
      labels:
        tier: frontend
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
```
3. Putting them altogether
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    tier: frontend
spec:
  replicas: 3
  selector:
    matchLabels:
      tier: frontend
  template:
    metadata:
      labels:
        tier: frontend
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
```
```
kubectl apply -f deployment.yaml
```
![image](https://github.com/user-attachments/assets/3908a9f2-2535-4960-8744-e23d6f630c97)

**Run commands to get the following**

1. Get the Deployment
```
kubectl get deployments
```
![image](https://github.com/user-attachments/assets/cf03cf47-cc52-4d64-8eaa-df4727840eb8)

2. Get the ReplicaSet
```
kubectl get rs
```
3. Get the Pods
```
kubectl get pods
```
![image](https://github.com/user-attachments/assets/9f0395b3-c6cc-4f57-ac80-3d823d732310)

4. Scale the replicas in the Deployment to 15 Pods
```
kubectl scale deployment/nginx-deployment --replicas=15
```
```
kubectl get pods

```
![image](https://github.com/user-attachments/assets/443fc89e-5b5d-42d6-b00e-e49782e2092f)

5. Exec into one of the Pod's container to run Linux commands
```
kubectl exec -it nginx-deployment-75b7745567-4tg6p -- bash
```
List `ls -ltr /etc/nginx/` the files and folders in the Nginx directory
![image](https://github.com/user-attachments/assets/2c0e6e3b-55c1-406f-80cf-19abb7f9f7b5)

Check the content of the default Nginx configuration file `cat  /etc/nginx/conf.d/default.conf `
![image](https://github.com/user-attachments/assets/0737c7a7-1107-41a1-9641-4771513689e3)

Now, as we have got acquainted with most common Kubernetes workloads to deploy applications:
![image](https://github.com/user-attachments/assets/9e1567cc-8bd0-4092-acbd-f0555b6669b9)


it is time to explore how Kubernetes is able to manage persistent data.

### Persisting Data for Pods
Deployments are stateless by design. Hence, any data stored inside the Pod's container does not persist when the Pod dies.

If you were to update the content of the index.html file inside the container, and the Pod dies, that content will be lost since a new Pod will replace the dead one.

Let us try that:

1. Scale the Pods down to 1 replica
```
kubectl scale deployment nginx-deployment --replicas=1
```
2. Exec into the running container (figure out the command yourself)
```
kubectl exec -it nginx-deployment-75b7745567-mjqk2 -- bash
```
3. Install vim so that you can edit the file
```
apt-get update
apt-get install vim
```
![image](https://github.com/user-attachments/assets/6d0aacfe-c7ed-4711-b9c6-cfb847585664)

4. Update the content of the file and add the code below /usr/share/nginx/html/index.html
```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to STEGHUB.COM!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to STEGHUB.COM!</h1>
<p>I love experiencing Kubernetes</p>

<p>Learning by doing is absolutely the best strategy at 
<a href="https://steghub.com/">www.steghub.com</a>.<br/>
for skills acquisition
<a href="https://steghub.com/">www.steghub.com</a>.</p>

<p><em>Thank you for learning from STEGHUB.COM</em></p>
</body>
</html>
```
![image](https://github.com/user-attachments/assets/b014a960-d46b-443e-b843-bb1c02792e1b)

5. Check the browser - You should see this
![image](https://github.com/user-attachments/assets/eccfecdf-4ec1-47e0-9aed-7bbd0e4eeeae)

6. Now, delete the only running Pod so that a new one is automatically recreated.
![image](https://github.com/user-attachments/assets/069f5996-6e85-4423-a542-64eff96316fd)

7. Refresh the web page - You will see that the content you saved in the container is no longer there.
![image](https://github.com/user-attachments/assets/784dc4b4-fc0b-4a18-8d29-243f171a7baf)

 That is because Pods do not store data when they are being recreated - that is why they are called ephemeral or stateless. (But not to worry, we will address this with persistent volumes in the next project)

Storage is a critical part of running containers, and Kubernetes offers some powerful primitives for managing it. Dynamic volume provisioning, a feature unique to Kubernetes, which allows storage volumes to be created on-demand. Without dynamic provisioning, DevOps engineers must manually make calls to the cloud or storage provider to create new storage volumes, and then create PersistentVolume objects to represent them in Kubernetes. The dynamic provisioning feature eliminates the need for DevOps to pre-provision storage. Instead, it automatically provisions storage when it is requested by users.

To make the data persist in case of a Pod's failure, you will need to configure the Pod to use Volumes:

**Clean up the deployment**
```
kubectl delete deployment nginx-deployment
```
![image](https://github.com/user-attachments/assets/c1098c60-8e11-4056-9eb2-8f0705a8a700)

### The End of Project 22 Deploying Applications Into Kubernetes Cluster
In the next project,
You will understand how persistence work in Kubernetes using Volumes.

You will use eksctl to create a Kubernetes EKS cluster in AWS, and begin to use some powerful features such as PV, PVCs, ConfigMaps.

Experience Dynamic provisioning of volumes to make your Pods stateful, using Kubernetes Statefulset

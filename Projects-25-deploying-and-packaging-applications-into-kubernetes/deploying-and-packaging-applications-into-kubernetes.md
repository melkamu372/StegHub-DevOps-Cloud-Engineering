### Deploying and Packaging Applications in to kubernetes 101


_DEPLOYING AND PACKAGING APPLICATIONS INTO KUBERNETES WITH HELM_


In the previous project, you started experiencing helm as a tool used to deploy an application into Kubernetes. You probably also 
tried installing more tools apart from Jenkins.

In this project, you will experience deploying more DevOps tools, get familiar with some of the real world issues faced during such
deployments and how to fix them. You will learn how to tweak helm values files to automate the configuration of the applications 
you deploy. Finally, once you have most of the DevOps tools deployed, you will experience using them and relate with the DevOps 
cycle and how they fit into the entire ecosystem.

**Our focus will be on the following tools.**

1. Artifactory
2. Hashicorp Vault
3. Prometheus
4. Grafana
5. Elasticsearch ELK using [ECK](https://www.elastic.co/guide/en/cloud-on-k8s/current/k8s-install-helm.html)


For the tools that require paid license, don’t worry, you will also learn how to get the license for free and have true experience
exactly how they are used in the real world.

**Lets start first with Artifactory.**

 _What is it exactly?_

Artifactory is part of a suit of products from a company called [Jfrog](https://jfrog.com/). Jfrog started out as an artifact repository
where software binaries in different formats are stored. Today, Jfrog has transitioned from an artifact repository to a DevOps 
Platform that includes CI and CD capabilities. This has been achieved by offering more products in which Jfrog Artifactory is part of.
Other offerings include

- JFrog Pipelines – a CI-CD product that works well with its Artifactory repository. Think of this product as an alternative to Jenkins.
- JFrog Xray – a security product that can be built-into various steps within a JFrog pipeline. Its job is to scan for security 
vulnerabilities in the stored artifacts. It is able to scan all dependent code.

In this project, the requirement is to use Jfrog Artifactory as a private registry for the organisation’s Docker images and Helm 
charts. This requirement will satisfy part of the company’s corporate security policies to never download artifacts directly from 
the public into production systems. We will eventually have a CI pipeline that initially pulls public docker images and helm charts 
from the internet, store in artifactory and scan the artifacts for security vulnerabilities before deploying into the corporate
infrastructure. Any found vulnerabilities will immediately trigger an action to quarantine such artifacts.

Lets get into action and see how all of these work.

Deploy Jfrog Artifactory into Kubernetes

The best approach to easily get Artifactory into kubernetes is to use helm.

1. Search for an official helm chart for Artifactory on [Artifact Hub](https://artifacthub.io/)

![image](https://github.com/user-attachments/assets/c4fa6c89-ef67-4a96-8791-bb47715ae24b)


2. Click on See all results
3. Use the filter checkbox on the left to limit the return data. As you can see in the image below, "Helm" is selected. In some cases, 
you might select "Official". Then click on the first option from the result.

![image](https://github.com/user-attachments/assets/8f66f90d-7202-4343-ab3a-e583d7768d47)


4. Review the Artifactory page

![image](https://github.com/user-attachments/assets/b56a2d93-e542-4ca8-a0fd-77357ad2732d)


5. Click on the install menu on the right to see the installation commands.

![image](https://github.com/user-attachments/assets/6c8144d4-e8c3-4af4-bc37-d4ea081e5409)



6. Add the jfrog remote repository on your laptop/computer

```
helm repo add jfrog https://charts.jfrog.io
```

7. Create a namespace called tools where all the tools for DevOps will be deployed. (In previous project, you installed Jenkins in
 the default namespace. You should uninstall Jenkins there and install in the new namespace)
 
 ```
 kubectl create ns tools
 ```
 
 8. Update the helm repo index on your laptop/computer

```
helm repo update
```

9. Install artifactory

```
helm upgrade --install artifactory jfrog/artifactory --version 107.90.10 -n tools
```
![image](https://github.com/user-attachments/assets/74fa20b4-b405-4035-b43b-4bc7e67268e2)

![image](https://github.com/user-attachments/assets/90eba64c-5ad3-4b37-a306-c670563557ce)



**NOTE:**

- We have used upgrade --install flag here instead of helm install artifactory jfrog/artifactory This is a better practice, especially
when developing CI pipelines for helm deployments. It ensures that helm does an upgrade if there is an existing installation.
But if there isn’t, it does the initial install. With this strategy, the command will never fail. It will be smart enough to 
determine if an upgrade or fresh installation is required.

- The helm chart version to install is very important to specify. So, the version at the time of writing may be different from what 
you will see from Artifact Hub. So, replace the version number to the desired. You can see all the versions by clicking on "see all"
as shown in the image below.
![image](https://github.com/user-attachments/assets/ac625e1a-f59d-42c2-8074-34948956c654)

![image](https://github.com/user-attachments/assets/7c4dee32-2d0b-447e-9389-18687d8f3c61)


The output from the installation already gives some Next step directives.

Getting the Artifactory URL
Lets break down the first Next Step.

1. The artifactory helm chart comes bundled with the Artifactory software, a PostgreSQL database and an Nginx proxy which it uses 
to configure routes to the different capabilities of Artifactory. Getting the pods after some time, you should see something like 
the below.

```
kubectl get po -n tools
```
![image](https://github.com/user-attachments/assets/3fd4e6e5-0f5e-4590-8a20-8d9872befe5f)



2. Each of the deployed application have their respective services. This is how you will be able to reach either of them.

```
kubectl get svc -n tools
```
![image](https://github.com/user-attachments/assets/67340e60-039b-44b1-9224-6b4ff69dc454)



3. Notice that, the Nginx Proxy has been configured to use the service type of LoadBalancer. Therefore, to reach Artifactory, we will
need to go through the Nginx proxy’s service. Which happens to be a load balancer created in the cloud provider. Run the kubectl
command to retrieve the Load Balancer URL.
```
 kubectl get svc artifactory-artifactory-nginx -n tools
 ```
![image](https://github.com/user-attachments/assets/94723f0a-87cf-4b28-af80-2b297e40f189)

 4. Copy the URL and paste in the browser

![image](https://github.com/user-attachments/assets/526d8dc9-cdb9-4587-ba81-d4c10a421aa2)



5. The default username is admin

6. The default password is password


![image](https://github.com/user-attachments/assets/efebdc87-9453-4b6b-9d53-537c7909468e)



## How the Nginx URL for Artifactory is configured in Kubernetes
Without clicking further on the Get Started page, lets dig a bit more into Kubernetes and Helm. How did Helm configure the URL in
kubernetes?

Helm uses the values.yaml file to set every single configuration that the chart has the capability to configure. THe best place to 
get started with an off the shelve chart from artifacthub.io is to get familiar with the DEFAULT VALUES

- click on the DEFAULT VALUES section on Artifact hub

![image](https://github.com/user-attachments/assets/f461b5e5-2064-4a38-8ab2-c963038d098f)


- Here you can search for key and value pairs

![image](https://github.com/user-attachments/assets/834570da-d500-4410-89d7-e4660a654f71)


- For example, when you type nginx in the search bar, it shows all the configured options for the nginx proxy.


![image](https://github.com/user-attachments/assets/3ef1576e-cd25-454b-980e-6f43d77a80ee)



- selecting nginx.enabled from the list will take you directly to the configuration in the YAML file.


![image](https://github.com/user-attachments/assets/d8b776be-04e6-4186-b62d-fbab61db5ec1)


- Search for nginx.service and select nginx.service.type

![image](https://github.com/user-attachments/assets/ce7afa97-d502-4638-af9d-dcae88672574)



- You will see the confired type of Kubernetes service for Nginx. As you can see, it is LoadBalancer by default


![image](https://github.com/user-attachments/assets/69baa45d-f915-4317-a4fb-daaab2b6669f)


- To work directly with the values.yaml file, you can download the file locally by clicking on the download icon.


![image](https://github.com/user-attachments/assets/c53d7936-210c-413b-951a-2339b3bfc182)



**Is the Load Balancer Service type the Ideal configuration option to use in the Real World?**


Setting the service type to Load Balancer is the easiest way to get started with exposing applications running in kubernetes
externally. But provissioning load balancers for each application can become very expensive over time, and more difficult to manage.
Especially when tens or even hundreds of applications are deployed.

The best approach is to use [Kubernetes Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/) instead. But to do
that, we will have to deploy an [Ingress Controller](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/).

A huge benefit of using the ingress controller is that we will be able to use a single load balancer for different applications we 
deploy. Therefore, Artifactory and any other tools can reuse the same load balancer. Which reduces cloud cost, and overhead of
managing multiple load balancers. more on that later.

For now, we will leave artifactory, move on to the next phase of configuration (Ingress, DNS(Route53) and Cert Manager), and then 
return to Artifactory to complete the setup so that it can serve as a private docker registry and repository for private helm charts.

# DEPLOYING INGRESS CONTROLLER AND MANAGING INGRESS RESOURCES
Before we discuss what ingress controllers are, it will be important to start off understanding about the Ingress resource.

An ingress is an API object that manages external access to the services in a kubernetes cluster. It is capable to provide load 
balancing, SSL termination and name-based virtual hosting. In otherwords, Ingress exposes HTTP and HTTPS routes from outside the 
cluster to services within the cluster. Traffic routing is controlled by rules defined on the Ingress resource.

Here is a simple example where an Ingress sends all its traffic to one Service:

![9000](https://user-images.githubusercontent.com/85270361/210277374-d600cbfd-fd4b-4f23-accd-73e74e03544e.PNG)

image credit  `kubernetes.io`

An ingress resource for Artifactory would like like below

```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: artifactory
spec:
  ingressClassName: nginx
  rules:
  - host: "tooling.artifactory.sandbox.svc.darey.io"
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: artifactory
            port:
              number: 8082
```


- An Ingress needs apiVersion, kind, metadata and spec fields
- The name of an Ingress object must be a valid DNS subdomain name
- Ingress frequently uses annotations to configure some options depending on the Ingress controller.
- Different Ingress controllers support different annotations. Therefore it is important to be up to date with the ingress 
controller’s specific documentation to know what annotations are supported.
- It is recommended to always specify the ingress class name with the spec ingressClassName: nginx. This is how the Ingress 
controller is selected, especially when there are multiple configured ingress controllers in the cluster.
- The domain total.com should be replaced with your own domain.


## Ingress controller

If you deploy the yaml configuration specified for the ingress resource without an ingress controller, it will not work. In order 
for the Ingress resource to work, the cluster must have an ingress controller running.

Unlike other types of controllers which run as part of the kube-controller-manager. Such as the Node Controller, Replica Controller,
Deployment Controller, Job Controller, or Cloud Controller. Ingress controllers are not started automatically with the cluster.

Kubernetes as a project supports and maintains AWS, [GCE](https://github.com/kubernetes/ingress-gce/blob/master/README.md#readme), and 
[NGINX](https://github.com/kubernetes/ingress-nginx/blob/main/README.md#readme) ingress controllers.

There are many other 3rd party Ingress controllers that provide similar functionalities with their own unique features, but the 3 
mentioned earlier are currently supported and maintained by Kubernetes. Some of these other 3rd party Ingress controllers include 
but not limited to the following


- [AKS Application Gateway Ingress Controller](https://learn.microsoft.com/en-gb/azure/application-gateway/tutorial-ingress-controller-add-on-existing) (Microsoft Azure)
- [Istio](https://istio.io/latest/docs/tasks/traffic-management/ingress/kubernetes-ingress/)
- [Traefik](https://doc.traefik.io/traefik/providers/kubernetes-ingress/)
- [Ambassador](https://www.getambassador.io/)
- [HA Proxy Ingress](https://haproxy-ingress.github.io/)
- [Kong](https://docs.konghq.com/kubernetes-ingress-controller/latest/)
- [Gloo](https://docs.solo.io/gloo-edge/latest/)


An example comparison matrix of some of the controllers can be found [here](https://kubevious.io/blog/post/comparing-top-ingress-controllers-for-kubernetes#comparison-matrix).
Understanding their unique features will help businesses determine which product works well for their respective requirements.

It is possible to deploy any number of ingress controllers in the same cluster. That is the essence of an ingress class. By specifying
the spec ingressClassName field on the ingress object, the appropriate ingress controller will be used by the ingress resource.

Lets get into action and see how all of these fits together.

**Deploy Nginx Ingress Controller**

On this project, we will deploy and use the **Nginx Ingress Controller**. It is always the default choice when starting with Kubernetes 
projects. It is reliable and easy to use.

Since this controller is maintained by Kubernetes, there is an [official guide](https://kubernetes.github.io/ingress-nginx/deploy/) 
the installation process. Hence, we wont be using artifacthub.io here. Even though you can still find ready to go charts there, 
it just makes sense to always use the official guide in this scenario.

Using the Helm approach, according to the official guide;

1. Install Nginx Ingress Controller in the ingress-nginx namespace

```
helm upgrade --install ingress-nginx ingress-nginx \
--repo https://kubernetes.github.io/ingress-nginx \
--namespace ingress-nginx --create-namespace
```
![image](https://github.com/user-attachments/assets/4d6db2ee-ca33-4501-bf48-e02f9e340322)


Notice:

This command is idempotent:

- if the ingress controller is not installed, it will install it,

- if the ingress controller is already installed, it will upgrade it.

- Self Challenge Task – Delete the installation after running above command. Then try to re-install it using a slightly different 
method you are already familiar with. Ensure NOT to use the flag --repo

- Hint – Run the helm repo add command before installation


2. A few pods should start in the ingress-nginx namespace:

```
kubectl get pods --namespace=ingress-nginx
```
![image](https://github.com/user-attachments/assets/3b0bd45f-f872-467c-b4ae-39d9cd458e65)

3. After a while, they should all be running. The following command will wait for the ingress controller pod to be up, running, 
and ready:


```
kubectl wait --namespace ingress-nginx \
  --for=condition=ready pod \
  --selector=app.kubernetes.io/component=controller \
  --timeout=120s
````

4. Check to see the created load balancer in AWS

```
kubectl get service -n ingress-nginx
```
![image](https://github.com/user-attachments/assets/29559dc7-1d18-4c23-a4ea-e9031590bd70)



The ingress-nginx-controller service that was created is of the type LoadBalancer. That will be the load balancer to be used by all
applications which require external access, and is using this ingress controller.

If you go ahead to AWS console, copy the address in the **EXTERNAL-IP** column, and search for the loadbalancer, you will see an 
output like below.

![image](https://github.com/user-attachments/assets/8e7e4d19-9bbd-4110-b803-bb669ce04885)

5. Check the IngressClass that identifies this ingress controller.

```
kubectl get ingressclass -n ingress-nginx
```

**Output:**
![image](https://github.com/user-attachments/assets/6c8dcc31-d93c-4f7f-84b0-d0aa5a9cf30a)


## Deploy Artifactory Ingress
Now, it is time to configure the ingress so that we can route traffic to the Artifactory internal service, through the ingress
controller’s load balancer.

Notice the spec section with the configuration that selects the ingress controller using the ingressClassName


```
cat <<EOF > myartifactory.yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: artifactory
spec:
  ingressClassName: nginx
  rules:
  - host: "tooling-artifactory-melkamu.dns-dynamic.net"
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: artifactory
            port:
              number: 8082
EOF
```


```
kubectl apply -f myartifactory.yaml -n tools
```
```
kubectl get ingress -n tools
```

**OUTPUT:**

![image](https://github.com/user-attachments/assets/d7a647cb-6b23-41e8-b500-36754282bac6)

Now, take note of

- CLASS – The nginx controller class name nginx
- HOSTS – The hostname to be used in the browser tooling.artifactory.sandbox.svc.darey.io
- ADDRESS – The loadbalancer address that was created by the ingress controller


**Configure DNS**
If anyone were to visit the tool, it would be very inconvenient sharing the long load balancer address. Ideally, you would create a
DNS record that is human readable and can direct request to the balancer. This is exactly what has been configured in the ingress 
object - host: "tooling.artifactory.sandbox.svc.total.com" but without a DNS record, there is no way that host address can reach the
load balancer.


The sandbox.svc.total.com part of the domain is the configured HOSTED ZONE in AWS. So you will need to configure Hosted Zone in 
AWS console or as part of your infrastructure as code using terraform.

![image](https://github.com/user-attachments/assets/659b5283-2332-4af1-944b-28b1946090c2)

If you purchased the domain directly from AWS, the hosted zone will be automatically configured for you. But if your domain is registered with a different provider
such as freenon or namechaep, you will have to create the hosted zone and update the name servers.

**Create Route53 record**

Within the hosted zone is where all the necessary DNS records will be created. Since we are working on Artifactory, lets create the
record to point to the ingress controller’s loadbalancer. There are 2 options. You can either use the CNAME or AWS Alias

**CNAME Method**

1. Select the HOSTED ZONE you wish to use, and click on the create record button


2. Add the subdomain tooling.artifactory, and select the record type CNAME

3. Successfully created record

4. Confirm that the DNS record has been properly propergated. Visit https://dnschecker.org and check the record. Ensure to select CNAME.
 The search should return green ticks for each of the locations on the left.



**AWS Alias Method**

1. In the create record section, type in the record name, and toggle the alias button to enable an alias. An alias is of the A DNS record type which basically 
routes directly to the load balancer. In the choose endpoint bar, select Alias to Application and Classic Load Balancer


2. Select the region and the load balancer required. You will not need to type in the load balancer, as it will already populate.


For detailed read on selecting between CNAME and Alias based records, read the [official documentation](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resource-record-sets-choosing-alias-non-alias.html)

## Visiting the application from the browser

So far, we now have an application running in Kubernetes that is also accessible externally. That means if you navigate to
https://tooling.artifactory.sandbox.svc.total.com/ (replace the full URL with your domain), it should load up the artifactory application.

Using Chrome browser will show something like the below. It shows that the site is indeed reachable, but insecure. This is because 
Chrome browsers do not load insecure sites by default. It is insecure because it either does not have a trusted TLS/SSL certificate, 
or it doesn’t have any at all.


Nginx Ingress Controller does configure a default TLS/SSL certificate. But it is not trusted because it is a self signed certificate 
that browsers are not aware of.

To confirm this,


1. Click on the Not Secure part of the browser.

![9003](https://user-images.githubusercontent.com/85270361/210280903-7ec693a0-1935-48f5-94e6-e99d49364bad.PNG)


2. Select the Certificate is not valid menu

![9004](https://user-images.githubusercontent.com/85270361/210280896-1ce01c51-05eb-422a-bd80-5cd82b7d7f2e.PNG)


3. You will see the details of the certificate. There you can confirm that yes indeed there is encryption configured for the 
traffic, the browser is just not cool with it.

![9005](https://user-images.githubusercontent.com/85270361/210281128-cb3b6a5d-2024-4089-adb5-1da35c6e2952.PNG)


Now try another browser. For example Internet explorer or Safari


![9006](https://user-images.githubusercontent.com/85270361/210281333-fedf21b1-11bb-42ec-b8e4-0c77985906ed.PNG)


## Explore Artifactory Web UI

Now that we can access the application externally, although insecure, its time to login for some exploration. Afterwards we will 
make it a lot more secure and access our web application on any browser.

1. Get the default username and password – Run a helm command to output the same message after the initial install

```
helm test artifactory -n tools
```


**Output:**

```
NAME: artifactory
LAST DEPLOYED: Sat May 28 09:26:08 2022
NAMESPACE: tools
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
Congratulations. You have just deployed JFrog Artifactory!

1. Get the Artifactory URL by running these commands:

   NOTE: It may take a few minutes for the LoadBalancer IP to be available.
         You can watch the status of the service by running 'kubectl get svc --namespace tools -w artifactory-artifactory-nginx'
   export SERVICE_IP=$(kubectl get svc --namespace tools artifactory-artifactory-nginx -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
   echo http://$SERVICE_IP/

2. Open Artifactory in your browser
   Default credential for Artifactory:
   user: admin
   password: password
```


2. Insert the username and password to load the Get Started page


![9010](https://user-images.githubusercontent.com/85270361/210281586-c88bcee2-b153-4341-a565-d1e10c36d55d.PNG)


3. Reset the admin password


![9011](https://user-images.githubusercontent.com/85270361/210281683-c5871e66-35b5-4cfb-b389-85c30a3e87fb.PNG)


4. Activate the Artifactory License. You will need to purchase a license to use Artifactory enterprise features.


![9012](https://user-images.githubusercontent.com/85270361/210281798-41cfe86c-fa33-4d75-b3a3-21dee0aaff5f.PNG)

5. For learning purposes, you can apply for a free trial license. Simply fill the form [here](https://jfrog.com/start-free/) and 
a license key will be delivered to your email in few minutes.
 
![9013](https://user-images.githubusercontent.com/85270361/210281988-18397533-9823-446b-b0da-039d2d79682b.PNG)

![9014](https://user-images.githubusercontent.com/85270361/210282029-fcb0857d-cdef-48b1-837b-0f490ffcb1ab.PNG)


6. In exactly 1 minute, the license key had arrived. Simply copy the key and apply to the console.


![9015](https://user-images.githubusercontent.com/85270361/210282185-d34c2056-d801-4d43-bf52-311d9bed3309.PNG)

7. Set the Base URL. Ensure to use https

 
![9016](https://user-images.githubusercontent.com/85270361/210282433-13c086bf-6cf0-46a2-9891-75db24df596f.PNG)
 
   
8. Skip the Proxy setting
   
   
![9017](https://user-images.githubusercontent.com/85270361/210282457-f7abe704-96be-4c8e-82b6-e87e228e534d.PNG)
  
   
9. Skip creation of repositories for now. You will create them yourself later on.
   
   
![9018](https://user-images.githubusercontent.com/85270361/210282492-0b6a1466-1944-4780-a6a2-1c04c2945b70.PNG)
 
   
10. finish the setup
   
![9019](https://user-images.githubusercontent.com/85270361/210282528-b8364875-390c-41dd-a1a4-a8aeb2bf07b0.PNG)
 
![9020](https://user-images.githubusercontent.com/85270361/210282537-47a447d8-089f-4524-9e7a-49a0003bffd6.PNG)
  
   
 Next, its time to fix the TLS/SSL configuration so that we will have a trusted HTTPS URL

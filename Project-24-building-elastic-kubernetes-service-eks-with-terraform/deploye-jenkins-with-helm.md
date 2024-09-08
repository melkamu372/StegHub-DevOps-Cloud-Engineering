### DEPLOY JENKINS WITH HELM

Before we begin to develop our own helm charts, lets make use of publicly available charts to deploy all the tools that we need.

One of the amazing things about helm is the fact that you can deploy applications that are already packaged from a public helm repository directly with very minimal configuration. An example is Jenkins.

1. Visit [Artifact Hub](https://artifacthub.io/packages/search) to find packaged applications as Helm Charts

2. Search for Jenkins
![image](https://github.com/user-attachments/assets/67c7d788-9472-40a4-8db1-39ec52cafaa1)

3. Add the repository to helm so that you can easily download and deploy

```
helm repo add jenkins https://charts.jenkins.io
```

4. Update helm repo

```
helm repo update 
```
![image](https://github.com/user-attachments/assets/f55cff49-0355-4fb2-af72-610aaa98939d)


5. Install the chart

```
helm install [RELEASE_NAME] jenkins/jenkins --kubeconfig [kubeconfig file]
```
```
helm install jenkins-server jenkins/jenkins --kubeconfig kubeconfig
```
You should see an output like this

![image](https://github.com/user-attachments/assets/57174a7c-486d-4ef8-8be1-f301d0fa6d9a)

6. Check the Helm deployment

```
helm ls --kubeconfig [kubeconfig file]
```
![image](https://github.com/user-attachments/assets/39590312-0660-485a-9a65-cdaa7ec74148)

7. Check the pods

```
kubectl get pods --kubeconfigo [kubeconfig file]
```
**Output:**
![image](https://github.com/user-attachments/assets/5c010885-fb42-43a6-9b75-82a21ada9777)

8. Describe the running pod (review the output and try to understand what you see)

```
kubectl describe pod jenkins-0 --kubeconfig [kubeconfig file]
```
![image](https://github.com/user-attachments/assets/b9c26b3e-fb15-44ea-b6ac-2b55827187b4)
![image](https://github.com/user-attachments/assets/fb16cbf6-6c09-419f-9ec9-2e6cd8996a22)
9. Check the logs of the running pod

```
kubectl logs jenkins-0 --kubeconfig [kubeconfig file]
```

You will notice an output with an error

```
error: a container name must be specified for pod jenkins-0, choose one of: [jenkins config-reload] or one of the init containers: [init]
```

This is because the pod has a [Sidecar container](https://www.weave.works/blog/container-design-patterns-for-kubernetes/) alongside
with the Jenkins container. As you can see fromt he error output, there is a list of containers inside the pod [jenkins config-reload] i.e jenkins and config-reload containers. The job of the config-reload is mainly to help Jenkins to reload its configuration without recreating the pod.

Therefore we need to let kubectl know, which pod we are interested to see its log. Hence, the command will be updated like:

```
kubectl logs jenkins-0 -c jenkins --kubeconfig [kubeconfig file]
```

Now lets avoid calling the [kubeconfig file] everytime. Kubectl expects to find the default kubeconfig file in the location 
~/.kube/config. But what if you already have another cluster using that same file? It doesn’t make sense to overwrite it. What you will do is to merge all the kubeconfig files together using a kubectl plugin called [konfig](https://github.com/corneliusweig/konfig)
and select whichever one you need to be active.

1. Install a package manager for kubectl called [krew](https://krew.sigs.k8s.io/docs/user-guide/setup/install/) so that it will enable you to install plugins to extend the functionality of kubectl. Read more about it [Here](https://github.com/kubernetes-sigs/krew)
 

 2. Install the [konfig plugin](https://github.com/corneliusweig/konfig)
 
 ```
 kubectl krew install konfig
 ```
 
 3. Import the kubeconfig into the default kubeconfig file. Ensure to accept the prompt to overide.

```
sudo kubectl konfig import --save  [kubeconfig file]
```

4. Show all the contexts – Meaning all the clusters configured in your kubeconfig. If you have more than 1 Kubernetes clusters configured, you will see them all in the output.

```
 kubectl config get-contexts
```

5. Set the current context to use for all kubectl and helm commands

```
kubectl config use-context [name of EKS cluster]
```

6. Test that it is working without specifying the --kubeconfig flag

```
kubectl get po
```



**Output:**
```
NAME        READY   STATUS    RESTARTS   AGE
  jenkins-0   2/2     Running   0          84m
```

7. Display the current context. This will let you know the context in which you are using to interact with Kubernetes.

```
 kubectl config current-context
```


 Now that we can use kubectl without the --kubeconfig flag, Lets get access to the Jenkins UI. (In later projects we will further configure Jenkins. For now, it is to set up all the tools we need)
 
 1. There are some commands that was provided on the screen when Jenkins was installed with Helm. See number 5 above. Get the password to the admin user
 
 ```
 kubectl exec --namespace default -it svc/jenkins -c jenkins -- /bin/cat /run/secrets/chart-admin-password && echo
 ```
 
 2. Use port forwarding to access Jenkins from the UI

```
kubectl --namespace default port-forward svc/jenkins 8080:8080
```


3. Go to the browser localhost:8080 and authenticate with the username and password from number 1 above


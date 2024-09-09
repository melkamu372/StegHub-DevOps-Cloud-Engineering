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
![image](https://github.com/user-attachments/assets/29b557cf-fe76-4ba5-a772-0a312e6d8c05)



5. Install the chart

```
helm install [RELEASE_NAME] jenkins/jenkins --kubeconfig [kubeconfig file]
```
```
helm install jenkins-server jenkins/jenkins --kubeconfig kubeconfig
```
You should see an output like this

![image](https://github.com/user-attachments/assets/13112322-02f5-4390-8f56-0b1a06f30a20)

6. Check the Helm deployment

```
helm ls --kubeconfig [kubeconfig file]
```
![image](https://github.com/user-attachments/assets/abac61cf-2ab8-4d22-95a9-6cc2df4b2d9e)


7. Check the pods

```
kubectl get pods --kubeconfigo kubeconfig
```
**Output:**

![image](https://github.com/user-attachments/assets/3e97529b-dc73-4511-8c48-77cb2d94ab5f)

8. Describe the running pod (review the output and try to understand what you see)

```
kubectl describe pod jenkins-server-0 --kubeconfig kubeconfig 
```
![image](https://github.com/user-attachments/assets/e8f2ad6e-b2a5-44d8-8cfd-6dcc18a5ea94)

![image](https://github.com/user-attachments/assets/364de1a4-fb4a-4e13-8ad2-2c57ccc9ddad)

9. Check the logs of the running pod

```
kubectl logs jenkins-server-0 --kubeconfig kubeconfig
```

![image](https://github.com/user-attachments/assets/7a956819-339b-4b47-a23f-5d88c8c64e7f)

You will notice an output with an error
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

 Krew is a plugin manager for kubectl. First, you need to install it. Open a terminal and run the following command:

```
(
  set -o errexit
  set -o nounset
  cd "$(mktemp -d)"
  OS="$(uname | tr '[:upper:]' '[:lower:]')"
  ARCH="$(uname -m | sed -e 's/x86_64/amd64/' -e 's/aarch64/arm64/' -e 's/arm.*/arm/')" 
  KREW="krew-${OS}_${ARCH}"
  curl -fsSLO "https://github.com/kubernetes-sigs/krew/releases/latest/download/${KREW}.tar.gz"
  tar zxvf "${KREW}.tar.gz"
  ./"${KREW}" install krew
)

```
 ![image](https://github.com/user-attachments/assets/ef86adaa-7bb6-4495-9a6f-c52ce488a6f9)

**Update Your Shell Configuration**
```
export PATH="${KREW_ROOT:-$HOME/.krew}/bin:$PATH"

```

 2. Install the [konfig plugin](https://github.com/corneliusweig/konfig)
 
 ```
 kubectl krew install konfig
 ```
 ![image](https://github.com/user-attachments/assets/4358c085-4723-4a2e-96fa-fe1aaf73f1e4)

 3. Import the kubeconfig into the default kubeconfig file. Ensure to accept the prompt to overide.

```
sudo kubectl konfig import --save  kubeconfig
```

4. Show all the contexts – Meaning all the clusters configured in your kubeconfig. If you have more than 1 Kubernetes clusters configured, you will see them all in the output.

```
 kubectl config get-contexts
```
![image](https://github.com/user-attachments/assets/4a898c2e-dc4f-4e23-8de8-a3f9392f2c36)

5. Set the current context to use for all kubectl and helm commands

```
kubectl config use-context arn:aws:eks:us-east-1:736498736845:cluster/tooling-app-eks
```

6. Test that it is working without specifying the --kubeconfig flag

```
kubectl get po
```
**Output:**
![image](https://github.com/user-attachments/assets/3a876550-e93f-4e46-a0af-c2c6842987af)

7. Display the current context. This will let you know the context in which you are using to interact with Kubernetes.

```
 kubectl config current-context
```
![image](https://github.com/user-attachments/assets/ca11820a-0fe7-4858-bdee-93558289a903)


 Now that we can use kubectl without the --kubeconfig flag, Lets get access to the Jenkins UI. (In later projects we will further configure Jenkins. For now, it is to set up all the tools we need)
 
 1. There are some commands that was provided on the screen when Jenkins was installed with Helm. See number 5 above. Get the password to the admin user
 
 ```
kubectl exec --namespace default -it svc/jenkins-server -c jenkins -- /bin/cat /run/secrets/additional/chart-admin-password && echo
 ```
 ![image](https://github.com/user-attachments/assets/1b121b70-20b3-45b6-92d3-ce28ed130da9)

 2. Use port forwarding to access Jenkins from the UI

```
kubectl --namespace default port-forward svc/jenkins-server 8082:8080
```
![image](https://github.com/user-attachments/assets/96b04fb4-5094-4a3d-85b6-2e22506072dd)


3. Go to the browser localhost:8082 and authenticate with the username and password from number 1 above
![image](https://github.com/user-attachments/assets/967c116d-837f-41cc-802a-34ca66b368fb)


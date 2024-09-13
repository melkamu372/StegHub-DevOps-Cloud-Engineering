### QUICK TASK FOR YOU

Now setup the following tools using Helm

This section will be quite challenging for you because you will need to spend some time to research the charts, read their 
documentations and understand how to get an application running in your cluster by simply running a helm install command.

1. Artifactory

**Step 1: Add the JFrog Helm Repository**

```
helm repo add jfrog https://charts.jfrog.io
```

```
helm repo update
```

![image](https://github.com/user-attachments/assets/8139a915-0088-4dab-8303-bab1b70fa97e)

**Step 2 install the chart with the release name artifactory**

```
helm upgrade --install artifactory --namespace default jfrog/artifactory
```
![image](https://github.com/user-attachments/assets/f9d05414-ecb8-4ad8-b32e-3324e553261b)

**Step 3: Verify Installation**
Check the status of the Artifactory deployment:
```
helm status artifactory --namespace artifactory
```
![image](https://github.com/user-attachments/assets/63dbf3db-e035-4191-ab4f-35332ddc7993)

**Step 4: Access Artifactory**

Find the service URL:
```
kubectl get svc --namespace artifactory
```
![image](https://github.com/user-attachments/assets/35f1ec6a-5ee8-460e-ab05-adc18e029825)

2. Hashicorp Vault

HashiCorp Vault is a tool for managing secrets and protecting sensitive data.

**Step 1: Add the HashiCorp Helm Repository**
```
helm repo add hashicorp https://helm.releases.hashicorp.com
helm repo update
```


![image](https://github.com/user-attachments/assets/e0893031-7479-4e98-a67e-ee99013afeaf)

Step 2: Install Vault
```
helm install vault hashicorp/vault --namespace vault --create-namespace
```
![image](https://github.com/user-attachments/assets/53b79657-85c7-436f-9e82-a58236d29d1e)



Step 3: Verify Installation
Check the status of the Vault deployment:
```
helm status vault --namespace vault

```


3. **Prometheus**

**Prometheus is a monitoring and alerting toolkit.**

Step 1: Add the Prometheus Community Helm Repository
```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
```
![image](https://github.com/user-attachments/assets/47de3cfb-b653-4726-bb41-f85b153df3be)


Step 2: Install Prometheus
```
helm install prometheus prometheus-community/prometheus
```
![image](https://github.com/user-attachments/assets/342a6951-6da3-4f68-b4dd-d811515c3e8f)

![image](https://github.com/user-attachments/assets/2554e334-c053-46cf-9af3-d9f7a01970ae)

Step 3: Check Prometheus Service
To see if the Prometheus service is running:
```
kubectl get svc
```
![image](https://github.com/user-attachments/assets/a4c8fc2b-1e4d-480a-baed-6578e2c9b04e)


Step 4: Access Prometheus
To access the Prometheus UI locally, you can use kubectl port-forward
```
kubectl port-forward svc/prometheus-server 9090:80
```
![image](https://github.com/user-attachments/assets/0da51123-679f-4743-ad42-a537c096a956)

Use this URL to access the Prometheus UI.
Now, you should be able to access Prometheus by opening a browser and going to 
```
http://localhost:9090
```
![image](https://github.com/user-attachments/assets/b5522ab7-5777-4ad1-b671-5844f5cb6c69)

4. Grafana
Grafana is a popular tool for creating monitoring dashboards using metrics collected by systems like Prometheus.
 1.  Add Grafana Helm Repository
First, add the Grafana Helm chart repository:

```
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
```
![image](https://github.com/user-attachments/assets/041a9605-a1a5-48e2-b17e-bc7de1fa337d)

2. Install Grafana
Install Grafana with Helm:
```
helm install grafana grafana/grafana --set persistence.enabled=true --set persistence.size=10Gi
```
![image](https://github.com/user-attachments/assets/2add3942-90ab-4fd3-a683-e6ae5048b4e1)

3. Access Grafana
By default, the Grafana service will be running, but you need to expose it for local access.

First, check the services:
```
kubectl get svc
```
![image](https://github.com/user-attachments/assets/dc83ea45-da0c-45f6-b55e-008c3697bf62)

4. Now, forward the port to access it locally:
```
kubectl port-forward svc/grafana 3000:80
```
![image](https://github.com/user-attachments/assets/a98176e5-e30c-4d64-946c-be896b293448)

5. You can now access Grafana at:
```
http://localhost:3000
```
![image](https://github.com/user-attachments/assets/f9650c29-e16d-429d-a816-88efda47898d)

ogin to Grafana
The default login credentials for Grafana are:
`
Username: admin
Password: prom-operator
`
```
kubectl get secret --namespace default grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
```
![image](https://github.com/user-attachments/assets/e6d014f2-d00e-4f0c-bee6-daa758204616)

![image](https://github.com/user-attachments/assets/47f65de7-947d-46cd-8e90-28788d91a2e2)

5. Installing Elasticsearch ELK using ECK (Elastic Cloud on Kubernetes)
ECK (Elastic Cloud on Kubernetes) is the official Kubernetes Operator for deploying and managing the Elastic Stack (Elasticsearch, Logstash, and Kibana).

1. Install the Elastic ECK Operator
Elastic provides an operator for managing the full ELK stack (Elasticsearch, Logstash, and Kibana) on Kubernetes.

**install the ECK operator:**
```
kubectl apply -f https://download.elastic.co/downloads/eck/2.10.0/crds.yaml
```
![image](https://github.com/user-attachments/assets/a5a396a1-336a-49b7-b26d-ec94c2ea8766)

```
kubectl apply -f https://download.elastic.co/downloads/eck/2.10.0/operator.yaml
```
![image](https://github.com/user-attachments/assets/b1de1c76-543a-4c56-9365-8345b22def38)

**Verify the ECK Operator Installation**
```
kubectl get pods -n elastic-system
```
![image](https://github.com/user-attachments/assets/a1425e80-ff6d-4a4b-b7a8-7d5108f1efba)

Deploy Elasticsearch, Kibana, and Logstash Using ECK
Once the ECK operator is running, follow the steps to deploy the ELK stack (Elasticsearch, Kibana, Logstash).

**Deploy Elasticsearch**
Create a YAML file for Elasticsearch, for example, elasticsearch.yaml:

Add the following content
```
apiVersion: elasticsearch.k8s.elastic.co/v1
kind: Elasticsearch
metadata:
  name: my-elasticsearch
spec:
  version: 7.10.0
  nodeSets:
  - name: default
    count: 1
    config:
      node.store.allow_mmap: false
    podTemplate:
      spec:
        containers:
        - name: elasticsearch
          resources:
            limits:
              memory: 2Gi
              cpu: "1"
            requests:
              memory: 1Gi
              cpu: "500m"


```
This configuration creates a single-node Elasticsearch cluster. Now apply this configuration:
```
kubectl apply -f elasticsearch.yaml
```
![image](https://github.com/user-attachments/assets/38eaf447-d15d-4ccd-9401-6485c3bf0cfc)

**Check the Status of Elasticsearch**
After deploying Elasticsearch, you can check the status of the pods:
```
kubectl get pods
```
Look for the Elasticsearch pod, which will have a name like quickstart-es-<some-id>. Wait for it to reach the Running status.
![image](https://github.com/user-attachments/assets/2a0fba84-f77f-4b09-a309-7e3df07562ec)

**Deploy Kibana**
Once Elasticsearch is running, let's deploy Kibana.

Create a YAML file for Kibana, for example, kibana.yaml:
```
apiVersion: kibana.k8s.elastic.co/v1
kind: Kibana
metadata:
  name: quickstart
  namespace: default
spec:
  version: "7.10.0"  # Downgraded Kibana version to match Elasticsearch
  count: 1
  elasticsearchRef:
    name: my-elasticsearch-es-http  # Update this line to match the correct Elasticsearch service
  podTemplate:
    spec:
      containers:
        - name: kibana
          resources:
            requests:
              memory: "512Mi"
              cpu: "250m"
            limits:
              memory: "1Gi"
              cpu: "500m"


```

This configuration links Kibana to your Elasticsearch deployment.

Now apply this configuration:
```
kubectl apply -f kibana.yaml
```
![image](https://github.com/user-attachments/assets/01bb431e-106d-4030-bb5e-0979971ff345)

4. Check the Status of Kibana
You can check the status of the Kibana pod by running:
```
kubectl get pods
```

Wait for Kibana to reach the Running status.
![image](https://github.com/user-attachments/assets/1c7d2b5c-88f8-4fe1-ac74-2c6893ce003e)

5. Access Kibana
Once the Kibana pod is running, you can access Kibana through port forwarding.
Run this command to forward port 5601 from the Kibana pod to your local machine:
```
kubectl port-forward service/quickstart-kb-http 5601
```
![image](https://github.com/user-attachments/assets/3af2c3cf-fc5e-458d-ba16-9d0a445348d1)

**Now, you can open your browser and access Kibana at:**
```
http://localhost:5601
```
**Deploy Logstash**
If you also need Logstash, create a YAML file for it, for example, logstash.yaml:

Add the following content:
```
apiVersion: logstash.k8s.elastic.co/v1
kind: Logstash
metadata:
  name: quickstart
spec:
  version: 8.10.0
  count: 1
```
Apply the configuration:
```
kubectl apply -f logstash.yaml
```
Check the status of Logstash with:
```
kubectl get pods
```

7.**Retrieve Elasticsearch Password**
To get the password for the elastic user, run:
```
kubectl get secret quickstart-es-elastic-user -o=jsonpath='{.data.elastic}' | base64 --decode
```

Use this password when accessing Elasticsearch or Kibana.

By following these steps, you should now have Elasticsearch, Kibana, and optionally Logstash



### The End of Project 24 
 In the next project,

1. You will write custom Helm charts
2. Configure Ingress for all the tools and applications running in the cluster
3. Integrate Secrets management using Hashicorp Vault
4. Integrate Logging with ELK
5. Inetegrate monitoring with Prometheus and Grafana

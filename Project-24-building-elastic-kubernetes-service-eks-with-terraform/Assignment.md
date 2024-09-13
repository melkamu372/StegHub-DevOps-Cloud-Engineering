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

Create a Namespace for Vault
```
kubectl create namespace vault
```

Step 2: Install Vault
```
helm install vault hashicorp/vault --namespace vault
```
![image](https://github.com/user-attachments/assets/0bb0f94a-1823-42a4-b64e-83da1dcc96fc)

Step 3: Verify Installation
Check the status of the Vault deployment:
```
kubectl get pods --namespace vault
```
![image](https://github.com/user-attachments/assets/caeb3b7f-e8f2-496f-96d3-5b92649f6999)


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

    1.Add Grafana Helm Repository
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


 5. **Installing Elasticsearch ELK using ECK([Elastic Cloud on Kubernetes](https://www.elastic.co/guide/en/cloud-on-k8s/current/k8s-install-helm.html))**

    1.Install ECK using the Helm chart

```
helm repo add elastic https://helm.elastic.co
helm repo update
```
**Install**

```
helm install elastic-operator elastic/eck-operator -n elastic-system --create-namespace
```

**Verify the ECK Operator Installation**
```
kubectl get pods -n elastic-system
```
![image](https://github.com/user-attachments/assets/a1425e80-ff6d-4a4b-b7a8-7d5108f1efba)

**Verify the ECK Operator Installation**
```
kubectl logs -n elastic-system sts/elastic-operator
```
![image](https://github.com/user-attachments/assets/50d54e8b-a315-4dbf-ac45-7977f54b89ba)


**Deploy Elasticsearch**

Once the ECK operator is running, follow the steps to deploy the ELK stack (Elasticsearch, Kibana, Logstash).

**Deploy Elasticsearch**
Create a namespace for Elasticsearch
```
kubectl create namespace elastic
```

Create an Elasticsearch cluster using a custom resource: Save the following YAML as elasticsearch.yaml


```
apiVersion: elasticsearch.k8s.elastic.co/v1
kind: Elasticsearch
metadata:
  name: quickstart
  namespace: elastic
spec:
  version: 8.9.1
  nodeSets:
  - name: default
    count: 1
    config:
      node.store.allow_mmap: false
```
**Apply the Elasticsearch deployment:**

```
kubectl apply -f elasticsearch.yaml
```
![image](https://github.com/user-attachments/assets/38eaf447-d15d-4ccd-9401-6485c3bf0cfc)

**Check Elasticsearch Pods: Verify that the Elasticsearch pods are running:**
```
kubectl get pods -n elastic
```
![image](https://github.com/user-attachments/assets/f0c16c2e-a415-43c9-be6b-3c05834cf506)

**Access Elasticsearch: You can access the Elasticsearch service within the Kubernetes cluster:**

```
kubectl get service quickstart-es-http -n elastic
```

**Forward the service port to your local machine:**

```
kubectl port-forward service/quickstart-es-http 9200 -n elastic
```
![image](https://github.com/user-attachments/assets/cf369d06-314b-4dba-a6d6-6b17635b2417)

**Then, test it locally:**
You can retrieve the password with the following command:
```
kubectl get secret quickstart-es-elastic-user -o=jsonpath='{.data.elastic}' -n elastic | base64 --decode
```
![image](https://github.com/user-attachments/assets/45b6f772-fb58-4088-aa56-ff2f5680c98f)

```
curl -u "elastic:y3E42y5b8ep9sbOQ0C4z8zk2" -k "https://localhost:9200"
```
![image](https://github.com/user-attachments/assets/6aaffe87-cd7c-4f7f-9731-08c6d15bcbda)

**Deploy Kibana**
Once Elasticsearch is running, let's deploy Kibana.

Create a YAML file for Kibana, for example, kibana.yaml:
```
apiVersion: kibana.k8s.elastic.co/v1
kind: Kibana
metadata:
  name: kibana
  namespace: elastic
spec:
  version: 8.9.1
  count: 1
  elasticsearchRef:
    name: quickstart

```

This configuration links Kibana to your Elasticsearch deployment.

Now apply this configuration:
```
kubectl apply -f kibana.yaml
```
![image](https://github.com/user-attachments/assets/01bb431e-106d-4030-bb5e-0979971ff345)

**Check the Status of Kibana**

You can check the status of the Kibana pod by running:
```
kubectl get pods -n elastic
```

Wait for Kibana to reach the Running status.
![image](https://github.com/user-attachments/assets/01b30070-4421-460c-9552-a04c0c0de992)


 **Access Kibana**

Once the Kibana pod is running, you can access Kibana through port forwarding.
Run this command to forward port 5601 from the Kibana pod to your local machine:
```
kubectl port-forward service/kibana-kb-http 5601 -n elastic
```
![image](https://github.com/user-attachments/assets/4934cbdc-7fc7-4de4-a30d-59aa105aca05)


**Now, you can open your browser and access Kibana at:**
```
http://localhost:5601
```
**Deploy Logstash**

Create a Logstash ConfigMap
First, you need to create a ConfigMap that contains the Logstash configuration (logstash.conf).

Create the following ConfigMap YAML (logstash-config.yaml):
```
apiVersion: v1
kind: ConfigMap
metadata:
  name: logstash-config
  namespace: elastic
data:
  logstash.conf: |
    input {
      beats {
        port => 5044
      }
    }
    filter {
      grok {
        match => { "message" => "%{COMBINEDAPACHELOG}" }
      }
    }
    output {
      elasticsearch {
        hosts => ["https://quickstart-es-http.elastic:9200"]
        user => "elastic"
        password => "<your_elastic_password>"
        ssl => true
        cacert => '/usr/share/logstash/config/certs/ca.crt'
      }
      stdout { codec => rubydebug }
    }

```
Replace <your_elastic_password> with the password you retrieved for the Elasticsearch elastic user.

Apply the ConfigMap:

```
kubectl apply -f logstash-config.yaml

```
Create the Logstash manifest: Save this as logstash.yaml

Add the following content:
```
apiVersion: logstash.k8s.elastic.co/v1alpha1
kind: Logstash
metadata:
  name: logstash
  namespace: elastic
spec:
  version: 8.9.1
  count: 1
  elasticsearchRef:
    name: quickstart
  config:
    log.level: info
  podTemplate:
    spec:
      containers:
        - name: logstash
          resources:
            limits:
              memory: 2Gi
              cpu: 1

```
Apply the configuration:
```
kubectl apply -f logstash.yaml
```
Check the status of Logstash with:
```
kubectl get pods -n elastic
```
![image](https://github.com/user-attachments/assets/7fac42ac-63af-4ede-9137-03b61f89f73f)


**Monitor and Manage**

To ensure everything is working correctly, you can monitor the Elastic Stack resources using standard Kubernetes commands:

Check all Elastic resources: Our ELK stack (Elasticsearch, Kibana) is up and running on your Kubernetes cluster using the ECK operator
```
kubectl get elasticsearch,kibana,logstash -n elastic
```
![image](https://github.com/user-attachments/assets/6e61f5bc-5065-4b0b-8d60-ab41f8019c28)



### The End of Project 24 Assignment
 In the next project,

1. You will write custom Helm charts
2. Configure Ingress for all the tools and applications running in the cluster
3. Integrate Secrets management using Hashicorp Vault
4. Integrate Logging with ELK
5. Inetegrate monitoring with Prometheus and Grafana

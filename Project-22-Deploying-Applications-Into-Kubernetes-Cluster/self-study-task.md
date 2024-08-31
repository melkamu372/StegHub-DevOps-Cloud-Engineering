## Self Side Task:
1. Build the Tooling app Dockerfile and push it to Dockerhub registry
2. Write a Pod and a Service manifests, ensure that you can access the Tooling app's frontend using port-forwarding feature.

### Step 1: Build the Tooling App Dockerfile and Push it to DockerHub
  1. Build the Docker Image
```
docker-compose -f tooling.yml build
```
2. Run  the docker image 

```
docker-compose -f tooling.yml up -d
```
3. Check running dockers
 ```
docker ps
``` 
![image](https://github.com/user-attachments/assets/0ac8d2d4-aa9a-4e29-b605-8274072fdc38)

4. Test does it works

 ```
http://localhost:5000
```
![image](https://github.com/user-attachments/assets/6fc896d1-8af9-463f-b5f1-b82466d9d847)

5. Access the MySQL Container

```
docker exec -it tooling-containerization_db_1 bash
```

```
mysql -u todo_user -p
```

![image](https://github.com/user-attachments/assets/4efc8ebd-e71b-48b2-9eea-2639beb2a395)

**Now login to the system**

![image](https://github.com/user-attachments/assets/e98e5a79-bdcb-4de2-bf98-25caef770c1a)
![image](https://github.com/user-attachments/assets/63051c12-1f75-486f-8d8c-9d36cb367484)
![image](https://github.com/user-attachments/assets/612a2ad8-ac1c-4178-8d55-8295ca622fcf)

6. Tag the Docker Image
```
docker tag melkamu372/php-todo-app:latest melkamu372/tooling-app:latest
```
 
7. Login to DockerHub
```
docker login
```
8.Push the Docker Image

```
docker push melkamu372/tooling-app:latest
```

![image](https://github.com/user-attachments/assets/6b4bb93d-b0c3-43a1-94d0-531785c177b7)

![image](https://github.com/user-attachments/assets/55ccb061-1c81-4ed3-98cf-b3ea21cb6ea8)


### Step 2: Write Pod and Service Manifests for Kubernetes

1. Create the Pod Manifest: Create a file named tooling-app-pod.yaml 
```
apiVersion: v1
kind: Pod
metadata:
  name: tooling-app
  labels:
    app: tooling-app
spec:
  containers:
  - name: tooling-app-container
    image: melkamu372/tooling-app:latest
    ports:
    - containerPort: 80  # Change this to the port your app listens on

```
![image](https://github.com/user-attachments/assets/5ba3f72b-a8c1-4b3f-b4e7-f4b6e7b5574f)

2. Create the Service Manifest: Create a file named `tooling-app-service.yaml` 
```
apiVersion: v1
kind: Service
metadata:
  name: tooling-app-service
spec:
  selector:
    app: tooling-app
  ports:
    - protocol: TCP
      port: 5000       # Port to expose the service
      targetPort: 80   # Port that the app listens on in the container
  type: ClusterIP

```
![image](https://github.com/user-attachments/assets/a7d8bbca-cb9f-4f66-b0cc-0f2a1c5c7268)

3. Deploy the Pod and Service: Run the following commands to apply the manifests:
```
kubectl apply -f tooling-app-pod.yaml
kubectl apply -f tooling-app-service.yaml
```
![image](https://github.com/user-attachments/assets/bde0b9d8-df3b-424a-8a43-04e7c860796e)

4. Access the Tooling App using Port-Forwarding: To access your Tooling app, run the port-forwarding command:
```
kubectl port-forward pod/tooling-app 8081:80
```
![image](https://github.com/user-attachments/assets/2dda0875-159c-4825-96e4-bf66f6923553)
![image](https://github.com/user-attachments/assets/5cd13a45-a756-4e65-a07f-bdbc043a60cf)


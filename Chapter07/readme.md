(loosly based on https://www.qwiklabs.com/focuses/663?catalog_rank=%7B%22rank%22%3A1%2C%22num_filters%22%3A0%2C%22has_search%22%3Atrue%7D&parent=catalog&search_id=16209680)


Set the project id
```
DEVSHELL_PROJECT_ID=qwiklabs-gcp-04-ce901d937663
```

Build the client image
```
cd client

docker build -t gcr.io/$DEVSHELL_PROJECT_ID/react-hello:v1 .

gcloud docker -- push gcr.io/$DEVSHELL_PROJECT_ID/react-hello:v1
```

```
cd ../server

docker build -t gcr.io/$DEVSHELL_PROJECT_ID/node-server:v1 .

gcloud docker -- push gcr.io/$DEVSHELL_PROJECT_ID/node-server:v1
```



Create a GKE cluster
```
gcloud container clusters create hello-dotnet-cluster \
           --num-nodes 2 \
           --machine-type n1-standard-1 \
           --zone europe-west1-b

```


Deploy everything
```
kubectl apply -f client-deployment.yaml

kubectl apply -f server-deployment.yaml

kubectl get pods  # wait for pods to be running

kubectl expose deployment client-deployment --type="LoadBalancer" --port=3001

kubectl expose deployment api-deployment --type="LoadBalancer" --port=4000

kubectl apply -f ingress.yaml
```


Wait for the externañl IP address to be defined to test frontend
```
kubectl get services

NAME                TYPE           CLUSTER-IP   EXTERNAL-IP     PORT(S)          AGE
client-deployment   LoadBalancer   10.96.4.83   34.140.63.173   3001:31112/TCP   7m32s
kubernetes          ClusterIP      10.96.0.1    <none>          443/TCP          24m
```


Now you can go to a browser http://34.140.63.173:3001



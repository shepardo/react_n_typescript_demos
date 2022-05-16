(loosly based on https://www.qwiklabs.com/focuses/663?catalog_rank=%7B%22rank%22%3A1%2C%22num_filters%22%3A0%2C%22has_search%22%3Atrue%7D&parent=catalog&search_id=16209680)


Set the project id
```
DEVSHELL_PROJECT_ID=qwiklabs-gcp-04-ce901d937663
```


Before creating the docker images for client & server it is necessary to edit the deployment files of both to change the Docker registry url, so
open `server-deployment.yaml` and edit the line `image: gcr.io/qwiklabs-gcp-01-69ec38c49945/node-server:v1` to change the `qwiklabs-gcp-01-69ec38c49945` to the current project id (the one set previously for variable `DEVSHELL_PROJECT_ID`), do the same for `client-deployment.yaml`.



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


Wait for the external IP address to be defined to test frontend
```
kubectl get services

NAME                TYPE           CLUSTER-IP   EXTERNAL-IP     PORT(S)          AGE
client-deployment   LoadBalancer   10.96.4.83   34.140.63.173   3001:31112/TCP   7m32s
kubernetes          ClusterIP      10.96.0.1    <none>          443/TCP          24m
```


Now you can go to a browser http://34.140.63.173:3001




Some useful references


https://www.packtpub.com/product/react-projects/9781789954937
https://github.com/PacktPublishing/React-Projects
https://github.com/shepardo/react_n_typescript_demos

https://www.manning.com/books/cors-in-action
https://effectivetypescript.com/

https://exercism.org/tracks/typescript
https://github.com/type-challenges/type-challenges
https://angular.io/tutorial



https://www.stackhawk.com/blog/react-cors-guide-what-it-is-and-how-to-enable-it/
https://stackabuse.com/handling-cors-with-node-js/
https://betterprogramming.pub/deploy-a-full-stack-go-and-react-app-on-kubernetes-4f31cdd9a48b





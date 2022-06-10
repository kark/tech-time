Docker:

```bash
docker build -t krzykaczu/k8s-react-test .
docker run -d -p 3000:80 krzykaczu/k8s-react-test # only to test if container config is 👍
docker push krzykaczu/k8s-react-test:latest
```

To run k8s cluster:

```bash
minikube start
kubectl create namespace k8s-react-test
kubectl config set-context --current --namespace=k8s-react-test

# app deployment
kubectl apply -f k8s/deployment.yaml
kubectl get deployment -w
# or
kubectl get all

# load-balancer service
kubectl apply -f k8s/load-balancer.yaml
kubectl get services -w # to monitor

minikube tunnel ## tunnels load balancer
kubectl get service/load-balancer
# the application is accessible under http://EXTERNAL-IP:80 from 👆
kubectl delete --all pods,services,deployments,replicasets
```

helm:

```bash
cd helm
helm create k8s-react-test-helm-chart
helm install k8s-react-test-helm-chart .
helm template k8s-react-test-helm-chart . # to verify
minikube tunnel ## tunnels load balancer
kubectl get service/load-balancer
# the application is accessible under http://EXTERNAL-IP:80 from 👆
kubectl delete k8s-react-test-helm-chart
```

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

# `contexts`
# kubectl config get-contexts
# kubectl config use-context <context-name>
# kubectl config view

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
# helm create k8s-react-test-helm-chart
helm upgrade --install k8s-react-test-helm-chart .
# more options
# helm upgrade --install <release-name> \
#  --namespace <namespace> \
#  -f values/<env>/<chart-name>/values.yaml \
#  -f values/<env>/<chart-name>/secrets.yaml \
#  --set image.app.tag=<git-sha> \
#  <chart-name>


helm template k8s-react-test-helm-chart . # to verify
# Also to test that changes to your chart are correct, run the upgrade command with --dry-run --debug options
# helm upgrade --install <release-name> \
#  --dry-run --debug \
#  --namespace <namespace> \
#  -f values/<env>/<chart-name>/values.yaml \
#  -f values/<env>/<chart-name>/secrets.yaml \
#  --set image.app.tag=<git-sha> \
#  <chart-name>

minikube tunnel ## tunnels load balancer
kubectl get service/load-balancer
# the application is accessible under http://EXTERNAL-IP:80 from 👆
# also `kubectl proxy` to  start a proxy to the Kubernetes API server -> https://kubernetes.io/docs/tasks/extend-kubernetes/http-proxy-access-api/
kubectl delete k8s-react-test-helm-chart
```

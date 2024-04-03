# Deploy Bookinfo Rating Service on Kubernetes Workshop

## Prepare Kubernetes Environment

* Install `docker-compose`

```bash
kubectl create namespace workshop-[X]-bookinfo-dev
kubectl config set-context $(kubectl config current-context) --namespace=workshop-[X]-bookinfo-dev
```

## Push Rating Service Docker Image to Harbor

```bash
# Login to Private Registry first
docker login registry.devops.ntplc.co.th
# Put bookinfo credentials

# Check to see Rating Service Docker Image name
docker images

# Build Ratings Service Docker Image
cd ~/ratings/
docker compose build

# Push Rating Docker Image
docker push registry.devops.ntplc.co.th/workshop-[X]/bookinfo/ratings:dev
```

* Check your pushed Docker Image at <https://registry.devops.ntplc.co.th>

### Create Secret to pull Docker Image from Harbor Docker Private Registry

```bash
# See the Docker credentials file
cat ~/.docker/config.json

# Show secret
kubectl get secret

# Create Docker credentials Kubernetes Secret
kubectl create secret generic registry-bookinfo \
  --from-file=.dockerconfigjson=$HOME/.docker/config.json \
  --type=kubernetes.io/dockerconfigjson

# See newly created secret
kubectl get secret
kubectl describe secret registry-bookinfo
```

### Create Rating Service Kubernetes Manifest File

* `mkdir ~/ratings/k8s/` to make a directory to store manifest file
* Create `ratings-deployment.yaml` file inside `~/ratings/k8s/` directory with below content

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: bookinfo-dev-ratings
  namespace: workshop-[X]-bookinfo-dev
spec:
  replicas: 1
  selector:
    matchLabels:
      app: bookinfo-dev-ratings
  template:
    metadata:
      labels:
        app: bookinfo-dev-ratings
    spec:
      containers:
      - name: bookinfo-dev-ratings
        image: registry.devops.ntplc.co.th/workshop-[X]/bookinfo/ratings:dev
        imagePullPolicy: Always
        env:
        - name: SERVICE_VERSION
          value: v1
      imagePullSecrets:
      - name: registry-bookinfo
```

* Create `ratings-service.yaml` file inside `~/ratings/k8s/` directory with below content

```yaml
apiVersion: v1
kind: Service
metadata:
  name: bookinfo-dev-ratings
  namespace: workshop-[X]-bookinfo-dev
spec:
  type: ClusterIP
  ports:
  - port: 8080
  selector:
    app: bookinfo-dev-ratings
```

* Create `ratings-ingress.yaml` file inside `~/ratings/k8s/` directory with below content

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /$2
  name: bookinfo-dev-ratings
  namespace: workshop-[X]-bookinfo-dev
spec:
  ingressClassName: nginx
  tls:
  - hosts:
    - workshop-[X]-bookinfo-dev.devops.ntplc.co.th
    secretName: wildcard-cert-opstella-tls
  rules:
  - host: workshop-[X]-bookinfo-dev.devops.ntplc.co.th
    http:
      paths:
      - path: /ratings(/|$)(.*)
        pathType: Prefix
        backend:
          service:
            name: bookinfo-dev-ratings
            port:
              number: 8080
```

```bash
# Create deployment resource
kubectl apply -f k8s/

# Check status of each resource
kubectl get deployment
kubectl get service
kubectl get ingress
```

* Try to access <https://workshop-[X]-bookinfo-dev.devops.ntplc.co.th/ratings/health> and <https://workshop-[X]-bookinfo-dev.devops.ntplc.co.th/ratings/ratings/1> to check the deployment
* Commit and push your code

## Exercises

### Exercises 1

* Use `dev` branch
* Build and push Docker Image `Details Service` to Harbor
* Create Kubernetes manifest files for `Details Service`
  * Using domain and path `https://workshop-[X]-bookinfo-dev.devops.ntplc.co.th/details/*`
* Deploy, test, commit, and push manifest files to repository

### Exercises 2

* Use `dev` branch
* Build and push Docker Image `Reviews Service` to Harbor
* Create Kubernetes manifest files for `Reviews Service`
  * Using domain and path `https://workshop-[X]-bookinfo-dev.devops.ntplc.co.th/reviews/*`
* Deploy, test, commit, and push manifest files to repository

### Exercises 3

* Use `dev` branch
* Build and push Docker Image `Product Page Service` to Harbor
* Create Kubernetes manifest files for `Product Page Service`
  * Using domain and path `https://workshop-[X]-bookinfo-dev.devops.ntplc.co.th/productpage/*`
* Deploy, test, commit, and push manifest files to repository
* Make sure it can call other services correctly

## Navigation

* Previous: [Kubernetes Manifest File Workshop](06-k8s-manifest.md)
* [Home](../README.md)
* Next: [Deploy MongoDB with Helm Chart Workshop](08-helm-mongodb.md)

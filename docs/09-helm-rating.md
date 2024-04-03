# Deploy Rating Service with Helm Chart

## Create Helm Chart for Ratings Service

* Delete current Ratings Service first with command `kubectl delete -f k8s/`
* `mkdir ~/ratings/k8s/helm` to create directory for Ratings Helm Charts
* Create `Chart.yaml` file inside `helm` directory and put below content

```yaml
apiVersion: v1
description: Bookinfo Ratings Service Helm Chart
name: bookinfo-ratings
version: 1.0.0
appVersion: 1.0.0
home: https://workshop-[X]-bookinfo-dev.devops.ntplc.co.th/ratings
maintainers:
  - name: workshop-[X]
    email: workshop-[X]@ntplc.co.th
sources:
  - https://git.devops.ntplc.co.th/workshop-[X]/ratings.git
```

* `mkdir -p ~/ratings/k8s/helm/templates` to create directory for Helm Templates
* Move our ratings manifest file to template directory with command `mv k8s/ratings-*.yaml k8s/helm/templates/`
* Let's try deploy Ratings Service

```bash
# Deploy Ratings Helm Chart
helm install bookinfo-dev-ratings k8s/helm

# Get Status
kubectl get deployment,pod,service,ingress
```

* Try to access <https://workshop-[X]-bookinfo-dev.devops.ntplc.co.th/ratings/health> and <https://workshop-[X]-bookinfo-dev.devops.ntplc.co.th/ratings/ratings/1> to check the deployment

## Create Helm Value file for Ratings Service

* Create `values-bookinfo-dev-ratings.yaml` file inside `k8s/helm-values` directory and put below content
* Copy Helm template files to `k8s/helm/templates`

* This is sample syntax to have default value

```yaml
{{ .Values.ingress.path | default "/" }}
```

* This is sample syntax of using if and range to assign annotation

```yaml
  {{- if .Values.ingress.annotations }}
  annotations:
    {{- range $key, $value := .Values.ingress.annotations }}
    {{ $key }}: {{ $value | quote }}
    {{- end }}
  {{- end }}
```

* This is a complexity sample of using if and range syntax

```yaml
        {{- if or (.Values.extraEnv) (.Values.extraEnvSecret) }}
        env:

        {{- if .Values.extraEnv }}
        {{- range $key, $value := .Values.extraEnv }}
        - name: {{ $key }}
          value: {{ $value | quote }}
        {{- end }}
        {{- end }}

        {{- if .Values.extraEnvSecret }}
        {{- range $key, $value := .Values.extraEnvSecret }}
        {{- range $secretName, $secretValue := $value }}
        - name: {{ $key }}
          valueFrom:
            secretKeyRef:
              name: {{ $secretName }}
              key: {{ $secretValue | quote }}
        {{- end }}
        {{- end }}
        {{- end }}

        {{- end }}
```

* After copy, you can write the values down to value file and upgrade release with below command

```bash
helm upgrade -f k8s/helm-values/values-bookinfo-dev-ratings.yaml \
  bookinfo-dev-ratings k8s/helm
```

* Commit and push code to Git

## Exercise

### Exercise 1

* Use `dev` branch
* Create Kubernetes & Helm deployment for `details` service
* Deploy `details` service on 3 environments on each namespaces
* There is no database for `details` service

### Exercise 2

* Use `dev` branch
* Create Kubernetes & Helm deployment for `reviews` service
* Deploy `reviews` service on 3 environments on each namespaces
* There is no database for `reviews` service

### Exercise 3

* Use `dev` branch
* Create Kubernetes & Helm deployment for `productpage` service
* Deploy `productpage` service on 3 environments on each namespaces
* There is no database for `productpage` service

## Navigation

* Previous: [Deploy MongoDB with Helm Chart Workshop](08-helm-mongodb.md)
* [Home](../README.md)

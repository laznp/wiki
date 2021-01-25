Install Prometheus and Grafana on Kubernetes Cluster
===
Prerequisites
---
- Internet Access
- Kubernetes Cluster
- Helm v3 installed on your system
- Kubectl installed on your system
- Basic knowledge of Linux Command Line

Create Monitoring Namespace
---
1. Create with Kubectl CLI
```sh
$ kubectl create namespace monitoring
```
2. Create with Kubernetes YAML, create file `monitoring-namespace.yaml` then add below code.
```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: monitoring
```
Apply the `yaml` with below command
```sh
$ kubectl apply -f monitoring-namespace.yaml
```

Installing Prometheus Grafana Stack
---
```sh
 $ helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
 $ helm repo update
 $ helm install prometheus prometheus-community/kube-prometheus-stack --version 13.2.1 --namespace monitoring --debug
```

Get Grafana Password
---
```sh
$ kubectl get secret prometheus-grafana -o jsonpath="{.data.admin-password}" --namespace monitoring | base64 -d; echo
```
Copy the password.

Forward Port to your local
---
```sh
$ kubectl port-forward svc/prometheus-kube-prometheus-prometheus 9090 # Forward Prometheus Service to your localhost port 9090
$ kubectl port-forward svc/prometheus-grafana 3000:80 # Forward Grafana Service to your localhost port 3000
```

Accessing Dashboard
---
Lets say that you already successfully setup all above command, open http://localhost:3000 to access Grafana and login with `admin:<grafana_password>`

##### If you want to explore more about Prometheus and Grafana on Kubernetes, you can read [on this Github Repository](https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack).

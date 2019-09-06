This repo contains customizations to your localdev minikube environment.
Win10 PC Target Environment

Overview of env parts:
- VirtualBox VM-Manager https://www.virtualbox.org
- Minikube Container-Orchestrator https://kubernetes.io
- Helm Package-Manager https://helm.sh
- Traefik DNS-Ingress https://traefik.io
- Kubeapps App-Manager https://kubeapps.com

Installation Walkthrough
Prep
```
Set-ExecutionPolicy AllSigned
```
Chocolatey is a package manager for Win10
```
Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
```
Install VirtualBox from https://www.virtualbox.org/wiki/Downloads
Minikube is a small kubernetes container manager for devs
```
choco install minikube kubernetes-cli
minikube start
```
(recommended) Increase minikube ram for better responsiveness later
```
minikube stop
```
Open up virtualbox and set more ram for the minikube vm, then
```
minikube start
```
Helm is a package manager for clusters
```
choco install kubernetes-helm
helm init
```
Traefik helps with outside http access
```
helm install stable/traefik --name localdev-traefik --namespace kube-system --set accessLogs.enabled=true,dashboard.enabled=true,dashboard.domain=traefik.localdev.net
```
Kubeapps helps manage apps on a cluster
```
kubectl create serviceaccount kubeapps-operator
kubectl create clusterrolebinding kubeapps-operator --clusterrole=cluster-admin --serviceaccount=default:kubeapps-operator
helm repo add bitnami https://charts.bitnami.com/bitnami
helm install --name kubeapps --namespace kubeapps bitnami/kubeapps
```
Note this code for logging into corresponding browser dashboard later
```
kubectl get secret $(kubectl get serviceaccount kubeapps-operator -o jsonpath='{.secrets[].name}') -o jsonpath='{.data.token}' | base64 --decode && echo
```
From the top level of this repository, install customizations
```
helm install --namespace kube-system localdev-kubernetes-dashboard
helm install --namespace kubeapps localdev-kubeapps-dashboard
```

To access the dashboards:
Get the minikube IP
```
minikube ip
```
Get the LoadBalancer Ports with
```
kubectl -n kube-system get services
```
Append above results to your hostfile (c:\Windows\System32\Drivers\etc\hosts)
```
MINIKUBE-IP kubernetes.localdev.net
MINIKUBE-IP kubeapps.localdev.net
MINIKUBE-IP traefik.localdev.net
```
Visit from a browser
- http://kubernetes.localdev.net:PORT
- http://kubeapps.localdev.net:PORT
- http://traefik.localdev.net:PORT

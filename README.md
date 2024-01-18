# Kubernetes commands for ArgoCD

### Install ArgoCd in k8s cluster
 https://argo-cd.readthedocs.io/en/stable/getting_started/ 

 With minikube started, run the following commands 
 ```bash
 kubectl create namespace argocd
 kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argocd/stable/manifests/install.yaml
 kubectl get pod -n argocd
 ```

### Access ArgoCD UI

Use kubectl port forward to access the service 

```bash
kubectl get svc -n argocd
kubectl port-forward -n argocd svc/argocd-server 8080:443
```
### Login Using the CLI
Username is `admin` 
Password: 
```bash
kubectl get sectret argocd-initial-admin-secret -n argocd -o yaml
echo <password> | base64 --decode
```
ignore the % suffix 

Sign in

### Configure ArgoCD with "Application" CRD

```bash
kubectl apply -f application.yml
```
### Trigger manual sync in ArgoUI

Refresh: compare the latest code in Github with the live state. 

Sync: Move to target state, by actually applying the changes to the k8s cluster. 

## Setup Ingress

- [Install nginx ingress controller](https://kubernetes.github.io/ingress-nginx/deploy/#minikube):

    Enable ingress add-ons
    ```bash
    minikube addons enable ingress
    minikube addons enable ingress-dns
    ```
    Apply ingress
    ```bash
    kubectl apply -f k8s/ingress.yaml
    kubectl get ingress
    ```

- Configure ingress:
    
    Add ingress host to /etc/hosts as follows

    ```bash
    sudo vi /etc/hosts
    ```
    Append to the file 
    ```bash
    127.0.0.1       broker-service.local
    ```

    Create a minikube tunnel

    ```bash
    kubectl expose deployment broker-service --type=LoadBalancer --port=8080 --target-port=80
    minikube tunnel
    ```

    The app is now available at `http://broker-service.local` in the browser.

    Test ingress by running 
    
    ```bash
    nslookup broker-service.local
    ```

- [Install cert-manager](https://cert-manager.io/docs/installation/kubernetes/):

    ```bash
    kubectl apply -f https://github.com/jetstack/cert-manager/releases/download/v1.4.0/cert-manager.yaml
    ```
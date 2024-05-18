
### Create Azure kubernetes cluster terraform

see [AKS-README.md](Terraform/AKS-README.md).

### Since there is cost involved in creating and using AKS cluster we are using Minikube for demostration


### Install MediaWiki on Minikube

Clone the current repo to your local machine using 
```
git clone https://github.com/ashishbansal8383/tw-mediawiki-assignment.git
```
Open to helm Chart directory
```
cd helm-chart
```
# Mediawiki
Helm chart for mediawiki to deploy on kubernetes

# Pre-requisites 

1.) A Kubernetes cluster on cloud or Raspberry pi. You can also run a minikube or docker for windows desktop . \
2.) Helm \
3.) kubectl

versions installed on my machine . \
kubernetes v1.26.3 \
helm v3.11.3 \
docker image > ashishbansal8383/mediawiki

# How to run 

The repo conatines two charts . 

1.) mediawiki-chart runs mediawiki app \
2.) mediawiki-mariadb-chart runs database for mediawiki app.

To deploy, hop over to the chart and execute below command

helm install chartname /directory
 
example > 
```
helm install mediawiki ./mediawiki-chart
helm install database ./mediawiki-mariadb-chart
```
The application will be served on the external ip provided by load balancer . In my case it was > http://localhost:8090. 
The databse host will be available at database:3306. Set the db root password, username and db name from values file placed inside mediawiki-mariadb-chart . Use the same to configure mediawiki db details page .

At the end of configuartion , LocalSettings.php will be downloaded . The same file need to be placed at /var/www/html inside conatiner . This can be done by removing commented hostmount in deployment.yaml of mediawiki-chart and providing a hostmount path.

## Important kubectl commands to checks pods, services
```
Kubectl get pods -A -o wide
Kubectl get pods -A -o wide
```
## Automate deployment using ArgoCD. A great continuous delivery tool.

1. Create a namespace for argocd
```bash
kubectl create namespace argocd
```
2. Apply ArgoCD manifest installation file from ArgoCD [github repository](https://github.com/argoproj/argo-cd/releases) 
```bash
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/v2.8.4/manifests/install.yaml
```
3. Verify the installation by getting all the objects in the ArgoCD namespace.
```bash
kubectl get all -n argocd
```
4. wait till all pods in that namespace running

# Access ArgoCD UI
1. we need to do a port forwarding from the argocd-server service
```bash
kubectl port-forward svc/argocd-server -n argocd 9080:443
```
2. now lets go to browser with http://localhost:9080
3. retrieve password from secret in argocd namespace
```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
```
4. the default login username is ```admin``` with the password we took above
kubectl port-forward svc/argocd-server -n argocd 8080:443
# Argo CD 
### install
```bash
brew install argocd
k create ns argocd
kubectl apply -n argocd  -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml 
```

# Access The Argo CD API Server
By default, the Argo CD API server is not exposed with an external IP. To access the API server, choose one of the following techniques to expose the Argo CD API server:
Port Forwarding
Kubectl port-forwarding can also be used to connect to the API server without exposing the service.
```bash
kubectl -n argocd port-forward svc/argocd-server 8080:443
```
The API server can then be accessed using the localhost:8080
User: admin , Password: 
```bash
kubectl get -n argocd secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d && echo 
```
Change the password using the command:
```bash
#first you have to login:
argocd login localhost:8080
argocd account update-password
argocd logout localhost:8080
#then login with your new password:
```bash
argocd login localhost:8080
```
### Create an app:
```bash
argocd cluster list
argocd app list
argocd proj list
```
To create proj :
```bash
argocd proj create myproj
```
you can create your app in myproj or create inner default:
```bash
argocd app create guestbook --repo https://github.com/argoproj/argocd-example-apps.git --path guestbook --dest-server https://kubernetes.default.svc --dest-namespace default
#OR try:
argocd app create argocd-demo --repo https://github.com/RFinland/argocd --path yamls --dest-server https://kubernetes.default.svc --dest-namespace default 
```

Lets sync our apps:
```bash
argocd app sync guestbook
argocd app sync argocd-demo
```

if you change your github repo , you have to click on refresh buttom in argocd UI (if you enabled autosync-also you can wait to see your changes)
```bash
k scale deploy nginx --replicas 2
```
```bash
helm repo add stable https://charts.helm.sh/stable
```
after add helm reo (via https) from UI
Lets create application :
in SOURCE:
select the helm url and chart = grafana
then select previuos version ,cuz we are going to update it 
also  you can change the  helm values
then create an app
```bash
argocd app wait mygrafana || argocd app logs mygrafana
argocd app diff mygrafana
```
other way:
```bash
argocd app create guestbook \
    --repo https://github.com/argoproj/argocd-example-apps.git \
    --path helm-guestbook \
    --dest-server https://kubernetes.default.svc \
    --dest-namespace default
```
```bash
 k -n argocd get  applications
 k -n argocd describe  application mygrafana
 k describe pod argocd-application-controller -n argocd
 ```
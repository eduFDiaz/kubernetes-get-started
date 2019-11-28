# Get started with Kubernetes in Windows 10
Before proceding installing minikube, install Docker Desktop for windows or Docker Toolbox for for windows.
## Install choco package manager
Open a powershell terminal as admin and install choco with the next statement:
```
Set-ExecutionPolicy Bypass -Scope Process -Force; `
  iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
```
read carefully and accept all the requirements of the installation.
## Install minikube
```
choco install minikube
```
## Create a minikube vm
Check your kubernetes version
```
kubectl version
```
If using hyperv:
```
minikube start --kubernetes-version "v1.16.2" --vm-driver "hyperv" --hyperv-virtual-switch "Minikube"
```
if using virtualbox:
```
minikube start --kubernetes-version "v1.16.2" --vm-driver "virtualbox" --hyperv-virtual-switch "Minikube"
```
Change the kubernetes in the previous versions on the previous steps by your own kubernetes version.

# Setup the Kubernetes dashboard
## Create Service account for admin user
Create the file dashboard-admin.yaml and add the next:
```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
```
save it and execute:
```
kubectl apply -f .\dashboard-admin.yaml
```
## Bind cluster role to admin user just created
Create the file ckuster-role.yaml and add the next:
```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
```
save it and execute:
```
kubectl apply -f .\cluster-role.yaml
```
## Generate an access token
```
kubectl -n kubernetes-dashboard describe secret $(kubectl -n kubernetes-dashboard get secret | grep admin-user | awk '{print $1}')
```
Copy the token to the clipboard

## Run the dasboard
```
kubectl.exe proxy
```
The dashboard will be available at http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
Provide the token and enjoy!

### Contribute
Please feel free to contribute to this guide.
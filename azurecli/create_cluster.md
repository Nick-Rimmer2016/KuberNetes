# Kubernetes Deep Dive

### Create a Resource group

```az group create --name aks-training --location uksouth```

### Create a Service principal

```az ad sp create-for-rbac --skip-assignment```

### Remove a Service Principal

```az ad sp delete --id ```

### Create Azure Container Registry

**Create ACR**   
```az acr create --resource-group aks-training --name aksndr2024 --sku Basic --admin-enabled true```

**Attach Service Principal to ACR**   
```az role assignment create --assignee "app id" --role acrpull --scope "acr id"```

### Create a Test Image

**Create simple dockerfile**   
```echo "FROM hello-world" > Dockerfile```   
**Build and Upload image**   
```az acr build --image sample/hello-world:v1 --registry aksndr2024 --file Dockerfile . --resource-group aks-training```   
**Test-Image**   
```az acr run --registry aksndr2024 --cmd '$Registry/sample/hello-world:v1' /dev/null --resource-group aks-training```   
**Further example**   
```git clone https://github.com/chadmcrowell/aks-node-docker.git```
```az acr build --registry aksndr2024 --image node:v1 . --resource-group aks-training```


### Create a cluster

Use the following in a BASH session or replace the forward slash with a backtick for PowerShell.   

```
    az aks create \
      --resource-group aks-training \
      --name TrainingAKSCluster \
      --node-count 1 \
      --enable-addons monitoring \
      --generate-ssh-keys \
      --node-vm-size Standard_B2s \
      --service-principal "sp id" \
      --client-secret ""
```

### Config Files

These files are held in local user folder. Under .kube   
Get config and merge   
```az aks get-credentials --resource-group aks-training --name TrainingAKSCluster```

### Basic Commands

```kubectl get nodes```

### Create a Deployment
```bash
kubectl create deployment nodeapp --image=aksndr2024.azurecr.io/node:v1 --replicas=1 --port=8080
kubectl get deploy
kubectl get pods
kubectl get replicaset
```

### Create a Service

```kubectl expose deploy nodeapp --port=80 --target-port=8080 --dry-run -o yaml >svc.yaml```   
Edit svc.yaml and add Loadbalancer under spec:   
```kubectl apply -f svc.yaml```   
```kubectl get svc (Browse to target)```   

### Tear Down

```az group delete --name aks-training --yes```
```az group delete --name DefaultResourceGroup-SUK --yes```

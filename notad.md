## Actions Runner Controller Quickstart
https://github.com/actions/actions-runner-controller/blob/master/docs/quickstart.md

## 1- Install cert-manager in your cluster
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.8.2/cert-manager.yaml

## Crear un access token

Generar un token classic en github

## Deploy and Configure ARC
1️⃣ Deploy and configure ARC on your K8s cluster. You may use Helm or Kubectl.


## Agregar el repo en helm
helm repo add actions-runner-controller https://actions-runner-controller.github.io/actions-runner-controller

## Install Helm chart
helm upgrade --install --namespace actions-runner-system --create-namespace\
  --set=authSecret.create=true\
  --set=authSecret.github_token=TOKEN_GITHUB\
  --wait actions-runner-controller actions-runner-controller/actions-runner-controller

## Create the GitHub self hosted runners and configure to run against your repository.
apiVersion: actions.summerwind.dev/v1alpha1
kind: RunnerDeployment
metadata:
  name: self-hosted-runners
spec:
  replicas: 1
  template:
    spec:
      repository: juancrespo25/python-app

## Apply this file to your K8s cluster.
kubectl apply -f runnerdeployment.yaml

## Validar el runner creado
kubectl get runners -n actions-runner-system
NAME                              ENTERPRISE   ORGANIZATION   REPOSITORY                GROUP   LABELS   STATUS    MESSAGE   WF REPO   WF RUN   AGE
self-hosted-runners-4gggs-s9gp6                               juancrespo25/python-app                    Running                                4m25s

## Validar los pods creados
kubectl get pods -n actions-runner-system
NAME                                         READY   STATUS              RESTARTS   AGE
actions-runner-controller-6bdbf69656-shgjw   2/2     Running             0          11m
self-hosted-runners-4gggs-s9gp6              0/2     ContainerCreating   0          24s



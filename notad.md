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

## Para interactuar Argo CD con Github Action debe de instalar el CLI de Argo CD
## Instalacion de ArgoCD CLI
## https://argo-cd.readthedocs.io/en/stable/cli_installation/

$version = (Invoke-RestMethod https://api.github.com/repos/argoproj/argo-cd/releases/latest).tag_name

Replace $version in the command below with the version of Argo CD you would like to download:

$url = "https://github.com/argoproj/argo-cd/releases/download/" + $version + "/argocd-windows-amd64.exe"
$output = "argocd.exe"

Invoke-WebRequest -Uri $url -OutFile $output

## Mover argocd.exe

$dest = "C:\Users\JuanCrespo\bin"
New-Item -ItemType Directory -Force -Path $dest | Out-Null
Move-Item ".\argocd.exe" "$dest\argocd.exe" -Force
[Environment]::SetEnvironmentVariable("Path", "$env:Path;$dest", "User")

## Para deployar desde argocd cli

Logearse desde el argocd cli

argocd login localhost:8090 --insecure --username admin --password I-BVu9Sexh1ku5OK

## Para listar las app que estan en argocd
PS C:\Users\JuanCrespo> argocd app list
NAME               CLUSTER                         NAMESPACE  PROJECT  STATUS  HEALTH   SYNCPOLICY  CONDITIONS  REPO                                            PATH                    TARGET
argocd/python-app  https://kubernetes.default.svc  flask-app  default  Synced  Healthy  Manual      <none>      https://github.com/juancrespo25/python-app.git  charts/flask-app-chart  main

## Para sincronizar el app desde argocd cli

 argocd app sync python-app

## Conectarte al runner para probar si llegas al server argocd 

kubectl exec -ti self-hosted-runners-4gggs-565hn -n  actions-runner-system -- sh

$ curl -k  https://argocd-server.argocd
<!doctype html><html lang="en"><head><meta charset="UTF-8"><title>Argo CD</title><base href="/"><meta name="viewport" content="width=device-width,initial-scale=1"><link rel="icon" type="image/png" href="assets/favicon/favicon-32x32.png" sizes="32x32"/><link rel="icon" type="image/png" href="assets/favicon/favicon-16x16.png" sizes="16x16"/><link href="assets/fonts.css" rel="stylesheet"><script defer="defer" src="main.5ba26eb1c0b8a4b76936.js"></script></head><body><noscript><p>Your browser does not support JavaScript. Please enable JavaScript to view the site. Alternatively, Argo CD can be used with the <a href="https://argoproj.github.io/argo-cd/cli_installation/">Argo CD CLI</a>.</p></noscript><div id="app"></div></body><script defer="defer" src="extensions.js"></script></html>$ 

## Instalar helm
winget install Helm.Helm

Opción 1: Instalar Helm

##Cerrar la terminar y volver a abrir y ejecutar

helm install nginx-gateway oci://ghcr.io/nginx/charts/nginx-gateway-fabric --create-namespace -n ns-gateway-api

##Verificar

kubectl get pods -n ns-gateway-api
kubectl get gatewayclass

## Instalar el controller con Helm (NGINX Gateway Fabric):



helm repo add nginx-gateway https://nginx.github.io/nginx-gateway-fabric/helm-charts
helm repo update

helm install nginx-gateway nginx-gateway/nginx-gateway-fabric --create-namespace --namespace ns-gateway-api --set service.type=LoadBalancer

## Crear app
helm create flask-app-chart


## Asi deberia de quedar la estructura

flask-app-chart/
├── Chart.yaml
├── values.yaml
└── templates/
    ├── deployment.yaml
    ├── service.yaml
    ├── gateway.yaml
    └── httproute.yaml
	

## Comando para instalar la app via helm

kubectl create namespace flask-app
helm install flask-app .\flask-app-chart\

##Validar - Ver todos los objetos que tiene el namespace

kubectl get all -n flask-app

NAME                             READY   STATUS    RESTARTS   AGE
pod/flask-app-84689cd696-4dxh8   1/1     Running   0          26s
pod/flask-app-84689cd696-kqlp4   1/1     Running   0          26s

NAME                TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)   AGE
service/flask-app   ClusterIP   10.96.190.1   <none>        80/TCP    26s

NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/flask-app   2/2     2            2           26s

NAME                                   DESIRED   CURRENT   READY   AGE
replicaset.apps/flask-app-84689cd696   2         2         2       26s

## Ver el gateway api creado

kubectl get gateway -n ns-gateway-api

NAME          CLASS   ADDRESS      PROGRAMMED   AGE
api-gateway   nginx   172.18.0.7   True         75s

## Ver el route creado

kubectl get httproute -n flask-app

NAME          HOSTNAMES   AGE
flask-route               111s

## Ver el gateway creado

$ kubectl get svc -n ns-gateway-api
NAME                                 TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)            AGE
api-gateway-nginx                    LoadBalancer   10.96.203.70    172.18.0.7    80:31428/TCP       11m
nginx-gateway-nginx-gateway-fabric   ClusterIP      10.96.243.209   <none>        443/TCP,9113/TCP   14h

kubectl port-forward -n ns-gateway-api svc/api-gateway-nginx 8080:80

## Para poder validar

http://localhost:8080/api/v1/details
http://localhost:8080/api/v1/healthz

## edita el archivo hosts y agrega lo siguiente

127.0.0.1 api.flask-app.com

kubectl port-forward -n ns-gateway-api svc/api-gateway-nginx 8070:80

http://api.flask-app.com:8070/api/v1/details
http://api.flask-app.com:8070/api/v1/healthz


## Para desinstalar

helm uninstall flask-app
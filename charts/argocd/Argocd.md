## Agregar repositorio de argo via helm

helm repo add argo https://argoproj.github.io/argo-helm

## Ver lista de repositorios

helm repo ls
NAME    URL
argo    https://argoproj.github.io/argo-helm

## Install Argocd
helm upgrade --install argocd argo/argo-cd -n argocd --create-namespace -f .\values-argo.yaml

## Ver pods de argocd

kubectl get pods -n argocd
NAME                                               READY   STATUS    RESTARTS   AGE
argocd-application-controller-0                    1/1     Running   0          77s
argocd-applicationset-controller-f6c4d5849-k8fj8   1/1     Running   0          77s
argocd-dex-server-5968db5ddb-4vknx                 1/1     Running   0          77s
argocd-notifications-controller-65d7d7545-8w7pw    1/1     Running   0          77s
argocd-redis-7f9487d4fd-svsnm                      1/1     Running   0          77s
argocd-repo-server-9dc474744-krjrb                 1/1     Running   0          77s
argocd-server-66858784f9-v6wkz                     1/1     Running   0          77s


## Ver ingress de argo 

kubectl get ing -n argocd
NAME            CLASS   HOSTS             ADDRESS   PORTS     AGE
argocd-server   nginx   argocd.test.com             80, 443   88s

## Ver services de argo 

kubectl get svc -n argocd
NAME                               TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)             AGE
argocd-applicationset-controller   ClusterIP   10.96.107.91    <none>        7000/TCP            9m13s
argocd-dex-server                  ClusterIP   10.96.79.217    <none>        5556/TCP,5557/TCP   9m13s
argocd-redis                       ClusterIP   10.96.111.94    <none>        6379/TCP            9m13s
argocd-repo-server                 ClusterIP   10.96.180.205   <none>        8081/TCP            9m13s
argocd-server                      ClusterIP   10.96.73.45     <none>        80/TCP,443/TCP      9m13s


## Ver contraseña de argocd

kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | ForEach-Object { [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($_)) }

I-BVu9Sexh1ku5OK

## forward del puerto

kubectl port-forward svc/argocd-server -n argocd 8090:443

https://localhost:8090/

## Credenciales
user: admin
password: I-BVu9Sexh1ku5OK

## edita el archivo hosts y agrega lo siguiente

127.0.0.1 argocd.test.com

## Validar los pods de namespace argocd

kubectl get pods -n argocd

NAME                                               READY   STATUS    RESTARTS      AGE
argocd-application-controller-0                    1/1     Running   0             7h42m
argocd-applicationset-controller-f6c4d5849-k8fj8   1/1     Running   0             7h42m
argocd-dex-server-5968db5ddb-4vknx                 1/1     Running   0             7h42m
argocd-notifications-controller-65d7d7545-8w7pw    1/1     Running   0             7h42m
argocd-redis-7f9487d4fd-svsnm                      1/1     Running   0             7h42m
argocd-repo-server-9dc474744-krjrb                 1/1     Running   1 (29m ago)   7h42m
argocd-server-66858784f9-v6wkz                     1/1     Running   0             7h42m

## Para ver los secretos 

kubectl get secrets -n argocd

NAME                           TYPE                 DATA   AGE
argocd-initial-admin-secret    Opaque               1      7h43m
argocd-notifications-secret    Opaque               0      7h43m
argocd-redis                   Opaque               1      7h43m
argocd-secret                  Opaque               5      7h43m
sh.helm.release.v1.argocd.v1   helm.sh/release.v1   1      7h44m


## Para ver el manifiesto
kubectl get secrets -n argocd argocd-initial-admin-secret -o yaml

apiVersion: v1
data:
  password: SS1CVnU5U2V4aDFrdTVPSw==
kind: Secret
metadata:
  creationTimestamp: "2026-09-15T17:25:42Z"
  name: argocd-initial-admin-secret
  namespace: argocd
  resourceVersion: "497831"
  uid: 53b14520-d490-4026-82fb-bd6dd3f05572
type: Opaque

## Para ver el contenido del password

echo "SS1CVnU5U2V4aDFrdTVPSw==" | base64 -d
I-BVu9Sexh1ku5OK
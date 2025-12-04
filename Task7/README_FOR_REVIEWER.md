1. Создаем неймспейс:

``` shell
kubectl create namespace audit-zone --dry-run=client -o yaml | kubectl apply -f 01-create-namespace.yaml
``` 

2. Проверяем создание подов с нарушениями insecure-manifests:

``` shell
kubectl apply -f insecure-manifests/01-privileged-pod.yaml
``` 
``` shell
kubectl apply -f insecure-manifests/02-hostpath-pod.yaml
``` 
``` shell
kubectl apply -f insecure-manifests/03-root-user-pod.yaml
``` 

3. Проверяем создание подов с исправлениями secure-manifests:

``` shell
kubectl apply -f secure-manifests/01-secure.yaml
``` 
``` shell
kubectl apply -f secure-manifests/02-secure.yaml
``` 
``` shell
kubectl apply -f secure-manifests/03-secure.yaml
``` 

4. Устанавливаем gatekeeper

``` shell
kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/release-3.13/deploy/gatekeeper.yaml
```

- Создаем неймспейс для проверки

``` shell
kubectl create namespace gatekeeper-zone
``` 
- применяем правила:


``` shell
kubectl apply -f gatekeeper/constraint-templates/privileged.yaml
``` 
``` shell
kubectl apply -f gatekeeper/constraints/privileged.yaml
``` 
``` shell
kubectl apply -f gatekeeper/constraint-templates/runasnonroot.yaml
``` 
``` shell
kubectl apply -f gatekeeper/constraints/runasnonroot.yaml
``` 
``` shell
kubectl apply -f gatekeeper/constraint-templates/hostpath.yaml
``` 
``` shell
kubectl apply -f gatekeeper/constraints/hostpath.yaml
``` 

- проверям создание подов с нарушениями:
``` shell
kubectl apply -f gatekeeper/insecure-manifests/01-privileged-pod.yaml
``` 
``` shell
kubectl apply -f gatekeeper/insecure-manifests/02-hostpath-pod.yaml
``` 
``` shell
kubectl apply -f gatekeeper/insecure-manifests/03-root-user-pod.yaml
``` 
1. Запустить миникуб:
``` shell
minikube start --network-plugin=cni --cni=calico
```
2. Создать неймспейс:

``` shell
kubectl create namespace sprint5
kubectl config set-context --current --namespace=sprint5
```
3. Создать ПОДы:

``` shell
kubectl run front-end-app --image=nginx --labels role=front-end --expose --port 80
kubectl run back-end-api-app --image=nginx --labels role=back-end-api --expose --port 80
kubectl run admin-front-end-app --image=nginx --labels role=admin-front-end --expose --port 80
kubectl run admin-back-end-api-app --image=nginx --labels role=admin-back-end-api --expose --port 80
```

4. Применяем файлы:

``` shell
kubectl apply -f non-admin-api-allow.yaml
kubectl apply -f admin-api-allow.yaml
```

5. Проверки:

Успешно role=front-end -> back-end-api-app:
``` shell
kubectl run test --rm -i -t --image=alpine --labels=role=front-end -- sh    
/ # wget -qO- --timeout=5 http://back-end-api-app:80
```
Ошибка timed out role=front-end -> admin-back-end-api-app:
``` shell
kubectl run test --rm -i -t --image=alpine --labels=role=front-end -- sh    
/ #  wget -qO- --timeout=5 http://admin-back-end-api-app:80
```

Успешно role=admin-front-end -> admin-back-end-api-app:
``` shell
kubectl run test --rm -i -t --image=alpine --labels=role=admin-front-end -- sh    
/ # wget -qO- --timeout=5 http://admin-back-end-api-app:80
```
Ошибка timed out admin-role=front-end -> back-end-api-app:
``` shell
kubectl run test --rm -i -t --image=alpine --labels=role=admin-front-end -- sh    
/ #  wget -qO- --timeout=5 http://back-end-api-app:80
```
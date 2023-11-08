University: [ITMO University](https://itmo.ru/ru/)
Faculty: [FICT](https://fict.itmo.ru)
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)
Year: 2023/2024
Group: K4112c
Author: Gryaznykh Ivan Dmitrievich
Lab: Lab4
Date of create: 01.10.2023
Date of finished: 09.11.2023


# Лабораторная работа №4 "Сети связи в Minikube, CNI и CoreDNS"

# Ход выполнения работы

### Запустим кластер с 2 нодами и плагином Calico
```
minikube start --network-plugin=cni --cni=calico --nodes 2 -p multinode
```
![Start up](https://github.com/Gryaznykh-Ivan/2023_2024-introduction_to_distributed_technologies-k4112c-gryaznykh_i-d/blob/master/lab4/images/1.jpg)

Проверим текущее состояние
```
kubectl get nodes
kubectl get pods -l k8s-app=calico-node -A
```
![Pods State](https://github.com/Gryaznykh-Ivan/2023_2024-introduction_to_distributed_technologies-k4112c-gryaznykh_i-d/blob/master/lab4/images/2.jpg)

### Предположим что у нас 2 стойки. Проставим для них метки.
![DIAGRAM](https://github.com/Gryaznykh-Ivan/2023_2024-introduction_to_distributed_technologies-k4112c-gryaznykh_i-d/blob/master/lab4/images/3.jpg)

### Напишем манифест для IPPOOL Calico
```
apiVersion: projectcalico.org/v3
kind: IPPool
metadata:
  name: first
spec:
  cidr: 192.168.0.0/24
  ipipMode: Always
  natOutgoing: true
  nodeSelector: rack == "second"

---
apiVersion: projectcalico.org/v3
kind: IPPool
metadata:
  name: second
spec:
  cidr: 192.168.1.0/24
  ipipMode: Always
  natOutgoing: true
  nodeSelector: rack == "first"
```

```
kubectl-calico apply -f calico.yaml --allow-version-mismatch --config=calico.cfg.yaml
```
![DIAGRAM](https://github.com/Gryaznykh-Ivan/2023_2024-introduction_to_distributed_technologies-k4112c-gryaznykh_i-d/blob/master/lab4/images/4.jpg)

### Теперь нужно удалить дефотный IPPOOL,
```
kubectl-calico delete ippools default-ipv4-ippool --allow-version-mismatch --config=calico.cfg.yaml
```

### Создадим деплоймент и запустим приложение
```
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  REACT_APP_USERNAME: "GID"
  REACT_APP_COMPANY_NAME: "ITMO"
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-deploy
spec:
  replicas: 2
  selector:
    matchLabels:
      app: app-deploy
  template:
    metadata:
      labels:
        app: app-deploy
    spec:
      containers:
        - name: app-deploy
          image: ifilyaninitmo/itdt-contained-frontend:master
          env:
            - name: REACT_APP_USERNAME
              valueFrom:
                configMapKeyRef:
                  name: app-config
                  key: REACT_APP_USERNAME
            - name: REACT_APP_COMPANY_NAME
              valueFrom:
                configMapKeyRef:
                  name: app-config
                  key: REACT_APP_COMPANY_NAME
          ports:
            - containerPort: 3000
---
apiVersion: v1
kind: Service
metadata:
  name: app-service-name
spec:
  selector:
    app: app-deploy
  ports:
    - name: http
      protocol: TCP
      port: 3000
      targetPort: 3000
```
```
kubectl apply -f app.yaml
```
```
kubectl port-forward services/app-service-name 3000:3000
```
![port-forward](https://github.com/Gryaznykh-Ivan/2023_2024-introduction_to_distributed_technologies-k4112c-gryaznykh_i-d/blob/master/lab4/images/5.jpg)
![Сайт](https://github.com/Gryaznykh-Ivan/2023_2024-introduction_to_distributed_technologies-k4112c-gryaznykh_i-d/blob/master/lab4/images/6.jpg)

Используемый сервис не балансирует нагрузку, поэтому name и ip приложения не меняются

### 7. ПИНГ
```
kubectl exec -it app-deploy-7cccd8db85-5l9c8 -- ping 192.168.0.129
```
![ПИНГ](https://github.com/Gryaznykh-Ivan/2023_2024-introduction_to_distributed_technologies-k4112c-gryaznykh_i-d/blob/master/lab4/images/7.jpg)

## Схема
![DIAGRAM](https://github.com/Gryaznykh-Ivan/2023_2024-introduction_to_distributed_technologies-k4112c-gryaznykh_i-d/blob/master/lab4/images/8.jpg)
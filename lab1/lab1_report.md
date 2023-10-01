University: [ITMO University](https://itmo.ru/ru/)
Faculty: [FICT](https://fict.itmo.ru)
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)
Year: 2023/2024
Group: K4112c
Author: Gryaznykh Ivan Dmitrievich
Lab: Lab1
Date of create: 01.10.2023
Date of finished: **.10.2023

# Ход выполнения работы

## Запуск Minikube:
```
minikube minikube start
```
![minikube minikube start](https://github.com/Gryaznykh-Ivan/2023_2024-introduction_to_distributed_technologies-k4112c-gryaznykh_i-d/blob/master/lab1/images/1.png)


## Создание YAML файла:
```
kind: Pod
apiVersion: v1
metadata:
  name: vault
  labels:
    app: vault-app
spec:
  containers:
    - name: vault-app
      image: vault:1.13.3
      ports:
        - containerPort: 8200
```

## Запуск Pod:
```
minikube kubectl apply -f ./lab1/app.yaml
```

## Создание сервиса:
```
minikube kubectl -- expose pod vault --type=NodePort --port=8200
```


## Прокидывание порта:
```
minikube kubectl -- port-forward service/vault 8200:8200 --address 0.0.0.0
```


## Token для авторизации можно найти в логах:
```
kubectl logs vault
```
![kubectl logs vault](https://github.com/Gryaznykh-Ivan/2023_2024-introduction_to_distributed_technologies-k4112c-gryaznykh_i-d/blob/master/lab1/images/2.png)


# Схема
![Schema](https://github.com/Gryaznykh-Ivan/2023_2024-introduction_to_distributed_technologies-k4112c-gryaznykh_i-d/blob/master/lab1/images/3.png)
University: [ITMO University](https://itmo.ru/ru/)
Faculty: [FICT](https://fict.itmo.ru)
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)
Year: 2023/2024
Group: K4112c
Author: Gryaznykh Ivan Dmitrievich
Lab: Lab3
Date of create: 01.10.2023
Date of finished: 25.10.2023



# Лабораторная работа №3 "Сертификаты и "секреты" в Minikube, безопасное хранение данных."

# Ход выполнения работы

## Запуск Minikube:
```
minikube minikube start
```
![minikube minikube start](https://github.com/Gryaznykh-Ivan/2023_2024-introduction_to_distributed_technologies-k4112c-gryaznykh_i-d/blob/master/lab2/images/7.png)

Команда "minikube start" используется для запуска локального кластера Kubernetes с использованием Minikube. После выполнения этой команды произойдет следующее:

Проверка системных требований: Minikube проверит, удовлетворяет ли ваша система минимальным требованиям для запуска Kubernetes. Это включает в себя проверку наличия гипервизора (например, VirtualBox или Hyper-V) и доступных ресурсов на вашем компьютере.

Загрузка образа: Если образ Minikube еще не был загружен на ваш компьютер, команда автоматически загрузит его. Этот образ содержит минимальное окружение Kubernetes, необходимое для работы Minikube.

Создание виртуальной машины: Minikube создаст виртуальную машину (VM) с использованием выбранного гипервизора. Эта виртуальная машина будет служить управляемым окружением Kubernetes.

Установка Kubernetes: Minikube установит выбранную версию Kubernetes внутри виртуальной машины. Версия Kubernetes может быть указана в командной строке, либо будет использована версия по умолчанию.

Запуск кластера: После успешного выполнения всех предыдущих шагов, Minikube запустит кластер Kubernetes внутри виртуальной машины. Кластер будет готов к использованию.

Настройка kubectl: Minikube настроит инструмент командной строки kubectl так, чтобы он указывал на ваш запущенный локальный кластер. Это позволит вам управлять кластером с помощью kubectl.

## Создание YAML файла:
```
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
        - name:  REACT_APP_USERNAME
          value: 'GID'
        - name:  REACT_APP_COMPANY_NAME
          value: 'itmo'
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
  - protocol: TCP
    port: 2000
    targetPort: 3000
  
```

Deployment:

apiVersion и kind определяют, что это ресурс деплоймента.
metadata содержит метаданные деплоймента, включая его имя.
spec определяет конфигурацию деплоймента:
replicas устанавливает количество реплик подов (в данном случае 2).
selector определяет, какие поды должны быть управляемы деплойментом.
template содержит шаблон для создания подов:
metadata устанавливает метки для подов.
spec определяет контейнеры, которые будут созданы в подах:
name - имя контейнера.
image - образ контейнера для развертывания.
env - переменные окружения, которые будут доступны в контейнере.
ports - определяет порты, на которых контейнер слушает внутри пода.

Service:

apiVersion и kind определяют, что это ресурс сервиса.
metadata содержит метаданные сервиса, включая его имя.
spec определяет конфигурацию сервиса:
selector указывает, какие поды будут обслуживаться этим сервисом, основываясь на метках подов.
ports определяет порты, на которых сервис будет доступен:
protocol устанавливает протокол (TCP в данном случае).
port - порт, по которому сервис будет доступен снаружи.
targetPort - порт, на котором контейнеры в подах слушают запросы.

## Применим файл конфигурации:
```
minikube kubectl apply -f app.yaml
```

Команда minikube kubectl apply -f app.yaml представляет собой команду для развертывания приложения в локальном кластере Kubernetes, управляемом Minikube.

![get all](https://github.com/Gryaznykh-Ivan/2023_2024-introduction_to_distributed_technologies-k4112c-gryaznykh_i-d/blob/master/lab2/images/3.png)


## Прокидывание порта:
```
minikube kubectl -- port-forward service/app-service-name 8080:2000 --address 0.0.0.0
```

Эта команда используется для настройки перенаправления портов (port forwarding) из локальной машины внутрь сервиса, который запущен в локальном кластере Minikube

![port-forward](https://github.com/Gryaznykh-Ivan/2023_2024-introduction_to_distributed_technologies-k4112c-gryaznykh_i-d/blob/master/lab2/images/2.png)


# Интерфейс приложения:
![WEB SITE](https://github.com/Gryaznykh-Ivan/2023_2024-introduction_to_distributed_technologies-k4112c-gryaznykh_i-d/blob/master/lab2/images/1.png)

Значения User и Company будут соответствовать переменным окружения, а имя контейнера и IP могут меняться в зависисмости от того, в какой контейнер попал запрос.

# Логи подов
![log 1](https://github.com/Gryaznykh-Ivan/2023_2024-introduction_to_distributed_technologies-k4112c-gryaznykh_i-d/blob/master/lab2/images/4.png)
![log 2](https://github.com/Gryaznykh-Ivan/2023_2024-introduction_to_distributed_technologies-k4112c-gryaznykh_i-d/blob/master/lab2/images/5.png)



# Схема
![Schema](https://github.com/Gryaznykh-Ivan/2023_2024-introduction_to_distributed_technologies-k4112c-gryaznykh_i-d/blob/master/lab2/images/6.jpg)
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
![minikube minikube start](https://github.com/Gryaznykh-Ivan/2023_2024-introduction_to_distributed_technologies-k4112c-gryaznykh_i-d/blob/master/lab3/images/1.jpg)

Команда "minikube start" используется для запуска локального кластера Kubernetes с использованием Minikube. После выполнения этой команды произойдет следующее:

Проверка системных требований: Minikube проверит, удовлетворяет ли ваша система минимальным требованиям для запуска Kubernetes. Это включает в себя проверку наличия гипервизора (например, VirtualBox или Hyper-V) и доступных ресурсов на вашем компьютере.

Загрузка образа: Если образ Minikube еще не был загружен на ваш компьютер, команда автоматически загрузит его. Этот образ содержит минимальное окружение Kubernetes, необходимое для работы Minikube.

Создание виртуальной машины: Minikube создаст виртуальную машину (VM) с использованием выбранного гипервизора. Эта виртуальная машина будет служить управляемым окружением Kubernetes.

Установка Kubernetes: Minikube установит выбранную версию Kubernetes внутри виртуальной машины. Версия Kubernetes может быть указана в командной строке, либо будет использована версия по умолчанию.

Запуск кластера: После успешного выполнения всех предыдущих шагов, Minikube запустит кластер Kubernetes внутри виртуальной машины. Кластер будет готов к использованию.

Настройка kubectl: Minikube настроит инструмент командной строки kubectl так, чтобы он указывал на ваш запущенный локальный кластер. Это позволит вам управлять кластером с помощью kubectl.

## Создание YAML файла:
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
kind: ReplicaSet
metadata:
  name: itdt-contained-frontend
spec:
  replicas: 2
  selector:
    matchLabels:
      app: itdt-contained-frontend
  template:
    metadata:
      labels:
        app: itdt-contained-frontend
    spec:
      containers:
        - name: itdt-contained-frontend
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

---
apiVersion: v1
kind: Service
metadata:
  name: app-service
spec:
  type: ClusterIP
  ports:
    - port: 3000
      targetPort: 3000
      protocol: TCP
  selector:
    app: itdt-contained-frontend
---
apiVersion: v1
kind: Secret
metadata:
  name: app-secret
type: kubernetes.io/tls
stringData:
  tls.crt: |
    -----BEGIN CERTIFICATE-----

    -----END CERTIFICATE-----
  tls.key: |
    -----BEGIN PRIVATE KEY-----

    -----END PRIVATE KEY-----
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: app-ingress
spec:
  tls:
    - hosts:
        - gidapp.com
      secretName: app-secret
  rules:
    - host: gidapp.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: app-service
                port:
                  number: 3000
```

Этот YAML файл описывает различные ресурсы Kubernetes, которые могут быть использованы для развертывания приложения. Вот краткое описание каждого блока:

1. **ConfigMap (Конфигурационная карта)**:
   - **Имя**: `app-config`
   - **Данные**: Содержит ключ-значение для параметров, таких как `REACT_APP_USERNAME` (значение: "GID") и `REACT_APP_COMPANY_NAME` (значение: "ITMO").

2. **ReplicaSet (Набор реплик)**:
   - **Имя**: `itdt-contained-frontend`
   - **Спецификация**: Создает и управляет репликами приложения `itdt-contained-frontend`.
   - **Контейнеры**: Использует образ `ifilyaninitmo/itdt-contained-frontend:master` и передает переменные среды из ConfigMap для `REACT_APP_USERNAME` и `REACT_APP_COMPANY_NAME`.

3. **Service (Сервис)**:
   - **Имя**: `app-service`
   - **Тип**: ClusterIP (доступен только внутри кластера)
   - **Порт**: Пробрасывает трафик с порта 3000 на поды, соответствующие Selector'у `app: itdt-contained-frontend`.

4. **Secret (Секрет)**:
   - **Имя**: `app-secret`
   - **Тип**: `kubernetes.io/tls`
   - **Данные**: Содержит TLS-сертификат и приватный ключ для использования в безопасной связи.

5. **Ingress (Вход)**:
   - **Имя**: `app-ingress`
   - **Спецификация TLS**: Ассоциирует секрет `app-secret` с хостом `gidapp.com` для обеспечения безопасного соединения.
   - **Правила маршрутизации**: Настроены для перенаправления трафика с хоста `gidapp.com` на сервис `app-service` через путь `/`.

## Применим файл конфигурации:
```
minikube kubectl apply -f app.yaml
```

Команда minikube kubectl apply -f app.yaml представляет собой команду для развертывания приложения в локальном кластере Kubernetes, управляемом Minikube.

![get all](https://github.com/Gryaznykh-Ivan/2023_2024-introduction_to_distributed_technologies-k4112c-gryaznykh_i-d/blob/master/lab3/images/3.jpg)


## Включим ingress и запустим tunnel 
```
minikube addons enable ingress
minikube tunnel
```

Команда minikube addons enable ingress активирует дополнение (addon) под названием Ingress в Minikube. Ingress - это механизм в Kubernetes, который управляет внешним доступом к службам внутри кластера. Активация Ingress позволяет использовать эту функциональность для маршрутизации внешнего трафика в приложения в вашем локальном кластере Minikube.
![port-forward](https://github.com/Gryaznykh-Ivan/2023_2024-introduction_to_distributed_technologies-k4112c-gryaznykh_i-d/blob/master/lab3/images/7.jpg)

Команда minikube tunnel используется для создания сетевого туннеля между вашим локальным кластером Minikube и сетью вашей машины. Это полезно, когда ваши приложения в кластере требуют внешнего доступа извне или когда они должны быть доступны из других устройств в вашей сети. minikube tunnel устанавливает соединение между внутренними IP-адресами вашего кластера и внешними IP-адресами вашей машины, обеспечивая возможность общения с сервисами в кластере через внешний IP-адрес вашей машины.

![port-forward](https://github.com/Gryaznykh-Ivan/2023_2024-introduction_to_distributed_technologies-k4112c-gryaznykh_i-d/blob/master/lab3/images/8.jpg)


# Интерфейс приложения:
![WEB SITE](https://github.com/Gryaznykh-Ivan/2023_2024-introduction_to_distributed_technologies-k4112c-gryaznykh_i-d/blob/master/lab3/images/5.jpg)

# Сертификат:
![WEB SITE](https://github.com/Gryaznykh-Ivan/2023_2024-introduction_to_distributed_technologies-k4112c-gryaznykh_i-d/blob/master/lab3/images/6.jpg)


# Схема
![Schema](https://github.com/Gryaznykh-Ivan/2023_2024-introduction_to_distributed_technologies-k4112c-gryaznykh_i-d/blob/master/lab3/images/9.jpg)
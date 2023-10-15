University: [ITMO University](https://itmo.ru/ru/)
Faculty: [FICT](https://fict.itmo.ru)
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)
Year: 2023/2024
Group: K4112c
Author: Gryaznykh Ivan Dmitrievich
Lab: Lab1
Date of create: 01.10.2023
Date of finished: 05.10.2023

# Ход выполнения работы

## Запуск Minikube:
```
minikube minikube start
```
![minikube minikube start](https://github.com/Gryaznykh-Ivan/2023_2024-introduction_to_distributed_technologies-k4112c-gryaznykh_i-d/blob/master/lab1/images/1.png)

Команда "minikube start" используется для запуска локального кластера Kubernetes с использованием Minikube. После выполнения этой команды произойдет следующее:

Проверка системных требований: Minikube проверит, удовлетворяет ли ваша система минимальным требованиям для запуска Kubernetes. Это включает в себя проверку наличия гипервизора (например, VirtualBox или Hyper-V) и доступных ресурсов на вашем компьютере.

Загрузка образа: Если образ Minikube еще не был загружен на ваш компьютер, команда автоматически загрузит его. Этот образ содержит минимальное окружение Kubernetes, необходимое для работы Minikube.

Создание виртуальной машины: Minikube создаст виртуальную машину (VM) с использованием выбранного гипервизора. Эта виртуальная машина будет служить управляемым окружением Kubernetes.

Установка Kubernetes: Minikube установит выбранную версию Kubernetes внутри виртуальной машины. Версия Kubernetes может быть указана в командной строке, либо будет использована версия по умолчанию.

Запуск кластера: После успешного выполнения всех предыдущих шагов, Minikube запустит кластер Kubernetes внутри виртуальной машины. Кластер будет готов к использованию.

Настройка kubectl: Minikube настроит инструмент командной строки kubectl так, чтобы он указывал на ваш запущенный локальный кластер. Это позволит вам управлять кластером с помощью kubectl.

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

Этот фрагмент представляет собой YAML-файл, используемый для определения конфигурации Kubernetes-пода

kind: Pod: Эта строка определяет тип Kubernetes-ресурса, который мы создаем. В данном случае это "Pod", что означает одноконтейнерный под. Поды - это наименьшие выполнимые единицы в Kubernetes, и они могут содержать один или несколько контейнеров.

apiVersion: v1: Этот параметр указывает на версию API Kubernetes, которую мы используем для определения ресурса. "v1" - это стабильная версия API, доступная в Kubernetes.

metadata: Этот раздел содержит метаданные для пода, такие как его имя и метки (labels). Метаданные помогают управлять и идентифицировать ресурс в Kubernetes.

name: vault: Здесь указывается имя пода, которое будет использоваться для идентификации его в кластере.

labels: Метки предоставляют возможность прикреплять ключевые слова или метки к подам. В данном случае у пода есть метка "app" со значением "vault-app".

spec: Этот раздел определяет спецификацию (конфигурацию) пода.

containers: Здесь определяются контейнеры, которые будут работать внутри пода. В данном случае есть один контейнер:

name: vault-app: Это имя контейнера, которое будет использоваться для идентификации контейнера внутри пода.

image: vault:1.13.3: Это образ Docker, который будет использоваться для создания контейнера. В данном случае используется образ "vault:1.13.3".

ports: Здесь определяются порты, которые будут проксироваться из контейнера в под. В данном случае, контейнер прослушивает порт 8200.

## Запуск Pod:
```
minikube kubectl apply -f ./lab1/app.yaml
```

## Создание сервиса:
```
minikube kubectl -- expose pod vault --type=NodePort --port=8200
```

Эта команда используется для создания сервиса (Service) в Kubernetes, который будет связан с определенным подом (Pod) и позволит обеспечить доступ к этому поду извне кластера

## Прокидывание порта:
```
minikube kubectl -- port-forward service/vault 8200:8200 --address 0.0.0.0
```

Эта команда используется для настройки перенаправления портов (port forwarding) из локальной машины внутрь сервиса, который запущен в локальном кластере Minikube

## Token для авторизации можно найти в логах:
```
kubectl logs vault
```
![kubectl logs vault](https://github.com/Gryaznykh-Ivan/2023_2024-introduction_to_distributed_technologies-k4112c-gryaznykh_i-d/blob/master/lab1/images/2.png)

## Интерфейс Vault:
![kubectl logs vault](https://github.com/Gryaznykh-Ivan/2023_2024-introduction_to_distributed_technologies-k4112c-gryaznykh_i-d/blob/master/lab1/images/4.png)
![kubectl logs vault](https://github.com/Gryaznykh-Ivan/2023_2024-introduction_to_distributed_technologies-k4112c-gryaznykh_i-d/blob/master/lab1/images/5.png)


# Схема
![Schema](https://github.com/Gryaznykh-Ivan/2023_2024-introduction_to_distributed_technologies-k4112c-gryaznykh_i-d/blob/master/lab1/images/3.png)
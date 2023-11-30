# 2023_2024-introduction_to_distributed_technologies-k4111c-Petryakov_P_V
University: [ITMO University](https://itmo.ru/ru/)
Faculty: [FICT](https://fict.itmo.ru)
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)

Year: 2023/2024

Group: K4111с

Author: Petryakov Pavel

Lab: Lab3

Date of create: 

Date of finished: 

## Лабораторная работа №3 "Сертификаты и "секреты" в Minikube, безопасное хранение данных."

### Цель работы
Познакомиться с сертификатами и "секретами" в Minikube, правилами безопасного хранения данных в Minikube. 

### Задачи
- Вам необходимо создать `configMap` с переменными: `REACT_APP_USERNAME`, `REACT_APP_COMPANY_NAME`.

- Вам необходимо создать `replicaSet` с 2 репликами контейнера [ifilyaninitmo/itdt-contained-frontend:master](https://hub.docker.com/repository/docker/ifilyaninitmo/itdt-contained-frontend) и используя ранее созданный `configMap` передать переменные `REACT_APP_USERNAME`, `REACT_APP_COMPANY_NAME` .

- Включить `minikube addons enable ingress` и сгенерировать TLS сертификат, импортировать сертификат в minikube. 

- Создать ingress в minikube, где указан ранее импортированный сертификат, FQDN по которому вы будете заходить и имя сервиса который вы создали ранее.

> Если вы делаете эту работу на Windows/macOS для доступа к ingress вам необходимо использовать команду `minikube tunnel` к созданному ingress. 
> Если вы делаете эту работу на Windows/macOS для доступа к ingress вам необходимо в hosts добавить ip address localhost и ваш FQDN. Если установлен Linux, то нужно указывать minikube ip.

- В `hosts` пропишите FQDN и IP адрес вашего ingress и попробуйте перейти в браузере по FQDN имени. 

- Войдите в веб приложение по вашему FQDN используя HTTPS и проверьте наличие сертификата.

> Обычно в браузере это маленький замочек рядом с FQDN сайта, нажмите на него и сделайте скриншот с информацией.

### Ход работы
Ниже представлен пошаговый ход работы 

### 1. Создается манифеста
Первым делом запускается minikube start как в прошлых лабораторных работах
```
minikube start
```
![](https://github.com/PetryakovPavel/2023_2024-introduction_to_distributed_technologies-k4111c-Petryakov_P_V/blob/main/lab3/picture/МиникубСтарт.png)

Далее создается манифест `deployment.yaml`,  там создаются  Deployment  , Service для доступа к подам. 
![](https://github.com/PetryakovPavel/2023_2024-introduction_to_distributed_technologies-k4111c-Petryakov_P_V/blob/main/lab3/picture/Деплоймент.png)

![](https://github.com/PetryakovPavel/2023_2024-introduction_to_distributed_technologies-k4111c-Petryakov_P_V/blob/main/lab3/picture/Сервис.png)

Напишем манифест ConfigMap 

![](https://github.com/PetryakovPavel/2023_2024-introduction_to_distributed_technologies-k4111c-Petryakov_P_V/blob/main/lab3/picture/КонфигМап.png)


В ConfigMap ключами выступают `react_app_user_name` и `react_app_company_name`, значения `Petryakov Pavel` и `ITMO`, соответственно.
Поскольку `Ingress` работает с сервисами типа `nodePort` или `LoadBalancer`. Указан явно в сервисе тип. 

Задеплоим написаные нами манифесты

![](https://github.com/PetryakovPavel/2023_2024-introduction_to_distributed_technologies-k4111c-Petryakov_P_V/blob/main/lab3/picture/деплоймент.png)

![](https://github.com/PetryakovPavel/2023_2024-introduction_to_distributed_technologies-k4111c-Petryakov_P_V/blob/main/lab3/picture/конфиг.png)

SSL – Secure Sockets Layer. 
TLS – Transport Layer Security. 
Сертификат представляет собой технологию безопасности, с помощью которой шифруется связь между браузером и сервером. Из-за такого сертификата сложнее украсть или подменить данные пользователей. SSL/TLS-сертификат устанавливают на сервер. Помимо шифрования всех коммуникаций, с его помощью можно проверить подлинность веб-сайта.
Теперь создается TLS сертификат. Обращаем внимание на CN, там мы вписываеится имя хоста, для которого предназначен сертификат
```
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=petryakov.itmo"
```

![](https://github.com/PetryakovPavel/2023_2024-introduction_to_distributed_technologies-k4111c-Petryakov_P_V/blob/main/lab3/picture/Снимок%20экрана%20от%202023-11-30%2014-35-52.png)


Secret содержит небольшое количество конфиденциальных данных, таких как пароль, токен или ключ.
```
kubectl create secret tls tls-secret --cert=tls.crt --key=tls.key
```
![](https://github.com/PetryakovPavel/2023_2024-introduction_to_distributed_technologies-k4111c-Petryakov_P_V/blob/main/lab3/picture/Создание%20секрет.png)

Проверяем:
```
kubectl get secret
```
![](https://github.com/PetryakovPavel/2023_2024-introduction_to_distributed_technologies-k4111c-Petryakov_P_V/blob/main/lab3/picture/секрет.png)

Ingress

`Ingress` в Kubernetes - это объект, который управляет входящим сетевым трафиком и обеспечивает доступ к `Services` внутри кластера. `Ingress` предоставляет механизм для настройки маршрутизации внешнего трафика на pod'ы внутри кластера на основе правил, определенных в самом `Ingress`.

Сперва его надо подключить:
```
minikube addons enable ingress
```

![](https://github.com/PetryakovPavel/2023_2024-introduction_to_distributed_technologies-k4111c-Petryakov_P_V/blob/main/lab3/picture/Ингресс%20старт.png)

Теперь создаем манифест Ingress.

![](https://github.com/PetryakovPavel/2023_2024-introduction_to_distributed_technologies-k4111c-Petryakov_P_V/blob/main/lab3/picture/Ингрес.png)

деплоим манифест
```
kubectl apply -f ingress.yaml
```
![](https://github.com/PetryakovPavel/2023_2024-introduction_to_distributed_technologies-k4111c-Petryakov_P_V/blob/main/lab3/picture/Инресссс.png)



Далее, чтоб перейти в сервис, нужно вписать в hosts IP и наше доменное имя (FQDN) - `petryakovitmo.com` :

```
sudo nano /etc/hosts
```
![](https://github.com/PetryakovPavel/2023_2024-introduction_to_distributed_technologies-k4111c-Petryakov_P_V/blob/main/lab3/picture/Хостс.png)

Перенаправляем трафик командой: 
```
minikube tunnel
```
![](https://github.com/PetryakovPavel/2023_2024-introduction_to_distributed_technologies-k4111c-Petryakov_P_V/blob/main/lab3/picture/Тунел.png)

Переходим в сервис по доменному имени :

![](https://github.com/PetryakovPavel/2023_2024-introduction_to_distributed_technologies-k4111c-Petryakov_P_V/blob/main/lab3/picture/сайт.png)

Проверям сертификат

![](https://github.com/PetryakovPavel/2023_2024-introduction_to_distributed_technologies-k4111c-Petryakov_P_V/blob/main/lab3/picture/Сертифик.png)

### Схема


![](https://github.com/PetryakovPavel/2023_2024-introduction_to_distributed_technologies-k4111c-Petryakov_P_V/blob/main/lab3/picture/Схема.png)

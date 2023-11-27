# 2023_2024-introduction_to_distributed_technologies-k4111c-Petryakov_P_V
University: [ITMO University](https://itmo.ru/ru/)
Faculty: [FICT](https://fict.itmo.ru)
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)
Year: 2023/2024
Group: K4111с
Author: Petryakov Pavel
Lab: Lab2
Date of create:
Date of finished:


## Лабораторная работа №2 "Развертывание веб сервиса в Minikube, доступ к веб интерфейсу сервиса. Мониторинг сервиса."

### Цель работы
Ознакомиться с типами "контроллеров" развертывания контейнеров, ознакомится с сетевыми сервисами и развернуть свое веб приложение.

### Задачи
1. Создать `deployment` с 2 репликами контейнера [ifilyaninitmo/itdt-contained-frontend:master](https://hub.docker.com/repository/docker/ifilyaninitmo/itdt-contained-frontend) и передаются переменные в эти реплики: `REACT_APP_USERNAME`, `REACT_APP_COMPANY_NAME`.
2. Создать сервис через который будет доступ на эти "поды"
3. Запустить в `minikube` режим проброса портов и подключиться к нашим контейнерам через веб браузер
4. Проверить на странице в веб браузере переменные REACT_APP_USERNAME, REACT_APP_COMPANY_NAME и Container name. Изменяются ли они? Если да то почему?
5. Проверьте логи контейнеров.

### Ход работы
Ниже представлен пошаговый ход работы 

### 1. Запуск minikube
Первым делом, запускается minikube. 

![minikube](https://github.com/PetryakovPavel/2023_2024-introduction_to_distributed_technologies-k4111c-Petryakov_P_V/blob/main/lab2/picture/стартМиникуб.png)

### 2. Создание манифеста

Создадим  манифест `deployment` . В нем описана конфигурация развертывания веб-сервиса. 
Назначение полей в `spec`:
* `replicas` отвечает за количество реплик контейнеров, которые необходимо создать, т.е. в нашем случае 2
* `selector` указывает `ReplicaSet` (которая создается деплойментом), какими подами она сможет управлять. 
* `template` описывает шаблон подов, которые необходимо создать. 
* `env` позволяет выставить переменные окружения внутри контейнеров.
  
![manifest](https://github.com/PetryakovPavel/2023_2024-introduction_to_distributed_technologies-k4111c-Petryakov_P_V/blob/main/lab2/picture/Manifest.png)

### 3. Запуск
С помощью следующей команды на API k8s кластера отправляется информация о том, что необходимо создать:
```
kubectl apply -f deployment.yaml
```

![deploy](https://github.com/PetryakovPavel/2023_2024-introduction_to_distributed_technologies-k4111c-Petryakov_P_V/blob/main/lab2/picture/Деплой.png)

Удостоверяемся, что все создалось с помощью следующих команд:
```
kubectl get deployments,rs,pods

```

![check](https://github.com/PetryakovPavel/2023_2024-introduction_to_distributed_technologies-k4111c-Petryakov_P_V/blob/main/lab2/picture/Проверка.png)

Как видим, все успешно запустилось.

### 4. Подключение к контейнерам

Сначала запускаем в minikube режим проброса портов с помощью следующей команды:
```
kubectl port-forward service/app 3000:3000

```

![port](https://github.com/PetryakovPavel/2023_2024-introduction_to_distributed_technologies-k4111c-Petryakov_P_V/blob/main/lab2/picture/Запущенное.png)

Переходим на http://localhost:3000/ , отлично, все работает

![](https://github.com/PetryakovPavel/2023_2024-introduction_to_distributed_technologies-k4111c-Petryakov_P_V/blob/main/lab2/picture/сайт.png)

Ответ на вопросы :
Переменные `REACT_APP_USERNAME`, `REACT_APP_COMPANY_NAME` и `Container name`. Изменяются ли они? Если да, то почему?
Значения переменных не изменяются, они соответствуют переданным в манифесте значениям. Название пода и IP изменяется, в зависимости от того, в какой под идет запрос. 
В браузере отображается информация одной из наших реплик.

### 5. Проверка логов 

Смотрим логи с помощью команды:

```
kubectl logs app-d666448d6-m9l2k
```

![logs](https://github.com/PetryakovPavel/2023_2024-introduction_to_distributed_technologies-k4111c-Petryakov_P_V/blob/main/lab2/picture/Логи.png)

Логи одинаковые

### 6. Схема организации контейеров и сервисов нарисованная в draw.io

![scheme](https://github.com/PetryakovPavel/2023_2024-introduction_to_distributed_technologies-k4111c-Petryakov_P_V/blob/main/lab2/picture/Схема.png)

# 2023_2024-introduction_to_distributed_technologies-k4111c-ishutina_e
University: [ITMO University](https://itmo.ru/ru/)
Faculty: [FICT](https://fict.itmo.ru)
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)
Year: 2023/2024
Group: K4111с
Author: Petryakov Pavel
Lab: Lab1



## Лабораторная работа №1 "Установка Docker и Minikube, мой первый манифест."

### Цель работы
Ознакомиться с инструментами Minikube и Docker, развернуть свой первый "Pod".

### Задачи
1. Установить Docker, minikube
2. Развернуть minikube cluster
3. Создать под Vault и получить доступ к контейнеру
4. Найти сгенерированный корневой токен, чтобы получить доступ к Vault.

### Ход работы
Ниже представлен пошаговый ход работы 

### 1. Установка Docker и minikube
Изначально был установлен Docker Desktop,Minikube,VirtualBox для Ubuntu

### 2. Разворачиваем minikube cluster
После установки проверим версию миникуба 
```bash
minikube version
```
Развернем minikube cluster с помощью команды minikube start:
```bash
minikube start
```
![версия миникуба](https://github.com/PetryakovPavel/2023_2024-introduction_to_distributed_technologies-k4111c-Petryakov_P_V/blob/main/lab1/picture/версия%20миникуба.png)

Далее выполняются команды, которые добавляют на нашей локальной машине в список образов образ нужного ПО - Vault.
```bash
docker pull vault:1.13.3
docker images
```
Теперь посмотрим список образов с помощью следующих команд:
```bash
#просмотреть в minikube список всех образов
minikube image ls
```
![images](images.png)

В конце мы видим, что есть нужный нам образ vault:1.13.3

### 3. Написание манифеста для развертывания "пода" HashiCorp Vault
Следующим шагом было написание манифеста для развертывания "пода" (наименьший объект рабочей нагрузки)  HashiCorp Vault, и при этом прокинуть внутрь порт 8200.
Манифест (vault.yaml) прилагается в папке lab1.
Манифест составлялся в соответствии с object model Kubernetes.
Обязательные поля:
- `apiVersion` - используемая версия API;
- `kind` - тип описываемого объекта;
- `metadata` - метаданные;
- `spec` - конфигурация объекта;

### 4. Создание пода Vault и получение доступа к контейнеру
Далее запускается описанный под с помощью команды:
```bash
minikube kubectl -- apply -f vault.yaml
```
![launch](launch.png)

Далее необходимо создать сервис для доступа к этому контейнеру, воспользуемся простым способом, с помощью команды:
```bash
minikube kubectl -- expose pod vault --type=NodePort --port=8200
```
Сервис создался, далее нужно попасть в контейнер с помощью команды `kubectl port-forward`:
```bash
minikube kubectl -- port-forward service/vault 8200:8200
```
Данная команда перенаправляет трафик с клиентского устройства по указанному порту (`8200`) на указанный порт (`8200`) сервиса пода vault (`service/vault`).
Minikube прокинул порт нашего компьютера в контейнер и теперь можем зайти в vault по ссылке http://localhost:8200
![vault](entry.png)

### 5. Найти сгенерированный корневой токен, чтобы получить доступ к Vault
Теперь необходимо войти в наш vault ипользуя токен, который нам необходимо НАЙТИ, а не сгенерировать.
Для поиска токена, необходимого для входа воспользуемся `kubectl logs`, который используется для получения логов из контейнера в указанном поде:
```bash
minikube kubectl logs vault
```
![token](token.png)

Копируем данные Root Token и используем для входа в наш vault:

![entry](entry.png)

Ответы на вопросы:
1. Что сейчас произошло и что сделали команды ранее?
Изначально развернули кластер. Далее по написанному манифесту, в нем развернут "под" с контейнером, запущенным из образа vault и развернут 1 сервис для подключения к приложению

2. Где взять токен для входа в Vault?
Как было сказано раннее, в логах "пода"


Схема организации контейеров и сервисов 
![diagram](diagram.png)



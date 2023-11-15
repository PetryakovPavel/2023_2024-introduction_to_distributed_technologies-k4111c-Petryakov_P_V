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
![version](https://github.com/PetryakovPavel/2023_2024-introduction_to_distributed_technologies-k4111c-Petryakov_P_V/blob/main/lab1/picture/версия%20миникуба.png)


Развернем minikube cluster с помощью команды minikube start:
```bash
minikube start
```
![start](https://github.com/PetryakovPavel/2023_2024-introduction_to_distributed_technologies-k4111c-Petryakov_P_V/blob/main/lab1/picture/запуск%20миникуба.png)


Дальше выполним  команды, которые добавляют на нашей локальной машине в список образов образ нужного ПО - Vault.
```bash
docker pull vault:1.13.3
docker images
```
![pull](https://github.com/PetryakovPavel/2023_2024-introduction_to_distributed_technologies-k4111c-Petryakov_P_V/blob/main/lab1/picture/добавление%20образа.png)



### 3. Написание манифеста для развертывания "пода" HashiCorp Vault
Теперь мы пишем  манифест для развертывания "пода"  HashiCorp Vault в Visual Studio Code.
Манифест (vault.yaml) прилагается в папке lab1.

Обязательные поля для манифеста:
- `apiVersion` - используемая версия API;
- `kind` - тип описываемого объекта;
- `metadata` - метаданные;
- `spec` - конфигурация объекта;

![pod](https://github.com/PetryakovPavel/2023_2024-introduction_to_distributed_technologies-k4111c-Petryakov_P_V/blob/main/lab1/picture/манифест.png)

### 4. Создание пода Vault и получение доступа к контейнеру
Теперь запустим написанный нами под с помощью команды:
```bash
minikube kubectl -- apply -f vault.yaml
```
![apply](https://github.com/PetryakovPavel/2023_2024-introduction_to_distributed_technologies-k4111c-Petryakov_P_V/blob/main/lab1/picture/создание%20пода.png)

Далее необходимо создать сервис для доступа к этому контейнеру, для этого мы  воспользуемся  командой:
```bash
minikube kubectl -- expose pod vault --type=NodePort --port=8200
```
![service](https://github.com/PetryakovPavel/2023_2024-introduction_to_distributed_technologies-k4111c-Petryakov_P_V/blob/main/lab1/picture/сервис.png)


Сервис создался, далее нужно попасть в контейнер с помощью команды `kubectl port-forward`:
```bash
minikube kubectl -- port-forward service/vault 8200:8200
```
![start](https://github.com/PetryakovPavel/2023_2024-introduction_to_distributed_technologies-k4111c-Petryakov_P_V/blob/main/lab1/picture/запущенный%20сайт.png)


Данная команда перенаправляет трафик с клиентского устройства по указанному порту (`8200`) на указанный порт (`8200`) сервиса пода vault (`service/vault`).
Minikube прокинул порт нашего компьютера в контейнер и теперь мы можем зайти в vault по ссылке http://localhost:8200

![vault](https://github.com/PetryakovPavel/2023_2024-introduction_to_distributed_technologies-k4111c-Petryakov_P_V/blob/main/lab1/picture/сайт.png)

### 5. Найти сгенерированный корневой токен, чтобы получить доступ к Vault
Теперь необходимо войти в наш vault используя токен, который нам необходимо найти, а не сгенерировать.
Для поиска токена, необходимого для входа воспользуемся командой `kubectl logs`, которая позволяет нам получить логи из контейнера в указанном поде:

```bash
minikube kubectl logs vault
```
![token](https://github.com/PetryakovPavel/2023_2024-introduction_to_distributed_technologies-k4111c-Petryakov_P_V/blob/main/lab1/picture/логи.png)

Вот необходимый нам токен

![tocken](https://github.com/PetryakovPavel/2023_2024-introduction_to_distributed_technologies-k4111c-Petryakov_P_V/blob/main/lab1/picture/токен.png)


Копируем данные Root Token и используем для входа в наш vault:

![entry](https://github.com/PetryakovPavel/2023_2024-introduction_to_distributed_technologies-k4111c-Petryakov_P_V/blob/main/lab1/picture/Sait.png)

Ответы на вопросы:
1. Что сейчас произошло и что сделали команды ранее?
 Мы развернули кластер minikube, загрузили для docker образ Vault. Написали манифест и развернули Pod с контейнером,запущенным из образа vault и развернули 1 сервис для подключения к приложению.   
2. Где взять токен для входа в Vault?
Для входа в Vault необходимо зайти в логи  и найти в них токен.


Схема организации контейеров и сервисов 
![diagram](https://github.com/PetryakovPavel/2023_2024-introduction_to_distributed_technologies-k4111c-Petryakov_P_V/blob/main/lab1/Диаграмма%20.png)



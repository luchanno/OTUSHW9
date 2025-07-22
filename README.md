# Реализация паттерна SAGA (распределенные транзакции) 

## SAGA

Создано 5 сервисов:
* Сервис пользователей (MSUSERS)
* Сервис заказов (Order)
* Сервис биллинга (Billing)
* Сервис склада (Inventory)
* Сервис доставки (Delivery)

Формирование заказа идет в следующей последовательности:
* Создается заказ в статусе NEW (сервис Order)
* Снимаются деньги со счета (сервис Billing)
* Снимается позиция на складе (сервис Inventory)
* Создается запись о доставке (сервис Delivery)
* Заказ переводится в статус CONFIRMED

В случае любой из проблем происходит откат предыдущих шагов

Реализовано по паттерну SAGA-хореография, с использованием брокера сообщений Kafka

См. диаграмму в папке Diagrams

## UPD: Добавлена индемпотентость создания заказа

Метод создания заказа /orders/createOrder требует обязательного параметра в HTTP Header "rqid" типа UUID

При попытке создать заказ в течение 120 секунд с таким же "rqid" вместо создания заказа будет выдаваться созданный ранее.


# Установка
## 1) Установить в k8s ingress-nginx (опционально, если отсутствует)
```kubectl create namespace m && helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx/ && helm repo update && helm install nginx ingress-nginx/ingress-nginx --namespace m -f nginx/0_OPT_nginx_ingress-25239-20146a.yaml```

## 2) Создать отдельный namespace

```kubectl create ns msusers-ns```

## 3) Установить Kafka

kubectl create -f ./kafka/resources/zookeeper.yml && kubectl create -f ./kafka/resources/kafka.yml

## 4) Развернуть БД в k8s

```helm install ./DB/helm-chart --generate-name```

## 5) Провести миграцию данных в БД (опционально, в первый раз!)

```kubectl apply -f DB/migr```

## 6) Развернуть сервисы в k8s

```kubectl apply -f Delivery/k8s && kubectl apply -f Inventory/k8s && kubectl apply -f Billing/k8s && kubectl apply -f Order/k8s && kubectl apply -f MSUSERS/k8s```

## 7) Подождать окончания деплоя 10 минут

# Tecтирование

## Postman

Рекомендуется установить расширение для newman `npm install -g newman-reporter-htmlextra`

* Коллекция: https://www.postman.com/alexkorkhov/public/collection/794gnl5/otus-saga-8
* Сценарий: https://www.postman.com/alexkorkhov/public/collection/kx2umvz/otus-saga-8-tests
* Проверка коллекции: `newman run testing/OTUS-SAGA-8-TESTS.postman_collection.json -r htmlextra` 
* Результат выполнения: `newman/OTUS-SAGA-8-TESTS-2025-07-22-17-30-40-141-0.html`


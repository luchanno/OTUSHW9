1) Установить в k8s ingress-nginx (опционально, если отсутствует)

kubectl create namespace m && helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx/ && helm repo update && helm install nginx ingress-nginx/ingress-nginx --namespace m -f nginx/0_OPT_nginx_ingress-25239-20146a.yaml

1.5) Kafka

2) Создать отдельный namespace
 
kubectl create ns msusers-ns

3)Развернуть БД в k8s

helm install ./DB/helm-chart --generate-name

4) Провести миграцию данных в БД (опционально, в первый раз!)
kubectl apply -f DB/migr

8) Развернуть приложение msusers в k8s

kubectl apply -f MSUSERS/k8s


7) Развернуть приложение order в k8s

kubectl apply -f Order/k8s

6) Развернуть приложение billing в k8s

kubectl apply -f Billing/k8s

5) Развернуть приложение inventory в k8s

kubectl apply -f Inventory/k8s


5) Развернуть приложение delivery в k8s

kubectl apply -f Delivery/k8s














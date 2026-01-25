Перед началом надо добавить в /etc/hosts запись:

127.0.0.1 wallet.example.com

# create minikube

minikube start --driver=docker

# enable addos:

pod metrics

minikube addons enable metrics-server

ingress

minikube addons enable ingress

# apps

cd chart && helmfile apply

sudo minikube tunnel

база данных создается внутри кластера без pv + pvc потому что дз направленно не на правильное создание базы. В идеале я
бы развернул istio + sa на базу на железке (например развернутую локально в контейнере докера с volume), но это делать
дольше, чем создать тестовую базу в манифесте

# Prometheus

Прометеус описан в prometheus-monitoring и docker-compose. Для того чтобы поднять прометеус - в корне репозитория:
docker-compose up. После этого надо зайти в ui по адресу http://localhost:9090

Если приложение запущено, то в Status -> Targets будет ![img.png](images/target.png)

## Запросы

В Graph можно вводить следующие запросы / строить панели:

RPS по методам:
`sum by(method, uri) (rate(http_server_requests_seconds_count[1m]))`
![img.png](images/rps.png)

99-й перцентиль по методам и uri:

```
histogram_quantile(0.99,
  sum by(le, method, uri) (rate(http_server_requests_seconds_bucket[5m]))
)
```

![img.png](images/quantile.png)

Активные соединения с БД: `hikaricp_connections_active`
![img.png](images/connections.png)


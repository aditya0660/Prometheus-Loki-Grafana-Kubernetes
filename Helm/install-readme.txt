how to install Loki prometheus and grafana

Step -1 

kubectl create ns dev

++++++++++++++++++++++++++++++++++++++++++++++

Step-2

helm repo add grafana https://grafana.github.io/helm-charts 

helm repo update

++++++++++++++++++++++++++++++++++++++++++++++

install command

helm upgrade --install loki-stack grafana/loki-stack   -n dev   --create-namespace   --set loki.config.table_manager.retention_deletes_enabled=true --set loki.config.table_manager.retention_period=720h   --set prometheus.prometheusSpec.retention=30d   -f loki-stack-values.yaml

kubectl -n dev edit deployment loki-stack-prometheus-server

change from 15d to 30d

update retention period of prometheus manually from deployment prometheus server deployment file if it's incorrect.


+++++++++++++++++++++++++++++++++++++++++++

patch for alertmanger

kubectl patch pv alertmanager-pv -p '{
  "spec": {
    "claimRef": {
      "namespace": "dev",
      "name": "storage-loki-stack-alertmanager-0"
    }
  }
}'

kubectl get pvc storage-loki-stack-alertmanager-0 -n dev

+++++++++++++++++++++++++++++++++++++++++++

nano loki-stack-vakues.yaml

prometheus:
  enabled: true
  server:
    persistentVolume:
      enabled: true
      existingClaim: prometheus-pvc

  alertmanager:
    enabled: true
    alertmanagerSpec:
      storage:
        volumeClaimTemplate:
          enabled: false
        existingClaim: alertmanager-pvc

loki:
  enabled: true
  persistence:
    enabled: true
    existingClaim: loki-pvc

promtail:
  enabled: true

grafana:
  enabled: true
  adminPassword: "admin"
  persistence:
    enabled: false

  grafana.ini:
    server:
      root_url: "%(protocol)s://%(domain)s/mon/"
      serve_from_sub_path: true

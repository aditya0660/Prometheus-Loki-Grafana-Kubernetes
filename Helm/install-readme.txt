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


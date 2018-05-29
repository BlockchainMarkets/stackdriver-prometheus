make
make docker

docker tag gcr.io/prometheus-to-sd/stackdriver-prometheus:master  us.gcr.io/bmi-da-181915/stackdriver-prometheus
docker push us.gcr.io/bmi-da-181915/stackdriver-prometheus
gcloud container images list-tags us.gcr.io/bmi-da-181915/stackdriver-prometheus
prometheus --config.file=/etc/prometheus/prometheus.yml

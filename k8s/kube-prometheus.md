git clone https://github.com/prometheus-operator/kube-prometheus.git

k create -f manifests/setup

k apply -f manifests
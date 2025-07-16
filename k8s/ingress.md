## AWS
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.12.3/deploy/static/provider/aws/deploy.yaml

## Baremetal
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.12.3/deploy/static/provider/baremetal/deploy.yaml


kubectl wait --namespace ingress-nginx \
--for=condition=ready pod \
--selector=app.kubernetes.io/component=controller \
--timeout=120s

## Link Ref
https://kubernetes.github.io/ingress-nginx/deploy/#bare-metal-clusters
https://kubernetes.github.io/ingress-nginx/deploy/#aws
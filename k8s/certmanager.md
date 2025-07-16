### Link ref
[text](https://cert-manager.io/)

Para usar o lets encrypt, usaremos a conf genérica ACME.

ISSUER -> valido apenas para o namespace atual
CLUSTER-ISSUER -> valido para todo o cluster


### Install
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.18.0/cert-manager.yaml


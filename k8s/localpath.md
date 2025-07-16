# Local Path Provisioner
kubectl apply -f https://raw.githubusercontent.com/rancher/local-path-provisioner/v0.0.31/deploy/local-path-storage.yaml
storageclass.kubernetes.io/is-default-class: true

k create secret tls tls-secret --cert=certificado.crt --key=chave-privada.key
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout chave-privada.key -out certificado.crt

k create configmap nginx-config --from-file nginx.conf --dry-run=client -o yaml > configmap.yaml
k create secret tls tls-secret --cert=certificado.crt --key=chave-privada.key --dry-run=client -o yaml > secret=tls.yaml
# Aplicando autenticação no ingress


## YAML do ingress
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: giropops-senhas
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
    nginx.ingress.kubernetes.io/auth-type: "basic"
    nginx.ingress.kubernetes.io/auth-secret: "senha-users"
    nginx.ingress.kubernetes.io/auth-realm: "Autenticação necessária"
spec:
  ingressClassName: nginx
  rules:
  - host: giropops-senhas.io
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: giropops-senhas
            port:
              number: 5000

## Configuração

Utilize o htpasswd:

htpasswd -c auth usuário senha
    repete a senha

## transforme a senha em uma secret (com o mesmo nome uqe vc citou no yaml)

k create secret generic senha-user --from-file-auth (auth é o arquivo criado pelo htpasswd)
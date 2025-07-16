# RBAC

## Criando um certificado

Primeira coisa que precisamos é criar uma chave privada para o nosso usuário. Para isso, vamos utilizar o comando openssl:
```
openssl genrsa -out developer.key 2048
```

Com a chave criada, precisa agora criar a um CSR, ou Certificate Signing Request, que é um arquivo que contém o certificado que criamos, e que será enviado para o Kubernetes assinar e gerar o certificado final.
```
openssl req -new -key developer.key -out developer.csr -subj "/CN=developer"
```
O parametro req indica que queremos criar um certificado, o parametro -key indica o nome do arquivo da chave privada que queremos utilizar, o parametro -out indica o nome do arquivo que queremos salvar o certificado, e o parametro -subj indica o nome do usuário que queremos criar.

Mas para que possamos criar o arquivo, precisamos antes ter o conteúdo do certificado em base64, para isso, vamos utilizar o comando base64:
```
cat developer.csr | base64 | tr -d '\n'
```
Lembre-se de remover a quebra de linha do final do arquivo, representada pelo %.

Agora que já temos o conteúdo do certificado em base64, copie ele e cole no arquivo [developer.yaml](../devops/pick/rbac/developer.yaml)

### No arquivo acima, estamos definindo as seguintes informações:

- apiVersion: Versão da API que estamos utilizando para criar o nosso usuário.
- kind: Tipo do recurso que estamos criando, no caso, um CSR.
- metadata.name: Nome do nosso usuário.
- spec.request: Conteúdo do certificado em base64.
- spec.signerName: Nome do assinador do certificado, que no caso é o kube-apiserver, que será o responsável por assinar o nosso certificado.
- spec.expirationSeconds: Tempo de expiração do certificado, que no caso é de 1 ano.
- spec.usages: Tipo de uso do certificado, que no caso é client auth.

Ainda precisamos aprovar o certificado:
```
kubectl certificate approve developer
```

Pronto, o nosso certificado foi assinado com sucesso, agora podemos pegar o certificado do nosso usuário e salvar em um arquivo, para isso, vamos utilizar o comando kubectl get csr:
```
kubectl get csr developer -o jsonpath='{.status.certificate}' | base64 --decode > developer.crt
```
No comando acima, estamos pegando o certificado do nosso usuário, convertendo ele para base64, e salvando ele no arquivo developer.crt.

Para pegar o certificado, estamos usando o parametro -o jsonpath='{.status.certificate}', para que o comando retorne apenas o certificado do usuário, e não todas as informações do CSR.

## ApiGroups

```
kubectl api-resources
```

```
kubectl api-resources | grep pods
```

### Onde:
- NAME: Nome do recurso.
- SHORTNAMES: Nome abreviado do recurso.
- APIVERSION: Versão da API que o recurso está.
- NAMESPACED: Indica se o recurso é ou não Namespaced.
- KIND: Tipo do recurso.

### Verbos (ações)
```
kubectl api-resources -o wide

# create: Permite que o usuário crie um recurso.
# delete: Permite que o usuário delete um recurso.
# deletecollection: Permite que o usuário delete uma coleção de recursos.
# get: Permite que o usuário obtenha um recurso.
# list: Permite que o usuário liste os recursos.
# patch: Permite que o usuário atualize um recurso.
# update: Permite que o usuário atualize um recurso.
# watch: Permite que o usuário acompanhe as alterações de um recurso.
```

## Criando a Role
Agora que já sabemos muito sobre os resources, apiGroups e verbs, vamos criar a nossa Role para o nosso usuário.

Para isso, vamos criar um arquivo chamado [developer-role.yaml](../devops/pick/rbac/developer-role.yaml).## Adicionando o cert do usuário no kubeconfig
```
kubectl config set-credentials developer --client-certificate=developer.crt --client-key=developer.key --embed-certs=true
```
O comando kubectl config set-credentials é utilizado para adicionar um novo usuário no kubeconfig, e ele recebe os seguintes parametros:

--client-certificate: Caminho do certificado do usuário.
--client-key: Caminho da chave do usuário.
--embed-certs: Indica se o certificado deve ser embutido no kubeconfig.

No nosso caso, estamos passando o caminho do certificado e da chave do usuário, e estamos indicando que o certificado deve ser embutido no kubeconfig.

Agora precisamos criar um contexto para o nosso usuário, para isso, vamos utilizar o comando kubectl config set-context:
```
kubectl config set-context developer --cluster=NOME-DO-CLUSTER --namespace=dev --user=developer
```
```
k auth can-i --list
```

## Mais uma vez, gerando certificado para fixar.
```
openssl genrsa -out platform.key 2048
openssl req -new -key platform.key -out platform.csr -subj "/CN=platform/O=platform"
```

Crie o plataform-csr-yaml

### Aprove 
```
kubectl certificate approve platform
```

### Pegue o certificado do usuário
```
kubectl get csr platform -o jsonpath='{.status.certificate}' | base64 --decode > platform.crt
```

### Bora aplicar os arquivos:
```
kubectl apply -f platform-clusterrole.yaml
kubectl apply -f platform-clusterrolebinding.yaml
```
### Para verificar se os recursos foram criados com sucesso, vamos listar os ClusterRoles e ClusterRoleBindings do cluster:
```
kubectl get clusterroles
kubectl get clusterrolebindings
```

### Agora vamos adicionar o certificado do usuário no kubeconfig:
```
kubectl config set-credentials platform --client-certificate=platform.crt --client-key=platform.key --embed-certs=true
```
### Falta agora criar o contexto para o usuário:
```
kubectl config set-context platform --cluster=NOME-DO-CLUSTER --user=platform
```
### E já era! Agora vamos testar o acesso ao cluster:
```
kubectl config use-context platform
kubectl get pods --all-namespaces
```
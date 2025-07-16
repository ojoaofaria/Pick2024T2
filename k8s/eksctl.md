### Criar o cluster
```
eksctl create cluster --name=eks-girus --version=1.33 --region=us-west-2 --nodegroup-name=eks-girus-nodegroup --node-type=t3.medium --nodes=2 --nodes-min=1 --nodes-max=3 --managed
```

### delete cluster
```
eksctl delete cluster --name=eks-girus --region=us-west-2
```
### Comandos uteis
k config get-context
k config current-context
k config use-context nome-do-contexto

### Addon network policy
eksctl create addon --name vpc-cni --version v1.16.0-eksbuild.1 --cluster nome-do-cluster --force

[VPC-CNI PAGE](https://docs.aws.amazon.com/eks/latest/userguide/managing-vpc-cni.html)


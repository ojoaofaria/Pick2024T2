# Taints
Taints são aplicados nos nodes:
NoSchedule == Não inicia mais nada nesse node
NoExecute == para de excutar o que está lá e tranfere para outro

Exemplo de aplicação de taint:
### k taint node nomde-do-node uma-tag-de-facil-identificação=true:[NoExecute ou NoSchedule....]
k taint node rhoades manutencao=true:NoExecute

Para remover a taint, repita o comando colocando sinal de "-" no final
k taint node rhoades manutencao=true:NoExecute-

### OBS: quando um node é colocado em TAINT e volta, os pods não são retornados a ele, é preciso ser feito manualmente, por exemplo:
k rollout restart deployment nginx

# Toleration
Tolerations também são aplicados nos nodes, quando há uma taint no node, voce precisa colocar uma toleration no código para que o pod seja alocado em um node com taint.
### OBS: Os pods serão executado em todos os nodes que não forem marcados e também naqueles que o toleration indicar.

tolerations:
- key: "gpu"
  operator: "Equal"
  value: "true"
  effect: "NoSchedule"

# Affinit
Affinits usam labels para criar afinidades e selecionar onde os pods serão executados

requiredDuringSchedulingIgnoredDuringExecution
preferedDuringSchedulingIgnoredDuringExecution

affinity:
  nodeAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
      nodeSelectorTerms:
      - matchExpressions:
        - key: "job"
          operator: "In"
          values:
          - investidor

# AntiAffinity
Ao contrário da afinidade, se for definida ela impede que os pods sejam executados caso o critério seja igual

affinity:
  podAntiAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
    - labelSelector
        matchLabels:
          app: nginx
      topologyKey: "region"

## Labels
k label nodes nome-node region=us-east-1 (chave:valor)
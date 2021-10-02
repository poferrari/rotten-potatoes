# Comandos

Instalar kind
* https://kind.sigs.k8s.io/

Instalar kubectl
* https://kubernetes.io/docs/tasks/tools/

Baixar imagem
* docker pull kindest/node:v1.21.1

Criar cluster Kubernetes
* kind create cluster --name meucluster --config cluster.yaml
- tem o arquivo de configuração de contexto, configura de maneira automático para kubectl acessar o cluster Kubernetes
- configura um load balancer, cenário de alta disponibilidade

Listar os nodes do Cluster
* kubectl get nodes

Listar quais são os clusters criados pelo Kind
* kind get clusters

Ver como foi criado o container
* docker container ls
- Terá o haproxy - load balancer
- Terá os control-plane e os worker
- Verificar o bind de porta

Excluir cluster
* kind delete cluster --name meucluster

## Comandos Pods
- executa os containers no kubernetes
- não traz escalabilidade
- não traz resiliência
- não traz gerenciamento de versão

Consultar grupo de recursos
* kubectl api-resources

Aplicar o manifesto do pod no Kubernetes
* kubectl create -f meupod.yaml
* kubectl apply -f meupod.yaml

Listar Pods - Pod rodando no cluster kubernetes
* kubectl get pods

Detalhes do Pod
* kubectl describe pod meupod
- mostra o nome, qual node do cluster está rodando, porta, imagem utilizada, eventos que aconteceram
- descrever melhor o objeto

Redirecionar qualquer chamada da porta 8080 para a porta 80 do container
* kubectl port-forward pod/meupod 8080:80
- parecido com o port bind
- faz o redirecionamento da porta local para a porta do pod

Deletar o Pod do cluster
* kubectl delete pod meupod
- não recria o pod, não tem resiliência neste cenário
- não tem escalabilidade, criação de replicas

Uso de label e selector
- label: marcar aquele objeto, elemento chave valor
- selector: selecionar o objeto pela label 

Selecionar objeto específico pela label
* kubectl get pods -l app=web

## Comandos ReplicaSet
- escalabilidade - diminuir ou aumentar número de replicas
- resiliência - confiabilidade: Pod sempre em execução no meu cluster, número de replicas sendo executadas no Kubernetes
- gerenciamento de replicas, controlar número de replicas
- não gerencia as versões de maneira automática, precisa deletar o pod para aplicar nova versão
- replicaset gerencia pods

Filtrar recurso
* kubectl api-resources | grep replicaset

Executar replicaset
* kubectl apply -f meureplicaset.yaml

Listar ReplicaSets
* kubectl get replicaset
- gera um nome aleatório

Verificar mais detalhes do ReplicaSet
* kubectl describe replicaset meureplicaset

Deletar um Pod
* kubectl delete pod [NOME_POD]

Alterar porta para verificar não atualização da imagem
* kubectl port-forward pod/[NOME_POD] 8080:80
- não consegue fazer a troca de versão de uma forma fácil e automática
- vai funcionar se deletar o pod, replicaset vai criar um novo pod
- quando recria um novo pod irá iniciar com a versão atualizada

Remover replicaset
* kubectl delete -f meureplicaset.yaml

## Comandos Deployment
- gerenciamento de versões dos pods e replicaset
- Deployment gerencia replicaset
- se mudar para versão 2, novo replicaset será criado, vai excluindo um pod e outro pod será criado no novo replicaset, consegue fazer o processo de rollback com maior facilidade
- melhor gestão da aplicação
- deploy eficiente no kubernetes

Aplicar deployment
* kubectl apply -f meudeployment.yaml

Listar o deployment
* kubectl get deployment

Detalhes do deployment
* kubectl describe deployment
- cria o nome do replicaset e pod dinâmico

Consegue ver as versões
* kubectl rollout history deployment meudeployment
- atualizar a versão da imagem
- consegue voltar a versão do pod

Voltar versão
* kubectl rollout undo deployment meudeployment
- faz todo o processo de troca
- mantêm o replicaset antigo para voltar versão com eficiência

Detalhes do pod, ver eventos
* kubectl describe pod [NOME_POD]
- voltou para o replicaset antigo

## Tipos de Service
- meio de comunicação com os pods
- balanciamento de carga, várias replicas do pod, precisa acessar eles e recebem carga iguais

* ClusterIP: gera comunicação internamente, ex.: API precisa acessar banco de dados, expõe internamente no kubernetes
    - mais utilizado, vários microservice se comunicando entre si
    - não consegue acessar externamente
* NodePort: cria um canal externo, consegue se comunicar com o pod
    - utiliza uma porta do cluster kubernetes, abertura de porta para poder acessar externamente
    - libera porta aleatória de 30000 até 30777, dá para definir no manifesto    
* LoadBalancer: para services rodando na nuvem
    - cria um load balance na frente do service
    - cria um IP público, para acessar a service

Listar os services
* kubectl get services
- acessar os pods

Obter detalhes do container
* docker container ls
* docker container inspect [CONTAINER_ID]
- para pegar o IP do container
- e acessar com a porta liberada pelo NodePort
- balanceamento de carga entre os containers
- não é boa prática trabalhar com IP e containers

Obter detalhes de todo os objetos
* kubectl get all

# Exemplo

Construir a imagem
* docker build -t poferrari/rotten-potatoes:v1 .

Login no dockerhub
* docker login

Subir para o repositório
* docker push poferrari/rotten-potatoes:v1

Criou a tag v1, precisa subir a tag latest também - Boa prática
* docker tag poferrari/rotten-potatoes:v1 poferrari/rotten-potatoes:latest

Subir para o repositório
* docker push poferrari/rotten-potatoes:latest
- imagem criada e disponível no docker registry

Aplicar deployment do Mongo
* kubectl apply -f deployment.yaml
* kubectl port-forward svc/mongodb 27017:27017
* watch 'kubectl get all'

Criar 20 replicas
* kubectl scale deployment web --replicas 20



# Projeto Miguel Aluguel - Deploy com Minikube + K8S

<h3>Contexto e Materiais Utilizados</h3>

O objeto para deploy é o projeto previamente desenvolvido pelo grupo como entrega no Projeto Mensal 1, mediante as adaptações necessárias para se adequar aos requisitos de compatibilidade com o Kubernetes e ao conteúdo da presente entrega.

Este código-fonte foi dividido entre os repositórios [```front-end```](https://github.com/miguelgarbo/miguel_aluguel_front_end.git) e [```back-end```](https://github.com/Xua1zin/MiguelAluguel-Back.git). Os dockerfiles correspondentes aos arquivos destes repositórios estão hospedados no DockerHub, e são utilizados para a construção das imagens dos compenentes.

O arquivo de configuração YAML do Kubernetes(chamado de Manifesto, presente no diretório ```/k8s```) contém Deployments, Services e os demais recursos empregados pela plataforma. Serve como modelo-guia para a construção e execução do projeto.

O presente arquivo(```README.md```) tem o intuito de servir como documentação explicativa do projeto, material de estudo aos integrantes e manual de instruções para facilitar a reprodutibilidade ao projeto dentro do ambiente MiniKube.

Para fins de entrega do projeto, criamos o diretório Evidências(```/evidencias```) contendo capturas de tela demonstrativas do pleno funcionamento de Pods, Services e Deployments em conjunto com a  aplicação em execução.


## Pré-requisitos para Execução

Para executar o conteúdo deste repositório, será necessário instalar [```Docker```](https://docs.docker.com/engine/install/), [```Minikube```](https://minikube.sigs.k8s.io/docs/start/?arch=%2Flinux%2Fx86-64%2Fstable%2Fbinary+download) e [```kubectl```](https://kubernetes.io/docs/tasks/tools/#kubectl) instalados. As instruções de instalação irão variar dependendo da atual versão de cada uma das ferramentas e serviços. Por isso, direcionamos o usuário aos endereços listados acima. Abaixo, uma instalação a título de exemplo dos três serviços em uma máquina Ubuntu.

### Antes de fazer o deploy

Configurar as demais credenciais que estão vazias

Para a var HASH_TOKEN gere um hash, com o seguinte comando:

```bash
openssl rand -base64 48
```

No arquivo: k8s/secrets/postgres.yaml
```code
DB_PASSWORD: senha-banco
HASH_TOKEN: 
```

No arquivo: k8s/secrets/backend.yaml
```code
POSTGRES_PASSWORD: senha-banco
```

## Preparando ambiente K8S

iniciar cluster com minikube

```bash
minikube start
```

## Inicializando deploy 

iniciar manifestos de uma vez só, pois foi mapeado os yaml 
dentro do arquivo kustomization.yaml

```bash
cd k8s
kubectl apply -k .
```

## Acessando o projeto em qualquer SO
abrir um túnel entre uma porta da máquina local e uma porta do Service

```bash
kubectl port-forward svc/frontend-service 8081:80 -n miguel-aluguel
```

## Prática para apresentaçãp

### Pod sendo destruido

Um terminal para acompanhar em tempo real 

```bash
kubectl get pods -n miguel-aluguel -l app=backend -w
```

Segundo terminal para matar o pod

```bash
kubectl delete pod nome-pod -n miguel-aluguel
```

### Balanceamento entre pods feito pelo service

```bash
kubectl logs -f -l app=backend -n miguel-aluguel --prefix --max-log-requests=5 | grep "CrudService"
```


## Conferindo Status dos manifestos

### Pods

```bash
kubectl get pods -o wide -n miguel-aluguel
```

### Deploys

```bash
kubectl get deployments -n miguel-aluguel
```

### Services
```bash
kubectl get services -n miguel-aluguel
```



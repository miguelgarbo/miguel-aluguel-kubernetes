# Projeto Miguel Aluguel - Deploy com Minikube + K8S

- Código-fonte(/frontend e /backend): projeto desenvolvido no Mensal 1, com as adaptações necessárias.

- Dockerfiles(/frontend e /backend): arquivos utilizados para construir as imagens dos componentes.(estão sendo puxados do dockerhub)

- Manifestos Kubernetes(/k8s): arquivos YAML contendo Deployments, Services e demais recursos utilizados.

- README.md(/): documentação explicando como executar o projeto no Minikube.

- Evidências(/evidencias): capturas de tela demonstrando os Pods, Services e Deployments funcionando, além da aplicação em execução.

- **Frontend:** [Repositório do Frontend](https://github.com/miguelgarbo/miguel_aluguel_front_end.git)
- **Backend:** [Repositório do Backend](https://github.com/Xua1zin/MiguelAluguel-Back.git)

## Pré-requisitos

Docker, Minikube e kubectl instalados


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



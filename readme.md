# Projeto Miguel Aluguel - Deploy com KUBERNETES

Código-fonte: projeto desenvolvido no Mensal 1, com as adaptações necessárias.

Dockerfiles: arquivos utilizados para construir as imagens dos componentes.(estão sendo puxados nas imagens do dockerhub)

Manifestos Kubernetes: arquivos YAML contendo Deployments, Services e demais recursos utilizados.

README.md: documentação explicando como executar o projeto no Minikube.

Evidências: capturas de tela demonstrando os Pods, Services e Deployments funcionando, além da aplicação em execução.

Os repositórios dos componentes estão disponíveis abaixo:

- **Frontend:** [Repositório do Frontend](https://github.com/miguelgarbo/miguel_aluguel_front_end.git)
- **Backend:** [Repositório do Backend](https://github.com/Xua1zin/MiguelAluguel-Back.git)

### Antes de fazer o deploy

Configurar as demais credenciais que estão vazias

No arquivo: k8s/secrets/postgres.yaml
```code
  DB_PASSWORD: colocar-sua-senha
  HASH_TOKEN: colocar-hash-token
```

No arquivo: k8s/secrets/backend.yaml
```code
  POSTGRES_PASSWORD: colocar-sua-senha
```

E alterar o IP na variável de controle de CORS (FRONT_URL) no arquivo (k8s/configmaps/backend.yaml) ao IP do minikube

Descobrir ip que o minikube está servindo

```bash
minikube ip
```

```code
  FRONT_URL: "http://minikube_ip:30081"
```

## Preparando ambiente K8S

iniciar cluster com minikube

```bash
minikube start
```


## Inicializando deploy 

iniciar manifestos de uma vez só na pasta (/k8s)

```bash
kubectl apply -k .
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
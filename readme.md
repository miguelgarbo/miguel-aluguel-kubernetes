# Projeto Miguel Aluguel adaptado para um deploy com kubernetes

Código-fonte: projeto desenvolvido no Mensal 1, com as adaptações necessárias.

Dockerfiles: arquivos utilizados para construir as imagens dos componentes.(estão sendo puxados nas imagens do dockerhub)

Manifestos Kubernetes: arquivos YAML contendo Deployments, Services e demais recursos utilizados.

README.md: documentação explicando como executar o projeto no Minikube.

Evidências: capturas de tela demonstrando os Pods, Services e Deployments funcionando, além da aplicação em execução.


Os repositórios dos componentes estão disponíveis abaixo:

- **Frontend:** [Repositório do Frontend](https://github.com/miguelgarbo/miguel_aluguel_front_end.git)
- **Backend:** [Repositório do Backend](https://github.com/Xua1zin/MiguelAluguel-Back.git)




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
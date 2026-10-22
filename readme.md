# Projeto Miguel Aluguel - Deploy com Minikube + K8S

<h2>Contexto e Materiais Utilizados</h2>

O objeto para deploy é o projeto previamente desenvolvido pelo grupo como entrega no Projeto Mensal 1, mediante as adaptações necessárias para se adequar aos requisitos de compatibilidade com o Kubernetes e ao conteúdo da presente entrega.

Este código-fonte foi dividido entre os repositórios [```front-end```](https://github.com/miguelgarbo/miguel_aluguel_front_end.git) e [```back-end```](https://github.com/Xua1zin/MiguelAluguel-Back.git). Os dockerfiles correspondentes aos arquivos destes repositórios estão hospedados no DockerHub, e são utilizados para a construção das imagens dos compenentes.

O arquivo de configuração YAML do Kubernetes(chamado de Manifesto, presente no diretório ```/k8s```) contém Deployments, Services e os demais recursos empregados pela plataforma. Serve como modelo-guia para a construção e execução do projeto.

O presente arquivo(```README.md```) tem o intuito de servir como documentação explicativa do projeto, material de estudo aos integrantes e manual de instruções para facilitar a reprodutibilidade ao projeto dentro do ambiente MiniKube.

Para fins de entrega do projeto, criamos o diretório Evidências(```/evidencias```) contendo capturas de tela demonstrativas do pleno funcionamento de Pods, Services e Deployments em conjunto com a  aplicação em execução.


<h2>Pré-requisitos para Execução</h2>

Para executar o conteúdo deste repositório, será necessário instalar [```Docker```](https://docs.docker.com/engine/install/), [```Minikube```](https://minikube.sigs.k8s.io/docs/start/?arch=%2Flinux%2Fx86-64%2Fstable%2Fbinary+download) e [```kubectl```](https://kubernetes.io/docs/tasks/tools/#kubectl).

As instruções de instalação irão variar dependendo da atual versão de cada uma das ferramentas e serviços. Por isso, recomendamos que o usuário visite os endereços listados acima para acessar a versão mais recente destes comandos, recomendada pelos respectivos emissores. Abaixo, listamos o procedimento de instalação utilizado pelos integrantes do grupo no ```Linux Ubuntu```.

1. Docker
```
# Remove old packages and add Docker's official repository
sudo apt-get update
sudo apt-get install -y ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] \
https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" \
| sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install Docker Engine
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Run Docker without sudo
sudo usermod -aG docker $USER
newgrp docker

docker --version
```

2. Minikube
```
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
rm minikube-linux-amd64

minikube version
minikube start --driver=docker
```

3. kubectl
```
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
rm kubectl

kubectl version --client
kubectl get nodes
```

<h2> Antes do Deploy </h2>
Antes de realizar o deploy da aplicação, é necessário configurar as credenciais e as variáveis de ambiente que ainda não foram definidas.
Para configurar a variável ```HASH_TOKEN```, primeiro é necessário gerar o hash correspondente utilizando o seguinte comando:

```
openssl rand -base64 48
```

Após esse procedimento de geração, armazenamos o valor obtido na variável conforme o método de configuração de credenciais adotado no ambiente de implantação.
Dentro do arquivo intitulado ```k8s/secrets/postgres.yaml```, adicionamos o valor disposto da seguinte maneira:
```
DB_PASSWORD: senha-banco
HASH_TOKEN: 
```

No arquivo ```k8s/secrets/backend.yaml```, listamos a senha do banco:
```
POSTGRES_PASSWORD: senha-banco
```

<h2>Preparando o Ambiente Kubernetes</h2>

Com as ferramentas instaladas e as credenciais configuradas, o primeiro passo é inicializar o cluster local por meio do Minikube:

```bash
minikube start
```

Caso o cluster já tenha sido criado anteriormente, o mesmo comando apenas o retoma, preservando os recursos existentes.

<h2>Realizando o Deploy</h2>

Todos os manifestos do projeto estão mapeados no arquivo ```k8s/kustomization.yaml```. Dessa forma, não é necessário aplicar cada arquivo YAML individualmente: o Kustomize, integrado ao ```kubectl```, reúne e aplica todos os recursos de uma só vez.

```
cd k8s
kubectl apply -k .
```

Ao final da execução, o terminal listará cada recurso criado (Namespace, Secrets, Deployments, Services etc.).

<h2>Acessando a Aplicação</h2>

Para acessar o front-end a partir de qualquer sistema operacional, utilizamos o recurso de port-forwarding, que funciona por meio da abertura de um túnel entre uma porta da máquina local e uma porta do Service dentro do cluster:

```
kubectl port-forward svc/frontend-service 8081:80 -n miguel-aluguel
```

Com o túnel ativo, a aplicação fica disponível em [```http://localhost:8081```](http://localhost:8081).

<h2>Demonstrações</h2>

Os comandos listados abaixo foram elaboradas para a apresentação do projeto e evidenciam dois comportamentos centrais do Kubernetes: a autorrecuperação de Pods e o balanceamento de carga realizado pelos Services.

<h3>Autorrecuperação de Pods</h3>

Em um primeiro terminal, acompanhamos em tempo real o estado dos Pods que correspondem ao back-end:

```
kubectl get pods -n miguel-aluguel -l app=backend -w
```

Em um segundo terminal, removemos manualmente um desses Pods, substituindo ```nome-pod``` pelo nome exibido no comando acima:

```
kubectl delete pod nome-pod -n miguel-aluguel
```

No primeiro terminal, é possível observar o Pod removido passar ao estado ```Terminating``` enquanto o Deployment cria imediatamente um novo Pod para manter a quantidade de réplicas definida no arquivo manifesto.

<h3>Balanceamento de Carga entre Pods</h3>

Para demonstrar que o Service distribui múltiplas requisições entre as réplicas do back-end, acompanhamos os logs simultâneos de todos os Pods, filtrando as mensagens geradas pela camada de serviço:

```
kubectl logs -f -l app=backend -n miguel-aluguel --prefix --max-log-requests=5 | grep "CrudService"
```

A opção ```--prefix``` identifica o Pod de origem de cada linha. Ao navegar pela aplicação, podemos observar que as requisições são atendidas por Pods diferentes.

<h2>Conferindo o Status dos Recursos</h2>

Os comandos abaixo verificarm o estado de cada recurso criado no nosso namespace. Neste caso, denominado ```miguel-aluguel```.

<h3>Pods</h3>

```bash
kubectl get pods -o wide -n miguel-aluguel
```

<h3>Deployments</h3>

```bash
kubectl get deployments -n miguel-aluguel
```

<h3>Services</h3>

```bash
kubectl get services -n miguel-aluguel
```


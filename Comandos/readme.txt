Aula 1 - Seus primeiros passos no universo DevOps

DevOps – Desenvolvimento e Operações
- Conjunto de ideias e praticas que se aplicam a todas a equipe de desenvolvimento de um produto

Se baseia em 5 pilares
	Cultura
	Automação
	Lean (metodologia)
	Monitoramento
	Compartilhamento

As oito fases do ciclo DevOps

Plan – Code – Build – Test – Release – Deploy – Operate – Monitor

##########################################################################################
Docker

Comandos

docker container run imagem → Executa e faz o download (caso necessario) de um container
docker container run –name nome_novo imagem → Executa o container e da um novo nome para ele
docker container run –rm → Executa o container e exclui ele no final da execução
docker container rm Container_id/nome → para remover o container
docker container run -it imagem /bin/bash→ Executar o container e ativar ele em modo interativo
docker container run -d imagem → Execute o container em background
docker container run -p porta_local:porta_container -d imagem → Vincula uma porta do computador para o docker
docker container stop → para parar um container
docker container rm -f id → para forçar a exclusão
docker image prune → Remove todas as imagens não utilizadas
docker image rm id → Remove a imagem especifica do Id

Criar um banco de dados usando container

postgres

Definir as variaveis de ambiente

docker container run -d -p 5432:5432 -e POSTGRES_PASSWORD=senha -e POSTGRES_USER=user -e POSTGRES_DB=nome_db postgres

docker container ls

usar o Dbeaver para acessar o banco de dados ou ate mesmo o terminal ubuntu

Criar imagem de container

Criar um DockerFile no vsCode ou em um editor.

Exemplo ubuntu:
----------------------------------
FROM ubuntu
RUN apt update
RUN apt install curl –yes
-----------------------------------
No terminal
docker build -t ubuntu-curl(nome da imagem) -f local onde esta o dockerfile(caso necessario, pois ele sempre prega o diretorio padrão) . (contexto)

docker container run -it ubuntu-curl /bin/bash

se o pacote for recente cuidado com a data deles, não pode ter uma diferença muito grande com o apt update.

Opções de uso no Dockerfile

From → Inicializa o build de uma imagem a partir de uma imagem base
Workdir → Define o seu diretório corrente
Run → Executa um comando
Label → Adiciona metadados a imagem
Expose → Define que o container precisa expor a porta em questão
Env → Define variaveis de ambiente
Copy → Copia arquivos ou diretorios e adiciona ao sistema de arquivos da imagem
Add → Copia arquivos ou diretórios ou arquivos remotos e adiciona ao sistema de arquivos da imagem
Cmd → Define o comando e/ou os parâmetros padrão
Arg → Define um argumento para ser usado no processo de construção
Entrypoint → Ajuda você a configurar um container que pode ser executado como um executável
Volume → Define volumes que devem ser definidos

Docker registry

Semelhante ao Github, mas usado para imagens docker.

Opções de registry

Docker Hub
Elastic Container Registry
Azure Container Registry
Google Container Registry

WSL --INSTALL
https://www.microsoft.com/store/productId/9PDXGNCFSCZV

https://github.com/KubeDev/conversao-temperatura

Validar o yaml
http://www.yamllint.com/

Fazer upload de imagens para o Docker Hub

Namespace/Repositorio:Tag

Namespace – usuario no docker hub
Repositorio – O repositorio do Docker hub
Tag – A versão da imagem (v1, v2)

Exemplo:

docker build -t namespace/repositorio:tag . → Executar o container com o padrão para subir no Docker hub
docker login → Seu Namespace e sua senha do Docker hub
docker push Namespace/Repositorio:Tag → Enviar a imagem para o Docker hub

Subir a latest

docker tag  Namespace/Repositorio:Tag Namespace/Repositorio:latest
docker push Namespace/Repositorio:latest

Executar uma aplicação do github em um container
Projeto em node
https://github.com/iniciativadevops/conversao-temperatura.git

Fazer um fork do projeto para sua conta.
Criar o Dockerfile na raiz do projeto

------------------------
FROM node:18.11.0
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 8080
CMD [“node”, ”server.js”]
---------------------------
No terminal
Entrar no diretorio src do projeto
docker build -t Namespace/Repositorio:Tag .
Docker container run -d -p 8080:8080  Namespace/Repositorio:Tag
https://github.com/rrddevops/conversao-temperatura/tree/main/src
docker container run -d -p 8080:8080 rodrigordavila/conversao-temperatura

OBS: Sempre versionar as imagens e sempre usar uma versão especifica da imagem que está sendo usada como base.

###################################################################################
Kubernets 

install k3d
install kubectl 

k3d cluster create
sudo kubectl get nodes
k3d cluster list
k3d cluster delete
k3d cluster create --no-lb
k3d cluster create meucluster --servers 3 --agents 3
k3d cluster create meucluster 

Service mesh (linkedin, ishu)
O POD compartilha o IP e filesystem

Vamos criar um manifesto:
metadados

--yaml 
apiVersion:
kind:
metadata:
spec:


sudo kubectl api-resources

kubectl apply -f pod.yaml 

apiVersion: v1
kind: Pod
metadata:
  name: meupod
  label:
    cor: azul
spec:
  containers:
  - name: site
    image: fabricioveronez/web-page:blue
    ports:
    -  containerPort: 80

sudo kubectl get nodes
sudo kubectl get pod
sudo kubectl describe pod meupod
kubectl port-forward pod/meupod 8080:80
será mapeado a porta 8080 localhost para acessar o web no pod port 80

sudo kubectl delete pod meupod


apiVersion: apps/v1
kind: Replicaset
metadata:
  name: meureplicaset
spec:
  replicas: 2
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
      - name: site
        image: fabricioveronez/web-page:blue
        ports:
        - containerPort: 80

sudo kubectl get all


k3d cluster delete meucluster

k3d cluster create meucluster -p "80:30000@loadbalancer"


kubectl get nodes

docker container ls

  https://github.com/KubeDev/kube-news
  docker build -t rodrigordavila/kube-news:v1 .

  
  kubectl delete deploy <deployment name>
  sudo kubectl get deployments
  sudo kubectl get services
  sudo kubectl delete svc web-page

 ################################################################3
 Terraform
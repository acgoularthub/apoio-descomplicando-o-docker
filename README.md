## [**LinuxTips: Descomplicando o Docker**](https://school.linuxtips.io/p/descomplicando-docker)

Diretório criado como material de apoio ao curso "Descomplicndo o Docker" fornecido pela [LinuxTips](https://linuxtips.io).

Este curso foi adquirido através de uma ação comunitária feita pelo [Jeferson Fernando](https://twitter.com/badtux_), onde ele distribuiu 5 mil acessos ao curso de sua plataforma de ensino de forma gratuita, com o objetivo de facilitar o acesso ao conhecimento por pessoas com menos condições.

O curso trata de uma uma trilha que inicia com os conhecimentos mais básicos sobre Containers, utilizando o [Docker](https://www.docker.com/), indo até um ponto onde o aluno possa manipular e utilizar o docker de maneira efetiva.

### **1. Aqui você irá encontrar (na ordem):**
* 2. Glossário
* 3. Lista de comando mais utilizado (cheatsheet, registry, outros)
* 4. Dockerfiles explicados
* 5. Comandos Linux que aprendi
* 6. Docker-machine
* 7. Docker Swarm
* 8. Secrets
* 9. Docker-compose

### **2. Glossário do docker**

* **Imagem**: Imagens Docker são arquivos compostos por vários sistemas de arquivos de camadas que ficam uma sobre as outras. Ela é a nossa base para construção de uma aplicação.
* **Container**: Container é um ambiente isolado, ele usa o kernel do sistema hospedeiro como base e baixa apenas o que é necessario para o funcionamento das aplicações desejadas.
* **Docker**: Um "container runtime" lançado em 2011, responsável pela criação e manipulação de containers. É um conjunto de ferramentas PaaS (Platform as a Service) que faz uma "virtualização" a nivel de SO.
* **Dockerhub**: É o repositório padrão onde o docker procura por imagens. Também é possivel armazenar imagens próprias e tem boa integração com github. Em resumo, é um repositório de imagens docker.
* **Docker compose**: É uma ferramenta que ajuda a definir e rodar aplicações multi-container de forma simplificada. Usa um arquivo YAML para configurar os serviços necessários para as aplicações e iniciar tudo com um único comando.
* **Docker Swarm**: Solução nativa do Docker para sistemas de Clusters para containers Docker.
* **Docker Volume**: Volume de dados isolados criados pelo docker que pode ser utilizado para persistir informações de containers.

### **3. Lista de comandos mais utilizados:**

Script de instalação automatizada do docker:
```bash
curl -fsSL https://get.docker.com/ | bash
```
**3.1 Criar um registry local**

```bash
docker container run -d -p 5000:5000 --restart=always --name registry registry:2
```
Esse registry serve para armazenar imagens localmente.

**3.1.1 Subir uma imagem no registry local**

Primeiro precisamos criar uma tag para a imagem:
```bash
docker image tag <id da imagem> localhost:5000/<nome da imagem>:<versão da imagem>
```
Em seguida precisamos das push no registry:
```bash
docker image push localhost:5001/<nome da imagem>:<versão da imagem>
```
**3.1.2 Registry com persistência local**

Para testar a criação de um registry com persistência local, utilizando dos conhecimentos adquiridos até aqui, eu criei um volume com o docker:
```bash
docker volume create <nome_do_volume>
```
Em seguida subi o container do registry como sugere a documentação da imagem oficial, com uma pequena mudança:

```bash
docker run -d -p 5000:5000 --restart=always --mount type=volume,src=<nome_do_volume>,dst=/var/lib/registry --name <nome_do_container> registry:2.7
```
Os dados salvos localmente podem ser acessados em `/var/lib/docker/volumes/<nome_do_volume>/_data/docker/registry/v2/repositories`

**3.2 Cheat Sheet**

![Cheatsheet](/imagens/Cheatsheet__1.jpeg)

Imagem retirada da aba de [Cheatsheets](https://www.linuxtips.io/cheatsheet) do Linuxtips.

* `docker version` --> Mostrar a versão do docker instalado.

* `docker container ls` --> Antigo `docker ps`, utilizado para listar os containers ativos.

* `docker container ls -a` --> Antigo `docker ps -a`, utilizado para listar todos os containers.

* `docker container run <nome da imagem>` --> Utilizado para subir o container. Pode ser utilizada a flag `-ti` para abrir o terminal do container de forma interativa ou `-d` para executar o container em modo "daemon", em segundo plano.

* Para sair do modo interativo sem matar o processo utilizamos a combinação de teclas `ctrl+p+q`.

* Para sair do modo interativo matando o processo utilizamos a combinação `ctrl+d`
* `docker container attach [CONTAINER ID]` --> entra em um container ativo em moto interativo.
* `docker container exec -ti [CONTAINER ID] [COMANDO]` --> Executa comandos no shell daquele container
* `docker container start [CONTAINER ID]` --> Inicia um container parado.
* `docker container stop [CONTAINER ID]` --> Para um container em execução.
* `docker container restart [CONTAINER ID]` --> Reinicia um container.
* `docker container pause [CONTAINER ID]` --> Pausa um container.
* `docker container unpause [CONTAINER ID]` --> "Despausa" um container.
* `docker container stats [CONTAINER ID]` --> Exibe os status de uso do container.
* `docker container top [CONTAINER ID]` -->
* `docker container run -d -m 128M --cpus 0.5 <nome da imagem>` --> a flag `-m` seta o limite de uso de memóra do container e `--cpus` seta o limite do uso de CPU.
* `docker container update [COMANDOS]` --> Atualiza detalhes em um container em execução.
* `docker container inspect [CONTAINER ID]` --> Exibe o manifesto de montagem do container.
* `docker image ls` --> Lista todas as imagens baixadas localmente.
* `docker image build` --> Constrói uma imagem a partir de um dockerfile.
  
  Para criar um diretório do tipo "bind" procedemos da seguinte maneira:
  ```bash
  docker container run -ti --mount type=bind,src=/<diretório-fonte>,dst=/<diretório-destino> <imagem>
  ```
  Caso você queira que o diretório seja do tipo "Read Only", basta por mais uma "vírgula" seguida de `ro`, após o caminho destino.
  
   `[...] type=bind,src=/<diretório-fonte>,dst=/<diretório-destino>,ro <imagem>`

* `docker volume ls` --> Lista os volumes criados
* `docker volume create <nome-do-volume>` --> Cria um volume com o nome desejado.
* `docker volume inspect <nome-do-volume>` --> Exibe informações sobre o volume.

  Para criar um diretório do tipo "volume" procedemos da seguinte maneira:
  ```bash
  docker container run -ti --mount type=volume,src=<nome-do-volume>,dst=/<diretório-destino> <imagem>
  ```
  Caso você queira que o diretório seja do tipo "Read Only", basta por mais uma "vírgula" seguida de `ro`, após o caminho destino.
  
   `[...] type=volume,src=<nome-do-volume>,dst=/<diretório-destino>,ro <imagem>`

* `docker container create <nome-da-imagem>` --> Cria o container sem iniciar.
* `docker container prune` --> Exclui todos os containers que não estiverem em uso. O prune pode ser usado para imagens e volumes também, mas recomenda-se cautela ao utilizar em volumes.
* 

### **4. Dockerfiles explicados**

Dockerfiles são documentos de montagem onde são postos os parâmetros do que esperamos que esteja presente nas nossas imagens docker. Em outras palavras, ele serve como base para construir um container, permitindo definir um ambiente personalizado que pode ser utilizado com os mais diversos propósitos.

As imagens docker, segundo o [Mundo Docker](https://www.mundodocker.com.br/o-que-e-uma-imagem/#:~:text=Images%20Docker%20s%C3%A3o%20compostas%20por,com%20Apache%2C%20PHP%20e%20MySQL.), são compostas por sistemas de arquivos de camadas que ficam uma sobre as outras. Ela é a nossa base para construção de uma aplicação.

No dockerfile podemos:
* Definir uma imagem oficial como base para ser modificada.
* Definir informações para a imagem (versão, descrição e autor/responsável).
* Criar a pasta onde serão enviados os arquivos carregados pelo site.
* Copiar o site para a imagem.
* Mapear uma pasta onde serão salva as imagens do site.
* Definir em qual porta o site irá rodar.
* Setar algumas variáveis de ambiente utilizadas no site.
* Definir qual será a pasta de trabalho da imagem (pasta que irá conter o site).

A seguir temos o [primeiro dockerfile](Dockerfiles/Primeiro%20Exemplo/Dockerfile) criado durante a aula do curso:

```Dockerfile
FROM debian

LABEL app="Teste1"
ENV ANDERSON="testando"
RUN apt-get update && apt-get install -y stress && apt-get clean

CMD stress --cpu 1 --vm-bytes 64M --vm1

```

Nesse dockerfile foi utilizada a "palavra-chave" `FROM` logo no topo do documento, para definir de qual imagem oficial nós iríamos buscar a base a qual vamos personalizar. Neste caso utilizamos a imagem do debian. 

Em `LABEL` podemos adicionar metadados à imagem no formato \<chave>=\<valor>. 

`ENV` \<chave>=\<valor> define a variável de ambiente \<chave> para o valor \<valor>. Este valor estará no ambiente em todas as instruções subsequentes durante o estágio de montagem.

`RUN` executa comandos durante o processo de montagem da imagem, não é aconselhável utilizar vários "runs" em um dockerfile, por isso costuma-se "concatenar" comando na lonha de RUN para evitar a criação de várias camadas no ponto de montagem, deixando a sua imagem grande sem necessidade. Em nosso primeiro dockerfile utilizamos o `&&` para concatenar os comando. A característica do `&&` é que o próximo comando será executado apenas se o comando anterior for finalizado com sucesso.

O programa "stress", que foi instalado, é utilizado para estressar os recursos da máquina para captura de informações para os mais diversos usos.

`CMD` pode ser apenas uma vez em um dockerfile, com o objetivo de passar instruções. Nesse caso ele foi utilizado para executar o programa que foi baixado e instalado com o `RUN`.

Essas foram explicações superficiais apenas com o objetivo de ter um meio de estudo e revisita das informações ganhas durante o curso. Provavelmente ainda irei alterar ou adicionar informações ao longo do curso.

**4.1 Outras opções:**

* ADD => Copia novos arquivos, diretórios, arquivos TAR ou arquivos remotos e os adicionam ao filesystem do container;

* CMD => Executa um comando, diferente do RUN que executa o comando no momento em que está "buildando" a imagem, o CMD executa no início da execução do container;

* LABEL => Adiciona metadados a imagem como versão, descrição e fabricante;

* COPY => Copia novos arquivos e diretórios e os adicionam ao filesystem do container;

* ENTRYPOINT => Permite você configurar um container para rodar um executável, e quando esse executável for finalizado, o container também será;

* ENV => Informa variáveis de ambiente ao container;

* EXPOSE => Informa qual porta o container estará ouvindo;

* FROM => Indica qual imagem será utilizada como base, ela precisa ser a primeira linha do Dockerfile;

* MAINTAINER => Autor da imagem; 

* RUN => Executa qualquer comando em uma nova camada no topo da imagem e "commita" as alterações. Essas alterações você poderá utilizar nas próximas instruções de seu Dockerfile;

* USER => Determina qual o usuário será utilizado na imagem. Por default é o root;

* VOLUME => Permite a criação de um ponto de montagem no container;

* WORKDIR => Responsável por mudar do diretório / (raiz) para o especificado nele;

### **5. Comandos Linux que aprendi** 

* `/<diretório> tar -cvf /<diretório-destino>/<nome-do-arquivo>.tar` --> Este comando empacota o diretório em um arquivo .tar. As flags `-c` de "Create", que vai criar o arquivo empacotado, `-v` de verboso, que mostrará todo o processo que está acontecendo aos fundos e `-f` de "file", necessário pra que a gente passe o arquivo que será criado.

Foi utilizado durante as aulas na criação de um conteiner que "faz backups" em um volume de dados criado previamente.

A seguir, o exemplo utilizado em aula.

```bash
docker container run -ti --mount type=volume,src=<nome-do-volume>,dst=/<diretório-destino> --mount type=bind,src=<diretório-fonte> <imagem> tar -cvf /backup/bkp-banco.tar /data
```

* **ls -lhart /\<caminho>/** --> exibe todos os itens listados no diretório com seu tamanho, data, e permições de uma forma facil de se ler. 
### **6. Docker-machine**

Docker-machine é uma ferramenta utilizada para gerência distribuída, permite a instalação e gerência de docker hosts de uma forma rápida e fácil. Mais detalhes podem ser vistos na [página oficial](https://docs.docker.com/machine/) disponibilizada pela Docker docs.

* Instalação (no linux, nesse meu caso, para outros SO, procurar na documentação):

```bash
base=https://github.com/docker/machine/releases/download/v0.16.0 \
  && curl -L $base/docker-machine-$(uname -s)-$(uname -m) >/tmp/docker-machine \
  && sudo mv /tmp/docker-machine /usr/local/bin/docker-machine \
  && chmod +x /usr/local/bin/docker-machine
```

* `docker-machine version` --> Versão do docker-machine

* `docker-machine create --driver <hypervisor/cloud_utilizado> <nome_escolhido>` --> cria um novo host 

* `docker-machine ls` --> lista os hosts

* `docker-machine env <nome do host>` --> lista as variáveis de ambiente

* `docker-machine ip <nome do host>` --> Retorna o ip da máquina

* `docker-machine ssh <nome do host>` --> conecta ao host

* `docker-machine inspect <nome do host>` --> retorna o manifesto do host

* `docker-machine stop <nome do host>` --> Para a máquina host criada

* `docker-machine ls ` --> lista os hosts criados

* `docker-machine start <nome do host>` --> inicia um host parado

* `docker-machine rm <nome do host>` --> remove um host

* `eval $(docker-machine env -u)` --> remove as variáveis de ambiente do host
  
### **7. Docker Swarm**

Docker Swarm é um orquestrador de containers fornecido pela própria Docker, ele não tem todas as feramentas que muitos orquestradores, como o Kubernetes, por exemplo, forneceria, mas é simples de usar e para pequenas aplicações é mais que o suficiente (é utilizado até em grandes aplicações, na verdade).

A seguir, uma lista com os comandos utilizados nas aulas:

* **docker swarm init** --> Inicia o node master (caso tenha mais de uma interface de rede, o parâmetro `--advertise-addr` seguido do IP da interface a ser utilizada.)

* **docker swarm join** --token <SWMTKN-1-100_SEU_TOKEN> <SEU_IP_MASTER:2377> --> adiciona a máquina como um novo nó do cluster

* **docker node ls** --> Lista os nós reconhecidos

* **docker swarm join-token manager** --> Exibe o token que pode ser utilizado para adicionar novos nós com status manager

* **docker swarm join-token worker** --> Exibe o token que pode ser utilizado para adicionar novos nós com status worker

* **docker node inspect <nome_do_nó>** --> Inspeciona o manifesto do determinado nó

* **docker node promote <nome_do_nó>** --> Promove o determinado nó a master

* **docker node demote <nome_do_nó>** --> Rebaixa o nó a worker

* **docker swarm leave** --> Tira a maquina do cluster

* **docker swarm leave** --force --> tira a maquina master do cluster

* **docker node rm <nome_do_nó>** --> Remove o nó.

* **docker service create --name <nome_da_aplicação> --replicas <número_de_réplicas> -p <porta_destino>:<porta_origem>  <nome_da_imagem>** --> Cria um service com X réplicas, expondo a porta Y na Y2 usando a imagem Z.

* **docker service ls** --> Lista todos os services criados.

* **docker service ps <nome_do_service>** --> Lista quantas réplicas estão ativas em cada nó.

* **docker service inspect <nome_do_service>** --> Exibe o manifesto do service.

* **docker service logs -f <nome-do-service>** --> Lista todos os logs gerados pelo service em todos os nós.

* **docker service rm <nome_do_serviço>** --> remove o service ativo

* **docker network create -d overlay <nome>** --> Uma rede overlay permite que os nós dentro do cluster possam trocar/buscar informações entre si com dns imbutido, sem a necessidade de buscar pelo ip, como acessar a página disponibilizada pelo nginx dentro de outro nó, por exemplo. A maior vantagem de criar uma rede overlay é que mesmo que os ips sejam alterados ao longo da criação/morte dos containers, o fato de o serviço resolver o nome da rede, no lugar do ip, garante que o serviço esteja sempre online.

* **docker network ls** --> lista as redes criadas

* **docker network inspect <nome_da_rede>** --> inspeciona a informações da rede citada

* **docker service scale <nome_do_container>=<numero_de_réplicas>** --> Redefine o número de réplicas que aquele serviço terá.

* **docker network rm <nome_da_rede>** --> remove a rede

* **docker service update <OPCOES> <Nome_Service>** --> atualiza detalhes de um serviço em execução.
  
  **Obs. Todos os `inspect` podem ser adicionados do comando --pretty para facilitar a leitura de tudo.**
### **8. Secrets**

* **echo 'minha secret' | docker secret create -** --> Cria a secret com a saída do echo
* **docker secret create <nome-da-secret> <arquivo-original>** --> cria uma secret a partir de um arquivo
* **docker secret inspect <nome-da-secret>** --> Exibe informações da secret
* **docker secret ls** --> exibe todas as secrets
* **docker secret rm <nome-da-secret>** --> remove a secret
* **docker service create --name <nome-do-app> --detach=false --secret <nome-da-secret>  <nome-do-app>:<versão-do-app>** --> 
* **docker service update --secret-rm <nome-da-secret> --detach=false --secret-add source=db_pass_1,target=password app** --> 

### **9. Docker-compose**

Docker-compose é um arquivo `.yml` (YAML) que consegue definir e rodar aplicações multi-container com um simples comando.

Você pode encontrar mais informações [aqui](https://docs.docker.com/compose/).
# Big-Data

Comandos Docker

- docker-compose pull -> baixar imagens
- docker image ls -> visualizar imagens baixada
- docker-compose up -d ->executar os containers 
- docker conteiner ls -> listar os containers

Inicar
- docker-compose up -d -> -d iniciar os serviços em backgroud - executar só a primeira vez

- docker-compose stop -> parar os serviços
- docker-compose start  -> iniciar serviços 

- docker-compose down -> matar os serviços
- docker volume prune ->apagar todos os valumes em uso
- docker system prune --all -> apager tudo image,volume, network

- docker ps -> container ativos
- docker ps -a -> todos container

- docker exec -it <container> <comando>  -> executar comandos no container


- docker logs <container> ->  visualizar logs
- docker-compose logs -> visualizar logs

- docker cp <diretório> <container>:/<diretório> -> enviar arquivos

- docker exec -it namenode bash  -> Acesso o container namenode
- docker exec -it hive-server bash

https://github.com/rodrigo-reboucas/docker-bigdata

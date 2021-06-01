# Redis

- Chave-Valor mais popular do mundo
    - Valor -> diversas estrutura de dados
    - Armazenamento em memória
- Acesso rápido aos dados
    - Escrita
    - Leitura

- Possível persistir os dados fisicamente
    - Não é um BD para armazenar todos os dados
- Servidor TCP
    - Modelo cliente-servidor
- Comandos são atômicos
    - Aplicação single-threaded
    - Um comando por vez
- Comunicação
    - Aplicação
    - Redis CLI

### instalação no Docker

docker-compose up –d

docker ps

docker exec -it redis bash

    -  redis-cli
    -  redis-server

### Estrutura de dados

- Servidor de estruturas de dados que oferece suporte a diferentes tipos de valores
    - Não é um armazenamento de chave-valor simples
        - String-string
- Aceita estruturas de dados mais complexas
    - Strings
    - Listas
    - Sets
    - Sets Ordenados
    - Hash
    - HyperLogLogs
    - Streams

### Redis Chave

- As chaves são binary safe
    - Sequência binária
    - Regras
        - Tamanho máximo 512 MB
            - Não indicado chaves > 1 MB
            - Gastar memória e largura de banda
- Dicas
    - Instanciar “object-type:id”
    - “user:1000” ao invés de “u1000”

- Sintaxe
    - Definir um valor de string: SET <chave> <valor>
    - Recuperar um valor de string: GET <chave>

 - Opções para a chave String
    - NX – Falhar se a chave existir
    - XX (Default) – Substituir o valor da chave


- Sintaxe
    - MSET <chave 1> <valor> <chave 2> <valor> ... <chave N> <valor>
    - MGET <chave 1> <chave 2>... <chave N>

### Execícios
1. Criar a chave "usuario:nome" e atribua o valor do seu nome 
SET usuario:nome isaque
2. Criar a chave "usuario:sobrenome" e atribua o valor do seu sobrenome
SET usuario:sobrenome Elcio
3. Busque a chave "usuario:nome“
get usuario:nome 

4. Verificar o tamanho da chave "usuario:nome“
strlen usuario:nome 

5. Verificar o tipo da chave "usuario:sobrenome"
type usuario:sobrenome
6. Criar a chave "views:qtd" e atribua o valor 100
set views:qtd 100
7. Incremente o valor em 10 da chave "views:qtd"
incrby views:qtd 10
8. Busque a chave "views:qtd"
get views:qtd 
9. Deletar a chave "usuario:sobrenome"
del usuario:sobrenome

10. Verificar se a chave "usuario:sobrenome" existe
exists usuario:sobrenome
11. Definir um tempo de 3600 segundos para a chave "views:qtd" ser removida
expire views:qtd 3600
12. Verificar quanto tempo falta para a chave "views:qtd" ser removida
ttl views:qtd
13. Verificar a persistência da chave "usuario:nome"
ttl usuario:nome
14. Definir para a chave "views:qtd" ter persistência para sempre
persist views:qtd

### Exercicíos 
1. Criar a chave “views:ultimo_usuario" e insira nesta ordem os seguintes valores como lista:
Final da lista “Joao”
Final da lista “Ana”
Inicio da lista “Carlos”
Final da lista “Carol”

rpush views:ultimo_usuario João Ana

lpush views:ultimo_usuario Carlos

rpush views:ultimo_usuario Carol

2. Visualizar todos os elementos da lista

lrange views:ultimo_usuario 0 -1

3. Visualizar todos os elementos da lista, com exceção do último
lrange views:ultimo_usuario 0 2
lrange views:ultimo_usuario 0 -2
4. Visualizar o tamanho da lista
llen views:ultimo_usuario
5. Redefinir o tamanho da lista, removendo o primeiro elemento (Sem usar o pop)
ltrim views:ultimo_usuario 1 -1 

6. Visualizar o tamanho da lista
lrange views:ultimo_usuario 0 -1
7. Recuperar os elementos da lista da seguinte ordem:
Primeiro
lpop views:ultimo_usuario
Último
rpop views:ultimo_usuario

Primeiro com bloqueio de 5 segundos se a lista estiver vazia
blpop views:ultimo_usuario 5
exists views:ultimo_usuario
Primeiro com bloqueio de 5 segundos se a lista estiver vazia

### Sets

### Exercícios 
1. Deletar a chave “pesquisa:produto”
del pesquisa:protuto
2. Criar a chave "pesquisa:produto" do tipo set com os seguintes valores: monitor, mouse e teclado
sadd pesquisa:produto monitor mouse teclado
3. Visualizar a quantidade de valores da chave
scard pesquisa:produto
4. Retornar todos os elementos da chave
smembers pesquisa:produto
5. Verificar se existe o valor monitor
sismember pesquisa:produto monitor
6. Remover o valor monitor
srem pesquisa:produto monitor
7. Recuperar um elemento e remove-lo do set
spop pesquisa:produto
8. Criar a chave "pesquisa:desconto“ do tipo set com os seguintes valores: memória RAM, monitor, teclado, HD
sadd pesquisa:desconto 'memoria RAM' monitor teclado HD 
9. Próximas questões fazem uso dos sets pesquisa:produto e pesquisa:desconto

Visualizar a interseção entre os 2 sets
sinter pesquisa:produto pesquisa:desconto
Visualizar a diferença entre os 2 sets
sdiff pesquisa:produto pesquisa:desconto
Criar o set "pesquisa:produto_desconto" com a união entre os 2 sets
sunion pesquisa:produto pesquisa:desconto

sunionstore pesquisa:produto_desconto pesquisa:produto pesquisa:desconto

smembers pesquisa:produto_desconto

### Sets Ordenados
- Sorted sets são compostos de elementos de string únicos e não repetitivos
    - Combinação de Set e Hash
    - Também é um Set

1. Deletar a chave "pesquisa:produto"
del pesquisa:produto

2. Criar a chave "pesquisa:produto" do tipo set ordenado com os seguintes valores:
• Valor: monitor, Score: 100
• Valor: HD, Score: 20
• Valor: mouse, Score: 10
• Valor: teclado, Score: 50

zadd pesquisa:produto 100 monitor 200 HD 10 mouse 50 teclado

O score representa a quantidade de pesquisas feitas para aquele produto na aplicação
1. Visualizar a quantidade de produtos
zcard pesquisa:produto

2. Visualizar todos os produtos do mais pesquisado ao menos pesquisado
zrevrange pesquisa:produto 0 -1

3. Visualizar o rank do produto teclado
zrevrank pesquisa:produto teclado

4. Visualizar o score do produto teclado
zscore pesquisa:produto teclado

5. Remover o valor HD da chave
zrem pesquisa:produto HD

6. Recuperar e remover do set o produto mais pesquisado
zpopmax pesquisa:produto

7. Recuperar e remover do set o produto menos pesquisado
zpopmin pesquisa:produto

8. Visualizar todos os produtos
zrange pesquisa:produto -1 0


### Redis Hashes
1. Deletar a chave “usuario:100”
del usuario:100
2. Criar uma chave “usuario:100” do tipo hash com a seguinte estrutura
nome – Augusto
estado – SP
views – 10
hmset usuario:100 nome Augusto estado SP views 10

3. Visualizar todas as chaves e valores
hgetall usuario:100
4. Contar a quantidade de campos
hlen usuario:100
5. Visualizar apenas o nome e views
hmget usuario:100 nome views
6. Contar o tamanho do valor do campo nome
hstrlen usuario:100 nome 
7. Incrementar em 2 o valor do campo views
hincrby usuario:100 views 2
8. Visualizar apenas os campos
hkeys usuario:100
9. Visualizar apenas os valores
hvals usuario:100
10. Deletar o campo estado
hdel usuario:100 estado
hgetall usuario:100


### Mensagens Pub/Sub
subscribe canal1
publish canal1clear


1. Criar um assinante para receber as mensagens do canal noticias:sp
subscribe noticias:sp

2. Criar um editor para enviar as seguintes mensagens no canal noticias:sp

Msg 1
Msg 2
Msg 3

publish noticias:sp "Msg 1"
3. Cancelar o assinante do canal noticias:sp
unsubscribe 
4. Criar um assinante para receber as mensagens dos canais com o padrão noticias:*
psubscribe noticias:*
5. Criar um editor para enviar as seguintes mensagens no canal noticias:rj
publish noticias:rj "Msg 1"
Msg 4
Msg 5
Msg 6

### cache
1. Visualizar todos os parâmetros de configuração
config get *
2. Verificar o parâmetro “appendonly”
config get appendonly
3. Remover a persistência dos dados, alterando o parâmetro “appendonly” para “no”
config set appendonly no
config set appendonly
4. Verificar o parâmetro “save”
config get save
5. Adicionar a persistência dos dados, para a cada 2 minutos (120 segundos) se pelo menos 500 chaves forem alteradas, adicionando o parâmetro “save” para “120 500”
config set save '120 500'
config set save
6. Verificar os parâmetros “maxmemory*”
config get maxmemory*
7. Permitir que o Redis remova automaticamente os dados antigos à medida
que você adiciona novos dados,  com uso da politica “allkeys-lru”, quando chegar a 1mb de memória.
config set maxmemory-policy allkeys-lru
config set maxmemory 1mb

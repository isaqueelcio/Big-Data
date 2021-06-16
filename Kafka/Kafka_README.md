# Kafka

docker exec -it broker bash

kafka-topics --bootstrap-server localhost:9092 --list
kafka-topics --bootstrap-server broker:9092 --list


### Exercícios
1. Criar o tópico msg-cli com 2 partições e 1 réplica

kafka-topics --bootstrap-server localhost:9092 --create --topic msg-cli --partitions 2 --replication-factor 1

2. Descrever o tópico msg-cli

kafka-topics --bootstrap-server localhost:9092 --topic msg-cli --describe

3. Criar o consumidor do grupo app-cli
kafka-console-consumer --bootstrap-server localhost:9092 --topic msg-cli --grup app-cli

4. Enviar as seguintes mensagens do produtor
kafka-console-producer --bootstrap-server localhost:9092 --topic msg-cli --grup app-cli


Msg 1
Msg 2
5. Criar outro consumidor do grupo app-cli
kafka-console-producer --bootstrap-server localhost:9092 --topic msg-cli --grup app-cli

6. Enviar as seguintes mensagens do produtor

Msg 4
Msg 5
Msg 6
Msg 7
7. Criar outro consumidor do grupo app2-cli
kafka-console-consumer --bootstrap-server localhost:9092 --topic msg-cli --grup app2-cli

8. Enviar as seguintes mensagens do produtor

Msg 8
Msg 9
Msg 10
Msg 11
9. Parar o app-cli

10. Definir o deslocamento para -2 offsets do app-cli
kafka-consumer-groups --boostrap-server localhost:9092 --reset-offsets --shift-by-2 --execute --topic msg-cli --group app-cli

11. Descrever grupo

kafka-consumer-groups --boostrap-server localhost:9092 --group app-cli --describe

12. Iniciar o app-cli

kafka-console-consumer --bootstrap-server localhost:9092 --topic msg-cli --grup app2-cli

13. Redefinir o deslocamento o app-cli

kafka-consumer-groups --boostrap-server localhost:9092 --reset-offsets --to-earliest --execute --topic msg-cli --group app-cli

14. Iniciar o app-cli

kafka-console-consumer --bootstrap-server localhost:9092 --topic msg-cli --grup app-cli

15. Listar grupo

kafka-consumer-groups --boostrap-server localhost:9092 --list

### Exercícios

### Control Center

1. Criar um tópico com o nome msg-rapida com 4 partições, 1 replicação e deletar os dados após 5 minutos de uso.
Na interface 
2. Produzir e consumir 2 mensagens para o tópico msg-rapida
Na interface 
3. Qual o nome do cluster?

Controlcenter.cluster

4. Quantos tópicos existem no cluster?

 61 Topicos

5. Quantas partições existem o tópico msg-cli?

2 partições

6. Todas as réplicas estão sincronizadas no tópico msg-cli?

2 réplicas

7. Qual a política de limpeza do tópico msg-cli?

604800000 ms 7 dias

8. Alterar a política de limpeza do tópico msg-cli para deletar depois de um ano.

Na interface 

9. Qual o diretório de armazenamento de logs do cluster?

/var/lib/kafka/data

10. Por padrão os dados são mantidos por quantos dias no Kafka?

168 horas - 7 dias

11. Visualizar os gráficos de produção e consumo de dados do tópico msg-rapida.

na interface 

### KSQL Conceitos

- Engine de Streaming SQL do Kafka
- Interface SQL interativa
    - Facilidade de uso
    - Focada para o processamento streaming no Kafka
    - Sem a necessidade de escrever código em Java ou Python
- Escalável
- Tolerante a falhas
- Tempo real
- Suporta várias operações de streaming
    - Filtragem de dados
    - Transformações
    - Agregações

- Acesso terminal 
    - docker exec -it ksqldb-cli bash
    - ksql
- Comandos
    - Visualizar topicos
        - list topics;
    - Mostrar conteúdo do tópico em tempo real
        - ksql> print "<nomeTopico>" <propriedades>;
            - Propriedades
            - from beginning;
            - interval
            - limit
    ksql> print "topic-produto" from beginning interval 5 limit 10;

### KSQL Stream

- Sequencia de dados estruturados

- Imutáveis
    - Apenas pode inserir dados, não atualizar ou excluir
- Podem ser criados a partir de um tópico do Kafka ou derivados de um stream existente
    - Fornecer o formato dos valores armazenados no tópico
    - Não inferi o formato de dados do tópico

-  Criar Stream
    - Ksql> create stream <nomeStream> (<campo> <tipo>, ..., <campo> <tipo>) with (Kafka_topic='<nomeTopic>', value_format='<formato>', KEY='<campoChave>',TIMESTAMP='<campoTimestamp> ...');

### Comandos Stream  
- Visualizar conteúdo do Stream
    - Ksql> select <campo>,…<campo> from <nomeStream>
        - Ksql> select nome from cad_str;
        - Ksql> select * from cad_str limit 10; 
        - Ksql> describe <nomeStream>
        - Ksql> insert into foo (rowtime, rowkey, key_col, col_a) values (1234, 'key', 'key', 'a');
        - ksql> drop stream <nomeStream> delete topic;
        - ksql> drop stream if exists <nomeStream> delete topic;

### Comandos Stream - Agregações

- Ksql> select <campo> count(*) from <nomeStream> group by <campo>;
- Ksql> select cidade, count(*) from cad_str group by cidade;
- ksql> CREATE STREAM <novoStream> AS SELECT 1 AS unit FROM <nomeSteamParaContar>;
- select count(unit) from <novoStream> group by unit

### Exercícios

1. Criar o tópico msg-usuário-csv

kafka-topics --bootstrap-server localhost:9092 --create --topic msg-usuario-csv

2. Criar um produtor para enviar 3 mensagens contendo id e nome separados por virgula para o tópico msg-usuário-csv

kafka-console-producer --broker-list localhost:9092 --topic msg-usuario-csv

1,isaque
2,isaque
3,isaque

3. Visualizar os dados do tópico msg-usuário-csv

kafka-topics --bootstrap-server localhost:9092 --topic msg-usuario-csv --describe

4. Criar o Stream usuario_csv para ler os dados do tópico msg-usuário-csv

docker exec -it ksqldb-cli bash
ksql

create stream usuario_csv(nome varchar, id int) with(kafka_topic='msg-usuario-csv', value_format='delimited');

5. Visualizar o Stream usuario_csv

select * from usuario_csv

6. Visualizar apenas o nome do Stream usuario_csv

select nome from usuario_csv

### Conceitos - KSQL Datagen

docker exec --it ksql-datagen bash

 ksql-datagen <argumentos>

  ksql-datagen help

### Exercício 

1. Criar o tópico users com os dados do ksql-datagen

quickstart=users
topic=users
2. Visualizar os dados do tópico no Ksql

3. Criar o stream users_raw com os dados do tópico users com as seguintes propriedades

kafka_topic='users’
value_format='JSON’
key = 'userid’
timestamp='viewtime’
4. Visualizar a estrutura da Stream  users_raw

5. Visualizar os dados da Stream  users_raw

6. Repetir todo o proceso para o tópico pageviews

ksql-datagen quickstart=pageviews topic= pageviews
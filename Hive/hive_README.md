# Hive
- Não é SGBD
   - SQL para map reduce 
   - data warehouse em cima do hadoop (hdfs)

Não trabalha em tempo real 

### Particionamento
Recursos de particionamento 


Não existe um formato Hive

### Componentes
HCatalog 
- camada de gerenciamento de armazenamento para o hadoop
- diferentes ferramentas de processamento de dados leiam e gravem os
dados
WebHCat
- Servidor web para se conectar com o Metastore Hive
HiveServer2
- Serviço que permite aos clientes executar consultas no Hive
Metastore
    - Todos os metadados das tabelas e partições do Hive são acessados ​​através do Hive Metastore
    - configurar o servidor metastore
        - Embedded Metastore
        - Local Metastore
        - Remote Metastore
Beeline
    - Cliente Hive
### Estrutura de dados
- Salva dados estruturados e semi-estruturados no hive
- Hierarquia dos dados
    - Database
        - Table
            - Partition- Coluna de armazenamento dos dados no sistema de arquivo (diretórios) coluna com nome do diretório
                - Bucket - Dados são divididos em uma coluna através de Hash
- Exemplo da estrutura de diretório                
/user/hive/warehouse/banco.db/tabela/data=010119(partição)/000000_0(Bucket) 

### Linguagem Hive
- Hive Query Language -HQL

### Banco de dados de tabelas (comandos) 
- Listar todos os BD
    - show database;
- Estrutura sobre o bd
    - desc database <nomeBD>;
- Listar as tabelas
    - show tables;
- Estrutura da tabela
    - desc <nomeTabela>;
    - desc formatted <nomeTabela>;
    - desc extended <nomeTabela>;
- Criar BD
    - create database <nomeBanco>;
- Local diferente do conf.Hive (mudar diretório)
    - create database <nomeBanco> location "/diretorio";
- Adicionar comentário
    - create database <nomeBanco> comment "descrição";
Exemplo
    - create database test location "/user/hive/warehouse/test" comment "banco de dados para treinamento"

### Tabelas
- Tipos
 - Internas - dados dentro da tabela interna
    - Ex. create table user(cod int, name string);
    - drop table - apaga os dados e os metadados 
 - Externas - mapeia diretório para ler arquivos
    - create external table user(cod int, name string) location '/user/semantix/data_users';
    - drop table
        - Não apaga os dados armazenados
        - Apaga apenas os metadados
        - Usar para compartilhar os dados com outras ferramentas
- Partição
    - Não particionada
    - Particionada
        - Dinâmico
        - Estático

### Atributos para Criação de Tabelas

- Tipos de dados
    - INT
    - SMALLINT
    - TINYINT
    - BIGINT
    - BOOLEAN
    - FLOAT
    - DOUBLE
    - DECIMAL
    - STRING
    - VARCHAR
    - CHAR

### Tipos Dados Complexos

- ARRAY
    - Lista de Elementos [‘Seg’, ‘Ter’, ‘Qua’, ‘Qui’, ‘Sex’]
- MAP
    - Tipo Chave-valor ‘nome’ ->’Rodrigo’
- STRUCT
    - Define os campos dos seus tipos de dados
    - STRUCT<col_name
        - : data_type, ...
- UNION
    - Armazenar diferentes tipos de dados no mesmo local de memória
    - UNIONTYPE<data_type,
    - data_type, ...>

### Leitura de dados

- Definir delimitadores
    - row format delimited
    - fields terminated by '<delimitador>' (delimitar campos ,)
    - lines terminated by '<delimitador>'  (Delimitar linhas \n)
    - Delimitadores: 'qualquer coisa', \b (backspace), \n (newline), \t (tab)

- Pular um número de linhas de leitura do arquivo
    - tblproperties("skip.header.line.count"=“<número de linhas"); (pular linha do cabeçalho)

- Definir Localização dos dados (Tabela externa)
    - location '/user/cloudera/data/client’; (localizar dados, mapear dados)

### Criação de Tabela 
- Tabela Externa
    create external table user(
    id int,
    name String,
    age int
    )
    row format delimited
    fields terminated by '\t'
    lines terminated by ‘\n’
    stored as textfile
    location '/user/cloudera/data/client’;

    - Tabela interna não tem o location
 
### Acessar o Cliente Hive

docker exec -it hive-server bash

beeline --help 

beeline -u jdbc:hive2://localhost:10000 username password

show databases;
### Exercícios

1. Enviar o arquivo local “/input/exercises-data/populacaoLA/populacaoLA.csv” para o diretório no HDFS “/user/aluno/<nome>/data/populacao”

hdfs dfs -mkdir /user/aluno/isaque/data/populacao
hdfs dfs -put /input/exercises-data/populacaoLA/populacaoLA.csv /user/aluno/isaque/data/populacao
hdfs dfs -ls /user/aluno/isaque/data/populacao

hdfs dfs -cat /user/aluno/isaque/data/populacao/populacaoLA.csv | head -n 3

2. Listar os bancos de dados no Hive
show databases;

3. Criar o banco de dados <nome>
create database isaque; 
4. Criar a Tabela Hive no BD <nome>
use isaque; 
create table pop(zip_code int, total_population int, median_age float, total_males int, total_females int, total_households int, average_household_size float) row format delimited fields terminated by ',' lines terminated by '\n' stored as textfile tblproperties("skip.header.line.count"="1");  

desc formatted pop;

Tabela interna: pop
Campos:
zip_code - int
total_population - int
median_age - float
total_males - int
total_females - int
total_households - int
average_household_size - float
Propriedades
Delimitadores: Campo ‘,’ | Linha ‘\n’
Sem Partição
Tipo do arquivo: Texto
tblproperties("skip.header.line.count"="1")’

5. Visualizar a descrição da tabela pop

desc formatted pop;

- Repositório bigdata
    - https://github.com/rodrigo-reboucas/docker-bigdata
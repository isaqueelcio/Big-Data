# MongoDB

https://db-engines.com/

### ACID
relacional
- Atomicidade
- Consistência
- Isolamento
- Durabilidade

### BASE 
não relacional
- Basically Available
- Soft-State
- Eventually Consistent

### CAP
Impossível no armazenamento de dados distribuído garantir mais de duas das três garantias:
- Consistency (Consistência)
- Availability (Disponibilidade)
- Partition Tolerance

### MongoDB
- Distribuído
- NoSQL
- Orientado a documentos
- Formato BSON (JSON Binário)
- Esquema flexível
https://www.mongodb.com/

### Formato BSON
- JSON Binário e expandido
- JSON
    - JavaScript Object Notation
    - Objetos são baseados em Chave/Valor
        - String / (String, Número, Objeto)
        - Esquema flexível
    - JavaScript
        - Linguagem padrão para desenvolvimento Web
    - Formato texto
        - Fácil leitura para humanos

### Docker
- docker-compose stop -> parar os serviços
- docker-compose start  -> iniciar serviços 

### Acessos de ferramentas
    - MongoDB
        - $ docker exec -it mongo bash
            - ls /data
            - ls /data/db
            - cat /data/db/storage.bson

            - # mongo
            -  show dbs
            - CTRL+D - Sair mongo
            - CTRL+D - Sair conteiner
    - Mongo Express
        - http://localhost:8081

### Criação Banco de dados

- Criar banco de dados
    - use <nomeBD>

- Criar Collection
    - db.createCollection(‘<nomeCollection>’)

- Visualizar Collections
    - show collections
- Excluir Collection
    - db. <nomeCollection>.drop()

- Excluir Banco de dados
    - db.dropDatabase() - banco conectado

- Renomear Collection
    - db. <nomeCollection>.renameCollection(‘<nomeNovaCollection>’)

- Criação Documentos
    - Inserir um documento
        - db.<nomeCollection>.insertOne({<documento>})
    - Inserir N documentos (Array) o Sintaxe:
        - db. <nomeCollection>.insertMany([{<documento1>}, {<documento2>},{<documentoN>}])
    - Exemplo:
        - Exemplo de um documento na collection cliente
            - db.cliente.insertOne({nome : "Lucas",idade : 20,conhecimento : "Windows, Linux, Hadoop"})

            - Exemplo de N documentos na collection cliente
                - db.cliente.insertMany([ {nome : "Ana",idade : 25,conhecimento : "Windows, Linux,NoSQL"},{nome : “João“}])

- Listar documentos da collection
    - db.<nomeCollection>.find().pretty()

### Tipos de documentos
- ObjectId
- String
- Número
- Long
    - Int
    - Decimal
    - Double
- Boolean
- Array
- Date
- Null
- Regex
- Timestamp

### Consulta Documentos

- Sintaxe:
    - db.<nomeCollection>.find({<critérioConsulta>},{<projeção>})
- Critério de Consulta
    - Operadores aplicados nos atributos
    - { <atributo> : { <operador> : <valor> }
    - Projeção
        - Atributos que são visualizados pela consulta
        - { <atributo> : 0 }
            - Ocultar o atributo
        - { <atributo> : 1 }
            - Mostrar o atributo

### Consulta Documentos

- Critérios de consulta
    - Todos os documentos
        - Sintaxe: db.<nomeCollection>.find()
        - Exemplo: db.cliente.find({ })
    - Por um atributo
        - Sintaxe: db.<nomeCollection>.find( { <atributo> : <valor> } )
        - Exemplo:
            db.cliente.find({nome: “Jõao”})
            db.cliente.find( { “endereco.cidade”: “São Paulo” })
    - Por um atributo com operador
        - Sintaxe: db.<nomeCollection>.find( { <atributo> : { <operador> : <valor> } } )
        - Exemplo: db.client.find({idade: {$lt : 18}})

### Operadores Pesquisa

Operador | Descrição | Sintaxe
:-------|:----------:|----------:
$eq | Igual a| { <field> : {$eq: 10} } ou { <field> : 10} }
$ne | Diferente de | { <field> : {$ne: 10} }
$gt | Maior que | { <field> : {$gt: 10} }
$gte | Maior ou igual | { <field> : {$gte: 10} }
$lt | Menor que | { <field> : {$lt: 10} }
$lte | Menor ou igual | { <field> : {$lte: 10} }
$in | Na lista | { <field> : {$in: [10,20]} }
$nin | Fora da lista | { <field> : {$nin: [10,20]} }
$not | Não | { <field> : {$not: {$eq: 10}} } ou { <field> : {$ne: 10} }

### Operador And e Or

- Pesquisar múltiplos critérios
    - And – Default
        - db.cliente.find({"endereco.cidade": "São Paulo", idade: {$gte: 18} } )
    - Or
        - db.cliente.find({$or:[{"endereco.cidade": "São Paulo"},{idade: {$gte: 18}}]})
    - And e Or
        - db.cliente.find({conhecimento: "Windows", { $or: [ {"endereco.cidade": "São Paulo"}, {idade: {$gte: 18}}]})

### Ordenando Pesquisas
- Função sort do método find
    - Sintaxe:
        - db.<nomeCollection>.find({...}).sort({<atributo>: <valor>})
    - Atributo
        - .sort({<atributo>: 1})
    - Ordenação ascendente
        - .sort({<atributo>: -1})
    - Ordenação descendente
        - .sort({<atributo1>: 1, <atributo2>: 1})
    - Ordenação por N atributos
        - Exemplo:
        - db.cliente.find({}).sort({“endereco.cidade”: 1, nome: 1})

### Limitar Pesquisas
- Função limit do método find
    - Sintaxe:
    - db.<nomeCollection>.find({...}).limit(<valor>)
    - Exemplo
        - Mostrar os 5 primeiros documentos
        - db.cliente.find({}).limit(5)

### Buscar 1 documento

- FindOne
    - Mais performatico que o Find
    - Garantir na saída sempre 1 documento
    - Sintaxe:
        - db.<nomeCollection>.findOne({...})
    - Exemplo
        - db.cliente.findOne({nome: "lucas"},{_idade: 0})
        - db.cliente.find({nome: "lucas"},{_idade: 0}).limit(1)

### Atualizar Documentos
- Principais métodos
    - db.<nomeCollection>.updateOne(<filtro>, <atualização>)
    - db.<nomeCollection>.updateMany(<filtro>, <atualização>)

- Filtro é o Critério de Consulta
    - Operadores aplicados nos atributos
    - { <atributo> : { <operador> : <valor> }

- Atualização
    - Operadores de atualização aplicados nos atributos
    - https://docs.mongodb.com/manual/reference/operator/update/
    - {<operadorAtualização>: { <atributo>: <valor>, <atributo>: <valor>, ...}}

### Atualizar 1 Documento

- Operador set
    - Atualizar o atributo
    - Caso o atributo não exista, é criado
    - db.cliente.updateOne({_id: 1},{$set: {idade: 25, estado: “SP”}})
- Operador unset
    - Remover o atributo
    - O valor do atributo deve ser ""
    - db.cliente.updateOne({_id: 1},{$unset: {idade: “”}})

### Atualizar N Documentos
- Operador set
    - Atualizar o atributo
    - Caso o atributo não exista, é criado
    - db.cliente. updateMany({idade: {$gt: 27}},{$set: {seguro_carro: “baixo”}})
### Atualizar nome do Atributo
- Operador rename
    - Atualizar os nomes dos atributos
    - {$rename: { <nomeAtributo>: <novoNome>, <nomeAtributo2>: <novoNome2>, ... } }
    - db.cliente. updateMany({},{$rename: {“nome”: “nome_completo”}})

### Documentos com data
- Tipos de Data e tempo
    - Date() – Retorna a data atual como string
    - new Date() - Retorna a data atual como objeto de data UTC (ISODate)
    - new Date("<YYYY-mm-ddTHH:MM:ssZ>")
    - New Timestamp() – Retorna o timestamp atual

    - db.test.insertOne({ts: new Timestamp() ,date: new Date(),data_string: Date(),config_date: new Date("2020-08")});

### Atualizar data de Documentos
- Operador currentDate
    - Setar um valor de data atual
    - { $currentDate: { <atributo>: $type: <tipo> } }
        - Tipo
            - timestamp
            - date
    
    db.cliente. updateMany( {idade: {$gt: 27}},{$set: {seguro_carro: " baixo "},$currentDate: { atualizado: { $type: "timestamp" }}})

### Exercícios
db.produto.updateOne({_id: 1},{$set: {nome:"cpu i7"}})

db.produto.findOne({_id: 1})

db.produto.updateOne({_id: 1},{$set:{"qtd" : 15}})

db.produto.updateMany({},{$rename: {"descricao.so":"descricao.sistema"}})

db.produto.updateMany({"descricao.conexao": "USB"},{$set:{"descricao.conexao": "USB 2.0"}})


db.produto.updateMany({"descricao.conexao": "USB 2.0"},{$set:{"descricao.conexao": "USB 3.0"}, 
$currentDate: {data_modificacao: { $type: "date"}}})

db.produto.updateOne({_id:3, "descricao.sistema": "Windows"},{$set: {"descricao.sistema.$": "Windows 10"}})

db.produto.updateOne({_id:4},{$push: {"descricao.sistema": "Linux"}})


db.produto.updateOne({_id:3},{$pull: {"descricao.sistema": "Mac"}, $currentDate:{ts_modificado: {$type: "timestamp"}}})

### Métodos de Remover Documentos

- Deletar Documentos
    - Principais métodos
        - db.<nomeCollection>.deleteOne(<filtro>)
        - db.<nomeCollection>.deleteMany(<filtro>)

    - Filtro é o Critério de Consulta
        - Operadores aplicados nos atributos
        - { <atributo> : { <operador> : <valor> }
    - Deletar todos os documentos
        - db.cliente.deleteMany({})

    - Deletar N documentos
        - db.cliente.deleteMany({status: “Inativo”})
    - Deletar um documento
    - Se a busca encontrar mais de um documento, irá deletar apenas o primeiro
        - db.cliente.deleteOne({_id: 2})

### Exercícios 

db.createCollection('teste')
show collections

db.teste.insertOne({usuario: 'Semantix', data_acesso: new Date()})

db.teste.find()

db.teste.find({data_acesso: {$gte: new Date("2020")}})

db.teste.find({data_acesso: {$gte: ISODate("2020-01-01")}})

db.teste.updateOne({usuario: "Semantix"},{$currentDate: {datta_acesso: {$type: "timestamp"}}})

db.teste.deleteOne({"_id": ObjectId("60a1bdbd4dd3380d64faa76a")})

db.teste.drop()

show collections

### Trabalhando com Index

- São estruturas de dados especiais
    - Armazena o valor de um atributo específico ou conjunto de atributos, ordenado pelo valor do atributo
    - Estrutura de dados B-tree

- Index Default
    - MongoDB cria um index exclusivo no atibuto _id
    - Recomenda-se sempre usar esse default
        - Aplicação deve garantir a exclusividade dos valores no atributo _id para evitar erros

- Criação de Index
    - Método createIndex
    - Irá criar um índice se ainda não existir um índice com a mesma especificação
        - db.<nomeCollection>.createIndex( {<key>, <opções>} )
        - Key - <atributo>:<valor>
            - Valor
                - 1 – Ordenação Ascendente
                - 1 – Ordenação Descendente

- Criação e Visualização - Index
    - Criação de index
        - db.cliente.createIndex({nome: 1})
    - Visualizar indexes
        - db.cliente.getIndexes()

- Criação e Visualização - Index
    - Nome Padrão do Index
        - <atributo>_<valor>_ <atributo>_<valor> ...

        - db.cliente.createIndex({nome: 1, item: -1})
    - Nome do Index: nome_1_item:_-1
        - db.cliente.createIndex({nome: 1, item: -1},{name: “query itens”})
- Index Exclusivo
    - db.cliente.createIndex( { user_id: 1 }, { unique: true } )
    - db.cliente.createIndex( { user_id: 1, nome: 1 }, { unique: true } 

### Remover Index
    - Excluir um Index
        - db.<nomeCollection>.dropIndex({<key>})
        - db.cliente.dropIndex({nome: 1})
    - Excluir todos os indexes de uma collection
        - db.<nomeCollection>.dropIndexes()
        - db.cliente.dropIndexes()

### Consultas com Index
- Método Hint
    - Forçar o Otimizador de consultas do MongoDB fazer uso de um índice específico
        - db.<nomeCollection>.find({<filtro>}).hint({<key>})
        - db.cliente.find().hint({nome: 1})
    - Como confirmar se a consulta faz uso do índice?
- Método explain
    - Plano de execução
        - Compreender e otimizar consultas
        - Verificar stage "queryPlanner.winningPlan"
        - COLLSCAN: Collection Scan
        - IXSCAN: Scan com Index keys
        - FETCH : Recuperar documentos
        - SHARD_MERGE: Junção de shards
        - SHARDING_FILTER: Filtrar documentos órfãos em shards

### Exercícios

db.produto.createIndex({nome: 1},{name: "query_produtos"})

db.produto.getIndexes()
    
db.produto.find().explain()

db.produto.find().hint({nome:1})

db.produto.find().hint({nome:1}).explain()

db.produto.dropIndex({nome: 1})

db.produto.getIndexes()

### Consultas com Regex
    - Aplicar expressão regulares nas consultas

        - db.<nomeCollection>.find({ <field>: { $regex: /pattern/, $options: '<options>' } })
        - db.<nomeCollection>.find({{ <field>: { $regex: 'pattern', $options: '<options>' } })
        - db.<nomeCollection>.find({{ <field>: { $regex: /pattern/<options> }})
    - Options
        - i – Ignorar case-sensitive
        - m – Combinar várias linhas
            - Incluir as ancoras ^ no inicio e $ no final


### Exercícios

db.produto.find({nome: {$regex: "cpu"}})

db.produto.find({nome: {$regex: /cpu/}})

db.produto.find({nome: {$regex: /^hd/}})

db.produto.find({nome: {$regex: /^externo/}})

db.produto.find({nome: {$regex: /^hd/}},{nome: 1, qtd: 1, _id:0})

db.produto.find({"descricao.armazenamento": {$regex: /GB/}})

db.produto.find({"descricao.armazenamento": {$regex: /gb/i}})

db.produto.find({"descricao.armazenamento": {$regex: /gb/i}}, {nome:1, descricao: 1})

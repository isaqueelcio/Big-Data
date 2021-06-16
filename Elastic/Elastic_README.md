# Elastic

***********sempre antes de iniciar o cluster 
No power shell

wsl -d docker-desktop 
sysctl -w vm.max_map_count=262144
**********
Kibana: http://localhost:5601/
Elasticsearch: http://localhost:9200/

### Exercícios - CRUD

1. Criar o índice produto e inserir os seguintes documentos:

_id: 1, "nome": "mouse", "qtd": 50, "descricao": "com fio USB, compatível com Windows, Mac e Linux"
_id: 2, "nome": "hd", "qtd": 20, "descricao": "Interface USB 2.0, 500GB, Sistema: Windows 10, Windows 8, Windows 7 "
_id: 3, "nome": "memória ram", "qtd": 10, "descricao": "8GB, DDR4"
_id: 4, "nome": "cpu", "qtd": 15, "descricao": "i5, 2.5Ghz"
************************
POST produto/_doc/4
{
  "nome": "cpu", "qtd": 15, "descricao": "i5, 2.5Ghz"
}


POST produto/_doc/3
{
  "nome": "memória ram", "qtd": 10, "descricao": "8GB, DDR4"
}

POST produto/_doc/2
{
  "nome": "hd",
  "qtd": 20,
  "descricao": "Interface USB 2.0, 500GB, Sistema: Windows 10, Windows 8, Windows 7"
}


POST produto/_doc/1
{
  "nome": "mouse",
  "qtd": 50,
  "descricao": "com fio USB, compatível com Windows, Mac e Linux"
}

*********************

2. Verificar se existe o documento com  id 3

HEAD produto/

HEAD produto/_doc/3

3. Alterar o valor do atributo qtd para 30 do documento com id 3

POST produto/_update/3
{
  "doc": {
    "qtd":30
  }
}

4. Buscar o documento com id 1

GET produto/_doc/1

5. Deletar o documento com id 4

DELETE produto/_doc/4
GET produto/_doc/4

6. Contar quantos documentos tem o índice produto

GET produto/_count

### Exercícios - Bulk API e Importação

1. Importar os dados na Guia Arquivos para os índices

Índice: concessionaria2
dataset/cars.bulk
Índice: populacao
dataset/populacaoLA.csv
2. Executar as consultas

Contar o número de documentos de cada um dos novos índices
GET concessionaria2/_count
GET populacao/_count

Mostrar todos os documentos de cada um dos novos índices

GET concessionaria2/_search
GET populacao/_search

### Exercícios - Pesquisa e Paginação

1. Pesquisar no índice produto os documentos com os seguintes atributos:
a) Nome = mouse

GET produto/_search?q=nome:mouse

b) Quantidade = 30

GET produto/_search?q=qtd:30


c) Descrição = USB

GET produto/_search?q=descricao:usb

d) Nome = hd e descrição = windows

GET produto/_search?q=nome:hd&q=descricao:windows

e) Nome = memória e descrição = GB

GET produto/_search?q=nome:memória&q=descricao:DDR4

2. Pesquisar todos os índices, limitando a pesquisa em 5 documentos em cada página e visualizar a 4 página (Documentos entre 16 á 20 )

GET _all/_search?&size=5&from=15

### Exercícios -  Índices  

1. Visualizar as configurações do índice produto

GET produto/_settings

2. Visualizar o mapeamento do índice produto

GET produto/_mapping

3. Visualizar o mapeamento do atributo nome do índice produto

GET produto/_mapping/field/nome

4. Inserir o campo data do tipo date no índice produto

PUT produto/_mapping
{
  "properties":{
    "data":{
      "type":"date"
    }
  }
}

5. Adicionar o documento:

_id: 6, "nome": "teclado", "qtd": 100, "descricao": "USB", "data":"2020-09-18"

PUT produto/_doc/6
{
  "nome": "teclado",
  "qtd": 100,
  "descricao": "USB",
  "data":"2020-09-18"
}

6. Reindexar o índice produto para produto2, com o campo quantidade para o tipo short

PUT produto2/_mapping
{
  "properties": {
    "data": {
      "type": "date"
    },
    "descricao": {
      "type": "text",
      "fields": {
        "keyword": {
          "type": "keyword",
          "ignore_above": 256
        }
      }
    },
    "nome": {
      "type": "text",
      "fields": {
        "keyword": {
          "type": "keyword",
          "ignore_above": 256
        }
      }
    },
    "qtd": {
      "type": "short"
    }
  }
}

********
POST _reindex
{
  "source": {
    "index": "produto"
  }
  , "dest": {
    "index": "produto2"
  }
}
************
GET produto2/_search

7. Visualizar o mapeamento do índice produto2

GET produto2/_mapping

8. Fechar o índice produto

POST produto/_close

9. Pesquisar todos os documentos no índice produto

POST produto/_open
GET produto/_search

10. Abrir o índice produto

POST produto/_open

Exercícios - Query e Filtros

1. Buscar no termo nome o valor mouse
#---------------#
HEAD produto

GET produto/_search
{
  "query":{
    "term": {
      "nome": {
        "value": "mouse"
      }
    }
  }
}
# -------------------#
2. Buscar no termo nome os valores mouse e teclado
#------------------------------#
GET produto/_search
{
  "query": {
    "terms": {
      "nome": ["mouse","teclado"]
    }
  }
}
#------------------------------#

3. Realizar a mesma busca do item 1 e 2, desconsiderando o score
#------------------------------#
GET produto/_search
{
  "query": {
    "constant_score": {
      "filter": {
        "terms": {
          "nome": ["mouse","teclado"]
        }
      }
    }
  }
}


GET produto/_search
{
  "query": {
    "constant_score": {
      "filter": {
        "term": {
          "nome": "mouse"
        }
      }
    }
  }
}

#------------------------------#

4. Buscar os documentos que contenham a palavra “USB” no atributo descrição
#------------------------------#
GET produto/_search
{
  "query": {
    "match": {
      "descricao": "USB"
    }
  }
}

#------------------------------#

5. Buscar os documentos que contenham a palavra “USB” e não contenham a palavra “Linux” no atributo descrição
#------------------------------#
GET produto/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match":{
            "descricao": "USB"
          }
          
        }
      ],
      "must_not": [
        {"match": {
          "descricao": "linux"
        }}
      ]
    }
  }
}

#------------------------------#
6. Buscar os documentos que podem ter a palavra “memória” no atributo nome ou contenham a palavra “USB” e não contenham a palavra “Linux” no atributo descrição
#------------------------------#
GET produto/_search
{
  "query": {
    "bool": {
      "should": [
        {
          "match": {
            "nome": "memória"
          }
        },
        {
          "match": {
            "descricao": "USB"
          }
        }
      ],
      "must_not": [
        {
          "match": {
            "descricao": "Linux"
          }
        }
      ]
    }
  }
}

#------------------------------#
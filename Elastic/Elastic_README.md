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

### Exercícios - Query e Filtros

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

### Exercícios - Ordem de Busca

1. Buscar os documentos que contenham as palavras “Windows” e “Linux” no atributo descrição
#------------------------------#
GET produto/_search
{
  "query": {
    "match": {
      "descricao": {
        "query": "windows linux",
        "operator": "and"
      }
    }
  }
}
#------------------------------#

2. Buscar os documentos que contenham as palavras “Windows”, “Linux” ou “USB” no atributo descrição
#------------------------------#
GET produto/_search
{
  "query": {
    "match": {
      "descricao": {
        "query": "windows linux usb",
        "operator": "or"
      }
    }
  }
}
#------------------------------#
#------------------------------#
GET produto/_search
{
  "query": {
    "match": {
      "descricao": "windows linux usb"
    }
  }
}
#------------------------------#

3. Buscar os documentos que contenham pelo menos 2 palavras da seguinte lista de palavras: “Windows”; “Linux” e “USB” no atributo descrição
#------------------------------#
GET produto/_search
{
  "query": {
    "match": {
      "descricao": {
        "query": "windows linux usb",
        "operator": "or",
        "minimum_should_match": 2
      }
    }
  }
}
#------------------------------#
4. Buscar os documentos que contenham pelo menos 50 % da seguinte lista de palavras: “Windows”; “Linux” e “USB” no atributo descrição
#------------------------------#
GET produto/_search
{
  "query": {
    "match": {
      "descricao": {
        "query": "windows linux usb",
        "operator": "or",
        "minimum_should_match": "50%"
      }
    }
  }
}
#------------------------------#

### Exercícios - Consultas por Intervalo
1. Verificar se existe o índice populacao
#------------------------------#
HEAD populacao
#------------------------------#

2. Executar as consultas no índice populacao
a) Mostrar os documentos com o atributo "Total Population" menor que 100
#------------------------------#
GET populacao/_search
{
  "query": {
    "range": {
      "Total Population": {
          "lt": 100
      }
    }
  }
}
#------------------------------#

b) Mostrar os documentos com o atributo "Median Age" maior que 70
#------------------------------#
GET populacao/_search
{
  "query": {
    "range": {
      "Median Age": {
          "gt": 70
      }
    }
  }
}
#------------------------------#

c) Mostrar os documentos 50 (Zip Code: 90056) à 60 (Zip Code: 90067) do índice de populacao
#------------------------------#
GET populacao/_search
{
  "query": {
    "range": {
      "Zip Code": {
          "gte": 90056,
          "lte":90067
      }
    }
  }
}
#------------------------------#

3. Importar através do Kibana o arquivo weekly_MSFT.csvPré-visualizar o documento (Guia Arquivos/dataset/weekly_MSFT.csv) com o índice bolsa



4. Executar as consultas no índice bolsa

a) Visualizar os documentos do dia 2019-01-01 à 2019-03-01. (hits = 9)
#------------------------------#
GET bolsa/_mapping

GET bolsa/_search
{
  "query": {
    "range": {
      "timestamp": {
        "gte": "2019-01-01",
        "lte": "2019-03-01",
        "format": "yyyy-MM-dd"
      }
    }
  }
}
#------------------------------#

b) Visualizar os documentos do dia 2019-04-01 até agora. (hits = 3) 
#------------------------------#
GET bolsa/_search
{
  "query": {
    "range": {
      "timestamp": {
        "gte": "2019-04-01",
        "lte": "now",
        "format": "yyyy-MM-dd"
      }
    }
  }
}
#------------------------------#

### Exercícios - Analyzer

1. Criar os Analyzer simple, standard, brazilian e portuguese para a seguinte frase:
O elasticsearch surgiu em 2010
#------------------------------#
POST _analyze
{
  "analyzer": "simple",
  "text": "O elasticsearch surgiu em 2010"
}
#------------------------------#
#------------------------------#
POST _analyze
{
  "analyzer": "standard",
  "text": "O elasticsearch surgiu em 2010"
}
#------------------------------#
#------------------------------#
POST _analyze
{
  "analyzer": "brazilian",
  "text": "O elasticsearch surgiu em 2010"
}
#------------------------------#
#------------------------------#
POST _analyze
{
  "analyzer": "portuguese",
  "text": "O elasticsearch surgiu em 2010"
}
#------------------------------#

2. Realizar os passos no índice produto
a) Criar um analyzer brazilian para o atributo descricao
#------------------------------#
GET produto/_mapping

PUT produto1
{
  "settings": {
    "index":{
      "number_of_shards": 1,
      "number_of_replicas": 0
    }
  }, 
  "mappings": {
    "properties": {
      "descricao":{
        "type":"text",
        "analyzer": "brazilian"
      }
    }
  }
}

POST _reindex
{
  "source": {
    "index": "produto"
  },
  "dest": {
    "index": "produto1"
  }
}

GET produto1/_mapping
#------------------------------#
b) Para o atributo descricao aplicar o analzyer brazilian para o tipo de campo text e criar o  atributo descricao.original com o dado do tipo keyword
#------------------------------#
GET produto/_mapping

DELETE produto

PUT produto
{
  "settings": {
    "index":{
      "number_of_shards": 1,
      "number_of_replicas": 0
    }
  }, 
  "mappings": {
    "properties": {
      "descricao":{
        "type":"text",
        "analyzer": "brazilian",
        "fields":{
          "original":{"type":"keyword"}
        }
      }
    }
  }
}

POST _reindex
{
  "source": {
    "index": "produto1"
  },
  "dest": {
    "index": "produto"
  }
}

GET produto/_search
#------------------------------#

c) Buscar a palavra “compativel” no campo descricao.original (hits = 0)
#------------------------------#
GET produto/_search
{
  "query": {
    "match": {
      "descricao.original": "compativel"
    }
  }
}
#------------------------------#

d) Buscar a palavra “compativel” no campo descricao
#------------------------------#
GET produto/_search
{
  "query": {
    "match": {
      "descricao": "compativel"
    }
  }
}
#------------------------------#

### Exercícios - Agregações

1. Calcular a média do campo volume
#------------------------------#
GET bolsa/_search
{
  "size": 0, 
  "aggs": {
    "media": {
      "avg": {
        "field": "volume"
      }
    }
  }
}
#------------------------------#

2. Calcular a estatística do campo close
#------------------------------#
GET bolsa/_search
{
  "size": 0, 
  "aggs": {
    "media": {
      "stats": {
        "field": "close"
      }
    }
  }
}
#------------------------------#
3. Visualizar os documentos do dia 2019-04-01 até agora. (hits = 3)
#------------------------------#
GET bolsa/_search
{
  "query": {
    "range": {
      "@timestamp": {
        "gte": "2019-04-01",
        "lte": "now"
      }
    }
  }
}
#------------------------------#
4. Calcular a estatística do campo open do período do dia 2019-04-01 até agora
#------------------------------#
GET bolsa/_search
{
  "size": 0, 
  "query": {
    "range": {
      "@timestamp": {
        "gte": "2019-04-01",
        "lte": "now"
      }
    }
  },
  "aggs": {
    "estatistica": {
      "stats": {
        "field": "open"
      }
    }
  }
}
  
#------------------------------#
5. Calcular a mediana do campo open
#------------------------------#
GET bolsa/_search
{
  "size": 0,
  "aggs": {
    "mediana": {
      "percentiles": {
        "field": "open",
        "percents": [
          1,
          5,
          25,
          50,
          75,
          95,
          99
        ]
      }
    }
  }
}
#------------------------------#
6. Contar a quantidade de documentos agrupados por ano
#------------------------------#
GET bolsa/_search
{
  "size": 0,
  "aggs": {
    "doc_anos": {
      "date_histogram": {
        "field": "@timestamp",
        "calendar_interval": "year"
      }
    }
  }
}
#------------------------------#
7. Contar a quantidade de documentos de 2 anos atrás até hoje
#------------------------------#
GET bolsa/_search
{
  "size": 0,
  "aggs": {
    "qtd_2anos": {
      "date_range": {
        "field": "@timestamp",
        "ranges": [
          {
            "from": "now-2y",
            "to": "now"
          }]
      }
    }
  }
}
#------------------------------#

### Exercícios - Beats

1. Enviar o arquivo <local>/paris-925.logs com uso do Filebeat
#------------------------------#
$ curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.9.2-linux-x86_64.tar.gz

 tar xzvf filebeat-7.9.2-linux-x86_64.tar.gz


/home/ies/treinamentos/elastic/dataset


./filebeat test config
./filebeat test output 

sudo chown root filebeat.yml

./filebeat -e
#------------------------------#
2. Verificar a quantidade de documentos do índice criado pelo Filebeat e visualizar seus 10 primeiros documentos
#------------------------------#
GET filebeat-7.9.2-2021.06.17-000001/_count

GET filebeat-7.9.2-2021.06.17-000001/_search

#------------------------------#

3. Monitorar as métricas do docker

Referência:
https://www.elastic.co/guide/en/beats/metricbeat/current/metricbeat-module-docker.html (Links para um site externo.)

curl -L -O https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-7.9.2-linux-x86_64.tar.gz
tar xzvf metricbeat-7.9.2-linux-x86_64.tar.gz
cd metricbeat-7.9.2-linux-x86_64/

./metricbeat modules list

./metricbeat modules enable docker

./metricbeat test config

./metricbeat test output

Encontrar o socket do Docker
$ sudo find / -name docker.sock

/mnt/wsl/docker-desktop/shared-sockets/guest-services/docker.sock

sudo chown root modules.d/docker.yml
sudo chown root modules.d/system.yml
sudo chown root metricbeat.yml

sudo ./metricbeat -e

4. Verificar a quantidade de documentos do índice criado pelo Metricbeat e visualizar seus 10 primeiros documentos

GET metricbeat-7.9.2-2020.10.13-000001/_count

5. Monitorar o site https://www.elastic.co/pt/ (Links para um site externo.) com uso do Heartbeat

curl -L -O https://artifacts.elastic.co/downloads/beats/heartbeat/heartbeat-7.9.2-linux-x86_64.tar.gz

tar xzvf heartbeat-7.9.2-linux-x86_64.tar.gz

 cd heartbeat-7.9.2-linux-x86_64/

./heartbeat test config

./heartbeat test output

./heartbeat -e



6. Verificar a quantidade de documentos do índice criado pelo Heartbeat e visualizar seus 10 primeiros documentos

GET heartbeat-7.9.2-2021.06.20-000001/_count

### Exercício de Logstash

1. Enviar o arquivo <local>/paris-925.logs  para o Logstash, com uso do Filebeat.

2. Configurar e executar o logstash com as seguintes configurações

Entrada:
beats {
            port => 5044

        }

Saída:
elasticsearch {
            hosts => [ “elasticsearch:9200" ]

            index => “seu_nome-%{+YYYY.MM.dd}"

        }

3. Verificar a quantidade de documentos do índice criado pelo Logstash e visualizar seus 10 primeiros documentos
#---------------------------------#
GET _cat/indices?v

GET isaque-2020.10.15/_count

GET isaque-2020.10.15/_search
#---------------------------------#
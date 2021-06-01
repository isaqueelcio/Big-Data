# Hadoop

https://github.com/rodrigo-reboucas/docker-bigdata

### Sistema HDFS e diretórios

    - Similar ao Linux, mas inicia com "hdfs dfs"

- hdfs dfs -<comando>[argumentos]
    - Sistema HDFS
    - $ hdfs dfs -help

### Comandos Diretórios
- Criar diretório
    - mkdir <diretório>
    - Criar estrutura de diretórios
    - mkdir -p <diretório>/<diretório>/<diretório>

- Listar diretório
    - ls <diretório>
    - Recursivo: -R

- Remoção arquivos e diretórios
    - rm <src>
    - Argumentos
    - -r: Deletar diretório
    - -skipTrash: Remover permanentemente

### Comandos Enviar dados
-Local /HDFS
    - Enviar arquivo ou diretório
        - put <src> <dst> (mais usado)
        - -f: Sobrescreve o destino, se já existir.
        - -l: Força um fator de replicação de
        - copyFromLocal <src> <dst>

    - Mover arquivo ou diretório
        - Put que deleta do local
        - moveFromLocal <src> <dst>

- HDFS/Local
    - Receber arquivo ou diretório
        - get <src> <dst>
        - Argumento - f
    - Cria um único arquivo mesclado
        - getmerge <src> <dst>

    - Mover para o local
        - Get que deleta a cópia do HDFS
        - moveToLocal <src> <localdst>

    - Copiar arquivo ou diretório
        - cp <src> <dst>
            - Argumento –f

- HDFS/HDFS
    - Mover arquivo ou diretório
        - mv <src> <dst>
        - mv <arquivo1> <arquivo2> <arquivo3> <dst>

- Comandos 
    - Mostrar o uso do disco
        - du -h <diretório>
    - Exibir o espaço livre
        - df -h <diretório>
    - Mostrar as informações do diretório
        - stat <diretório>
            - hdfs dfs -stat %r name.txt #fator de replicação
            - hdfs dfs -stat %o name.txt #tamanho do bloco
    - Contar o número de diretórios, número de arquivos e tamanho do arquivo especificado
        - count -h <diretório>
    - Esvaziar a lixeira
        - expunge
    - Retornar as informações da soma de verificação de um arquivo
        - checksum <arquivo>
    - Localiza todos os arquivos que correspondem à expressão
        - find <caminho> <procura> -print
        - hdfs dfs -find / -name data
        - hdfs dfs -find / -iname Data -print /user/semantix/input/data
        - hdfs dfs -find input/ -name \*.txt -print input/teste1.txt, input/teste2.txt
    
### Exercícios
1. Iniciar o cluster de Big Data
cd docker-bigdata
docker-compose up -d

2. Baixar os dados dos exercícios do treinamento
cd input
sudo git clone https://github.com/rodrigo-reboucas/exercises-data.git

3. Acessar o container do namenode
docker exec -it namenode bash 
4. Criar a estrutura de pastas apresentada a baixo pelo comando: $ hdfs dfs -ls -R 
    /user/aluno/
        <nome>
            data
                recover
                    delete

listar
hdfs dfs -ls -R /
hdfs dfs -ls -R /user
hdfs dfs -mkdir -p /user/aluno/isaque/data
hdfs dfs -mkdir -p /user/aluno/isaque/delete
hdfs dfs -mkdir -p /user/aluno/isaque/recover
hdfs dfs -ls -R /user/aluno/
Enviar arquivos 
hdfs dfs -put /input/exercises-data/escola /user/aluno/isaque/data
Verificar envio 
hdfs dfs -ls -R /user/aluno/isaque/data

5. Enviar a pasta “/input/exercises-data/escola” e o arquivo “/input/exercises-data/entrada1.txt” para  data
Enviar arquivo
hdfs dfs -put /input/exercises-data/entrada1.txt /user/aluno/isaque/data
Verificar envio
hdfs dfs -ls /user/aluno/isaque/data

6. Mover o arquivo “entrada1.txt” para recover
hdfs dfs -mv /user/aluno/isaque/data/entrada1.txt /user/aluno/isaque/recover
Verificar
hdfs dfs -ls /user/aluno/isaque/recover
hdfs dfs -ls /user/aluno/isaque/data

7. Baixar o arquivo do hdfs “escola/alunos.json” para o sistema local /

8. Deletar a pasta recover
hdfs dfs -rm -R /user/aluno/isaque/delete

9. Deletar permanentemente o delete
hdfs dfs -rm -skipTrash -R /user/aluno/isaque/delete

10. Procurar o arquivo “alunos.csv” dentro do /user
hdfs dfs -find /user -name alunos.csv

11. Mostrar o último 1KB do arquivo “alunos.csv”
hdfs dfs -tail /user/aluno/isaque/data/escola/alunos.csv

12. Mostrar as 2 primeiras linhas do arquivo “alunos.csv”
hdfs dfs -cat /user/aluno/isaque/data/escola/alunos.csv | head -n 2

13. Verificação de soma das informações do arquivo “alunos.csv”
hdfs dfs -checksum /user/aluno/isaque/data/escola/alunos.csv

14. Criar um arquivo em branco com o nome de “test” no data
hdfs dfs -touchz /user/aluno/isaque/data/test

hdfs dfs -ls /user/aluno/isaque/data/

15. Alterar o fator de replicação do arquivo “test” para 2
hdfs dfs -setrep 2 /user/aluno/isaque/data/test
hdfs dfs -ls /user/aluno/isaque/data/

16. Ver as informações do arquivo “alunos.csv”
hdfs dfs -stat %r /user/aluno/isaque/data/escola
hdfs dfs -stat %o /user/aluno/isaque/data/escola

17. Exibir o espaço livre do data e o uso do disco
hdfs dfs -df /user/aluno/isaque/data

hdfs dfs -df -h /user/aluno/isaque/data

hdfs dfs -du -h /user/aluno/isaque/data

hdfs dfs -du -h /

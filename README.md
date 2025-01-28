# Demanda 4  

Desenvolvida em ambiente WSL com Ubuntu 22.04.4 LTS e executada localmente utilizando o IP da máquina obtido com o comando ```ip a```.  

## **Como executar o projeto**  

1. Clone o repositório
```
git clone https://github.com/anycarolinys/dockerized_elk_stack.git
```

2. Execute os containers em segundo plano 
```
docker-compose up --build -d 
```
Para execuções posteriores basta
```
docker-compose up -d 
```
3. Devem haver três containers em execução (ElasticSearch, Kibana, Logstash) confira com
```
docker ps
```

4. Para acessar a interface do Kibana 
```
http://<localhost>:5601
```
Username: elastic  
Password: password  

5. Na interface, para acessar os índices do ElasticSearch: 
    - Clique na barra lateral -> Management -> Dev Tools use a query ```GET _cat/indices?v```, os índices que não começam com '.' são indexados pelo Logstash no ElasticSearch
6. Na interface, para obter uma visualização com Kibana:  
    - Clique na barra lateral -> Analytics -> Discover
    - No canto superior esquerdo clique em "Filebeat" (a data view selecionada por padrão) e mude para "Air Data View"
    - Realize uma query como "message : *New York* and message: *Summer*" para obter um histograma com a quantidade de registros que possuem essas duas palavras chaves no índice
    - No canto superior direito é possível filtrar por data entre outros atributos  
    **\*[Link](https://youtu.be/VZqmJKEcG-0) para um vídeo produzido por mim com a demonstração desse passo a passo**

7. Para encerrar a execução dos containers  
```
docker-compose down
```

## **Como foi desenvolvido o projeto**  
**1. Busca por material de referência**
- Apesar de conhecer o ElasticSearch/OpenSearch não havia configurado do zero
- Encontrei um [tutorial](https://www.elastic.co/blog/getting-started-with-the-elastic-stack-and-docker-compose
) publicado pela Elastic para subir os serviços da stack com docker compose e resolvi segui-lo, ignorando as etapas relacionadas ao Filebeat e Metricbeat  

**2. Implementação do Docker Compose**  
- Segui os passos indicados no tutorial e não houveram problemas de execução no ambiente do Linux WSL  

**3. Teste dos serviços**  
- Para testar se o Logstash era capaz de processar logs, realizei modificações na pasta ```logstash_ingest_data``` adicionando arquivos de log de exemplo e acompanhando as alterações no recurso "Stack Management -> Index Management" na interface do Kibana  
- Ao adicionar logs manualmente na pasta notei que eles não estavam sendo atualizados automaticamente, sendo assim, foram realizadas duas modificações no arquivo ```logstash.conf```
    - O parâmetro ```path``` foi alterado para processar qualquer tipo de arquivo, não apenas CSV
    - O parâmetro ```exit_after_read``` foi removido para que o Logstash monitorasse a pasta continuamente  

## **Resultados**
- Elasticsearch deve ser configurado com uma senha de segurança  
Variável ```ELASTIC_PASSWORD``` em ```.env``` sendo referenciada no ```docker-compose.yml```  

- Logstash deve ser configurado para processar logs  
Arquivo ```logstash.conf```

- Kibana deve ser configurado para acessar o Elasticsearch e exibir logs  
Parâmetro ```environment``` do serviço kibana no ```docker-compose.yml```  

- O Kibana deve ser acessível na interface web (por padrão, na porta 5601)  
Variável ```KIBANA_PORT``` em ```.env``` sendo referenciada no ```docker-compose.yml```  

- O Elasticsearch deve persistir dados entre reinicializações  
Configuração ```- esdata01:/usr/share/elasticsearch/data``` no serviço ```es01``` no ```docker-compose.yml```  

## **O que poderia ter sido melhorado/realizado com mais tempo**  
- Poderia ser realizada uma aplicação com Shell Script para obter logs do sistema e enviá-los para processamento e indexação no Logstash  
- Poderia ter sido  integrado o Filebeat para coletar logs em tempo real de alguma aplicação e enviá-los diretamente ao Logstash ou Elasticsearch  
- Metricbeat poderia ser configurado para coletar métricas do sistema (como uso de CPU, memória e rede) e enviá-las ao Elasticsearch  

## **Principais dificuldades**  
**1. Tentativa de desenvolver o projeto em ambiente Windows 11**
- O container do ElasticSearch encerrava devido um erro com a configuração de memória virtual
- O [tutorial orienta](https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html#_windows_with_docker_desktop_wsl_2_backend) fazer a alteração do tamanho da memória virtual acessando o container do Docker fazendo  
```
wsl -d docker-desktop -u root
sysctl -w vm.max_map_count=262144
```
ou ainda, acessando o arquivo ```.wslconfig``` e adicionando o seguinte conteúdo 
```
[wsl2]
kernelCommandLine = "sysctl.vm.max_map_count=262144"
```
- Contudo, mesmo realizando essas alterações e reiniciando a máquina, o container do ElasticSearch continuava encerrando, então resolvi partir para o uso do Linux WSL como ambiente


## **Principais aprendizados**

- Compreensão queries e indexação para ElasticSearch
- Compreensão de processamento de logs com Logstash
- Configuração de dashboards de dados em tempo real com Kitana

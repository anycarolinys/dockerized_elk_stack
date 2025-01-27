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

7. Para encerrar a execução dos containers  
```
docker-compose down
```

## **Como foi desenvolvido o projeto**  
**1. Busca por material de referência**
- Apesar de conhecer o ElasticSearch/OpenSearch não havia configurado do zero
- Encontrei um [tutorial](https://www.elastic.co/blog/getting-started-with-the-elastic-stack-and-docker-compos
) publicado pela Elastic para subir os serviços da stack com docker compose e resolvi segui-lo, ignorando as etapas relacionadas ao Filebeat e Metricbeat  

**2. Implementação do Docker Compose**  
- Segui os passos indicados no tutorial e não houve problemas de execução no ambiente do Linux WSL  

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
- Falar do Filebeat/Metricbeat

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
- Falar sobre a intervenção do WSL no VirtualBox  

<!-- - Compreensão dos objetivos das ferramentas Prometheus e Grafana e como elas se comunicam entre si

- Compreensão do uso da API do Prometheus para Python
 
- Compreensão dos dashboards no Grafana, utilizando queries com código ou no builder, configuração de unidades de medida, formatação de legenda e título para o usuário final  ([vídeo utilizado](https://youtu.be/EGgtJUjky8w?si=H2K8y3eOY5DZTysX)) -->



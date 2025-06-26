# Домашнее задание к занятию «ELK» Шелухин Юрий

## Дополнительные ресурсы

При выполнении задания используйте дополнительные ресурсы:
- [docker-compose elasticsearch + kibana](11-03/docker-compose.yaml);
- [поднимаем elk в docker](https://www.elastic.co/guide/en/elasticsearch/reference/7.17/docker.html);
- [поднимаем elk в docker с filebeat и docker-логами](https://www.sarulabs.com/post/5/2019-08-12/sending-docker-logs-to-elasticsearch-and-kibana-with-filebeat.html);
- [конфигурируем logstash](https://www.elastic.co/guide/en/logstash/7.17/configuration.html);
- [плагины filter для logstash](https://www.elastic.co/guide/en/logstash/current/filter-plugins.html);
- [конфигурируем filebeat](https://www.elastic.co/guide/en/beats/libbeat/5.3/config-file-format.html);
- [привязываем индексы из elastic в kibana](https://www.elastic.co/guide/en/kibana/7.17/index-patterns.html);
- [как просматривать логи в kibana](https://www.elastic.co/guide/en/kibana/current/discover.html);
- [решение ошибки increase vm.max_map_count elasticsearch](https://stackoverflow.com/questions/42889241/how-to-increase-vm-max-map-count).

**Примечание**: если у вас недоступны официальные образы, можете найти альтернативные варианты в DockerHub, например, [такой](https://hub.docker.com/layers/bitnami/elasticsearch/7.17.13/images/sha256-8084adf6fa1cf24368337d7f62292081db721f4f05dcb01561a7c7e66806cc41?context=explore).

## Задание 1. Elasticsearch 

Установите и запустите Elasticsearch, после чего поменяйте параметр cluster_name на случайный. 

*Приведите скриншот команды 'curl -X GET 'localhost:9200/_cluster/health?pretty', сделанной на сервере с установленным Elasticsearch. Где будет виден нестандартный cluster_name*.

---

### Решение 1.
1. Установим Terraform    
`sudo snap install terraform --classic`
 
<img src = "img/1-1.png" width = 60%>      

2. Создадим плейбук. Установим Nginx путем передачи в Terraform  параметров с помощью #cloud-config в файле metadata.yaml. Ключ от сервисного аккаунта укажем в отдельном файле. 
   
`   terraform {
  required_providers {
    yandex = {
      source = "yandex-cloud/yandex"
    }
  }
  required_version = ">= 0.13"
}` 

`provider "yandex" {
  service_account_key_file = "/home/yury/key.json" 
  cloud_id                 = "b1ge6ksn8gkr97asu03a"
  folder_id                = "b1gsn46kdu9vi56ievnv"
  zone      = "ru-central1-b"
}`  
Весь плейбук в приложенном файле
#### Конфигурационные файлы.  
[плейбук](files/main1.tf)  
[cloud-config](files/metadata.yaml)

3. Запустим Terraform, проверим создание виртуальных машин и балансирощика, сделаем запрос на порт 80 балансирощика, убедимся,что работает Nginx.  
`terraform validate`  
`terraform init`    
`terraform apply`    
<img src = "img/1-2.png" width = 60%>
<img src = "img/1-3.png" width = 60%>
<img src = "img/1-4.png" width = 60%>
<img src = "img/1-5.png" width = 60%>
<img src = "img/1-6.png" width = 60%>  
  
`terraform destroy`  

---

#### Задание 2. Kibana

Установите и запустите Kibana.

*Приведите скриншот интерфейса Kibana на странице http://<ip вашего сервера>:5601/app/dev_tools#/console, где будет выполнен запрос GET /_cluster/health?pretty*.

---

### Решение 2*.
1. Создадим плейбук и файл метаданных. 
 
#### Конфигурационные файлы.  
[плейбук](files/main2.tf)  
[cloud-config](files/metadata.yaml)

2. Запустим Terraform, проверим создание виртуальных машин и балансирощика, сделаем запрос на порт 80 балансирощика, убедимся,что работает Nginx.    

<img src = "img/2-1.png" width = 60%>
<img src = "img/2-2.png" width = 60%>
<img src = "img/2-3.png" width = 60%>
<img src = "img/2-4.png" width = 60%>
<img src = "img/2-5.png" width = 60%>
<img src = "img/2-6.png" width = 60%>  

---


### Задание 3. Logstash

Установите и запустите Logstash и Nginx. С помощью Logstash отправьте access-лог Nginx в Elasticsearch. 

*Приведите скриншот интерфейса Kibana, на котором видны логи Nginx.*

---

### Задание 4. Filebeat. 

Установите и запустите Filebeat. Переключите поставку логов Nginx с Logstash на Filebeat. 

*Приведите скриншот интерфейса Kibana, на котором видны логи Nginx, которые были отправлены через Filebeat.*


## Дополнительные задания (со звёздочкой*)
Эти задания дополнительные, то есть не обязательные к выполнению, и никак не повлияют на получение вами зачёта по этому домашнему заданию. Вы можете их выполнить, если хотите глубже шире разобраться в материале.

### Задание 5*. Доставка данных 

Настройте поставку лога в Elasticsearch через Logstash и Filebeat любого другого сервиса , но не Nginx. 
Для этого лог должен писаться на файловую систему, Logstash должен корректно его распарсить и разложить на поля. 

*Приведите скриншот интерфейса Kibana, на котором будет виден этот лог и напишите лог какого приложения отправляется.*
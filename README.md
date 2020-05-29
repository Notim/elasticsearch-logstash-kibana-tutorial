Amazon Linux AMI 2015.09

Elasticsearch 7.7
===================
```bash
sudo su

yum update -y
yum install java-1.8.0-openjdk

cd /root
https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.7.0-x86_64.rpm
yum install elasticsearch-7.7.0-x86_64.rpm -y
rm -f elasticsearch-1.7.2.noarch.rpm

cd /etc/elasticsearch

echo '
cluster.name: "veget-elkstack"

node.name: "Phanton Assassin"
node.master: true
node.data: true

discovery.seed_providers: ec2
discovery.ec2.access_key: AKIA5ZQX3DCMR4IV5PXO
discovery.ec2.secret_key: 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
discovery.ec2.availability_zones: ["us-east-1"]
discovery.ec2.tag.Name: "veget-elasticsearch"

http.cors.enabled: true
http.cors.allow-origin: "*"

' > elasticsearch.yml

systemctl enable elasticsearch
systemctl start elasticsearch
```

Logstash 1.5.4-1
==============

```bash
sudo su

yum update -y
yum install java-1.8.0-openjdk

cd /root
wget https://download.elastic.co/logstash/logstash/packages/centos/logstash-1.5.4-1.noarch.rpm
yum install logstash-1.5.4-1.noarch.rpm -y
rm -f logstash-1.5.4-1.noarch.rpm

echo '
input {
  stdin{ }
  file {
    path => "/tmp/logstash.log"
  }
  http {
    host => "0.0.0.0"
    port => "8080"
  }
}

output {
  elasticsearch {
    host => "ELASTICSEARCH_URL:PORT"
    protocol => "http"
  }
  stdout {
    codec => rubydebug
  }
}
' > /etc/logstash/conf.d/logstash.conf

systemctl enable logstash
systemctl start logstash
```

Kibana 7.7.0
============
```bash
sudo su

yum update -y
yum install java-1.8.0-openjdk

cd /root
wget https://artifacts.elastic.co/downloads/kibana/kibana-7.7.0-x86_64.rpm
rpm --install kibana-7.7.0-x86_64.rpm

echo '
server.port: 5601
server.host: 0.0.0.0
elasticsearch.hosts: ["http://elasticsearch.veget.com.br:80"]
' > config/kibana.yml 

nohup ./bin/kibana &
```

http://KIBANA_URL:5601/

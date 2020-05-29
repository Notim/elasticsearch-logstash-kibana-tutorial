Amazon Linux AMI 2015.09

Elasticsearch 1.7.2
===================
```bash
sudo su

yum update -y
yum install java-1.8.0-openjdk

cd /root
wget https://download.elastic.co/elasticsearch/elasticsearch/elasticsearch-1.7.2.noarch.rpm
yum install elasticsearch-1.7.2.noarch.rpm -y
rm -f elasticsearch-1.7.2.noarch.rpm

cd /usr/share/elasticsearch/

./bin/plugin install mobz/elasticsearch-head
./bin/plugin install lukas-vlcek/bigdesk
./bin/plugin install elasticsearch/elasticsearch-cloud-aws/2.7.1
./bin/plugin install lmenezes/elasticsearch-kopf/1.5.7

cd /etc/elasticsearch

echo '
cluster.name: elkstack
cloud.aws.access_key: IAM_ACCESS_KEY_HERE
cloud.aws.secret_key: IAM_SECRET_KEY_HERE
cloud.aws.region: us-east-1
discovery.type: ec2
discovery.ec2.tag.Name: EC2_TAG_NAME
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

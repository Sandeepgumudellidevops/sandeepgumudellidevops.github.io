title: Install logstash and configure using s3 bucket on UBUNTU
date: 10-06-2020
author: Sandeep Gumudelli

# I am On My Way To learning

Create Ubuntu Ec2 machine on AWS and connect to it using SSH.

ssh -i <Pem key> ubuntu@*.*.*.*

wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -

sudo apt-get install apt-transport-https

echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list

sudo apt-get update && sudo apt-get install logstash

root@ip-10-0-35-151:~# find / -name logstash -type f
/usr/share/logstash/bin/logstash
/etc/default/logstash

export PATH=/usr/share/logstash/bin:${PATH}

systemctl status logstash
systemctl enable logstash
systemctl start logstash
systemctl stop logstash

apt install -y mlocate

updatedb

cd /usr/share/logstash
bin/logstash-plugin list

cd /etc/logstash/conf.d/
sudo vi s3_input.conf

sudo systemctl start logstash

mkdir /root/logstash

vi /root/logstash/logstash.conf
input {
  s3 {
    "access_key_id" => "access_key_here"
    "secret_access_key" => "secret_key_here"
    "region" => "eu-west-2"
    "bucket" => "bucket_name_here"
    #"prefix" => "Logs"
    "interval" => "10"
    "additional_settings" => {
      "force_path_style" => true
      "follow_redirects" => false
      }
  }
}
 
output {
  elasticsearch {
    hosts => [<elasticsearch_url>]
    index => "logs-%{+YYYY.MM.dd}"
    user => "username_here"
    password => "Password_here"
  }
}

logstash -f /root/logstash/logstash.conf
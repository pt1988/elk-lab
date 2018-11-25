[[Install ELK on CentOS](README.md)]

[[Excerise1: logstash](exercise-1)]
[[Excerise2: elasticsearch](exercise-2)]
[[Excerise3: elasticsearch](exercise-3)]

## วิธีติดตั้ง ELK บน Ubuntu

### Exercise0 : Install ELK 

#### 1. ตั่งค่าและติดตั้งซอร์ฟแวร์พิ้นฐาน

##### 1.1 ติดตั้งซอร์ฟแวร์พื้นฐาน

```
sudo apt install openjdk-8-jre git -y
sudo apt-get install apt-transport-https
```

##### 1.2 เพิ่ม Elastic Public Signature key เพื่อให้ CentOS เชื่อถือ Software จาก Elastic

```
sudo wget -qO - http://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add 
```

##### 1.4 เพิ่ม repository ของ Elastic เพื่อใช้ติดตั้ง Elasticsearch Logstash และ Kibana
```
sudo echo "deb http://artifacts.elastic.co/packages/6.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-6.x.list
```


#### 2. วิธีติดตั้ง logstash
[[reference](https://www.elastic.co/guide/en/logstash/current/installing-logstash.html)]

##### 2.1 คำสั่ง logstash
```
sudo apt-get update && sudo apt-get install logstash -y
```

##### 2.2 คำสั่งเปิด logstash service
```
#start logstash serivce with systemd 
sudo systemctl start logstash

#check logstash service status
sudo systemctl status logstash

#enable logstash auto start onboot
sudo systemctl enable logstash 
```


#### 3. วิธีติดตั้ง Elasticsearch
[[reference](https://www.elastic.co/guide/en/elasticsearch/reference/current/deb.html)]


##### 3.1 คำสั่งติดตั้ง Elasticsearch
```
sudo apt install elasticsearch -y 
```

##### 3.2 คำสั่งเปิด service  ของ elasticsearch และตั้งค่าให้เปิด Service อัตโนมัติเมื่อเปิดเครื่อง
```

#start service
sudo systemctl start elasticsearch 

#check service status
sudo systemctl status elasticsearch

#enable elasticsearch start on boot
sudo systemctl enable elasticsearch
```

##### 3.3 คำสั่งทดสอบใช้งาน Rest API ของ elasticsearch
```
curl 127.0.0.1:9200
```

#### 4. การติดตั้ง Kibana
[[reference](https://www.elastic.co/guide/en/kibana/current/rpm.html)]


##### 4.1 คำสั่งติดตั้ง kibana

```
sudo apt-get install kibana -y
```

##### 4.2 คำสั่งเริ่ม service ของ Kibana และตั้งค่าให้เปิด Service อัตโนมัติเมื่อเปิดเครื่อง
```
# start kibana
sudo systemctl start kibana

# check kibana service status
sudo systemctl status kibana

# enable kibana start onboot
sudo systemctl enable kibana 
```

#### 4.3 ทดสอบ Kibana โดยการเปิด URL : 127.0.0.1:9200 ผ่าน Firefox 
หรือทดสอบผ่าน command line โดยใช้สั่ง curl ตามด้านล่าง
```
curl 127.0.0.1:5601
```

#### 5. การติดตั้ง nginx revers proxy และตั้งค่ายืนยันตัวบุคคลแบบพื่้นฐานของ HTTP (http basic authentication)

##### 5.1) install nginx
[[reference](https://community.openhab.org/t/using-nginx-reverse-proxy-authentication-and-https/14542)]

```
sudo apt install -y nginx
```

##### 5.2) การตั้งค่า nginx

เปิดไฟล์ "/etc/nginx/nginx.conf" และเพิ่มการตั้งค่า reverse proxy ในส่วน "http { server { location / { [config] }}}"
```
sudo vim /etc/nginx/sites-available/default
```

คัดลอกคำสั่งด้านล่างและวางแทนที่ "location /{ ... }" ในไฟล์ nginx.conf
```
location / {
		proxy_pass                            http://localhost:5601/;
		proxy_buffering                       off;
		proxy_set_header Host                 $http_host;
		proxy_set_header X-Real-IP            $remote_addr;
		proxy_set_header X-Forwarded-For      $proxy_add_x_forwarded_for;
		proxy_set_header X-Forwarded-Proto    $scheme;
                auth_basic                            "Username and Password Required";
                auth_basic_user_file                  /etc/nginx/.htpasswd;
}
```

##### 5.3) สร้าง username and password สำหรับการยืนยันตัวบุคคลเพื่อเข้าใช้ Kibana ผ่าน Nginx's Reverse Proxy

```
sudo apt install apache2-utils
sudo htpasswd -c  /etc/nginx/.htpasswd admin
```

##### 5.4 คำสั่งปิด nginx service และตั้งค่าให้เปิด Service อัตโนมัติเม
```
# start nginx service
sudo systemctl start nginx

# check nginx service status
sudo systemctl status nginx

# enable nginx start onboot
sudo systemctl enable nginx
```

##### 5.4) คำสั่งเปิดไฟล์วอลล์เพิ่อใช้งาน Kibana ผ่านทาง Port 80
```
firewall-cmd --add-port 80/tcp --zone=public --permanent
firewall-cmd --reload
```


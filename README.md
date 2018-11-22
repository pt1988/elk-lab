[[Install ELK on Ubuntu](UBUNTU.md)]

[[Excerise1: logstash](exercise-1)]
[[Excerise2: elasticsearch](exercise-2)]
[[Excerise3: elasticsearch](exercise-3)]

## ติดตั้ง ELK บน CentOS7

### Exercise0 : ติดตั้ง ELK 

#### 1. การติดตั้งโปรแกรมพื้นฐาน. 

##### 1.1 ปิด SE Linux

##### 1.2 ติดตั้ง Java.

```
yum install java -y
```

##### 1.3 เพิ่ม Elastic Public Signature key เพื่อให้ CentOS เชื่อถือ Software จาก Elastic
```
rpm --import http://artifacts.elastic.co/GPG-KEY-elasticsearch
```

##### 1.4 เพิ่ม repository เพื่อใช้ติดตั้ง Elasticsearch Logstash และ Kibana
```
echo '
[elk-6.x] 
name=Elastic repository for 6.x packages
baseurl=http://artifacts.elastic.co/packages/6.x/yum
gpgcheck=1
gpgkey=http://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=1
autorefresh=1
type=rpm-md
' > /etc/yum.repos.d/elastic.repo
```


#### 2. การติดตั้ง logstash
[[reference](https://www.elastic.co/guide/en/logstash/current/installing-logstash.html)]

##### 2.1 คำสั่งติดตั้ง logstash
```
sudo yum install logstash -y
```

##### 2.2 คำสั่งเริ่ม service ของ logstash และตั้งค่าให้เปิด Service อัตโนมัติเมื่อเปิดเครื่อง
```
#start logstash serivce with systemd 
sudo systemctl start logstash

#check logstash service status
sudo systemctl status logstash

#enable logstash auto start onboot
sudo systemctl enable logstash 
```


#### 3. การติดตั้ง Elasticsearch
[[reference](https://www.elastic.co/guide/en/elasticsearch/reference/current/rpm.html)]


##### 3.1 คำสั่งติดตั้ง Elasticsearch
```
sudo yum -y install elasticsearch -y 
```

##### 3.2 คำสั่งเริ่ม service ของ Elasticsearch และตั้งค่าให้เปิด Service อัตโนมัติเมื่อเปิดเครื่อง
```
#start service
sudo systemctl start elasticsearch 

#check service status
sudo systemctl status elasticsearch

#enable elasticsearch start on boot
sudo systemctl enable elasticsearch
```

##### 3.3 ทดสองเรียก API ของ elasticsearch ผ่าน http rest api
```
curl 127.0.0.1:9200
```

#### 4. การติดตั้ง Kibana
[[reference](https://www.elastic.co/guide/en/kibana/current/rpm.html)]


##### 4.1 คำสั่งติดตั้ง kibana
```
yum install kibana -y
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

#### 4.3 ทอสอบเปิดเว็บ kibana ผ่าน url: 127.0.0.1:5601 ด้วย Firefox
หรือทดสอบจาก Command Line โดยใช้ คำสั่ง curl ตามด้านล่าง
```
curl 127.0.0.1:5601
```

#### 5. การติดตั้ง nginx revers proxy และตั้งค่ายืนยันตัวบุคคลแบบพื่้นฐาน

##### 5.1) ติดตั้ง nginx
[[reference](https://community.openhab.org/t/using-nginx-reverse-proxy-authentication-and-https/14542)]

```
yum install -y epel-release
yum install -y nginx
```

##### 5.2) ตั้งค่า nginx

Open file "/etc/nginx/nginx.conf" and add reverse proxy config to session http { server { location / { [config] }}} 
```
vim /etc/nginx/nginx.conf
```

คัดลอกคำสั่งด้านล้างและวางแทนที่ "location / { ... }" ในไฟล์ nginx.conf
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
sudo yum install -y httpd-tools
htpasswd -c /etc/nginx/.htpasswd admin
```

##### 5.4 คำสั่งเริ่ม nginx service
```
# start nginx service
sudo systemctl start nginx

# check nginx service status
sudo systemctl status nginx

# enable nginx start onboot
sudo systemctl enable nginx
```

##### 5.4) เปิดไฟล์วอลล์  Port 80 เพื่อให้ใช้ใช้งาน Kibana ผ่าน HTTP ได้
```
firewall-cmd --add-port 80/tcp --zone=public --permanent
firewall-cmd --reload
```


## Exercise 1 : Process web access log with logstash

#### 1 ตั้งค่าพื้่นฐานของ

##### 1.1 ดาวน์โหลดโปรเจคจาก Gitlab  
```
git clone https://gitlab.com/pt1988/lab-elk.git
cd lab-elk/exercise-1
```


##### 1.2) เปลี่ยน privilege เป็น root ด้วยคำสั่งดังนี้
```
sudo su -
```

##### 1.3) คัดลอกไฟล์ access log ไปเก็บไว้ที่ไดเรกทอรี "/var/log" เพื่อใช้เป็น Log เพื่อใช้ในข้อถัดไป
```
cp access.log /var/log
```

#### 2. การรันโปรแกรม Logstash ผ่าน command line
Using logstash to read logfile(/var/log/access.log) and process and print to screen.

##### 2.1) แสดง path ของโปรแกรม logstash โดยใช้คำสั่ง
```
whereis logstash
```
จะได้ผลลัพดังนี้
```
lab@elk-lab-demo:~$ whereis logstash
logstash: /etc/logstash /usr/share/logstash
```
โปรแกรมจะอยู่ใน Path "/usr/share/logstash"

##### 2.2) แสดงวิธีการใช้งานโกรแกรม Logstash โดยใช้คำสั่งดังนี้
```
/usr/share/logstash/bin/logstash -h
```

##### 2.3) ทดสอบรัน Logstash โดยใช้คำสั่งดังนี้
```
/usr/share/logstash/bin/logstash -f logstash-stdout.conf
```

#### 3. การรันโปรแกรม Logstash แบบ Daemon (รันเป็น backgroud process) ด้วย systemd 
การรัน Logstash เป็น backgroud process ด้วย systemd สามารถควบคุมการทำงานของโปรแกรมผ่านคำสั่ง "systemctl [command] logstash"
โดย Log การทำงานของ Logstash จะเก็บไว้ในไดเรกทอรี "/var/log/logstash/"

##### 3.1) คัดลอกไฟล์ configuration ของ Logstash ไปไว้ที่ไดเรกทอรี "/etc/logstash/conf.d/"
```
mkdir /etc/logstash/conf.d/
cp logstash-elasticsearch.conf /etc/logstash/conf.d/
```

##### 3.2) รันคำสั่งรีสตาร์ต logstash service ดังนี้
```
systemctl restart logstash
```

##### 3.3) รันคำสั่งตรวจสอบสถานนะของโปรแกรม Logstash ดังนี้
```
systemctl status logstash
```

##### 3.4) การตรวจสอบการทำงานของ Logstash สามารถตรวจสอบการทำงานได้จากไฟล์ "/var/log/logstash/logstash-plain.log" ดังนี้
```
tail /var/log/logstash/logstash-plain.log -n 20 
```

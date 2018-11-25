lt ## Exercise 1 : Process web access log with logstash

#### 1 ตั้งค่าพื้่นฐานของ

##### 1.1 ดาวน์โหลดโปรเจคจาก Gitlab  
```
sudo apt-get install git curl # for ubuntu
git clone https://gitlab.com/pt1988/lab-elk.git
cd lab-elk/exercise-1
```

##### 1.3) คัดลอกไฟล์ access log ไปเก็บไว้ที่ไดเรกทอรี "/var/log" เพื่อใช้เป็น Log เพื่อใช้ในข้อถัดไป
```
sudo cp access.log /var/log
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

##### 2.2) แสดงวิธีการใช้งานโปรแกรม Logstash โดยใช้คำสั่งดังนี้
```
sudo /usr/share/logstash/bin/logstash -h
```

##### 2.3) ทดสอบรัน Logstash โดยใช้คำสั่งดังนี้
```
sudo /usr/share/logstash/bin/logstash -f logstash-stdout.conf
```
จะได้ผลดังตัวอยางนี้ 
```
WARNING: Could not find logstash.yml which is typically located in $LS_HOME/config or /etc/logstash. You can specify the path using --path.settings. Continuing using the defaults
Could not find log4j2 configuration at path /usr/share/logstash/config/log4j2.properties. Using default config which logs errors to the console
[INFO ] 2018-11-24 19:06:19.549 [main] writabledirectory - Creating directory {:setting=>"path.queue", :path=>"/usr/share/logstash/data/queue"}
[INFO ] 2018-11-24 19:06:19.575 [main] writabledirectory - Creating directory {:setting=>"path.dead_letter_queue", :path=>"/usr/share/logstash/data/dead_letter_queue"}
[WARN ] 2018-11-24 19:06:20.650 [LogStash::Runner] multilocal - Ignoring the 'pipelines.yml' file because modules or command line options are specified
[INFO ] 2018-11-24 19:06:20.677 [LogStash::Runner] runner - Starting Logstash {"logstash.version"=>"6.5.1"}
[INFO ] 2018-11-24 19:06:20.743 [LogStash::Runner] agent - No persistent UUID file found. Generating new UUID {:uuid=>"15a7a39b-f61f-4999-a0d6-b2fe783f25ff", :path=>"/usr/share/logstash/data/uuid"}
[INFO ] 2018-11-24 19:06:31.205 [Converge PipelineAction::Create<main>] pipeline - Starting pipeline {:pipeline_id=>"main", "pipeline.workers"=>1, "pipeline.batch.size"=>125, "pipeline.batch.delay"=>50}
[INFO ] 2018-11-24 19:06:32.553 [Converge PipelineAction::Create<main>] pipeline - Pipeline started successfully {:pipeline_id=>"main", :thread=>"#<Thread:0x4d349218 run>"}
[INFO ] 2018-11-24 19:06:32.722 [Ruby-0-Thread-1: /usr/share/logstash/lib/bootstrap/environment.rb:6] agent - Pipelines running {:count=>1, :running_pipelines=>[:main], :non_running_pipelines=>[]}
[INFO ] 2018-11-24 19:06:32.762 [[main]<file] observingtail - START, creating Discoverer, Watch with file and sincedb collections
[INFO ] 2018-11-24 19:06:33.635 [Api Webserver] agent - Successfully started Logstash API endpoint {:port=>9600}
{
    "request_path" => "/kali/pool/main/u/udpkg/?C=M&O=D",
         "message" => "158.108.8.148 : 46.229.168.144 - - [15/Sep/2018:00:25:05 +0700] \"GET /kali/pool/main/u/udpkg/?C=M&O=D HTTP/1.1\" 200 3755 \"-\" \"Mozilla/5.0 (compatible; SemrushBot/2~bl; +http://www.semrush.com/bot.html)\" \"-\"",
       "timestamp" => "15/Sep/2018:00:25:05 +0700",
      "@timestamp" => 2018-09-14T17:25:05.000Z,
            "path" => "/var/log/access.log",
           "agent" => "Mozilla/5.0 (compatible; SemrushBot/2~bl; +http://www.semrush.com/bot.html)\" \"-",
             "app" => "HTTP/1.1",
          "method" => "GET",
        "@version" => "1",
       "repo_name" => "kali",
          "status" => "200",
       "client_ip" => "46.229.168.144",
            "host" => "lab-teacher"
}
```
โปรแกรมจะรันค้างไว้อ่าน log จากโปรแกรมที่สร้าง log เมื่อโปรแกรมพิมพ์ค่าออกหน้าจอหมดแล้วกดปุ่ม Clt + C เพื่อหยุดการทำงานของ Logstash


#### 3. การรันโปรแกรม Logstash แบบ Daemon (รันเป็น backgroud process) ด้วย systemd 
การรัน Logstash เป็น backgroud process ด้วย systemd สามารถควบคุมการทำงานของโปรแกรมผ่านคำสั่ง "systemctl [command] logstash"
โดย Log การทำงานของ Logstash จะเก็บไว้ในไดเรกทอรี "/var/log/logstash/"

##### 3.1) คัดลอกไฟล์ configuration ของ Logstash ไปไว้ที่ไดเรกทอรี "/etc/logstash/conf.d/"
```
sudo cp logstash-elasticsearch.conf /etc/logstash/conf.d/
```

##### 3.2) รันคำสั่งรีสตาร์ต logstash service ดังนี้
```
sudo systemctl restart logstash
```

##### 3.3) รันคำสั่งตรวจสอบสถานนะของโปรแกรม Logstash ดังนี้
```
systemctl status logstash
```

##### 3.4) การตรวจสอบการทำงานของ Logstash สามารถตรวจสอบการทำงานได้จากไฟล์ "/var/log/logstash/logstash-plain.log" ดังนี้
```
tail /var/log/logstash/logstash-plain.log -n 20 
```
ถ้าโปรแกรมทำงานได้ปกติจะ log ของ Logstash จะมีลักษณะดังนี้
```
[2018-11-24T19:32:53,814][INFO ][logstash.pipeline        ] Pipeline started successfully {:pipeline_id=>"main", :thread=>"#<Thread:0xd795dd1 run>"}
[2018-11-24T19:32:53,969][INFO ][logstash.agent           ] Pipelines running {:count=>1, :running_pipelines=>[:main], :non_running_pipelines=>[]}
[2018-11-24T19:32:54,014][INFO ][filewatch.observingtail  ] START, creating Discoverer, Watch with file and sincedb collections
[2018-11-24T19:32:54,812][INFO ][logstash.agent           ] Successfully started Logstash API endpoint {:port=>9600}
```

แต่ถ้าโปรแกรมขัดข้อง log ของ Logstash ระบุสาเหตุของข้อผิดพลาด

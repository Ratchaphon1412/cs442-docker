# Intro Docker

ทำไมต้องใช้ docker เนื่องจาก ปัญหา เกิดจาก การทำโปรเจ็ค หลายๆ โปรเจ็ค แล้วต้องการใช้ คนละ เวอร์ชั่น เช่น มี 2 version java ที่ต้องใช้ แต่ละ project แล้ว เครื่องเราลง 17 เพื่อทำ project แรก แล้วโปรเจคที่ 2 เราต้องใช้ version 10 จะทำไง
solution เก่า คือ ใช้ vm (Virtual machine ) เพื่อที่ จะ จำลองคอมมาอีก ตัว แล้ว ก็ ลง java คนละ version ไปเลย

แต่ข้อเสีย คือ กินพื้นที่ เนื่องจาก vm จะต้อง ลง OS อีกตัวในเครื่อง (guest OS ) ที่ทำงานรวมกับ host OS (host OS ก็คือเครื่องเรา) โดย ทำงานเชื่อมกัน(กิน ทรัพยากร) เสียเวลา เพราะต้องลง os ให้ vm

### Compare Docker and VM

![compare vm and docker](https://cdn.discordapp.com/attachments/1037627692981432350/1037767517877317662/Screen_Shot_2565-11-03_at_23.37.28.png)

### VM

แต่ก่อนเราใช้ vm ในการแยก environment ในการ develop ดังนี้

![VM diagram](https://cdn.discordapp.com/attachments/1037627692981432350/1038118907325718628/Screenshot_20221104_225335.jpg)

### Docker

solution ใหม่ คือ ใช้ docker ทำหน้าที่ สร้าง space ขึ้น คล้าย กับ vm แต่ จะไม่ต้องลง guest OS (ลดพื้นที่) โดย ใช้ docker core ในการ ควบคุม แบ่ง space ออกเป็นแต่ละ container แทน

![Docker diagram 1](https://cdn.discordapp.com/attachments/1037627692981432350/1038122829054476398/Screenshot_20221104_230706.jpg)

![Docker diagram 2](https://cdn.discordapp.com/attachments/1037627692981432350/1038124048447705209/Screenshot_20221104_231404.jpg)

## Acknowledgements

- [Overview Docker Flow](##Overview)
- [Docker File]()
- [Docker compose]()

## Overview Docker Flow

การสร้าง App container ใน docker
![Docker Flow](https://cdn.discordapp.com/attachments/1037627692981432350/1038126914105253948/vGuay.png)

เราจะต้องเขียน dockerfile ก่อน คล้ายกับ setting ที่ set ว่า container ที่เราต้องการใช้ มีอะไรบ้าง เพื่อ ให้ docker ในเครื่องเรา ไป build images ขึ้นมา
เมื่อได้ images มาแล้ว เวลาจะใช้งาน เราจะต้อง run ตัว images ซึ่ง ณ ตอนนี้ จะสร้างตัว containners มา
เวลาเรา dev จะทำ ใน containners

เวลา สิ่งที่ เราทำใน containners มันจะไม่ save ลงในตัว image ถ้าต้องการให้ save สิ่งที่ทำเพิ่่มใน container ลงใน docker ต้อง ทำใน stage comit (ซึ่ง images จะถูกเปลี่ยนแปลง ทำให้ พอ run ใหม่ ก็จะได้ของที่เรา save มา)

![Docker commit flow](https://cdn.discordapp.com/attachments/1037627692981432350/1038143558349246464/Screenshot_20221105_003133.jpg)

### คำสั่ง command แต่ละ stage

![Docker run](https://media.discordapp.net/attachments/1037627692981432350/1038148046627807352/Screenshot_2565-11-05_at_00.49.06.png?width=622&height=628)

```bash
docker run -d -p 8000:80 docker/getting-started
```

(สร้าง container จาก image)
docker run -d (mode การรัน แบบ daemon mode) - p (port -> host: port ->container) target (ชื่อ repo ใน docker hub)

## Basic Commands

```bash
docker run -it ubuntu /bin/bash
```

-it = iteractive terminal

/bin/bash บอกให้ run เสร็จแล้ว ให้ terminal access เข้าไปใน docker container ผ่าน shell

```bash
docker run -it -d --rm --name linux1 ubuntu /bin/bash
```

--rm คือเมื่อ ปิด container แล้ว จะลบ container ออกจาก docker ให้เอง

--name คือการตั้งชื่อให้กับ container

```bash
docker run --rm -v ${PWD}:/myvol ubuntu /bin/bash -c "ls -lha > /myvol/myfile.txt"
```

-v : volume mounting from host to container (เป็นการเชื่อมต่อ ข้อมูล จาก host ไปยัง container)

-c : comman
เป็นการส่ง command เข้าไป ใน container

```bash
docker run --rm -v ${PWD}:/myvol  -w /my vol ubuntu /bin/bash
```

-w : working directory คือ การเปลี่ยน directory ที่เราจะทำงานใน container

```bash
docker run -p 8080:80 -d nginx
```

-p: port mapping คือการเชื่อมต่อ port ของ host ไปยัง port ของ container

```bash
docker system prune
```

เป็นการลบ container ที่ไม่ได้ใช้งานออกจาก docker

```bash
docker ps
```

เป็นการ list container เฉพาะที่ใช้งานได้ และกำลัง run อยู่ทั้งหมดออกมา

```bash
docker ps -a
```

เป็นการ list container ทั้งหมด รวมทั้งที่ใช้ไม่ได้และใช้ได้ ออกมา

เมื่อเราสร้าง container ขึ้นมาแล้ว เวลาเราจะใช้งาน จะมี process ดังนี้

![Container Flow](https://cdn.discordapp.com/attachments/1037627692981432350/1038179109521588295/Screenshot_2565-11-05_at_02.52.03.png)

```bash
docker start <container-id> or <container-name>
```

เป็นการ start container ที่เราต้องการ โดยระบุเป็น id หรือ ชื่อของ container

```bash
docker stop <container-id> or <container-name>
```

เป็นการ stop container ที่เราต้องการ โดยระบุเป็น id หรือ ชื่อของ container

```bash
docker attach <container-id> or <container-name>
```

เป็นการ attach เข้าไปใน container ที่เราต้องการ โดยระบุเป็น id หรือ ชื่อของ container

```bash
docker restart <container-id> or <container-name>
```

เป็นการ restart container ที่เราต้องการ โดยระบุเป็น id หรือ ชื่อของ container

```bash
docker log <container-id> or <container-name>
```

เป็นการ log สิ่งที่เกิดขึ้นใน container ที่เราต้องการ โดยระบุเป็น id หรือ ชื่อของ container

# Docker File

เป็น file ที่เอาไว้ build image

สิ่งต้องคำนึ่ง หรือ ผลลัพธ์ของ image ที่เราจะได้

- required binaries -> คือ โปรเจคเราใช้ อะไรใน การทำ เช่น django ก็ต้องมี python อยู่ใน image
- File layout -> คือ structure project
- Port -> คือ ช่องทาง communicate ระหว่าง container กับ docker host (TCP/UDP)
- Environment Variable - > ใช้ ส่ง parameter เข้าไป เพื่อไป setup บางอย่าง
- Start Command -> run service

![Dockerfile process](https://cdn.discordapp.com/attachments/1037627692981432350/1038198540134002751/Screenshot_2565-11-05_at_04.10.00.png)

## Syntax Dockerfile base on Yaml

```yaml
FROM php:8.1.5-cli

EXPOSE 8000
RUN mkdir /myproject
COPY index.php /myproject
WORKDIR /myproject
CMD php index.php
```

FROM : คือการบอกว่า จะใช้ image อะไรในการ build

EXPOSE : คือการบอกว่า จะใช้ port อะไรในการ run

RUN : คือการรันคำสั่งใน container

COPY: คือการ copy file จาก host ไปยัง container

WORKDIR: คือการเปลี่ยน directory ที่เราจะทำงานใน container

CMD: คือการรันคำสั่งใน container ตอนที่เรา run container

## การสร้าง image จาก dockerfile

```bash
docker build -t myapp .
```

-t : คือการตั้งชื่อ image ที่เราจะสร้าง

. : คือ path ของ dockerfile

```bash
docker images
```

เป็นการ list image ทั้งหมดที่เรามีอยู่

![Push to docker hub](https://cdn.discordapp.com/attachments/1037627692981432350/1038157071868240013/Screenshot_2565-11-05_at_01.24.57.png)

เมื่อเราสร้าง images เสร็จเราสามารถนำไปเก็บไว้ใน docker hub ได้

```bash
docker push <image-name>
```

การ push image ไป เราต้อง push ตัว image ที่ เป็น ชื่อ repo เราไป
docker push (my repo / nameimage)

![Pull to local](https://cdn.discordapp.com/attachments/1037627692981432350/1038157071868240013/Screenshot_2565-11-05_at_01.24.57.png)

เมื่อเพื่อนต้องการนำมาใช้งาน เพื่อนสามารถ pull มาใช้ได้

```bash
docker pull <image-name>
```

# Docker Composer

เป็น tool ที่ใช้ในการ จัดการ และ สร้าง container หลายๆ ตัว ในเวลาเดียวกัน โดย จะใช้ file ชื่อ docker-compose.yml ในการ กำหนด ว่าจะใช้ container อะไรบ้าง และ จะใช้ อะไรบ้าง ในการ สร้าง container นั้นๆ

![Docker compose](https://media.discordapp.net/attachments/1037627692981432350/1038331504176136212/qwazyqc1ie3k2d4e7clgltckw7zx.png?width=1306&height=1068)

# 도커 기반으로 무중단 배포 가이드

1. dependency 추가

```gradle
implementation "org.springframework.boot:spring-boot-starter-actuator:$springBootVersion"
```

2. application.yml 에 actuator/health 설정

```yml
management:
  endpoint:
    health:
      enabled: true
  endpoints:
    enabled-by-default: false
    web:
      exposure:
        include:  health
```

3. nginx 세팅

3-1. app.conf

```conf
# /etc/nginx/sites-available/app.conf

server {
        include /etc/nginx/conf.d/service-url.inc;
        server_name     _;
        charset         utf-8;
        listen 80 default_server;
        listen [::]:80 default_server;
        root /home/appuser/public_html/dist;
        proxy_hide_header Access-Control-Allow-Origin;
        add_header 'Access-Control-Allow-Origin' '*';
        client_max_body_size 50m;

        location / {
                try_files $uri $uri/ /index.html;
        }

        location /api/ {
                proxy_pass      $service_url;
                proxy_set_header        X-Real-Ip $remote_addr;
                proxy_set_header        x-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header        Host $host;
        }
}

```

3-2. service-url.inc
```inc
# /etc/nginx/conf.d/service-url.inc

set $service_url http://127.0.0.1:8000;
```

3-3. sites-enabled symbolic link
```shell
ln -s /etc/nginx/sites-available/app.conf /etc/nginx/sites-enabled
```

3-4. restart nginx

```shell
nginx -s reload
```

4. Dockerfile

```dockerfile
FROM adoptopenjdk/openjdk11:jdk11u-ubuntu-nightly-slim
COPY docker/demo.jar app.jar
RUN mkdir /var/log/java
ENTRYPOINT ["java","-jar","/app.jar","-server","-Dfile.encoding=UTF-8"]
```

5. docker-compose

5-1. docker-compose.a.yml

```yml
version: "3"
services:
  a:
    build: .
    restart: on-failure
    ports:
      - 8000:80
    environment:
      - SPRING_PROFILES_ACTIVE=dev
```

5-2. docker-compose.b.yml

```yml
version: "3"
services:
  b:
    build: .
    restart: on-failure
    ports:
      - 8001:80
    environment:
      - SPRING_PROFILES_ACTIVE=dev
```

5. 패스워드 없이 sudo 권한 주기

```shell
vi /etc/sudoers

## Allow root to run any commands anywhere
root    ALL=(ALL)   ALL
appuser  ALL=(ALL)   NOPASSWD:ALL
```

6. deploy.sh

```shell
#!/bin/bash

# 실행 중인 도커 컴포즈 확인
EXIST_A=$(docker-compose -p app-a -f docker-compose.a.yml ps | grep Up)

if [ -z "${EXIST_A}" ] # -z는 문자열 길이가 0이면 true. A가 실행 중이지 않다는 의미.
then
        # B가 실행 중인 경우
        START_CONTAINER=a
        TERMINATE_CONTAINER=b
        START_PORT=8000
        TERMINATE_PORT=8001
else
        # A가 실행 중인 경우
        START_CONTAINER=b
        TERMINATE_CONTAINER=a
        START_PORT=8001
        TERMINATE_PORT=8000
fi

echo "app-${START_CONTAINER} up"

# 실행해야하는 컨테이너 docker-compose로 실행. -p는 docker-compose 프로젝트에 이름을 부여
# -f는 docker-compose파일 경로를 지정
docker-compose -p app-${START_CONTAINER} -f docker-compose.${START_CONTAINER}.yml up -d --build

for cnt in {1..10} # 10번 실행
do
        echo "check server start.."

        # 스프링부트에 등록했던 actuator로 실행되었는지 확인
        UP=$(curl -s http://127.0.0.1:${START_PORT}/actuator/health | grep 'UP')
        if [ -z "${UP}" ] # 실행되었다면 break
        then
                echo "server not start.."
        else
                break
        fi

        echo "wait 10 seconds" # 10 초간 대기
        sleep 10
done

if [ $cnt -eq 10 ] # 10번동안 실행이 안되었으면 배포 실패, 강제 종료
then
        echo "deployment failed."
        exit 1
fi

echo "server start!"
echo "change nginx server port"

# sed 명령어를 이용해서 아까 지정해줬던 service-url.inc의 url값을 변경해줍니다.
# sed -i "s/기존문자열/변경할문자열" 파일경로 입니다.
# 종료되는 포트를 새로 시작되는 포트로 값을 변경해줍니다.
sudo sed -i "s/${TERMINATE_PORT}/${START_PORT}/" /etc/nginx/conf.d/service-url.inc

# 새로운 포트로 스프링부트가 구동 되고, nginx의 포트를 변경해주었다면, nginx 재시작해줍니다.
echo "nginx reload.."
sudo service nginx reload

# 기존에 실행 중이었던 docker-compose는 종료시켜줍니다.
echo "app-${TERMINATE_CONTAINER} down"
docker-compose -p app-${TERMINATE_CONTAINER} -f docker-compose.${TERMINATE_CONTAINER}.yml down
echo "success deployment"
```

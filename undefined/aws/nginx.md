---
description: >-
  이전시간에 EC2 인스턴스에 필요한 프로그램을 설치하고 실제 프로젝트까지 배포해봤다. 하지만 우리의 프로젝트는 4000번 포트로 배포가 되고
  있기 때문에 외부 웹브라우저로 접근하기 위해서는 IP:4000 이라는 아주 불편한 형태로 들어와야만 했다. 물론, 포트를 80으로 바꾸면
  되긴하지만, 더 좋은 방법이 없는걸까?
---

# nginx로 리버스프록시 서버를 만들자

## nginx로 리버스 프록시 서버를 만들자

> 우리의 EC2 인스턴스 내부에서는 하나의 서버가 아닌 두 개의 서버가 돌아가고 있다고 하자.`saver.42seoul.io`는 4000번 포트를 사용하고 `saver-dev.42seoul.io`는 5000번 포트를 사용한다.

### 리버스 프록시란 무엇인가?

외부 유저가 우리 프로젝트에 접근하는 시나리오를 그려보면서 생각해보자.

1. EC2인스턴스는`3.123.123.123`  의 ip 주소를 갖는다.
2. Ec2 내부에서 nginx 서버가 실행되고 있다. Nginx 는 설정에 따라 아래 두 개의 도메인 이름을 갖는다. 여기서 주의할 건 도메인 이름일 두 개일 뿐이지 둘 모두 같은 ip를 갖는다는 것. `ping [domain-name]` 을 하면 같은 ip 가 나오는걸 확인할 수 있다. 
   * `http://saver.42seoul.io`
   * `http://saver-dev.42seoul.io`
3. 유저가 브라우저를 통해 접근한다. 접근 주소는  , `http://saver.42seoul.io` 다.
4. EC2 서버에서 80번 포트를 담당하는건 nginx 다. **하지만 Nginx 는 이 요청을 직접 처리할 수 없다.**  따라서 이 요청을 처리할 수 있는 다른 서버로 보내야 한다.
5. 요청으로 들어온 `http://saver.42seoul.io` 는 4000번 포트로 실행되고 있으므로 nginx는 자신의 설정 파일에 명시되어 있는대로 4000번 포트로 이 작업을 넘긴다.
6. 이제 이 요청은 처리가 된다.

만약 어떤 유저가 `http://saver-dev.42seoul.io` 로 요청을 보냈다고 해도 위와 같은 과정으로 요청이 처리가 된다. 다만 다른 것은 처리하는 서버가 달라진다는 것 뿐. 즉 이렇게 **외부의 요청을 받아 뒷단의 서버로 요청을 전달하는 행위를 리버스 프록시 라고 한다.**

### EC2에 nginx를 설치하자

#### 들어가기 앞서 Amazon Linux 2 에 대해 알아보자

우리의 인스턴스는 `Amazon Linux2` 다. 솔직히 생소하다. 로컬에서 사용하는 리눅스 배포판은 보통 centOS 또는 Ubuntu인데 아마존 리눅스 2라니? [이에 대한 자세한 설명은 링크로 대신한다.](https://aws.amazon.com/amazon-linux-2/faqs/#) 요약하자면 이 배포판은 ec2에서 아주 아주 잘 굴러가는 리눅스 배포판의 종류라는 것. 그리고 실제 사용성은 centOS7과 동일하다. 하지만 조금 다른 것이 있으니... nginx 설치 방법 등이 달라진다.

#### 진짜로 설치하자

> [아마존 공식 사이트 가이드](https://aws.amazon.com/ko/premiumsupport/knowledge-center/ec2-install-extras-library-software/)

1. `amazon-linux-extras list` 명령어로 설치 가능한 패키지들을 확인할 수 있다.
2. `amazon-linux-extras list | grep nginx` 로 nginx가 있는지 확인하자. 아마 백퍼 있을 것임
3. `sudo amazon-linux-extras install nginx1` 로 설치하자
4. `sudo systemctl start nginx` 로 실행한다.
5. 이제 ec2의 인바운스 규칙에서 80번 포트를 개방하자. 이후에 ip에 브라우저를 이용해서 접근하면 nginx 화면이 뜨는 것을 확인할 수 있다. \(귀찮으면 그냥 `curl -i ip` 로 확인할 수 있다. 만약 안된다면 nginx가 켜졌는지 그리고 인바운딩 규칙을 다시 확인할 것\) 

   ![&#xC6F0;&#xCEF4;&#xD398;&#xC774;&#xC9C0;](https://res.cloudinary.com/desnjikxy/image/upload/v1622255613/nginx_%E1%84%8B%E1%85%B0%E1%86%AF%E1%84%8F%E1%85%A5%E1%86%B7%E1%84%91%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%8C%E1%85%B5_zsqjep.png)

### Nginx 설정을 하자

> 설치는 끝났고... 제대로 실행까지 된다. 하지만 우리가 원하는건 ip에 접근하면 우리의 프로젝트가 나오는것이지 지금처럼 nginx의 웰컴페이지는 원하지 않아.

1. `cd /etc/nginx` 로 이동하자. 해당 파일로 이동하면 여러 파일이 존재하는데 이중에서 우리가 직접 수정할 건 `nginx.conf`다.
2. `vim nginx.con.f`로 파일이 어떻게 되어있는지 확인하자. \(주석은 지운 상태\)

   ```text
   user nginx;
   worker_processes auto;
   error_log /var/log/nginx/error.log;
   pid /run/nginx.pid;

   # Load dynamic modules. See /usr/share/doc/nginx/README.dynamic.
   include /usr/share/nginx/modules/*.conf;

   events {
       worker_connections 1024;
   }

   http {
       log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                         '$status $body_bytes_sent "$http_referer" '
                         '"$http_user_agent" "$http_x_forwarded_for"';

       access_log  /var/log/nginx/access.log  main;

       sendfile            on;
       tcp_nopush          on;
       tcp_nodelay         on;
       keepalive_timeout   65;
       types_hash_max_size 4096;

       include             /etc/nginx/mime.types;
       default_type        application/octet-stream;

       include /etc/nginx/conf.d/*.conf;

       server {
           listen       80;
           listen       [::]:80;
           server_name  _;
           root         /usr/share/nginx/html;

           include /etc/nginx/default.d/*.conf;

           error_page 404 /404.html;
           location = /404.html {
           }

           error_page 500 502 503 504 /50x.html;
           location = /50x.html {
           }
       }

   }
   ```

3. 이제 우리의 설정을 추가하자. 처음으로 돌아가서 우리의 설정에 대해 다시 생각해보자. 우리는 어떤 동작을 위해 설정을 하는거였지?

   > 리버스 프록시를 위해서. 즉 80번 포트를 통해 유저가 접근하면 nginx가 이 요청을 받고 그 요청을 처리할 서버로 보내는 것.

4. 설정을 추가하자.

   ```text
   user nginx;
   worker_processes auto;
   error_log /var/log/nginx/error.log;
   pid /run/nginx.pid;

   # Load dynamic modules. See /usr/share/doc/nginx/README.dynamic.
   include /usr/share/nginx/modules/*.conf;

   events {
       worker_connections 1024;
   }

   http {
       log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                         '$status $body_bytes_sent "$http_referer" '
                         '"$http_user_agent" "$http_x_forwarded_for"';

       access_log  /var/log/nginx/access.log  main;

       sendfile            on;
       tcp_nopush          on;
       tcp_nodelay         on;
       keepalive_timeout   65;
       types_hash_max_size 4096;

       include             /etc/nginx/mime.types;
       default_type        application/octet-stream;

       server {
           listen       80;
           listen       [::]:80;
           server_name  _;
           root         /usr/share/nginx/html;

           # Load configuration files for the default server block.
           include /etc/nginx/default.d/*.conf;

                   location / {
               sendfile off;
               proxy_pass         http://127.0.0.1:4000;
               proxy_redirect     default;
               proxy_http_version 1.1;
               proxy_set_header   Host              $host;
               proxy_set_header   X-Real-IP         $remote_addr;
               proxy_set_header   X-Forwarded-For   $proxy_add_x_forwarded_for;
               proxy_set_header   X-Forwarded-Proto $scheme;
               proxy_cache_bypass $http_upgrade;
               proxy_max_temp_file_size 0;
               }

           error_page 404 /404.html;
           location = /404.html {
           }

           error_page 500 502 503 504 /50x.html;
           location = /50x.html {
           }
       }
   }
   ```

   마지막을 보면 기존에 있던 `server` 블록 내부에 `location` 블록이 생긴것을 확인할 수 있다. 아주 간단히 이 설정파일을 해석해보자면, 80번 포트로 요청을 날리면 nginx 서버는 이 요청을 localhost:4000 으로 패스한다!!! 엥 근데 왠 `localhost`??? 왜냐면 외부의 요청은 `localhost` 로 접근하지 못하는게 당연하지만, ec2 내부에서는 4000번 포트로 돌아가고 있는 서버는 당연히 `localhost` 이기 때문. 어쨋든 이제 다시 서버로 접근해보자.

5. `curl -i [ip]` 를 입력하면 이전의 `nginx` 가 아닌 우리의 프로젝트가 보인다!!!!!!

#### 번외. 설정 파일을 모듈화해보자

> 다시 처음으로 돌아가자. 우리는 분명 `saver.42seoul.io` 와`saver-dev.42seoul.io` 두 개의 서버를 만든다고 하지 않았었나? 그리고 이 두 개의 서버를 같은 IP지만 다른 도메인 네임으로 접근하게 만든다고 했었고.... 하지만 위의 설정파일을 곱씹어 보면 단 하나의 서버만 돌아갈 수 있게 만들어진거 같은데...

어떻게 하면 처음에 우리가 생각했던 대로 2개 이상의 서버를 하나의 ip를 갖는 인스턴스 내부에서 배포할 수 있을까?

간단하다 `nginx.conf` 파일에 `server` 블록을 추가하면 된다. 아마 `webserv` 과제를 진행했던 사람이라면 아주 쉽게 추가가 가능할 것임.

그렇다면 이제 달라져야 할 부분이 생긴다. 바로 `server_name`!! 이 `nginx-directive` 를 고려해서 설정 파일에 수정을 하면 됨.

하지만 지금은 도메인 이름이 없는 관계로 설정 파일에 서버를 추가하는 대신 설정을 모듈화하자. 이렇게 모듈화하면 나중에 서버를 추가할 때 아주 손쉽게 처리할 수 있게 된다.

우선 `nginx.conf` 파일을 다시 보자. `server` `block-directive` 의 바로 위에 `include /etc/nginx/conf.d/*.conf` 를 확인할 수 있다. 이 `directive` 가 바로 모듈화 된 설정 파일을 가져오는 문구다.

1. `cd /etc/nginx/conf.d && vim server.conf`
2. ```text
   server {
         # 서버 이름을 명시함으로써 다른 서버도 돌릴 수 있게 된다.
       server_name  _ saver.42seoul.io;
       root         /usr/share/nginx/html;

       # Load configuration files for the default server block.
       include /etc/nginx/default.d/*.conf;

       location / {
           sendfile off;
           proxy_pass         http://127.0.0.1:4000;
           proxy_redirect     default;
           proxy_http_version 1.1;
           proxy_set_header   Host              $host;
           proxy_set_header   X-Real-IP         $remote_addr;
           proxy_set_header   X-Forwarded-For   $proxy_add_x_forwarded_for;
           proxy_set_header   X-Forwarded-Proto $scheme;
           proxy_cache_bypass $http_upgrade;
           proxy_max_temp_file_size 0;
       }

       error_page 404 /404.html;
           location = /40x.html {
       }

       error_page 500 502 503 504 /50x.html;
           location = /50x.html {
       }
   }
   ```
3. 그리고 `nginx.conf` 의 `location` 블록은 지워주자
4. `nginx -t` 로 설정파일 오류 확인.
5. `systemctl restart nginx` 로 nginx 재시작

## https 설정을 하자

> 허광남 멘토님의 사이트를 따라하면 아주 손쉽게 할 수 있습니다.


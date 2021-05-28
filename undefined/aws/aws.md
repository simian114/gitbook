# aws로 프로젝트를 배포해보자!

## ec2 인스턴스를 만들고 ssh로 접속하자

1. 만드는 과정이 끝나면 마지막에 `key` 에 대한 이야기가 나온다. 여기서 `key` 를 다운받아주자.
2. `xxx.pem` 파일의 권한을 바꿔준다.

   ```text
   chmod 400 sanam.pem
   ```

3. 이제 접속할 차례다.

   ```text
   ssh -i xxx.pem ec2-user@[Public IPv4 DNS]
   ```

## 프로젝트에 필요한 프로그램을 다운받자.

> git, node, mariadb ....

1. `sudo yum update` 로 업데이트를 먼저 진행한다.

   ```text
   sudo yum update -y
   ```

2. `git` 설치

   ```text
   sudo yum install git
   ```

3. `node` 설치

   > 절대로 root 유저로 설치하지 말자. root로 설치하면 npm install 을 할 때 마다 root로 진행해야함ㅋㅋㅋ

   [공식홈페이지를 보고하면 된다.](https://docs.aws.amazon.com/ko_kr/sdk-for-javascript/v2/developer-guide/setting-up-node-on-ec2-instance.html)

   ```text
   curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | bash
   . ~/.nvm/nvm.sh
   nvm install node

   # TEST
   node -e "console.log('Running Node.js ' + process.version)"
   ```

4. `pm2` 를 글로벌로 설치

   ```text
   npm install -g pm2
   ```

### MariaDB 설치

> [링크](https://okdevtv.com/mib/mariadb)를 따라하면 된다.

1. `yum repolist` 를 확인한다.

   ```text
   yum repolist
   # 결과로 mariadb가 있어야한다. 만약 없으면 아래의 과정을 진행하자. 100퍼 없음ㅋㅋ
   ```

2. `yum repostlist` 에 MariaDB 를 추가하자.

   ```text
   sudo vi /etc/yum.repos.d/MariaDB.repo
   ```

```text
   [mariadb]
   name = MariaDB
   baseurl = http://yum.mariadb.org/10.4/centos7-amd64
   gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
   gpgcheck=1
```

1. yum repolist를 확인한다.

   ```text
   yum repolist
   ```

   이전에는 보이지 않던 mariadb가 보인다.

2. 설치 및 실행을 하자.

   ```text
   # 설치
   sudo yum install MariaDB-server MariaDB-client
   # 실행
   sudo systemctl start mariadb
   # 보안설치 중요한 과정이니 생략하지 말자
   sudo mysql_secure_installation
   ```

   보안 설치에서 질문 답변 어떻게해야하지?

   1. `Enter current password for root (enter for none)`: 아직 등록하지 않았으니 그냥 엔터 누른다.
   2. `Switch to unix_socket authentication`: Y 를 누른다. 왜냐면 이제는 비밀번호를 등록해야하니깐 ㅎ\_ㅎ
   3. `Change the root password?`: y 를 입력하고 root 의 비밀번호를 등록해주자. 
   4. `Remove anonymous users?`: y를 누르자. 익명 사용자는 당연히 삭제.
   5. `Disallow root login remotely?`: 이건 뭐 마음대로 하면 되겠지만, y를 누르자. 왜냐하면 3306 포트를 이용해서  DB에 바로 연결하는 방법은 아름답지 못하기 때문이다. 이후에 터널링을 이용해서 22번 포트를 통해 접근하는게 좋다.
   6. `Remove test database and access to it?` 당연 y
   7. `Reload privilege tables now?` y를 누르자. 그러면 이젠 끝~

3. root가 아닌 일반 사용자를 등록하자.
   1. 등록하기 앞서 일반 사용자가 사용할 database를 만들자.

      ```text
      mysql -uroot -p[password]
      ```

      ```text
      # mysql
      create database [database-name];

      # 만들어졌는지 확인
      show databases;
      ```

   2. 이제 만들면 됨.

      ```text
      GRANT ALL PRIVILEGES ON [database-name].* TO [user]@localhost
      IDENTIFIED BY '[user-password]' WITH GRANT OPTION;
      ```

   3. 다시 터미널로 나와서 방금 만든 유저로 db에 접근해보자.

      ```text
      mysql -u[username] -p[password]
      ```

## 프로젝트를 클론 및 실행

1. `git clone https://github.com/innovationacademy-kr/slaps-saver.git`
2. `env` 를 작성하자.
3. `pm2 start ./bin/www` 로 실행하자.
4. 브라우저를 이용해서  `ip`  에 들어오자!!!!!!!

### 접속이 안된다..!!

왜냐하면 ec2 인스턴스에 접근할 수 있는 port가 22번 밖에 없기 때문이다.

## 포트 설정해주기

1. aws의 ec2에 들어가자
2. 왼쪽을 보면 `instances` 가 있다. `instances > instances` 를 누르자
3. 여기에 우리의 인스턴스들이 보인다. 지금 사용하고 있는 인스턴스를 선택하자.
4. 리다이렉트 되는 페이지에서 아래를 보면 여러 탭을 가지고 있는 뷰가 보인다. 여기서 `security`탭을 선택하자.
5. `inbound` 가 보인다. 그리고 여기에 등록되어있는 포트가 22번 포트라는 것도 알 수 있다. 이것 때문에 우리가 브라우저로 접근하지 못하고 있던것.
   * 여기서 `Security groups` 를 확인하자. 
6. 다시 왼쪽 메뉴로 와서 `Network & Security > Security Groups` 를 선택한다.
7. 5번에서 확인한 `Security groups` 를 선택해서 들어간다.
8. `Edit inbound rules`를 누르고 4000번을 추가하고 저장하자.
9. 이제 우리의 프로젝트에 접속할 수 있다!!!

## 아직 되지 않은거...

이렇게 된다고 해서 끝난게 아니다. 왜? 우리는 지금 4000번 포트로 들어가고 있다. 이게 우리가 진정 원하는건가? 아니다 우리는 80번 포트로 들어가고 싶다... 바로 이후에 nginx를 이용해서 80번 포트로 들어가게 만들어주자.

그리고 https 을 설치해서 보안도 좋게 만들어주자.


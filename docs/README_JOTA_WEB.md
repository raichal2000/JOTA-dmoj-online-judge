# Setting JOTA Web

*Last Update: 9. Jul. 2021*

**DMOJ site official docs**: https://docs.dmoj.ca/#/site/installation

## 시작하기 전 필독

* 반드시 공식 문서를 읽으면서 진행하십시오. 명령어는 아래의 수정된 명령어를 사용하세요.
* 본인의 github 계정에서 해당 repository를 **fork** 하십시오: https://github.com/hyunchan-park/JOTA-dmoj-online-judge
* JCloud 인스턴스에서 `jota` 디렉토리를 생성하십시오.
    ```
    ~$ mkdir jota
    ```
* pip3 패키지 설치 또는 python3 코드 실행 중 오류가 발생할 때, 접두어 `sudo` 를 붙이지 않았는지 확인하십시오.
* 패키지의 버전 업데이트로 인하여 세팅이 실패될 수 있습니다.
* 외부 IP로 웹서버를 테스트 하지 않았습니다. 현재 localhost 로만 테스트하였습니다.

## Installing the prerequisites
original: https://docs.dmoj.ca/#/site/installation?id=installing-the-prerequisites

* 각종 패키지 최신 업데이트 및 설치
    ```
    $ sudo apt update
    $ sudo apt install git gcc g++ make python3-dev python3-pip libxml2-dev libxslt1-dev zlib1g-dev gettext curl redis-server
    $ sudo curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -
    $ sudo apt install nodejs
    $ sudo npm install -g sass postcss-cli postcss autoprefixer
    ```

## Creating the database
original: https://docs.dmoj.ca/#/site/installation?id=creating-the-database

* 최신 버전의 mariadb-server 와 libmysqlclient-dev 패키지 설치
    ```
    $ sudo apt update
    $ sudo apt install mariadb-server libmysqlclient-dev
    ```
* 데이터베이스 생성하기
    ```
    $ sudo mysql -uroot -p
    Enter password: <ubuntu 비밀번호>
    mariadb> CREATE DATABASE dmoj DEFAULT CHARACTER SET utf8mb4 DEFAULT COLLATE utf8mb4_general_ci;
    mariadb> GRANT ALL PRIVILEGES ON dmoj.* to 'dmoj'@'localhost' IDENTIFIED BY '<DB-비밀번호>';
    mariadb> exit
    ```

## Installing prerequisites
original: https://docs.dmoj.ca/#/site/installation?id=installing-prerequisites

* 가상환경 구축 및 실행
    ```
    ~$ python3 -m venv jota/dmojsite
    ~$ . jota/dmojsite/bin/activate
    ```
* fork 했던 저장소를 `site` 폴더 안에 저장
    ```
    (dmojsite) ~/jota$ git clone https://github.com/<your-github-id>/JOTA-dmoj-online-judge site
    (dmojsite) ~/jota$ cd site
    (dmojsite) ~/jota/site$ git submodule init
    (dmojsite) ~/jota/site$ git submodule update
    ```
* python3 관련 패키지 모두 설치
    ```
    (dmojsite) ~/jota/site$ sudo pip3 install -r requirements.txt
    (dmojsite) ~/jota/site$ sudo pip3 install mysqlclient
    ```
* curl 명령어로 `local_settings.py` 파일 다운로드
    ```
    ~/jota/site$ curl -o dmoj/local_settings.py https://raw.githubusercontent.com/DMOJ/docs/master/sample_files/local_settings.py
    ```
* 데이터베이스 비밀번호 변경 및 필요한 부분 수정
    ```
    ~/jota/site$ vi dmoj/local_settings.py
    ```
    ```db
    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.mysql',
            'NAME': 'dmoj',
            'USER': 'dmoj',
            'PASSWORD': '<DB-비밀번호>',
            'HOST': '<IP-주소>',
            'OPTIONS': {
                'charset': 'utf8mb4',
                'sql_mode': 'STRICT_TRANS_TABLES,NO_ENGINE_SUBSTITUTION',
            },

    ```
* `manage.py` 실행
    ```
    (dmojsite) ~/jota/site$ sudo python3 manage.py check
    System check identified no issues (47 silenced).
    ```

## Compiling assets
original: https://docs.dmoj.ca/#/site/installation?id=compiling-assets

* 스타일 시트 생성
    ```
    (dmojsite) ~/jota/site$ ./make_style.sh
    ```
* 정적 파일 수집 (`dmoj/local_settings.py`의 환경변수 `STATIC_ROOT` 참고)
    ```
    (dmojsite) ~/jota/site$ sudo python3 manage.py collectstatic
    ```
* 국가별 언어 관련 파일 생성
    ```
    (dmojsite) ~/jota/site$ sudo python3 manage.py compilemessages
    (dmojsite) ~/jota/site$ sudo python3 manage.py compilejsi18n
    ```

## Setting up database tables
original: https://docs.dmoj.ca/#/site/installation?id=setting-up-database-tables
* 데이터베이스 스키마 생성
    ```
    (dmojsite) ~/jota/site$ sudo python3 manage.py migrate
    ```
* 초기 데이터 로드
    ```
    (dmojsite) ~/jota/site$ sudo python3 manage.py loaddata navbar
    (dmojsite) ~/jota/site$ sudo python3 manage.py loaddata language_small
    (dmojsite) ~/jota/site$ sudo python3 manage.py loaddata demo
    ```
* admin 계정 만들기
    ```
    (dmojsite) ~/jota/site$ sudo python3 manage.py createsuperuser
    ```

## Setting up Celery
original: https://docs.dmoj.ca/#/site/installation?id=setting-up-celery

* Celery workers 인증
    ```
    $ service redis-server start

    ==== AUTHENTICATING FOR org.freedesktop.systemd1.manage-units ===
    Authentication is required to start 'redis-server.service'.
    Authenticating as: Ubuntu (ubuntu)
    Password: 
    ==== AUTHENTICATION COMPLETE ===
    ```

## Running the server
original: https://docs.dmoj.ca/#/site/installation?id=running-the-server


* 웹 서버를 열기 위한 새로운 세션을 만듭니다.
    ```
    ~$ . jota/dmojsite/bin/activate
    (dmojsite) ~$ python3 jota/site/manage.py runserver 0.0.0.0:8000
    ```

* bridged를 실행하기 위한 새로운 세션을 만듭니다.
    ```
    ~$ . jota/dmojsite/bin/activate
    (dmojsite) ~$ python3 jota/site/manage.py runbridged
    ```

## Setting up uWSGI
original: https://docs.dmoj.ca/#/site/installation?id=setting-up-uwsgi

## Setting up supervisord
original: https://docs.dmoj.ca/#/site/installation?id=setting-up-supervisord

## Setting up nginx
original: https://docs.dmoj.ca/#/site/installation?id=setting-up-nginx

## Configuration of event server
original: https://docs.dmoj.ca/#/site/installation?id=configuration-of-event-server
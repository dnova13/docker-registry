### registry docker create

```
# quick start
docker run -p 5000:5000 --name registry-srv -d registry:2

# sttings
docker run --name registry-srv \
--restart=always \
-p 5000:5000 \
-e REGISTRY_AUTH=htpasswd \
-e REGISTRY_AUTH_HTPASSWD_REALM="Registry Realm" \
-e REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd \
-v ./auth:/auth \
-v ./registry_data:/var/lib/registry/docker/registry/v2 \
-d registry:2


```

### registry id, password create

```
docker run --rm --entrypoint htpasswd httpd:2 -Bbn {MY_ID} {MY_PASSWORD} > ./auth/htpasswd

ex)
docker run --rm --entrypoint htpasswd httpd:2 -Bbn admin 1q2w3e4r > ./auth/htpasswd

```

### docker login

```
docker login {ip or domain} -u { DOCKER_ID } -p { DOCKER_PW }

ex)
docker login localhost:5000 -u { DOCKER_ID } -p { DOCKER_PW }

docker login localhost:5000 -u admin --password 1q2w3e4r

```

### docker web

```
# quick start
docker run -it -p 8080:8080 --name registry-web --link registry-srv -e REGISTRY_URL=http://registry-srv:5000/v2 -e REGISTRY_NAME=localhost:5000 hyper/docker-registry-web


docker run -d -p 8080:8080 --name registry-web --link registry-srv -e REGISTRY_URL=http://registry-srv:5000/v2 -e REGISTRY_NAME=localhost:5000 hyper/docker-registry-web


```

### ssl 생성

```
openssl req -new -newkey rsa:4096 -days 365 -subj "/CN=localhost" -nodes -x509 -keyout conf/registry-web/auth.key -out conf/registry/auth.cert


# 윈도우 일 경우 - "/CN=localhost" 해당 옵션이 안 먹히므로 셋팅 시 COMMON NAME 일 때 localhost 로 입력
openssl req -new -newkey rsa:4096 -days 365 -nodes -x509 -keyout conf/registry-web/auth.key -out conf/registry/auth.cert
```

-   references docker-registry-web link
    ```
    https://hub.docker.com/r/hyper/docker-registry-web/
    https://github.com/mkuchin/docker-registry-web/tree/master/examples
    ```

### 도커 로그인 명령어

```
docker login localhost:5000 -u ${ docker_id} -p ${ docker_pw}
echo ${docker_pw} | docker login localhost:5000 --username ${ id } --password-stdin
```

### private 실행법

readme.ref.md 참조

### nginx 셋팅 방식

docker-registry-web/examples/nginx-auth-enabled 폴더 참조

### 개발용이 아닌 실제 서버 배포시 opnessl 설정

-subj "/CN=3.39.6.193" , cnname 을 해당 서버 아이피 또는 도메인으로 변경한다.

ex)

```
openssl req -new -newkey rsa:4096 -days 365 -subj "/CN=3.39.6.193" -nodes -x509 -keyout conf/registry-web/auth.key -out conf/registry/auth.cert
```

### `Get "https://{ip주소}:5000/v2/": http: server gave HTTP response to HTTPS client` 로 에러날 경우

-   리눅스인 경우
    받는 쪽에 도커에서 etc/docker/daemon.json 에서 아래와 같이 입력
    daemon.json가 만약 없다면 `vi etc/docker/daemon.json` 생성하여 입력 후 `systemctl restart docker` 도커 재시작

    ```
    {
        "insecure-registries": [
            {도커레지스트리 설치한 ip or 도메인}:5000"
        ]
    }
    ```

-   윈도우인경우 docker desktop 설정 -> 도커 엔진에서 입력 후, 도커 재시작

```
{
    "insecure-registries": [
        {도커레지스트리 설치한 ip or 도메인}:5000"
    ]
}
```

이렇게 설정했는데도 에러가 난다면 `sudo` 를 붙여 관리자 모드로 실행

ex)

```
{
    "insecure-registries": [
        "3.39.6.193:5000",
        "3.39.6.193:8080"
    ]
}

```

### 설정완료 후 컨테이너 재시작 명령어

```
sudo docker restart registry-srv registry-web
```

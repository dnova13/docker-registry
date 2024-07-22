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

nginx-auth-enabled 폴더 참조

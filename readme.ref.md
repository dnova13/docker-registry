## Example configuration of docker registry and docker-registry-web with token authorization

### How to run:

1.  Download and extract example files
    curl -Ls https://github.com/mkuchin/docker-registry-web/releases/download/v0.1.2/examples.tar.gz | tar -xzv
    cd examples/auth-enabled/
2.  Generate private key and self signed certificate with script:

        ./generate-keys.sh

3.  Start containers with docker-compose

        docker-compose up

It will run docker registry `localhost:5000` and web ui on `http://localhost:8080/`

### How to check if it working:

1.  Login into `http://localhost:8080/` with _admin/admin_ username/password
2.  Create test user and grant 'write-all' role to that user.
3.  On the local shell:

    ```
    docker login localhost:5000
    docker pull hello-world
    docker tag hello-world localhost:5000/hello-world:latest
    docker push localhost:5000/hello-world:latest
    docker rmi localhost:5000/hello-world:latest
    docker run localhost:5000/hello-world:latest
    
    docker login 3.39.6.193:5000
    docker tag hello-world 3.39.6.193:5000/hello-world:latest
    docker push 3.39.6.193:5000/hello-world:latest
    docker rmi 3.39.6.193:5000/hello-world:latest
    docker run 3.39.6.193:5000/hello-world:latest
    ```

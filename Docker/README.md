### Docker security cheatsheet , tools , best practices and basic commands to handle dockers
#### Content : 
1) Docker Basic Commands 
2) Docker deployment tools
3) Docker security analysis 
4) Security Tools for analysis/Vulnerability assessment
5) Security Best practices


# Docker basic commands :

Please refer to the docker following files for docker basics :

1) managing-containers.txt
2) Process-management.txt
3) podman-commands.txt
4) containers-using-runc.txt
5) multicontainer-setup.txt
6) cgroup.txt
7) PID_network_namespace.txt


# Docker deployment tools :

### Container as a Service : 
https://www.portainer.io/

### Web interfaces for docker : 
https://github.com/francescou/docker-compose-ui
https://github.com/tobegit3hub/seagull

### Daemonless Open Containers Initiative :
https://docs.podman.io/en/latest/


# Docker Security Analysis :

### Security Source code analysis rules :
https://github.com/buddy-works/dockerfile-linter/blob/master/Rules.md#EF0004

### API registry documentation :

Docker Registry HTTP API V2 : https://docs.docker.com/registry/spec/api/


# Security Tools for analysis/Vulnerability assessment :

### Docker file analysis tools :
Dockerlint : https://github.com/buddy-works/dockerfile-linter
Hadolint : https://github.com/hadolint/hadolint

### Docker container/socket analysis tool : 
Container : https://github.com/wagoodman/dive
Container : https://github.com/docker/docker-bench-security
Container : https://github.com/genuinetools/amicontained
Socket : https://github.com/kost/dockscan

### Vulnerability assessment framework : 
https://github.com/quay/clair

### Cloud native runtime security for monitoring : 
https://github.com/falcosecurity/falco


# Security Best practices :
### For writing a docker file :
https://docs.docker.com/develop/develop-images/dockerfile_best-practices/

## Securing Docker :
### 1) Enable TLS security support for SSH/TCP ports
```
mkdir certs
cd certs
``` 

#### Generate CA key
```
openssl ecparam -name prime256v1 -genkey -noout -out ca.key
```

#### Generate CA certificate corresponding to the generated key
```
openssl req -new -x509 -sha256 -key ca.key -out ca.crt
```

#### Generate Server key
```
openssl ecparam -name prime256v1 -genkey -noout -out server.key
```

#### Create a server certificate signing request (CSR)
```
openssl req -new -sha256 -key server.key -out server.csr
```

Note: Please remember to put registry2 in the Common Name (FQDN) field of the CSR.
Otherwise the TLS connection will fail.

Create a server certificate by using CA certificate/key and server certificate signing request (CSR).
```
openssl x509 -req -in server.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out server.crt -days 1000 -sha256
```

Run the registry container

```
docker run -d -p 5000:443 -v /root/registry:/var/lib/registry -v /root/certs:/certs -e REGISTRY_HTTP_ADDR=0.0.0.0:443 -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/server.crt -e REGISTRY_HTTP_TLS_KEY=/certs/server.key --name registry2 registry:5000/registry:2
```

#### Check the images present on the registry using curl.
```
curl registry2:5000/v2/_catalog
curl -k https://registry2:5000/v2/_catalog
```

Try to push the already tagged alpine image to the registry.
```
Command: docker push registry2:5000/alpine
```

### 2) User namespace remapping : 
a) https://docs.docker.com/engine/security/userns-remap/ <br />
b) https://github.com/flast101/docker-privesc

### 3) Enabling authentication :

#### Step 1: Stop the already running registry container.
```
docker stop registry2
```

#### Create password file using htpasswd.
```
htpasswd -Bbn user password > /root/certs/passwordfile
```

Credentials used:
Username: user
Password: password

#### Check the contents of the generated password file.
```
cat /root/certs/passwordfile
```

#### Run the registry 
```
docker run -d -p 5000:443 -v /root/registry:/var/lib/registry -v /root/certs:/certs -e REGISTRY_HTTP_ADDR=0.0.0.0:443 -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/server.crt -e REGISTRY_HTTP_TLS_KEY=/certs/server.key -e "REGISTRY_AUTH=htpasswd" -e "REGISTRY_AUTH_HTPASSWD_REALM=Registry Realm" -e REGISTRY_AUTH_HTPASSWD_PATH=/certs/passwordfile --name registry2 registry:5000/registry:2
```

#### Check the images present on the registry using curl.
```
curl -k -u user:password https://registry2:5000/v2/_catalog
```

#### Login into the registry to use it.
```
docker login registry2:5000 -u user
```

#### Try to push the already tagged alpine image to the registry.
```
docker push registry2:5000/alpine
```

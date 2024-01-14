# keycloak-integration

_proxy_

```bash
docker build -t ui .
```

_docker-compose_
```yml
---
version: '3.8'
services:
  keycloak:
    image: jboss/keycloak:latest
    container_name: keycloak
    environment:
      - KEYCLOAK_USER=admin
      - KEYCLOAK_PASSWORD=admin
      - DB_VENDOR=POSTGRES
      - DB_ADDR=postgres
      - DB_DATABASE=keycloak
      - DB_USER=keycloak
      - JDBC_PARAMS="useSSL=false"
      - DB_PASSWORD=keycloak
      - PROXY_ADDRESS_FORWARDING=true
      - DEBUG=true 
      - DEBUG_PORT='*:8787'
      - KEYCLOAK_LOGLEVEL=INFO
      - ROOT_LOGLEVEL=DEBUG
      - JAVA_OPTS=-server -Xms64m -Xmx512m -XX:MetaspaceSize=96M -XX:MaxMetaspaceSize=256m -Djava.net.preferIPv4Stack=true -Djboss.modules.system.pkgs=org.jboss.byteman -Djava.awt.headless=true --add-exports=java.desktop/sun.awt=ALL-UNNAMED --add-exports=java.naming/com.sun.jndi.ldap=ALL-UNNAMED --add-opens=java.base/java.lang=ALL-UNNAMED --add-opens=java.base/java.lang.invoke=ALL-UNNAMED --add-opens=java.base/java.lang.reflect=ALL-UNNAMED --add-opens=java.base/java.io=ALL-UNNAMED --add-opens=java.base/java.security=ALL-UNNAMED --add-opens=java.base/java.util=ALL-UNNAMED --add-opens=java.base/java.util.concurrent=ALL-UNNAMED --add-opens=java.management/javax.management=ALL-UNNAMED --add-opens=java.naming/javax.naming=ALL-UNNAMED
    ports:
      - "8080:8080"
    depends_on:
      - postgres
    networks:
      - keycloak-networks
  postgres:
    image: postgres:latest
    container_name: postgres
    environment:
      - POSTGRES_DB=keycloak
      - POSTGRES_USER=keycloak
      - POSTGRES_PASSWORD=keycloak
    networks:
      - keycloak-networks
    volumes:
      - /var/local/rcms/db:/var/lib/postgresql/data
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 1024M
        reservations:
          cpus: '1'
          memory: 512M
networks:
  keycloak-networks:
    name: keycloak-networks
    driver: bridge
```
_docs_

```console
1. https://hub.docker.com/r/jboss/keycloak
2. https://www.keycloak.org/documentation.html
```
_reference_

![image](https://github.com/januo-org/keycloak-integration/assets/57703276/6313931f-e320-4179-83d7-71030a557811)



official docker reference


```bash
docker network create keycloak
docker run --network keycloak -d --name db -e POSTGRES_DB=keycloak -e POSTGRES_USER=keycloak -e POSTGRES_PASSWORD=keycloak postgres:latest

docker run --network keycloak -d -e POSTGRES_DB=keycloak \
           -e POSTGRES_USER=keycloak \
           -e POSTGRES_PASSWORD=keycloak \
           -e DB_VENDOR=POSTGRES \
           -e DB_ADDR=db \
           -e KEYCLOAK_ADMIN=admin -e KEYCLOAK_ADMIN_PASSWORD=admin \
           -e JAVA_OPTS="-server -Xms1024m -Xmx4096m -XX:MetaspaceSize=512M -XX:MaxMetaspaceSize=1024m -Djava.net.preferIPv4Stack=true -Djboss.modules.system.pkgs=org.jboss.byteman -Djava.awt.headless=true --add-exports=java.desktop/sun.awt=ALL-UNNAMED --add-exports=java.naming/com.sun.jndi.ldap=ALL-UNNAMED --add-opens=java.base/java.lang=ALL-UNNAMED --add-opens=java.base/java.lang.invoke=ALL-UNNAMED --add-opens=java.base/java.lang.reflect=ALL-UNNAMED --add-opens=java.base/java.io=ALL-UNNAMED --add-opens=java.base/java.security=ALL-UNNAMED --add-opens=java.base/java.util=ALL-UNNAMED --add-opens=java.base/java.util.concurrent=ALL-UNNAMED --add-opens=java.management/javax.management=ALL-UNNAMED --add-opens=java.naming/javax.naming=ALL-UNNAMED" \
           -p 8080:8080 \
           --name keycloak \
           quay.io/keycloak/keycloak:23.0.4 start --http-port=8080 --http-enabled=true --http-relative-path=/auth --hostname-strict-https=false --hostname-strict=true --hostname=dcm4che.januo.io  --proxy=edge

docker run --network keycloak -d --name proxy -p 80:80 -p 443:443 jjino/nginx.dcm4che.januo.io 
```

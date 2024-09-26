# Images
* `spring-6-cloud-gateway:0.0.1-SNAPSHOT`
* `spring-6-auth-server:0.0.1-SNAPSHOT`
* `spring-6-rest-mvc:0.0.1-SNAPSHOT`
* `spring-6-reactive:0.0.1-SNAPSHOT`
* `spring-6-reactive-mongo:0.0.1-SNAPSHOT`

Run Gateway
```shell
docker run --name gateway -d -p 8080:8080 spring-6-cloud-gateway:0.0.1-SNAPSHOT
```

Run Gateway on Apple Silicon
```shell
docker run --name gateway -d -p 8080:8080 --platform linux/amd64 spring-6-cloud-gateway:0.0.1-SNAPSHOT
```

Remove Container
```shell
docker rm gateway
```

Run Auth Server
```shell
docker run --name auth-server -d -p 9000:9000 spring-6-auth-server:0.0.1-SNAPSHOT
```

Run Auth Server on Apple Silicon
```shell
docker run --name auth-server -d -p 9000:9000 --platform linux/amd64 spring-6-auth-server:0.0.1-SNAPSHOT
```

Run Rest MVC
```shell
docker run --name rest-mvc -d -p 8081:8081 spring-6-rest-mvc:0.0.1-SNAPSHOT
```

Show logs for Rest MVC
```shell
docker logs rest-mvc
```

Run Rest MVC
```shell
docker run --name rest-mvc -d -p 8081:8080 spring-6-rest-mvc:0.0.1-SNAPSHOT
```

Run Rest MVC on port 8081
```shell
docker run --name rest-mvc -d -p 8081:8081 -e SERVER_PORT=8081 spring-6-rest-mvc:0.0.1-SNAPSHOT
```

Run Rest MVC on port 8081, Apple Silicon
```shell
docker run --name rest-mvc -d -p 8081:8081 -e SERVER_PORT=8081 --platform linux/amd64 spring-6-rest-mvc:0.0.1-SNAPSHOT
```

Run Rest MVC with profile localmysql
```shell
docker run --name rest-mvc -d -p 8081:8081 -e SPRING_PROFILES_ACTIVE=localmysql spring-6-rest-mvc:0.0.1-SNAPSHOT
```

Run Gateway with active profile docker
```shell
docker run --name gateway -d -p 8080:8080 -e SPRING_PROFILES_ACTIVE=docker spring-6-cloud-gateway:0.0.1-SNAPSHOT
```
```shell
docker run --name gateway -d -p 8080:8080 -e SPRING_PROFILES_ACTIVE=docker --link auth-server:auth-server spring-6-cloud-gateway:0.0.1-SNAPSHOT
```

Run Auth Server with host name set to auth-server
```shell
docker run --name auth-server -h auth-server -d -p 9000:9000 spring-6-auth-server:0.0.1-SNAPSHOT
```

Run rest-mvc with jwt issuer host set and link
```shell
docker run --name rest-mvc -d -p 8081:8080 -e SPRING_SECURITY_OAUTH2_RESOURCESERVER_JWT_ISSUER_URI=http://auth-server:9000 --link auth-server:auth-server spring-6-rest-mvc:0.0.1-SNAPSHOT
```

Rerun gateway with link to auth-server and rest-mvc
```shell
docker stop gateway 
docker rm gateway
docker run --name gateway -d -p 8080:8080 -e SPRING_PROFILES_ACTIVE=docker --link auth-server:auth-server --link rest-mvc:rest-mvc spring-6-cloud-gateway:0.0.1-SNAPSHOT
```

Run PostgreSQL
```shell
docker run --name postgres -d -e POSTGRES_USER=restadmin -e POSTGRES_PASSWORD=password -e POSTGRES_DB=restdb postgres:16
```

Run rest-mvc with link to postgresql
```shell
docker stop rest-mvc
docker rm rest-mvc
docker run --name rest-mvc -d -p 8081:8080 -e SPRING_PROFILES_ACTIVE=localpostgresql \
 -e SPRING_SECURITY_OAUTH2_RESOURCESERVER_JWT_ISSUER_URI=http://auth-server:9000 -e SPRING_DATASOURCE_URL=jdbc:postgresql://posrgres:5432/restdb  \
 -e SERVER_PORT=8080 --link auth-server:auth-server --link postgres:postgres spring-6-rest-mvc:0.0.1-SNAPSHOT
```

Run Reactive Container
```shell    
docker run --name reactive -d -p 8082:8080 -e SPRING_SECURITY_OAUTH2_RESOURCESERVER_JWT_ISSUER_URI=http://auth-server:9000 \
-e SERVER_PORT=8080 --link auth-server:auth-server spring-6-reactive:0.0.1-SNAPSHOT
```

Rerun gateway with link to auth-server and rest-mvc and reactive
```shell
docker stop gateway 
docker rm gateway
docker run --name gateway -d -p 8080:8080 -e SPRING_PROFILES_ACTIVE=docker --link auth-server:auth-server --link rest-mvc:rest-mvc \
--link reactive:reactive spring-6-cloud-gateway:0.0.1-SNAPSHOT
```

Run MongoDB
```shell
docker run -d --name mongo -e MONGO_INITDB_ROOT_USERNAME=root -e MONGO_INITDB_ROOT_PASSWORD=example -p 27017:27017 mongo:latest
```

Run Reactive Mongo
```shell
docker run --name reactive-mongo -d -e SPRING_SECURITY_OAUTH2_RESOURCESERVER_JWT_ISSUER_URI=http://auth-server:9000 \
 -e MONGO_DB_HOST=mongo -e SERVER_PORT=8080 --link auth-server:auth-server --link mongo:mongo spring-6-reactive-mongo:0.0.1-SNAPSHOT
```
Rerun gateway with link to auth-server and rest-mvc and reactive and reactive-mongo
```shell
docker stop gateway 
docker rm gateway
docker run --name gateway -d -p 8080:8080 -e SPRING_PROFILES_ACTIVE=docker --link auth-server:auth-server --link rest-mvc:rest-mvc \
--link reactive:reactive --link reactive-mongo:reactive-mongo spring-6-cloud-gateway:0.0.1-SNAPSHOT
```
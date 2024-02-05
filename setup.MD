
https://www.elastic.co/blog/getting-started-with-the-elastic-stack-and-docker-compose
# Elasticsearch + Kibana Docker Compose Setup with SSL and OAuth2 Authentication

Note
- storing the Elasticsearch data in a volume outside of the container by specifying - esdata01:/usr/share/elasticsearch/data The two primary reasons for this are performance and data persistence. If we were to leave the data directory inside the container, we would see a significant degradation in the performance of our Elasticsearch node, as well as lose data anytime we needed to change the configuration of the container within our docker-compose file
  
- After es01 ready, run `docker cp es01:/usr/share/elasticsearch/config/certs/ca/ca.crt ./certs/.` to save cert
  - Query the node to verify: `curl --cacert ./certs/ca.crt -u elastic:changeme https://localhost:9200`

LOGIN:
1. login
   1. Super-user: elastic
   2. Password: changeme

2. Use keystore for keeping secrets
3. 



docker cp elasticstack_docker-es01-1:/usr/share/elasticsearch/config/certs/ca/ca.crt /tmp/.


`docker-compose -f compose.yaml up -d --no-deps --build kibana`

Start compose: `docker-compose -f compose.yaml up -d`
- ensure rebuilds images: `docker-compose -f compose.yaml up --build`
Stop compose: `docker-compose -f compose.yaml down`



Ensure ports are not already in use:
- ex, check mysql port: `netstat -ano | findstr :3306`
- if in use, stop service in services.msc (may need to use dev-enabled permissions)



1. update dockerfile
<!-- 2. build dockerfile
- `docker build -f tests.Dockerfile -t upload-run-tests .`
- `docker build -f tomcat.Dockerfile -t upload-web-server .`
- `docker build -f database.Dockerfile -t upload-database .` -->
3. only recreate/re-run container with updated image
- `docker-compose -f compose.yaml up -d --no-deps --build run-tests`
<!-- - `docker-compose -f compose.yaml up -d --no-deps --build web-server` -->
- `docker-compose -f compose.yaml up -d --no-deps --build database`
- `docker-compose -f compose.yaml up -d --no-deps --build tomcat`

<!-- updates the containers that have a newer image (than the one currently running): 
- `docker-compose up -d --no-deps <service>`
- ex: 
  - `docker build -f tests.Dockerfile -t upload-run-tests .`
  - `docker-compose -f compose.yaml pull`
  - `docker-compose f compose.yaml up -d --no-deps run-tests` -->


see directory structure: `find . -type d | sed -e "s/[^-][^\/]*\//  |/g" -e "s/|\([^ ]\)/|-\1/"`


test ping:
- `apk add --no-cache iputils`

curl host.docker.internal:8443
curl -k host.docker.internal:8443

debug ssl
- `openssl s_client -connect host.docker.internal:8443 -prexit -showcerts `
  - -debug
  - output will probably contain "Acceptable client certificate CA names" and a list of CA certificates from the server, or possibly "No client certificate CA names sent", if the server doesn't always require client certificates
  - see cert details: `openssl s_client -connect host.docker.internal:8443 | tee logcertfile`
    - the intermediate certificate is the issuer / signer of the server certificate
  - 

  - see own certs: `openssl s_client -connect hostname:port -cert client_cert.pem -key client_key.pem`
  - `openssl s_client -connect hostname:port -cert client_cert.pem -key client_key.pem`

<!-- mvn clean install -P JOEL_TMAP_TEST2 -->

list certs in my jks file
- `keytool -list -v -keystore <jks_file>`
- `keytool -list -v -keystore scripts/docker-internal/server.jks`
- `keytool -list -v -keystore scripts/old/trust.jks`
- `keytool -list -v -keystore tmap_test_certificates/new_certificates/server.jks`
- dafault password: `changeit` 
extract cert from server
- `echo | openssl s_client -connect host.docker.internal:8443 -prexit -showcerts 2>/dev/null | openssl x509 -noout -text > server_cert.txt`
- `echo | openssl s_client -connect host.docker.internal:8443 -prexit -showcerts 2>/dev/null | openssl x509 -noout -text`
Compare the certificates
- openssl x509 -noout -fingerprint -in <certificate_file>


keytool -list -v -keystore your_keystore.jks -storepass your_password

<!-- maven app: needs CA certificates of the CA that have issued the SSL/TLS certificates used by Apache Tomcat (ROOT CA). truststore contains the CA certificates in the chain of trust for Apache Tomcat's SSL/TLS certificate.-->






# Docker image

1. log in to dockerhub:

2. Pull image from dockerhub to artifactory:

- `docker pull imageName:tagName`
- Ex to pull node:20 image: `docker pull node:20`

3. Tag image: (default is latest)

- `docker tag node imageName:tagName`
- Ex: `docker tag node node:20`
  - tagged as 20

4. Push image to local artifactory repo:

- `docker push image:tag`
- Ex: `docker push node:20`

# Local testing

docker build -f tests.Dockerfile -t upload-run-tests .
docker run -it --rm upload-run-tests bash
docker run -it --rm upload-run-tests sh
docker run -it --rm upload-database sh
verify settings:
mvn --version
ls /usr/share/maven/ref/
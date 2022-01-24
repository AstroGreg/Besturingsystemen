# LABO 2: Container Orchestration

[uitleg](https://git.uclllabs.be/bs2/labos/container-orchestration)

```bash
ssh user1@193.191.177.8 -p 10680
wachtwoord: AzertY2021_2022
passwd

cd .ssh
nano config
	Host bs2
		HostName 193.191.177.8
		User user1
		Port 10680
```


```bash
ssh bs2

curl -L https://downloads.portainer.io/portainer-agent-stack.yml -o portainer-agent-stack.yml

nano portainer-agent-stack.yml 
	9000:9000 veranderen naar 9001:9000

docker stack deploy -c portainer-agent-stack.yml portainer
```

[http://193.191.177.8:10681/](http://193.191.177.8:10681/) 

### **OEFENING 1**

```bash
docker images
docker tag 0c220a1084b9 kianatrs/minecraft:latest
docker push kianatrs/minecraft
```

[http://193.191.177.8:10681/](http://193.191.177.8:10681/) 

- Services > Add service
    - Name: `minecraft`
    - Image: `kianatrs/minecraft`
    - Create the service

### OEFENING 2

[http://193.191.177.8:10681/](http://193.191.177.8:10681/) 

- Stacks > Add stack
    - Name: `wordpress`
    - Web editor:
        
        ```yaml
        version: '3.7'
        
        services:
           db:
             image: mysql:5.7
             volumes:
               - db_data:/var/lib/mysql
             restart: always
             environment:
               MYSQL_ROOT_PASSWORD: somewordpress
               MYSQL_DATABASE: wordpress
               MYSQL_USER: wordpress
               MYSQL_PASSWORD: wordpress
        
           wordpress:
             depends_on:
               - db
             image: wordpress:latest
             ports:
               - "9002:80"
             restart: always
             environment:
               WORDPRESS_DB_HOST: db:3306
               WORDPRESS_DB_USER: wordpress
               WORDPRESS_DB_PASSWORD: wordpress
               WORDPRESS_DB_NAME: wordpress
        volumes:
            db_data: {}
        ```
        
    - Deploy the stack

[http://193.191.177.8:10682/](http://193.191.177.8:10682/) 


### OEFENING 3

```yaml
cd ../..
sudo mkdir opt/wikidata
```

[http://193.191.177.8:10681/](http://193.191.177.8:10681/) 

- Stacks > Add stack
- Name: `wiki`
- Web editor:

```yaml
version: '3'
services:
db:
image: postgres:11-alpine
environment:
  POSTGRES_DB: wiki
  POSTGRES_PASSWORD: verysecurepassword
  POSTGRES_USER: cosci
logging:
  driver: "none"
restart: unless-stopped
volumes:
  - /opt/wikidata:/var/lib/postgresql/data 

wiki:
image: requarks/wiki:2
depends_on:
  - db
environment:
  DB_TYPE: postgres
  DB_HOST: db
  DB_PORT: 5432
  DB_USER: cosci
  DB_PASS: verysecurepassword
  DB_NAME: wiki
restart: unless-stopped
ports:
  - "9003:3000"
```

- Deploy the stack

[http://193.191.177.8:10683](http://193.191.177.8:10683/)

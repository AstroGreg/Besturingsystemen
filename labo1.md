# LABO 1: Docker

[uitleg](https://git.uclllabs.be/bs2/labos/docker/-/blob/main/README.md)

### image builden

```bash
docker build -t naam-image .
```

> . verwijst naar de working directory

### image opstarten

```bash
docker run naam-image
docker run ubuntu ls
```

```bash
docker run -it ubuntu bash
```

> -it: interactive

```bash
docker run -d -p 8080:80 coscicorp/website
```

> -d: Daemonized (applicatie blijft in achtergrond draaien) 

> -p [HOSTPORT]:[CONTAINERPORT]: containerport mappen op hostport

### minecraft

```bash
wget https://launcher.mojang.com/v1/objects/a16d67e5807f57fc4e550299cf20226194497dc2/server.jar

nano index.html
	Hello World

nano eula.txt
	eula=true

nano Dockerfile
	FROM openjdk
	COPY server.jar root/server.jar
	COPY eula.txt root/eula.txt
	WORKDIR /root
	CMD ["java","-jar","server.jar"]

docker build -t minecraft .
docker run -p 25565:25565 minecraft
```

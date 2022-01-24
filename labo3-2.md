# LABO 3: CI-CD

[uitleg](https://git.uclllabs.be/bs2/labos/ci-cd)

[https://git.uclllabs.be](https://git.uclllabs.be/)

- New project > Create blank project
    - naam geven
    - `Initialize repository with a README` uitzetten
    - Create project
- Push an existing folder (vb. WEB1 site)
    
    ```bash
    cd existing_folder
    git init
    git remote add origin ssh://git@git.uclllabs.be:22345/r0796353/labo3.git
    git add .
    git commit -m "Initial commit"
    git push -u origin master
    ```
    

### Dockerfile

- New file
    - file name: `Dockerfile`
        
        ```docker
        FROM nginx
        COPY src/ /usr/share/nginx/html/
        ```
        
    - Commit changes

### Automatisatie

```bash
wget https://gitlab-runner-downloads.s3.amazonaws.com/latest/deb/gitlab-runner_amd64.deb
sudo dpkg -i gitlab-runner_amd64.deb
```

[https://git.uclllabs.be](https://git.uclllabs.be/) > Settings > CI/CD > Runners Expand > copy registration token

```bash
sudo gitlab-runner register
	url: https://git.uclllabs.be/
	registration-token: SAWf3w3f3zqr-qwtHmsR
	description: labo 3
	tags:
	executor: shell
```

[https://git.uclllabs.be](https://git.uclllabs.be/) > Settings > CI/CD > Runners Expand > Available specific runners

- labo 3 zou hier moeten runnen

```bash
sudo usermod -aG docker gitlab-runner
sudo -u gitlab-runner -H docker info
```

### Pipeline

- New file
    - file name: `.gitlab-ci.yml`
        
        ```yaml
        before_script:
          - docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY
        
        build_image:
          stage: build
          script:
            - docker build -t $CI_REGISTRY_IMAGE:$CI_COMMIT_REF_SLUG .
            - docker push $CI_REGISTRY_IMAGE:$CI_COMMIT_REF_SLUG
        
        deploy_image:
          stage: deploy
          script:
            - curl -X POST http://cosci.be:9000/api/webhooks/5dc429ef-63f4-4e8d-89be-c47fd408d2cd
        ```
        
    - Commit changes
- CI/CD > Pipelines > Run pipeline > Run Pipeline
- Packages & Registries > Container Registry

[http://193.191.177.8:10681/](http://193.191.177.8:10681/) 

- Services > Add service
    - Name: `labo3`
    - Registry: `Gitlab`
    - Image: `r0796353/labo3:master`
    - Port mapping: host `9004` → container `80`
    - `Create the service`

[http://193.191.177.8:10684](http://193.191.177.8:10684/)

# OEFENING 1

- New project > Create blank project
    - naam geven
    - `Initialize repository with a README` uitzetten
    - Create project
- Push an existing folder (vb. WEB3 site)
    
    ```bash
    cd existing_folder
    git init
    git remote add origin ssh://git@git.uclllabs.be:22345/r0796353/labo3_oef1.git
    git add .
    git commit -m "Initial commit"
    git push -u origin master
    ```
    

### Dockerfile

- New file
    - file name: `Dockerfile`
        
        ```docker
        FROM maven:3 as build
        COPY src/ /usr/src/app/src
        COPY ddl/ /usr/src/app/ddl
        COPY pom.xml /usr/src/app/pom.xml
        RUN mvn -f /usr/src/app/pom.xml clean package
        
        FROM tomcat:8
        COPY --from=build /usr/src/app/target/*.war /usr/local/tomcat/webapps/ROOT.war
        CMD ["catalina.sh", "run"]
        ```
        
    - Commit changes

### Automatisatie

[https://git.uclllabs.be](https://git.uclllabs.be/) > Settings > CI/CD > Runners Expand > copy registration token

```bash
sudo gitlab-runner register
	url: https://git.uclllabs.be/
	registration-token: d2zxQEQHrtLKxDDoQf8b
	description: labo 3 oef 1
	tags:
	executor: shell
```

[https://git.uclllabs.be](https://git.uclllabs.be/) > Settings > CI/CD > Runners Expand > Available specific runners

- labo 3 oef 1 zou hier moeten runnen

```bash
sudo usermod -aG docker gitlab-runner
sudo -u gitlab-runner -H docker info
```

### Pipeline

- New file
    - file name: `.gitlab-ci.yml`
        
        ```yaml
        before_script:
          - docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY
        
        build_image:
          stage: build
          script:
            - docker build -t $CI_REGISTRY_IMAGE:$CI_COMMIT_REF_SLUG .
            - docker push $CI_REGISTRY_IMAGE:$CI_COMMIT_REF_SLUG
        
        deploy_image:
          stage: deploy
          script:
            - curl -X POST http://cosci.be:9000/api/webhooks/5dc429ef-63f4-4e8d-89be-c47fd408d2cd
        ```
        
    - Commit changes
- CI/CD > Pipelines > Run pipeline > Run Pipeline
- Packages & Registries > Container Registry

[http://193.191.177.8:10681/](http://193.191.177.8:10681/) 

- Services > Add service
    - Name: `labo3_oef1`
    - Registry: `Gitlab`
    - Image: `r0796353/labo3_oef1:master`
    - Port mapping: host `9005` → container `80`
    - `Create the service`

[http://193.191.177.8:10685](http://193.191.177.8:10685/)
S
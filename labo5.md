# LABO 5: PE

[uitleg](https://git.uclllabs.be/bs2/pe/opgave)

# Frontend

[https://git.uclllabs.be](https://git.uclllabs.be/)

- New project > Create blank project
    - naam geven
    - `Initialize repository with a README` uitzetten
    - Create project
- Clone an existing repo
    
    ```bash
    git clone ssh://git@git.uclllabs.be:22345/bs2/pe/pe_frontend.git
    cd existing_repo
    git init
    git remote add origin ssh://git@git.uclllabs.be:22345/r0796353/pe_frontend.git
    git add .
    git commit -m "Initial commit"
    git push -u origin master
    ```
    

### Dockerfile

- New file
    - file name: `Dockerfile`
        
        ```docker
        # pull official base image
        FROM node:13.12.0-alpine
        
        # set working directory
        WORKDIR /app
        
        # add `/app/node_modules/.bin` to $PATH
        ENV PATH /app/node_modules/.bin:$PATH
        
        # install app dependencies
        COPY package.json ./
        COPY package-lock.json ./
        RUN npm install --silent
        RUN npm install react-scripts@3.4.1 -g --silent
        
        # add app
        COPY . ./
        
        # start app
        CMD ["npm", "start"]
        ```
        
    - Commit changes

### CI/CD systeem

- [https://git.uclllabs.be](https://git.uclllabs.be/) > Settings > CI/CD > Runners Expand > copy registration token
    
    ```bash
    sudo gitlab-runner register
    	url: https://git.uclllabs.be/
    	registration-token: Zew8TgyWF1xxsBGKiJN2
    	description: labo5
    	tags:
    	executor: shell
    ```
    
    [https://git.uclllabs.be](https://git.uclllabs.be/) > Settings > CI/CD > Runners Expand > Available specific runners
    
    - `labo5` zou hier moeten runnen
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
        ```
        
    - Commit changes
- CI/CD > Pipelines > Run pipeline > Run Pipeline
- Packages & Registries > Container Registry

# Backend

[https://git.uclllabs.be](https://git.uclllabs.be/)

- New project > Create blank project
    - naam geven
    - `Initialize repository with a README` uitzetten
    - Create project
- Clone an existing repo
    
    ```bash
    git clone ssh://git@git.uclllabs.be:22345/bs2/pe/pe_backend.git
    cd existing_repo
    git init
    git remote add origin ssh://git@git.uclllabs.be:22345/r0796353/labo5_backend.git
    git add .
    git commit -m "Initial commit"
    git push -u origin master
    ```
    

### CI/CD systeem

- [https://git.uclllabs.be](https://git.uclllabs.be/) > Settings > CI/CD > Runners Expand > copy registration token
    
    ```bash
    sudo gitlab-runner register
    	url: https://git.uclllabs.be/
    	registration-token: 1kThT6yF6H8XN1MhPDKe
    	description: labo5 backend
    	tags:
    	executor: shell
    ```
    
    [https://git.uclllabs.be](https://git.uclllabs.be/) > Settings > CI/CD > Runners Expand > Available specific runners
    
    - `labo5 backend` zou hier moeten runnen
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
        ```
        
    - Commit changes
- CI/CD > Pipelines > Run pipeline > Run Pipeline
- Packages & Registries > Container Registry

# Front- en backend connecteren

[http://193.191.177.8:10681/](http://193.191.177.8:10681/) 

- Stacks > Add stack
    - Name: `pelabo5`
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
               MYSQL_ROOT_PASSWORD: labo5
               MYSQL_DATABASE: labo5
               MYSQL_USER: labo5
               MYSQL_PASSWORD: labo5
        
           backend:
              image: "git.uclllabs.be:5050/r0796353/labo5-backend:main"
              environment:
                MYSQL_HOST: db
                MYSQL_USER: labo5
                MYSQL_PASSWORD: labo5
                MYSQL_DATABASE: labo5
                MYSQL_PORT: "3306"
              ports:
                - "9006:8080"
              depends_on:
                - db
        
           frontend:
              image: "git.uclllabs.be:5050/r0796353/pe_frontend:main"
              container_name: "frontend"
              ports:
                - "9007:3000"
              depends_on:
                - backend
        
        volumes:
            db_data: {}
        ```
        
    - `Create the stack`
- aanpassen `API_URL` in `pe_frontend/src/App.js` naar link backend:
    
    ```jsx
    class App extends Component {
    	API_URL = "http://193.191.177.8:10686"
    	...
    }
    ```
    
- Services > Select `pelabo5_backend`, `pelabo5_db` en `pelabo5_frontend`
    - Update > Pull latest image version > Update

[http://193.191.177.8:10687](http://193.191.177.8:10687/)

## webhook

[http://193.191.177.8:10681/](http://193.191.177.8:10681/) 

- Services > pelabo5_backend > Service Webhook aanzetten
    - link kopieren
    - [https://git.uclllabs.be/r0796353/labo5-backend/-/blob/main/.gitlab-ci.yml](https://git.uclllabs.be/r0796353/labo5-backend/-/blob/main/.gitlab-ci.yml)
        
        ```bash
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
            - curl -X POST http://192.0.2.11:9001/api/webhooks/243247c9-f911-44dc-9924-123ed368d127
        ```
        
- zelfde voor frontend
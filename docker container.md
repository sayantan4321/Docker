**docker container** 

---

-> public/private container Docker repository

every module or language have docker-image hosted in public containers. docker we can search for building our own system to developed and deploy::Redis for messaging



**before container: how is Application development ?**



-> each technology/services have to install in every system depending on OS having diff. installation process (10 person 10 installations each)

-> Due to many steps and have to run that steps, high chance of getting wrong something.



**After Container: Application development**



-> own isolated image, having a container image

-> package with all needed led for configuration (files, configuration items, scripts)

-> one line docker command to install the service or package.

-> run same 2 app with diff versions (even for each 05/systems are same).



**before Container: Application deployment**



-> dev have to gave jar fie of the application + configuration manual + scripts + dB config manual for DB then Operation team have to install it into server. High risk of dependency, version conflict, misunderstanding in Manual following

-> there was an issue where every service(webservice, dB, messaging should align with OS with correct version).



**After Container: Application deployment**



-As dev and operation team work together in the application(configuration + JAR file + dependencies) into a container to package the whole.



it is encapsulated in one single env -- no need to configure directly any of this into server



-> run a docker command that pulls the container IMAGE that we stored somewhere in the repo. and run it. (no environmental configuration needed on server  --except docker runtime(which was ONF TIME effort)) 



**CONTATNER:**

-> it is generally layers of Images. one after another

-> Mostly Linus based image, because small in size. (base line named as: alpine:3.10) base images need to be small, so it will ensure container stay small in size.

-> Top image will be the application image \[intermediate images have the config data in between]



To run a docker container: go to docker hub-> select an service image (post grace): as it is public repo no need to login/auth. 

In CMD-> command: docker run \[imagename]: \[versionnane] -{else latest version will be installed}. This run command will pull download the images of the Library. 



--> docker ps (show all the images in the system)

--> docker image: it was the actual package of all the components/artifacts.

--> docker container: when we start the application in our local machine with the image.



--> other commands: docker ps -a, docker rm container\_name,

-> docker image: show the list of images present into our host.

-> docker rmi dbeaver: to remove a image from system (make sure that no container of that image is running while attempting to rai image).



command: docker pull image\_name



**To Install:** docker run 'posgres9.6'  -> If not present in local machine it will install all the layers.

--> if downloading a diff version then only new/missing layers of the version will get downloaded



##### DOCKER VS VIRTUAL MACHINE:



-> docker virtualize the application layer(contains images): it uses the OS Kernel to host

-> in vs the application layer can host the applications in its own kernel(which virtualize the whole Operating System): doesn't use host kernel and boots up on its own.



:: docker image is much smaller than VM's.

:: Docker Containers start and run much faster than VM's I

:: VM's of any OS can run on any OS host, but we can't do that with docker (Linux based docker image is not compatible on Windows kernel){solution: Docker Toolbox}



**Install Docker in diff OS(Mac/windows/Linux):**



:before installing Docker pre-requisites(windows above 18)



window: 1) check window version + virtualization enabled or not. 

&nbsp;       2)download from stable channel + go through manual



Linux: 1) setup repo and download and install it form there(manual in docker docs)

&nbsp;      2) install packages manually(not recommended)

&nbsp;      3) running through scripts(only development purpose)



Docker commands >>> docker null; docker run; docker start; docker stop; docker exec it; docker logs



**Container and Image is very different but simple: Image is a part of a container -> \[virtual file system yo store config files + env configs + application image + port number]**



command: docker pull Redis

-> docker images: all images present in system

-> **NEED REDIS RUNNING**: so container required(to use Redis in our application) => docker run redis



:: we will have a container id and when we terminate the session the container will stop running



**-> docker run -d redis (detach running):** container id will show and if we check it will in running state

**-> docker stop container\_Ld\[357894875849]:** to stop the container

**-> docker start container\_id\[357894875849]:** to start the container again

-> docker ps -a: list of previously running or stooped cont..(so we can restart it)

**-> pull + run:** for a already installed image docker run redis:4.0.3



###### Q) which container will work when 2 Redis is running?



both running in same port of 6379/tcp: so high chance of port conflict-> \[Container Port] vs \[Host Port]



--> so when we use multiple containers with same host our laptop/system will assign the available port for the first one, but for running the second one it will conflict with host with same port.



Now, we have to bind them with two diff ports from host machine (3000, 3001)-both listening to port 3000 >> someapp://localhost:3001



-> docker run -p\[laptops port]:\[container port] image\_name

-> docker run -p6000:6379 redis

-> Binding will show in when we check next time->0.0.0.0:6000->6379/tcp

-> docker run-p6000:6379 redis-d (in detached mode]

-> docker ps -> will slow all containers with all ports/portBindings



>> if we try to run the second Redis container with the already used port.. it will throw error\[use 6001] and run in detached mode



##### **CONTAINER DEBBUNG:**



-> **To check logs of a running container:** docker logs \[container\_id]



// also we can use the container name in place of id.

// name will be random, so better to give name while using docker run command(stop that container and run again).



-> docker run -d-p6001:6379-name redis\_Old redis:4.0.2

-> docker run -d-p6000:6379 -name redis \_Latest redis



::want to go inside a container and see the log files, config files, env variables



-> docker exec -it \[container id] /bin/bash (this will login inside the container with root user access) \[container name) can also been used in place of id.

-> it: stands for Interactive terminal

-> ls, cd, pwd we can use and check all the folders inside root

--> if we type ENV we can see environment config details for the image, --for exit type exit in prompt line.



###### **How Docker integrates into the steps of CI/CD?**



A JS application using Mongo Db database setup in local machine. the MongoDb image is downloaded from docker hub. To deploy, suppose we commit the application using git(version control)-> it will integrate a Continuous integration in Jenkins build Jenkins build will produce artifacts from our application (Build the JS Application first and built an docker image out of that).



Now it will get pushed(the image) to a private repo: now the docker image has been deployed in a development server(pulls the image from that private repo pulls the MongoDb image the app depends on).



###### **Docker in Software Development:**



-> In order to store the changes of a simple frontend+Node.js application changes need to connect with MongoDB image. \[docker container of mongo ui called Mongo Express, where we the do structure and changes we will make for our application].



-> To install MongoDb have to the localhost of mongo express image.

-> pull both images of mongoDB and mongo express -> create containers of those images.

-> now need to establish connection between mongo and mongo express.



**DOCKER NETWORK:** while hosting all the package and containers in a Isolated Docker Network(where the containers are actually running in) -> when two same container (MongoDb \& Mongo Express UI) deployed in sane docker network they can talk to each other with container name (without any port no./localhost).



::node server(node.js) will connect to this docker network with localhost and port number(this is application backend).

:: By default docker provide some network -> **docker network ls**(already existing on's will show).



1\) docker network create mongo-network (for mongo db and mongo express) - after this we can see the network in list. Now we need to add both the Container inside the network.



**Command for port setup:** docker run -p 27917:27017 -d -e (env variable name from docker documentation -username=admin) -e (variable name for password=password) --name mongodb --net mongo-network mongo



MONGO\_INITDB\_ROOT\_USERNAME 
MONGO\_INITDB\_ROOT\_PASSWORD



--> docker run -p 27017:27017 -d -e MONGO\_INITDB\_ROOT\_USERNAME=admin -e MONGO INITOB\_ROOT PASSWORD=password --name mongodb --net mongo-network mongo



2\) Now mongo express need to be connected within this network: need to configure



--> (Need admin username \& password variables as we override them in prev context):as express need some admin name and password to authenticate inside the network



:docker run -p 8081:8081 -d -e ME\_CONFIG\_MONGODB\_ADMINUSERNAME=admin -e ME\_CONFIG\_MONGODB\_ADMINPASSWORD=password --name mongo-express --net mongo-network mongo



3\) connect the database with node.js application. A server.js file need to create to connect the application with mongodb port:27017 (and also database we can create a new one and add to code, so the data will store in that DB only).



4\) we can see Db logs for each change and there is a start and end connection in the code.



###### **Docker Compose:**



It will make easy while the configuration is about to run multiple docker containers and their configuration.



***docker run command vs mongo-docker-compose.yaml***



###### version: "3"

###### services:

###### &nbsp; mongodb:

###### &nbsp;   image: mongo

###### 

###### &nbsp; mongo-express:

###### &nbsp;   image: mongo-express

###### &nbsp;   ports:

###### &nbsp;     - "8081:8081"

###### &nbsp;   environment:

###### &nbsp;     ME\_CONFIG\_MONGODB\_ADMINUSERNAME: admin

###### &nbsp;     ME\_CONFIG\_MONGODB\_ADMINPASSWORD: password



--Docker compose will take care of creating a common Network, (.yaml file)

--way to use this file: **docker-compose -f file\_name.yaml up**

--this create a network "myapp default" -> then create 2 docker containers -> then connection established between both.



\*\*while restart a container the old data/setup for the application previously will lost. (no persistant....specially problem for db containers)



**To stop: docker-compose of file\_name.yaml down**



###### BUILD OWN IMAGE FROM NODE.JS APPLICATION:



In general JENKINS will build the app.js + mongo\_db image --> simulate it into GIT(commit) --> after this a continuous integration has to run \[Jenkins].



**How Jenkins simulate it into a docker image and push it to Docker Repo:** On the local environment, in order to make docker image from a application we have to copy the replications/artifacts (jar, war, bundle.js):: into a Docker File( A blueprint).



DOCKERFILE:



-> starting will be always based on another image\[base image/prev version img], here its a js app with node is backend, so: FROM node 

-> declaring: ENV variables at the lest of the docker file.

ENV ME\_CONFIG\_MONGODB ADMINUSERNAME=admin\\ ME\_CONFIG\_MONGODB\_ADMINPASSWORD-password 



-> after this RUN mkdir -p /home/app (note: this directory will create inside the container)

-> Copy parameter is executing in host machine: COPY ./home/app (so i can copy the files inside of my host and paste inside in the container img)

-> CMD \["node", "server.js"] (start the app with: "node server.js"> copy the server.js file inside the container)



\*\*Multiple run commands are possible, but CMD is only one as its a entry point\*\* --> save always with ways with name "Dockerfile"



\*\*If every container we are creating has its own dockerfile for image, then every container in dockerhub should have. -> yes its there\*\*



COMMAND: docker build -t img\_name:version location\_to\_build\_a\_docker\_file

&nbsp;        docker build - my-app:1.0 .  {. here in the same dir -> current dir} 



\[Successfully built message with an id].

-> Run Docker image to see the image created



::Now we have to push this docker file with code into GIT, then Jenkins come into picture, it will build a docker image based on the docker file. Now the image need to be deployed into Server or anyone other person wants to download the image locally, Jenkins will push it to docker Repo.



###### start "my-app" container to verify: app starts successfully? app env is configured correctly?



docker run my-app:1.0 ==>(when we are adjusting a DockerFile, need to rebuild the image - cause the old image cannot be overridden)



delete the old image:

1\) First delete container, as its running there: docker rm container\_id

2\) second delete the image: docker eni image 

3\) Rebuild: docker build t my-app:1.0

4\) run it: docker run my-app



**To understand some more details: going inside the terminal of the container**



-> docker exec it container\_id /bin/bash (if bin/bash not worked /bin/sh)

&nbsp;  : now we are in root directory

&nbsp;  : some checks: env var check, /home/app dir check, files are copied properly or not. (for leaving terminal -> exit) 





##### **Docker Registry: (with amazon ECR)**



-> Its a docker private repo.

-> registry options

-> built and tag an image 

-> docker login \& push



1\) Create Private Docker repo on AWS: (also called docker registry) will use: ECR: get start -> give repo name (as application name)



{In this repo we can save diff tag/versions of the same image for diff images diff repo's are required for diff applications}



**\[In order to push the image what we have locally]:** we have login into the private repo (select the repo in AWS and top left login commands will be visible according to OS). if docker image is built and pushed from a Jenkins server, then have to give jenkins credentials to login into the repo.



(1) Docker login: this command is the main command and it will work behind the aws command: $(aws ecr get-login --no-include-email region eu-central-1)

&nbsp;   :: pre-requisite to run this command

&nbsp;               a) AMS CLI needs to be installed.

&nbsp;               b) credentials configured.



(2) built the image again. {as explained previously}



(3) after this we need to tag our builded image, so that we can push the image to repo.



**command:**

**docker tag my-app:latest (the image i want to rename) aws\_own\_id\_for\_this\_repo/my-app:latest (name of the image with image\_id]**

-- The above command is based on Image Naming in docker registries. 

-- Naming convention in docker registry: registryDomain/imageName: tag



\[But wondering that while pulling an image from dockerHub is not that much complex command - e.g. mongo4.2 -> means no need to specify registryDomain]. **Whats exactly executed in background: docker.io/library/monge:4.2**



(4) In AWS ECR(private registry -- no default config):



docker pull 520697801743.dkr.ecr......aws.com/my-app1.0 (this is the full registry domain)



-- In order to push the image into AWS with the name my-app, we need to tell docker: -> that's why docker tag is required to tag the image => it will rename the image now the 

-- renamed identical image will be pushed: docker push aws\_own\_id\_for\_this\_repo/my-app:latest (same as tag command)



-->\[layers will be pushed one after another]



(5) if change in code, we need to built the image again and we can push the version with a new version(1.1) (if its done from Jenkins then Jenkins need to push the image after tagging it).



Now, i want to run this my-app image pulling it from **aws private repo** and mongo images pulled from **docker hub**



:: Now the docker compose file which we created, so we need all the containers inside this file to start the app in development server we need to add the my-app container\[image will be in full details.....else docker will think our Image resides in docker-hub).



::next thing is the compose file has to pull the image from a docker repo, so here development server has to pull the image from the repo. what we need to do is the docker login from the server to pull the image. --> this docker-compose file would be used on the server to deploy all the apps/services.



:: so simulate in development server: the docker compose file need to be into the server



--> create a new mongo.yaml {open editor mode:copy the prev file:save}

--> docker-compose -f mongo.yaml up (all 3 app/images will start)



\*\***In the server file we can change the connect line with @container\_name, no need to write localhost:port number.**



###### Docker Volumes



-> when do we need Docker Volumes? :for data persistence



-> Use\_case: normally when a container is hosted on a host any change in virtual file system, while restarting/removing the container will restart the DB. \[prev updates are gone].



**How to preserve old changes in DB while re-create a container:**



a) In container: a physical file system will also be present. /home/mount/data (host file-system) ...............so a dir/folder in a physical host file system is mounted into the virtual file system of Docker.



b) Three types of docker volumes: diff way of creating them. creating with using docker run command.



:: **docker run -v /home/mount/data:/var/lib/mysql/data** (host directory:container directory). This is called HOST Volumes.



:: only specifying container directory -> **docker run -v /var/lib/mysql/data** {for each container a folder is generated that gets mounted automatically from docker}, called Anonymous Volumes



:: modified of anonymous volumes -> docker run -v name:/var/lib/mysql/data {random name by user}. Called Named Volumes(this type of volumes are used most)



c) So in Docker compose file under services; define port then type

&nbsp;  volumes:

&nbsp;      db-data:/var/lib/mysql/data

\*\*so have to create diff volumes for separate container\*\*



Dockers Volume Demo:

###### version: '3'

###### 

###### services:

###### &nbsp; my-app:

###### &nbsp;   # Uncomment and fix the image line if needed

###### &nbsp;   # image: ${docker-registry}/my-app:1.0

###### &nbsp;   # ports:

###### &nbsp;   #   - "3000:3000"

###### 

###### &nbsp; mongodb:

###### &nbsp;   image: mongo

###### &nbsp;   ports:

###### &nbsp;     - "27017:27017"

###### &nbsp;   environment:

###### &nbsp;     MONGO\_INITDB\_ROOT\_USERNAME: admin

###### &nbsp;     MONGO\_INITDB\_ROOT\_PASSWORD: password

###### &nbsp;   volumes:

###### &nbsp;     - mongo-data:/data/db

###### 

###### &nbsp; mongo-express:

###### &nbsp;   image: mongo-express

###### &nbsp;   ports:

###### &nbsp;     - "3080:8081"

###### &nbsp;   environment:

###### &nbsp;     ME\_CONFIG\_MONGODB\_ADMINUSERNAME: admin

###### &nbsp;     ME\_CONFIG\_MONGODB\_ADMINPASSWORD: password

###### &nbsp;     ME\_CONFIG\_MONGODB\_SERVER: mongodb

###### 

###### volumes:

###### &nbsp; mongo-data:

###### &nbsp;   driver: local



->for mysql:var/lib/mysql

->for postgres:var/lib/postgresql/data

-> So now if we restart the container again after stopping both the DB and collection will be preserved



###### Docker Volumes Locations by Operating System:



Windows:

C:\\ProgramData\\docker\\volumes



Linux:
/var/lib/docker/volumes



-- example of specific volume path:

/var/lib/docker/volumes/cfc1ab..ddf22/\_data



macOS:
/var/lib/docker/volumes












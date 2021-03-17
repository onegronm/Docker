## Building the App's Container Image

1. Create a file named Dockerfile with the following contents.
```shell
FROM node:12-alpine
WORKDIR /app
COPY . .
RUN yarn install --production
CMD ["node", "/app/src/index.js"]
```

2. Build the container image using the docker build command.
```shell
docker build -t getting-started .
```

## Starting an App Container
```shell
docker run -dp 3000:3000 getting-started
```

## Updating the App's Container Image
```shell
docker build -t getting-started .
```

## Replacing our Old Container
1. Get the ID of the container by using the docker ps command.
```shell 
docker ps
```

2. Use the docker stop command to stop the container.
```shell
# Swap out <the-container-id> with the ID from docker ps
docker stop <the-container-id>
```

3. Once the container has stopped, you can remove it by using the docker rm command.
```shell 
docker rm <the-container-id>
```

4. Now, start your updated container.
```shell 
docker run -dp 3000:3000 getting-started
```

## Sharing the App
1. Create the repository on Docker Hub.

2. Login.
```shell
docker login -u YOUR-USER-NAME
```

3. Use the docker tag command to give the getting-started image a new name. Be sure to swap out YOUR-USER-NAME with your Docker ID.
```shell
docker tag getting-started YOUR-USER-NAME/getting-started
```

4. Run the push command you see on Docker Hub.
```shell
docker push YOUR-USER-NAME/getting-started
```

## Container Volumes
1. Create a volume by using the docker volume create command.
```shell
docker volume create todo-db
```

2. Start the todo container, but add the -v flag to specify a volume mount. We will use the named volume and mount it to /etc/todos, which will capture all files created at the path.
```shell
docker run -dp 3000:3000 -v todo-db:/etc/todos getting-started
```

3. Once the container starts up, open the app and add a few items to your todo list.

4. Remove the container for the todo app. Use ```docker ps``` to get the ID and then remove it with
```shell
docker rm -f <id>
```

5. Use the ```docker volume inspect <the-volume-id>``` command to find where is Docker actually storing the data. The Mountpoint is the actual location on the disk where the data is stored. While running in Docker Desktop, the Docker commands are actually running inside a small VM on your machine. If you wanted to look at the actual contents of the Mountpoint directory, you would need to first get inside of the VM.

6. Restart the container and notice how the data was persisted.
```shell
docker run -dp 3000:3000 -v todo-db:/etc/todos getting-started
```

## Bind Mounts
1. Use the -v flag to add a bind mount. When adding a bind mount to a docker run command, you can use the shortcut $(pwd), (or ${pwd} depending on your shell). It runs the shell command to print the current working directory, to avoid having to type out the entirety of your directory location.
```shell
docker run -dp 3000:3000 \
    -w /app -v ${PWD}:/app \
    node:12-alpine \
    sh -c "yarn install && yarn run dev"
```

2. Use ```docker inspect <container-id>``` to verify that the bind mount was created correctly. Look for the Mounts section.

## Docker Logs
```shell
docker logs -f <container-id>
```

## Container Networking
If two containers are on the same network, they can talk to each other. If they aren't, they can't.

1. Create the network.
```shell
docker network create todo-app
```

2. Start a MySQL container and attach it the network.
```shell
docker run -d 
    --network todo-app --network-alias mysql
    -v todo-mysql-data:/var/lib/mysql
    -e MYSQL_ROOT_PASSWORD=secret
    -e MYSQL_DATABASE=todos
    mysql:5.7
```

3. To confirm we have the database up and running, connect to the database and verify it connects.
```shell
docker exec -it <mysql-container-id> mysql -p
```

4. Connect the container to our app network.
```shell
docker run -dp 3000:3000 \
  -w /app -v ${PWD}:/app \
  --network todo-app \
  -e MYSQL_HOST=mysql \
  -e MYSQL_USER=root \
  -e MYSQL_PASSWORD=secret \
  -e MYSQL_DB=todos \
  node:12-alpine \
  sh -c "yarn install && yarn run dev"
```

5. Connect to the mysql database and prove that the items are being written to the database. Remember, the password is secret.
```docker exec -ti <mysql-container-id> mysql -p todos```



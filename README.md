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







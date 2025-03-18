## Steps for a node.js application 
1. Create a *Dockerfile*, e.g.:
```docker
FROM node:16-alpine -> base image

WORKDIR /app -> working directory by default

COPY package.json . -> copy the mentioned file to mentioned location

RUN npm install -> run the command

COPY . . -> copy everything to the mentioned location

EXPOSE 8080 -> expose this port by default (if manual port mapping is not done, this is mapped by default when used -P)

CMD ["npm", "start"] -> command ran when everything is done
```
2. Build an image
`docker build -t <tag_for_image> <location_of_dockerfile(if same directory, give '.')>`
3. Run container from this image
`docker run -it <image_name>`
4. Use `docker login` to login with dockerhub account
5. Create a repository on dockerhub
6. Tag your image to the repository name to avoid naming conflicts on dockerhub
`docker tag <local_image_tag> <repository_name>`
7. Push the image to dockerhub
`docker push <image_name>`

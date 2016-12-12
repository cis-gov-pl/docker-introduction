# Hands On Lab 2
![dockerfile](images/dockerfile.png)
## Building a image

!SUB
## Dockerfile

* Dockerfile is a script describing how to create an image
* Each command results in a layer of the image
* Behaves a bit similarly to a Makefile
  * Tries to use existing layers if nothing changed
* Key to automatic creation of images
* Docs: [Dockerfile reference](https://docs.docker.com/engine/reference/builder/)

!SUB
## Dockerfile by example
```
# Base image
FROM python:3
MAINTAINER Konrad Klimaszewski <konrad.klimaszewski@ncbj.gov.pl>

# Copy to the image pip requirements file
COPY requirements.txt /usr/src/app/
WORKDIR /usr/src/app

# Install dependencies (notice --no-cache-dir)
RUN pip install --no-cache-dir -r requirements.txt

# Copy the rest of the application
COPY . /usr/src/app

# Tell the port number the container should expose
EXPOSE 5000

# Run the command
CMD ["python", "./app.py"]
```

!SUB
### Build docker image
* The Dockerfile and the static web app are already prepared:

```
cd /opt/docker-introduction-data/flask-app
ls
```
* Building the image based on a parent image will create only the diff image.
  * For building the image, you should specify a repository and tag.
  * Specify as repository "local/static-cats" and leave the tag empty.

```
docker build --tag local/static-cats .
```
* Check the result

```
docker images
```

!SUB
### Run the image
* Run new web app instance using `local/static-cats` image
  * Publish port 80 as 8888

```
docker run -d --name cats -p 8888:5000 local/static-cats
```
* Access your web page

```
firefox http://<hostname>:8888 &
```

!SUB
![static](images/catgif.png)


!SUB
### More advanced example

#### Part 1

```
# Use nodejs as base image
FROM node:4.3
MAINTAINER Konrad Klimaszewski <konrad.klimaszewski@ncbj.gov.pl>

# Add local user
RUN groupadd -r flask && useradd -r -g flask flask

## Run all commands in one chain: ##
## - reduces number of layers     ##
## - allows for cleanup step      ##
# Install system-wide deps for python and node
RUN apt-get update \
 && apt-get install -y --no-install-recommends \
      python-pip python-dev dnsutils \
 && rm -rf /var/lib/apt/lists/*

## Separate install of app dependencies from the app           ##
## - During rebuild after code change this step will be skiped ##
# Copy dependency lists
COPY flask-app/requirements.txt flask-app/package.json /opt/flask-app/
WORKDIR /opt/flask-app

# Install app specific dependencies
RUN npm install \
 && pip install -r requirements.txt
```

!SUB
### More advanced example

#### Part 2

```
# Copy our application code
COPY flask-app /opt/flask-app

# Build application (compactify json, css, etc)
RUN npm run build

## If possible drop as many privilidges as possible ##
# Run the application as normal user
USER flask

# expose port
EXPOSE 5000

# start app
CMD [ "python", "./app.py" ]
```

!SUB
### Build docker image for FoodTrucks
* The Dockerfile and the FoodTrucks web app are already prepared:

```
cd /opt/docker-introduction-data/food-trucks
ls
```
* Build the image

```
docker build --tag local/food-trucks .
```
* Check the result

```
docker images
```

!SUB
### Run the application
* Run new web app instance using `local/food-trucks` image
  * Publish the port 5000

```
docker run -d --name food-trucks -p 5000:5000 local/food-trucks
```
* Access your web page

```
firefox http://<hostname>:5000 &
```

!SUB
![static](images/food-trucks-empty.png)

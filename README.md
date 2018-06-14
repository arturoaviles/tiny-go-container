# Going from a Big Golang Docker Container to a Tiny One

## Creating the Golang program

```go
package main

import (
	"fmt"
)

func main() {
	fmt.Println("Hello World!")
}
```

File size: 
- 78 bytes or 0.000078 megabytes

Compiled file size:
- 2 megabytes

Just that!

## 1st Dockerfile (Complete Golang in an uknown OS)

The fastest way to deploy a Golang container is by creating an image like this:

```Dockerfile
FROM golang:1.10.0
WORKDIR /app
ADD . /app
RUN cd /app && go build -o goapp
# EXPOSE 8080 
ENTRYPOINT ./goapp
```
> I commented the EXPOSE beacuse my program doesn't need it, if you're creating a web app uncomment it!

This created a Container with a size of __782MB__!!! This is to much for just a tiny program. We can improve it! 

## 2nd Dockerfile (Using Alpine)

By using a smaller Linux Distribution like Alpine it looks like this:

```Dockerfile
FROM golang:alpine
WORKDIR /app
ADD . /app
RUN cd /app && go build -o goapp
# EXPOSE 8080
ENTRYPOINT ./goapp
```

The size went from __782MB__ to __378MB__. That's better but it still needs some improvements

## 3rd Dockerfile (Compiling it in a Dev Container and Run it in a fresh Prod Container)

To optimize the Dockerfile we are going to create a container with Golang, compile it and then copy just the compiled program to a new container:

```Dockerfile
FROM golang:alpine AS build-env
WORKDIR /app
ADD . /app
RUN cd /app && go build -o goapp

FROM alpine
RUN apk update && apk add ca-certificates && rm -rf /var/cache/apk/*
WORKDIR /app
COPY --from=build-env /app/goapp /app

# EXPOSE 8080
ENTRYPOINT ./goapp
```

## Final thoughts

The final size of the container went from __782MB__ to __378MB__ and finally to __6.71MB__

The program used __2MB__ and the final container uses __6.71MB__ so the container is using __4.71MB__

We minified the container and decreased the size a __99.14%__ 
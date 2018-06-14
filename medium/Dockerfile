FROM golang:alpine
WORKDIR /app
ADD . /app
RUN cd /app && go build -o goapp
# EXPOSE 8080
ENTRYPOINT ./goapp
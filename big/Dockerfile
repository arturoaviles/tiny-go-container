FROM golang:1.10.0
WORKDIR /app
ADD . /app
RUN cd /app && go build -o goapp
# EXPOSE 8080
ENTRYPOINT ./goapp
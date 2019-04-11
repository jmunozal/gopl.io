FROM golang:latest 
RUN mkdir /app 
ADD main.go /app/ 
WORKDIR /app 
RUN go build -o main . 
EXPOSE 8000
CMD ["/app/main"]

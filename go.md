# go

## init

```bash
# install
brew install go
go version

# init project
go mod init [repo_name]

# clear mod
go mod tidy
```

## env

```bash
# list env
go env

# set env
go env -w GOPATH=$HOME/go
export PATH=$PATH:$(go env GOPATH)/bin
export GOPATH=$(go env GOPATH)

# 无法安装依赖时
go env -w GOPROXY=https:#goproxy.io,direct
go env -w GOSUMDB="sum.golang.google.cn"
```

## basic

```bash
# init go.sum
go mod init my-repo

# list selected module versions
go list -m all

# install module
go get -u github.com/gin-gonic/gin

# run main
go run cmd/app/main.go
```

## test

```bash
go test
go test -v -run="<FUNCTION>"
go test ./internal/* -v
```

## crypto

```go
package main

import (
    "fmt"
    "golang.org/x/crypto/bcrypt"
    "encoding/base64"
)

func main() {
    encoded := base64.StdEncoding.EncodeToString([]byte("password"))
    fmt.Println(encoded)
    err := 	bcrypt.CompareHashAndPassword([]byte("hashed"), []byte(encoded))
    if err != nil {
        fmt.Println(err)
    }else{
        fmt.Println("OK")
    }
    pwd, err:= bcrypt.GenerateFromPassword([]byte("passwordbase64"), 10)
    fmt.Println(string(pwd))
}
```

## build

```bash
#
go tool dist list
```

```dockerfile
FROM golang:1.22 AS builder

WORKDIR /app
COPY . .

RUN go mod download

RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -a -installsuffix cgo -ldflags="-w -s" -o /app/bin/app ./cmd/app/main.go

FROM scratch
COPY --from=builder /app/bin/app /app/bin/app

CMD ["/app/bin/app"]

EXPOSE 3000
```

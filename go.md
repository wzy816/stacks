# go

## env

```go
// list env
go env

// set env
go env -w GOPATH=$HOME/go
export PATH=$PATH:$(go env GOPATH)/bin
export GOPATH=$(go env GOPATH)

// 无法安装依赖时
go env -w GOPROXY=https://goproxy.io,direct
go env -w GOSUMDB="sum.golang.google.cn"
```

## basic

```go
// init go.sum
go mod init my-repo

// list selected module versions
go list -m all

// install module
go get -u github.com/gin-gonic/gin
```

## test
```go
// run test
go test

// run 
go test -v -run="<FUNCTION>"
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

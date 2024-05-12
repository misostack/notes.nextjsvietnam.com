# Build simple, secure, scalable systems with Go

## Facts

- An open-source programming language supported by Google
- Easy to learn and great for teams
- Built-in concurrency and a robust standard library
- Large ecosystem of partners, communities, and tools

## Resources

- [Official Link](https://go.dev/)

## Getting start

### Install

- Current version: **1.21.6**

### Set GOPATH

```sh
# ~/.bashrc
export GOPATH=/c/workplace/001_projects/gobase
export PATH=$PATH:$GOROOT/bin:$GOPATH/bin
```

### Run

```sh
go mod init
go run ./main.go
go build
./go2024

# run when your main file import local file in the same package
go run *.go
# or
go run .
```

### Resources

- [Go Packages](https://pkg.go.dev/)
- [Git ignore template](https://github.com/github/gitignore/blob/main/Go.gitignore)

### Samples

- [Golang Tutorial 2024](https://github.com/misostack/go2024)
- [Go by examples](https://gobyexample.com/)
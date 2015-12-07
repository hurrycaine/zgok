# Zgok
======

[![GoDoc](https://godoc.org/github.com/srtkkou/zgok)]

  Zgok is a simple utility to embed any static file
into the binary executable file for [Go](http://golang.org/).

## Features

* It can create a single binary file containing all static files. Very easy to deploy.
* No dependency. Available both on Windows and Linux.

## Installation

  Use the following to install the library and command line program:

	go get -u github.com/srtkkou/zgok/...

## Usage

  Use the following command to build the zgok executable file.

	$GOPATH/bin/zgok build -e exePath -z zipPath1 -z zipPath2 -o outPath

  If you want to read the embedded file in the code, you can do like the
following.

```go
package main

import (
	"fmt"
	"github.com/srtkkou/zgok"
	"io/ioutil"
	"os"
)

func main() {
	var content []byte
	path := "test.txt"
	zfs, _ := zgok.RestoreFileSystem(os.Args[0])
	if zfs != nil {
		// For release.
		content, _ = zfs.ReadFile(path)
	} else {
		// For development.
		content, _ = ioutil.ReadFile(path)
	}
	fmt.Println(string(content))
}
```

  If you want to serve zgok embedded files as static files in the
web application, you can do like the following.
  Note: Assuming that static assets are stored in [./web/public].

1. This is the minimal example to use the zgok in web application.

```go
package main

import (
	"net/http"
	"github.com/srtkkou/zgok"
	"os"
)

func main() {
	zfs, err := zgok.RestoreFileSystem(os.Args[0])
	if err != nil {
		panic(err)
	}
	assetServer := zfs.FileServer("web/public")
	http.Handle("/assets/", http.StripPrefix("/assets/", assetServer))
	http.ListenAndServe(":8080", nil)
}
```

2. Build the zgok executable file.

	go build -o web web.go
	$GOPATH/bin/zgok build -e web -z web/public -o web_all

3. Access the URL like [http://localhost:8080/assets/css/sample.css] on browser.

## License

Apache License Version 2.0. See the LICENSE file for details.

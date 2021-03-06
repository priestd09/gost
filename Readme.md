# Gost

[![Build Status](https://travis-ci.org/usmanhalalit/gost.svg?branch=master)](https://travis-ci.org/usmanhalalit/gost)
[![Coverage Status](https://coveralls.io/repos/github/usmanhalalit/gost/badge.svg?branch=master)](https://coveralls.io/github/usmanhalalit/gost?branch=master)
[![Go Report Card](https://goreportcard.com/badge/github.com/usmanhalalit/gost)](https://goreportcard.com/report/github.com/usmanhalalit/gost)

Filesystem abstraction layer for Golang, that works with Local file system 
and Amazon S3 with a unified API. You can even copy-paste files from different sources.
FTP, Dropbox etc. will follow soon.


#### Quick Example

```go
import "github.com/usmanhalalit/gost/s3"

// Initialize a filesystem
fs, err := s3.New(s3.Config{ your-aws-credentials })

// Read
note, err := fs.File("my-note.txt").ReadString()
//Write
err := fs.File("another-note.txt").WriteString("another note")

// Traverse naturally
movies := fs.Directory("movies")
files := movies.Files()
movies.File("Pirated-movie.mp4").Delete()

// Copy file from one source to another
localFile := lfs.File("photo.jpg")
s3Dir := fs.Directory("photos")
err := localFile.CopyTo(s3dir)
```


## Initialize

Get the library:
```
go get github.com/usmanhalalit/gost
``` 

You just initialize the S3 and Local adapters differently, **everything else in the API is same**.

#### Amazon S3

```
import "github.com/usmanhalalit/gost/s3"

fs, err := s3.New(s3.Config{
	ID: "aws-id",
	Key: "aws-key",
	Region: "es-west-1",
	Bucket: "your-bucket",
})
```

#### Local
```go
import "github.com/usmanhalalit/gost/local"

fs, err := local.New(local.Config{
	BasePath: "/home/user",
})
```

## Read and Write

#### Read
Simple read, suitable for small files.

```go
fileContent, err := fs.File("test.txt").ReadString()
```

Bytes read, compatible with `io.Reader`, so you can do buffered read.
```go
b := make([]byte, 3)
n, err := fs.File("test.txt").Read(b)
```

#### Write
Simple write
```go
fs.File("test.txt").WriteString("sample content")
```

Bytes write
```go
n, err := file.Write(bytes)
// n == number of bytes written
```

## Traversing

You can explore the filesystem like you in your desktop file explorer.
File and directories are chained in a natural way. 

```go
dirs, err := fs.Directory("Parent").Directory("Child").Directories()
files, err := fs.Directory("Parent").Directory("Child").Files()
```

```go
dirs, err := fs.Directory("Parent").Directory("Child").Files()
```

## Listing

Get all files and loop through them
```go
files, err := fs.Files()
for _, file := range files {
    fmt.Println(file.ReadString())
}
```

Get all directories and loop through them
```go
dirs, err := fs.Directories()
for _, dir := range dirs {
    files := dir.Files()
    fmt.Println(files)
}
```

Get the directory which contains a file
```go
dir := fs.File("test.txt").Directory()
```

## Stat

Get file size and last modified timestamp:

```go
stat, _ := fs.File("test.txt").Stat()
fmt.Println(stat.Size)
fmt.Println(stat.LastModified)
```

You can get stat of directories too, but it's not available on S3.

```go
fs.Directory("Downloads").File("test.txt").GetPath()
```


## Create and Delete
Delete a file and directory:
```go
fs.File("test.txt").Delete()
// Delete an entire directory, beware please!
fs.Directory("Images").Delete()
```

Create a new directory:
```go
fs.Directory("Images").Create()
```

To create a new file simply write something to it:
```go
fs.File("non_existent_file").WriteString("")
```  

## Copy and Paste Between Different Sources

You can copy a file to any Directory, be it in in the same filesystem or not(local or S3)

```go
localFile := lfs.File("photo.jpg")
s3Dir := s3fs.Directory("photos")
err := localFile.CopyTo(s3dir)
``` 

Fun, eh? 

You can optionally provide a new filename too:
```go
err := localFile.CopyTo(anotherDir, "copied_file.jpg")
```

Also there is a helper to copy file in the same Directory:
```go
file.Copy("copied_file.jpg")
``` 
 

## Custom Adapter

Yes, you can write one and it'll be appreciated if you contribute back.
. `gost.go` file has all the interfaces defined. Basically you've to implement
`gost.File` and `gost.Directory` interfaces. Check the `local` adapter to get an idea.

## API Documentation

Please follow the Go Doc: [https://godoc.org/github.com/usmanhalalit/gost](https://godoc.org/github.com/usmanhalalit/gost)

Also check the `_test` files [here](https://github.com/usmanhalalit/gost/tree/master/local) to get more idea about the usage.


___
You can follow me on [Twitter](https://twitter.com/halalit_usman) 🙂


&copy; [Muhammad Usman](http://usman.it/). Licensed under MIT license.
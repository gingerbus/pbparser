[![GoDoc](https://godoc.org/github.com/tallstoat/pbparser?status.svg)](https://godoc.org/github.com/tallstoat/pbparser)
[![GoReportCard](https://goreportcard.com/badge/github.com/tallstoat/pbparser)](https://goreportcard.com/report/github.com/tallstoat/pbparser)

# pbparser

Pbparser is a library for parsing protocol buffer (".proto") files.

## Why?

Protocol buffers are a flexible and efficient mechanism for serializing structured data. 
The Protbuf compiler (protoc) is *the source of truth* when it comes to parsing proto files.
However since protoc (and the parser in it) is written in C++, it presents us with a few challenges :-

* Using it in any automation setup typically involves spawning a process for invoking it. This is not ideal as the output of the compiler in the case of a parse error is messages on stdout. That's not very conducive for building functionality which needs a standard defined output structure to work with.
* It does not return to the invoking process a parsed model of the proto file. So, it's not useful for another class of usecases where the caller needs a library so it can *use* the parsed model. 

This library is meant to address the above mentioned challenges.

## Installing

Using pbparser is easy. First, use `go get` to install the latest version of the library. 

```
go get -u github.com/tallstoat/pbparser
```

Next, include pbparser in your application code.

```go
import "github.com/tallstoat/pbparser"
```

## APIs

This library exposes two apis. Both the apis return a ProtoFile datastructure and a non-nil Error if there is an issue in the parse operation itself or the subsequent validations.

```go
func Parse(r io.Reader, p ImportModuleProvider) (ProtoFile, error)
```

The Parse() function expects the client code to provide a reader for the protobuf content and also a ImportModuleProvider which can be used to callback the client code for any imports in the protobuf content. If there are no imports, the client can choose to pass this as nil.

```go
func ParseFile(file string) (ProtoFile, error)
```

The ParseFile() function is a utility function which expects the client code to provide only the path of the protobuf file. If there are any imports in the protobuf file, the parser will look for them in the same directory where the protobuf file resides.

## Choosing an API

Clients should use the Parse() function if they are not comfortable with letting the pbparser library access the disk directly. This function should also be preferred if the imports in the protobuf file are accessible to the client code but the client code does not want to give pbparser direct access to them. In such cases, the client code has to construct a ImportModuleProvider instance and pass it to the library. This instance must know how to resolve a given "import" and provide a reader for it.  

On the other hand, Clients should use the ParseFile() function if all the imported files as well as the protobuf file are on disk relative to the directory in which the protobuf file resides and they are comfortable with letting the pbparser library access the disk directly.  

## Usage

Please refer to the [examples](https://godoc.org/github.com/tallstoat/pbparser#pkg-examples) for API usage.

## Issues

If you run into any issues or have enhancement suggestions, please create an issue [here](https://github.com/tallstoat/pbparser/issues).

## Contributing

1. Fork this repo.
2. Create your feature branch (`git checkout -b my-new-feature`).
3. Commit your changes (`git commit -am 'Add some feature'`).
4. Push to the branch (`git push origin my-new-feature`).
5. Create new Pull Request.

## License

Pbparser is released under the Apache 2.0 license. See [LICENSE](https://github.com/tallstoat/pbparser/blob/master/LICENSE)


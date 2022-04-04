Learning Go
===========


## Directory Structure ##

You home directory should look something like this:

	Desktop/
	Documents/
	Downloads/
	Work/
		bin/
		datacore/
		lib/
		spitfire/
		
	go/
	

package statement is first line of code
all files in the same directory have the same package statement
they must all be part of the same namespace


## *.go File Structre ##

	package _something_
	
	import _otherthing_
	
	func funcName(_arguments_) _returns_ {
	}

## Variables ##

Variables have a type

You can assign the type automatically with "short variable declaration' syntax
:=

## CLI ##

use package flag

## Compiler ##

	go run
	go build
	go install goes to $GOPATH/bin

## Docs ##

Use package comments with multi-line comment following KorfLab standard practice. The template is in here.

Use Doc comments for each exported/capitalized function


Learning Go
===========

## Assumptions ##

It is assumed you already know some Unix and Python and are familiar with
Conda.

## Installing Go ##

We use Conda to manage our golang distribution. To create a new environment for
go development start in base and then make a new enviornment:

	conda create --name godev
	conda activate godev
	mamba install -c conda-forge go

## $HOME/go ##

By default, go will create a `go` directory in your home directory with the
following directory structure.

	go/
		bin/
		pkg/

If you `go install` programs, they will be copied to `~/go/bin`. So it's a good
idea to have that in your `$PATH`. If you followed the KorfLab/setup, it
already is in your `$PATH`.

You will see some suggestions to put a `src` directory inside your `go`
directory and do development there, but we will be using our `Code` directory
as usual.

## Hello Go ##

Start by making a new directory in your `Code` directory.

	cd ~/Code
	mkdir hellogo

Initialize the module by providing its location. This creates a file called
`go.mod` whose contents are used for go administrative tasks. We will see more
a bit later.

	go mod init github.com/iankorf/hellogo

Now create a really simple hello world program called `helloworld.go`. Type it
in exactly as below (for reasons that will be explained later).

	// A program to demonstrate go
	package main
	import "fmt"
	func main() {
	fmt.Println("Hello, world")
	}

To run this program on the command line, type the following:

	go run helloworld.go

As you can see, this works sort of like a `python3 hello.py` except that you
have to tell `go` that you want to `run`. There are a bunch of other commands
you can use. For example, `build` creates an executable.

	go build

Now look at the directory. There is a new executable program called `hellogo`.
Note that the name of the program is the name of the project directory and not
the source file. The program you built is a standalone executable that can be
run by itself.

	./hellogo

What you might not appreciate until now is that `go run` did 3 things:

1. compiled the program
2. ran the program
3. deleted the program

When you do a `go build` it only performs step 1. To clean up your code
directory, for example, before doing a `git push`, use the `go clean` command.
Try that now.

	go clean

Your program, `hellogo` is now deleted. Most of the time when you are
developing software, you will probably use `go run`. Later, once you have a
working program that you want to deploy, you can `go install` it.

	go install

It doesn't look like anything happened. Hoever, your program was compiled and
copied to `~/go/bin`. Take a look.

	ls ~/go/bin

If your `$PATH` is set up properly, you will be able to type `hellogo` from
anywhere on your system.

	cd
	hellogo

If this doesn't work, please set up your environment properly.

Recall that your `helloworld.go` program looks rather ugly. There are no
vertical whitespaces or indentation. The go community is pretty rabid about
consistent style and provides `go fmt` to make sure your code follows that
style.

	cat helloworld.go
	go fmt
	cat helloworld.go

Note that the initial comment did not get separated from the `package`
statement. That's because comments that are directly in front of top-line
statements become documentation. Check it out.

	go doc

We will explore the documentation system more later.

## Go commands ##

Frequently used

+ `go run prog.go`: compile and run your program

Sometimes used

+ `go install`: compile and install program in `~/go/bin`
+ `go fmt`: reformat code before `git push`
+ `go doc`: get documentation

Infrequently used

+ `go mod init`: create a new `mod.go` file
+ `go mod tidy`: update your `mod.go` file

Not really needed

+ `go build` compile executable
+ `go clean` remove executable


## Language Basics ##

### Variables ###

### Conditonals and Loops ###

### Functions ###

### Arrays and Maps ###

### File I/O ###

### CLI ###

flag

### Standard Libraries ###

### Other Libraries ###


Learning Go
===========

## Assumptions ##

It is assumed you already know some Unix and Python and are familiar with
Conda.

## Installing Go ##

We use Conda to manage our golang distribution. To create a new environment for
Go development start in base and then make a new enviornment:

	conda create --name godev
	conda activate godev
	mamba install -c conda-forge go

## $HOME/go ##

By default, Go will create a `go` directory in your home directory with the
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
`go.mod` whose contents are used for Go administrative tasks. We will see more
a bit later.

	go mod init github.com/iankorf/hellogo

Now create a really simple hello world program called `helloworld.go`. Type it
in exactly as below (for reasons that will be explained later).

```
// A program to demonstrate go
package main
import "fmt"
func main() {
fmt.Println("Hello, world")
}
```

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
statements are special: they are documentation. Here's a taste of that.

	go doc

## Go commands ##

Frequently used

+ `go run prog.go`: compile and run your program

Sometimes used

+ `go doc`: get documentation
+ `go fmt`: reformat code before `git push`
+ `go install`: compile and install program in `~/go/bin`
+ `go test`: run your unit tests

Infrequently used

+ `go mod init`: create a new `mod.go` file
+ `go mod tidy`: update your `mod.go` file

Not usually needed

+ `go build` compile executable
+ `go clean` remove executable

## Language Basics ##

### Operators ###

The Go operators are mostly the same as Python. Like many languages, Go offers
`++` and `--` for increasing or decreasing by one. Very unlike Python, Go uses
`*` and `&`, like C for pointers and references. More on this later. There is
also a shortcut assignment operator `:=`.

### Varible Scope ###

Unlike Python, which uses function scope, Go uses lexical scope. Thank god.

### Simple Variables ###

Unlike Python, Go variables are all typed. When you create a variable, you must
also assign its type. There are a lot of flavors of integers based on their
size, and whether or not they are signed or not. Some of these have convenient
names like `byte` (which is like the `char` type in C) and `rune` (which is
used for unicode). There are also two flavors of floats. Most of the time, you
will simply use `int` for integers and `float64` for floats.

```
var b bool          // boolean
var c byte          // unsigned 8-bit integer
var r rune          // 32-bit integer
var i int           // integer
var f float64       // floating point
var s string        // string
fmt.Println(b, c, r, i, f, s)
```

Variables that aren't assigned an initial value, like those above, are
automatically set to something zero-ish (strings are empty, bools are false).

Although the code above shows the use of `var` to declare variables, this is an
uncommon usage. Most of the time we use the `:=` operator to assign a variable
both a type and value. This variable type is generally implicit from the
assignment.

```
j := 1               // int
x := 1.0             // float64
t := "hello world"   // string
```

Variables can be created and assigned in a list context. Since functions can
return multiple values (see below), this syntax is quite common.

```
k, v := "key", 1.0   // string, float64
```

Variables can be declared as a constant (read-only). Here, you use the `=`
operator even though you are doing assignment and declaration at the same time.

```
const AUTHOR = "Ian"
const LINE = 80
```

Strings in Go are immutable, just like in Python. However, you can make
reassignments that sort of look like you are making changes.

```
var s string // declare an empty string
s += "A"
s += "C"
fmt.Println(s)
```

Go has a slice operator very much like Python. You can use blanks for implicit
start and end, but unlike Python you cannot use negative indices or indicies
that are out of bounds.

```
seq := "ACGT"
fmt.Println(seq[0:])   // ACGT
fmt.Println(seq[0:1])  // A
fmt.Println(seq[0])    // 65 - fmt.Println() does ASCII with singles
```

### Conditonals ###

There is no `elif` in Go. Go if-else syntax is mostly like C.

```
if condition1 {
	stuff1
} else if condition2 {
	stuff2
} else {
	whatever
}
```

Unlike Python, Go has a `switch` statement. The Go `switch` is a little
different from `C` in that you don't need `break` statements because there is
no fall-through. Use it if you like, but there's nothing wrong with chaining
if-else.

```
switch j {
case 0:
	fmt.Println("zero")
case 1:
	fmt.Println("one")
default:
	fmt.Println("other")
}
```

### Loops ###

The Go `for` loop has a few different forms.

```
seq := "ACGT"

// C-like syntax
for i := 0; i < len(seq); i++ {
	fmt.Println(i, seq[i])
}

// Python-like syntax for containers
for i, v := range(seq) {
	fmt.Println(i, v)
}

// while-like syntax
i := 0
for i < 3 {
	fmt.Println(i)
	i++
}

for {} // endless loop
```

### Arrays and Slices ###

Both Python and Go have fixed-length arrays that we never use, so let's ignore
them. In Python, dynamic arrays are called _lists_ and in Go they are called
_slices_. I find it confusing that the word "slice" is used both for an
operator and a dynamic array. As a result, I'm just going to use "array" to
mean slices.

Arrays can be created with several different syntaxes.

```
var a []int            // empty array with initial size 0
b := make([]int, 0)    // empty array with initial size 0
c := make([]int, 3)    // array containing 3 zeroes
d := make([]int, 3, 4) // as above, but a hard limit of 4 values
e := []int{1, 2, 3}    // array containging [1, 2, 3]
```

Even though arrays (ok, actually slices) are dynamic, you can set the capacity
as shown in `d` above. All of the other arrays can grow infinitely. If you want
to append to an array, you use the `append()` function. It looks a little
different from Python for reasons that will become apparent later.

```
a = append(a, 4)
```

As you might expect, there is a `strings.Join()` to turn arrays into strings
and a `strings.Split()` to turn strings into arrays.

### Maps ###

Maps are like Python dictionaries or Perl hashes. I don't know why there is the
`make()` syntax when the composite literal is easier.

```
m := make(map[string]int, 0) // empty map
n := map[string]int{}        // empty map
o := map[string]bool{        // initialized map
	"go": true,
	"py": false,
}
```

Like Perl, but not Python, map keys have automagic assignment. For example,
imagine you are counting k-mers:

```
seq := "ACGATATCGGATATCT"
count := map[string]int{}
k := 3
for i := 0; i < len(seq) -k +1; i++ {
	kmer := string(seq[i:i+k])
	count[kmer] += 1    // assumes existence of key with value of 0
}
```

Unlike modern versions of Python, keys come out in random-ish order.

```
for key, val := range(count) {
	fmt.Println(key, val)
}
```

To delete keys, there is a `delete()` function.

### Functions ###

Go function declaration is a little different from Python because you have to
state the return value. In the example below, the `max()` function takes an
array of integers as input and returns an integer as output. Note the use of
the throwaway variable `_` in the `range()` function.

```
func max(vals []int) int {
	m := vals[0]
	for _, v := range(vals[1:]) {
		if v > m {
			m = v
		}
	}
	return m
}
```

Go functions can also return multiple values. Here, you have to put parentheses
around the multiple return values in the function declaration (but not in the
return).

```
func minmax(vals []int) (int, int) {
	min := vals[0]
	max := vals[0]
	for _, v := range(vals[1:]) {
		if v > max {
			max = v
		}
		if v < min {
			min = v
		}
	}
	return min, max
}
```

Unlike Python, you cannot mix positional and named arguments. In Go, there are
only positional arguments. If you have a function with really complex inputs
and outputs, pass structs. If you want to pass a variable number of arguments,
like `fmt.Println()`, you _can_ make a variadic fuction, but _should_ you?
Given that it's not explained here... probably not.

Closures...


### File I/O ###

unfinished

## Pointers and Reference ###

unfinished


### CLI ###

unfinished

### OOP: Structs and Methods ###

Go's version of object-oriented programming is based on structs and methods.
The Go struct is very much like C except that you don't have to typedef it to
give it a proper name.

```
type fasta struct {
	Def string    // definition line
	Seq string    // letters of sequence
}
```

Every struct should have a constructor that returns a pointer to the struct.
The name of the constructor should begin with "new" and then follow with a
capital letter version of the struct name as shown in `newFasta()` below. You
don't have to write destructors because Go does garbage collection for you.

```
func newFasta(d string, s string) *fasta {
	p := fasta{Def: d, Seq: s}
	return &p
}
```

Many of your structs will be defined in libraries. In order for other packages
to read your struct properties, their names must begin with a captial letter.
This is a bit different from every other language. If you want to keep
something private, start it with a lowercase letter.

```
type fasta struct {
	Def string    // exported definition
	Seq string    // exported sequence
	index int     // some private indexing thing
}
```

OOP-style function calls are supported by methods whose _receiver type_ matches
the pointer-to-struct type. Let's say we want to provide a pretty-printer for
our fasta struct.

```
func (fa *fasta) pretty() {
    fmt.Print(">")
    fmt.Println(fa.Def)
    fmt.Println(fa.Seq)
}
```

We can now use the convenient object syntax.

```
s := newFasta("Ian", "ATAGCGAAT")
s.pretty()
```

Now that you know about methods, you may be thinking that you should provide
getters and setters to encapsulate your data. Nah, just capitalize your struct
fields and access them directly.

```
s.Seq = "ACGT"     // yes
fmt.Println(s.Seq) // yes

s.setSeq("ACGT")        // no
fmt.Println(s.getSeq()) // no
```


### Standard Libraries ###

+ bufio
+ gzip
+ flag
+ fmt
+ math rand
+ path
+ reflect
+ regexp
+ sort
+ strings
+ text scanner
+ time

## Example Programs ##

some more complex examples than hello world


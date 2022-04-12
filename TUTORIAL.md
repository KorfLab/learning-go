Tutorial
========

In this tutorial, we are going to make a complete sequence analysis program
called `dust` that performs low-complexity sequence identification and masking.
This is based on a program of the same name that is used to preprocess
sequences before BLAST searches.

## Software Goals ##

+ Unix commandline interface
	+ usage statement
	+ fasta file input
	+ window size
	+ entropy threhold
	+ N or lowercase masking
+ Output FASTA file
+ Uses shared library for reading/writing FASTA

## Engineering Goals ##

+ Unit tests
+ Functional tests
+ Publishing

## Creating the Project ##

Make the project directory and initialize the project.

```
mkdir dust
cd dust
go mod init github.com/your-github-name/dust
```
## Unix CLI ##

Create the file and bring it up in your favorite editor.

	mkdir go.dust

The first line or lines of every program should be comments that indicate
something about the intent of the program. These doc-strings can be seen with
the `go doc` command.

```
// Low-complexity filter for nucleotide sequences
package main
```

Commandline arguments are parsed with the `flag` library.

```
fafile := flag.String("i", "", "fasta file (required)")
window := flag.Int("w", 11, "window size")
thresh := flag.Float64("t", 1.5, "entropy threshold")
lcmask := flag.Bool("l", false, "lowercase masking")
flag.Parse()
```

`flag` doesn't have good support for positional arguments, so don't use them.
If you want to print a usage statement for malformed commandlines, you'll have
to check that manually like below.

```
if *fafile == "" {
	flag.Usage()
	os.Exit(1)
}
```
Putting it all together looks like this so far.

```
// Low-complexity filter for nucleotide sequences
package main

import (
	"flag"
	"fmt"
	"os"
)

func main() {
	fafile := flag.String("i", "", "fasta file (required)")
	window := flag.Int("w", 11, "window size")
	thresh := flag.Float64("t", 1.5, "entropy threshold")
	lcmask := flag.Bool("l", false, "lowercase masking")
	flag.Parse()
	if *fafile == "" {
		flag.Usage()
		os.Exit(1)
	}
	fmt.Println(*fafile, *window, *thresh, *lcmask)
}
```

Note that flag returns pointers, so you have to dereference them.

## FASTA file input ##

### fastareader ###

Lots of programs need to read/write fasta files. They ought to share a common
library. So let's write that and have `dust` import it. Just like before, we
need to create a new directory and initialize the project.

```
cd ~/Code
mkdir fastareader
cd fastareader
go mod init github.com/your-github-name/fastareader
```

The file needs to start with the usual comment and package statment. This time
the name of the package isn't `main` but `fastareader`. Let's also add a small
function to make sure the import works.

```
// library for reading fasta files
package fastareader
import "fmt"
func Greetings () {fmt.Println("hello from fastareader")}
```

### dust ###

Back in you `dust` directory, the following won't work just yet.

```
cd ~/Code/dust
go run dust.go
```

We need to do 3 things

1. Import the `fastareader` library in `dust.go`
2. Tell the `go.mod` file in `dust` not to go to github just yet
3. Get a pseudo-version number to tie it all together

To import `fastareader` we just add it to the `import` statement in `dust.go`.
The full import statement now looks like this:

```
import (
	"flag"
	"fmt"
	"os"
	"github.com/iankorf/fastareader"
)
```

We just started development on `fastareader` and it isn't even in github yet.
We need to tell Go that this is just a development version and that the code is
in a nearby directory.


	go mod edit -replace github.com/iankorf/fastareader=../fastareader


The last thing we have to do is to get a pseudo-version number. This is done
with the following command:

	go mod tidy

Now if you look inside `go.mod` you'll see a file that looks like this:

```
module github.com/iankorf/dust

go 1.17

replace github.com/iankorf/fastareader => ../fastareader

require github.com/iankorf/fastareader v0.0.0-00010101000000-000000000000
```

Here's the complete `dust.go` program so far.

```
// Low-complexity filter for nucleotide sequences
package main

import (
	"flag"
	"fmt"
	"os"
	"github.com/iankorf/fastareader"
)

func main() {
	fafile := flag.String("i", "", "fasta file (required)")
	window := flag.Int("w", 11, "window size")
	thresh := flag.Float64("t", 1.5, "entropy threshold")
	lcmask := flag.Bool("l", false, "lowercase masking")
	flag.Parse()
	if *fafile == "" {
		flag.Usage()
		os.Exit(1)
	}
	fmt.Println(*fafile, *window, *thresh, *lcmask)

	fastareader.Greetings()
}
```

Let's give it a try:

	go run dust.go -i foo

The output should be:

```
foo 11 1.5 false
hello from fastareader
```

### fastareader ###

Now that the library is working, we should put some code in it. But first let's
imagine how we want to interact with a fastareader. A FASTA file has the
following structure.

```
>id1 some free text
ACCTATATGAGATATTA
CAGATATAT
>id2
ATAtTAATCAGAGG
```

Each _record_ or _entry_ in a FASTA file begins with a `>` sign. This is known
as the definition line. Immediately following is an identifier token that's
supposed to be unique in the file, for example "id1". After the identifier is
free text, which is entirely optional. Sequence "id2" doesn't have any
description. After the definition line comes an arbitrary number of sequence
lines of arbitrary length. The most common line length is 80. A FASTA file may
contain one or more entries.

A natural way to process the FASTA file would be something like the following
pseudo-code:

```
for each record in fastafile:
	dust(record.seq)
```

Let's build the `struct` to contain a FASTA record.

```
type fasta struct {
	Id   string // unique identifire
	Desc string // description
	Seq  string // sequence
}
```

Following the KorfLab Go SOP, every struct should have a constructor. Let's
build that now.

```
func NewFasta(id string, desc string, seq string) *fasta {
	r := fasta{Id: id, Desc: desc, Seq: seq}
	return &r
}
```

Most structs should also have an output method, so let's create that.

```
func (fa *fasta) Print() {
    fmt.Print(">")
    fmt.Println(fa.Id, fa.Desc)
    fmt.Println(fa.Seq)
}
```

### dust ###

Back in `dust` let's try making a fasta record and printing it out. Here's the
complete program so far.

```
// Low-complexity filter for nucleotide sequences
package main

import (
	"flag"
	"fmt"
	"os"
	"github.com/iankorf/fastareader"
)

func main() {
	// CLI
	fafile := flag.String("i", "", "fasta file (required)")
	window := flag.Int("w", 11, "window size")
	thresh := flag.Float64("t", 1.5, "entropy threshold")
	lcmask := flag.Bool("l", false, "lowercase masking")
	flag.Parse()
	if *fafile == "" {
		flag.Usage()
		os.Exit(1)
	}
	fmt.Println(*fafile, *window, *thresh, *lcmask)

	s1 := fastareader.NewFasta("seq1", "free text", "ACGT")
	s1.Print()
}
```

Here's the command line and ouput:

```
go run dust.go -i foo
foo 11 1.5 false
>seq1 free text
ACGT
```

### fastareader ###

Now it's time to write the code that will iterate through a FaSTA file handing
back one record at a time.

### dust ###

Iterate through a FASTA file (need a longer one)

## Entropy Filter ##

some code here

## Output ##

wrapped properly

## Unit Tests ##

## Functional Test ##


## Publishing ##



# LEARNING GO STRUCTURE

## Outline

* Intro
* Definitions
* Package vs. Module
* go.mod, Go!
* Text Editors Need Not Apply
* Testing


### Intro

Hello! Hopefully if you're reading this it's because you're trying to piece together how all this golang stuff works! I thought it might be useful to explore just *why* things are done in the way they are for golang, and what purpose all the extra bells and whistles serve. This will, eventually, be an exhaustive guide on everything about golang's logistical side, so that we can better take advantage of the language's robust style and reproducibility.


### Definitions

I found it useful to look into what the terms specific to golang actually mean.

* **Package**: A Package is essentially the same as a library in python. A package has child functions, and these functions can be called by other packages by importing them, with a few caveats

* **Module**: A module is...also a library, but it behaves differently in a few key ways. The relationship between modules and packages is hierarchical, that is modules contain packages and not the other way around. The purpose this structure serves is of version control and dependency management, especially when using "external", or remote packages.
 * Each Module contains its own, separate go.mod file, where packages do not.
 * When a package is included in a module, it needs its own directory within the directory that contains the module. Any other packages or directories inside will be treated as separate packages; while this may look different to us the compiler does *not* care



 So this implies an ideal structure for your code, where individual packages live in their own directories and are managed by a single "module."


 What controls modules, you might ask? Well that's a little something called the go.mod file.

 ### go.mod, go!



 This is essentially a recipe for your module. It serves to both identify your module to others, but also specify what 'ingredients' it needs to work and any substitutions or quirks of your home env that you need to account for.

 Here is an example, taken from the go.mod documentation found [here](go.dev/re/mod) at their official website:


 ```
 module example.com/my/thing

 go 1.12

 require example.com/other/thing v1.0.2
 require example.com/new/thing/v2 v2.3.4
 exclude example.com/old.thing v1.2.3
 replace example.com/bad/thing v1.4.5 => example.com/good/thing v1.4.5
 retract [v1.9.0, v1.9.5]
 ```

 Each line contains an argument called a "directive" that tells the compiler how to treat your program and any special steps it should take. The different directives can be learned about in more detail at the above link, but there's a few key parts of the structure of the file itself that are useful to know about.

 * **Module Path**: At the very top of the file, the directive `module` declares the path to your module. This serves two purposes: it tells other modules where your code is and it serves as a _unique identifier_ of your program. Or, in other words, each module should be separate from any others, and should have its own unique name and Path
  * As a note, the module path serves as the prefix for all child packages.

* **Go version**: The `go` directive specifies the *minimum* version of go that is required to run this module. There are ways to require specific versions, but by default it is considered a minimum

* **Require, replace, exclude, etc.**: These are all optional directives that add or change modules in order for your code to execute reliably. This can include a local directory! Most commonly when working on code you'll likely use replace, where you'll replace your github hosted module with a local one that you're actively working on. You don't technically need to do this, so long as you push every change you make to your file. For a complicated debugging procudure though, that might prove obnoxious.


### Text Editors Need Not Apply


There's one last crucial thing for go.mod files:

**For most operations, you do not need to open the go.mod file in a text editor, nor should you.**

Wild, right?

But as it turns out, golang has a whole host of built-in tools to perform most common actions to your go.mod file, and several uncommon ones. And while these achieve the same effect as manually opening the file and adding, removing, or changing lines as necessary, using the built in go mod edit command suite ensures that the all-too-important formatting of the file is kept consistent and in keeping with best practices.




### Testing

This section is still in progress, but golang also has a built in testing command that should handle it for us! It will require some more learning on my part, however.


To write a new test suite, create a file whose name ends _test.go thatt contains
the TestXxx functions as described here. Put the file in the same package as the
one being tested. The file will be excluded from regular package pbuilds but
will be included when the 'go test' command is run.

Somewhere between go help test and this blurb we can figure out what they mean
about testing!

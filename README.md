# JulieTest [![Build Status](https://travis-ci.org/arypurnomoz/JulieTest.jl.svg?branch=master)](https://travis-ci.org/arypurnomoz/JulieTest.jl)

A Julia testing framework inspired by javascripts's Mocha. It is also able to watch for changes and run specific the test.

![Screenshot](https://github.com/arypurnomoz/JulieTest.jl/raw/master/res/screenshot.png)

## Installation

```sh
julia -e 'Pkg.add("JulieTest")'
```

## Using Julie Command

Julie is a wrapper for julia to be used with JulieTest
```sh
Usage: julie [commands] arguments [options]

Commands:
    init  initialize JulieTest directory
  module  create basic module template
   start  start JulieTest to watch the directory
  single  run all test then exit
    help  print this help

Options:
  --reporter=dot  use specified reporter
  --skip-init     skip running all test at start

Examples:
  julie init
  julie module first second
  julie start -p 4
```
_`JulieTest will add julie alias to your bashrc/zshrc`_

### Without Julie Command

In case if you want to run JulieTest without the julie command you can run it directly from the `testConfig.jl`
```sh
julia testConfig.jl --single # will run in single mode
julia testConfig.jl --skip-init # will not run all test on enter
```

## Configuration

`julie start` will run `julia testConfig.jl` in the current directory, you can use `julie init` if you don't have one
```jl
# testConfig.jl example
 
# this need to be included
include(joinpath(Pkg.dir(),"JulieTest/src/watch.jl"))

# the directory to watch (will be recursive)
watch = ["src","test"]

# you can list any quotes that will be shown randomly at startup here
quotes = ["Julia rocks!"]

# currently only `spec` and `dot` is available
reporter = "spec"

# the interval of each test, default is int 0.25 second
interval = 0.25 

# this will start JulieTest
julieTest()
```

## Test File

```jl
using JulieTest

describe ("myTest") do
  it("basic") do
    @is 1 => 1
  end
  
  it("above") do
    @is 10 => above 9
  end
  
  it("builtin functions") do
    @is 10 => isa Number
  end
  
  # _it will be ignored
  _it("should fail") do
    @is true => falsy
  end
  
  # iit will make the test ignore the it
  # and only run the iit
  iit("the test should only run this") do
    @is true => not not truthy
  end
  
  # describe can be recursive
  describe("Array") do
    it("should not empty") do
      @is [1:5] => not empty
    end
  end
end
```

## @is macro
| syntax            | assertion      |
|-------------------|----------------|
| `@is x => y`      | `x == y`       |
| `@is x => fn y`   | `fn(x,y)`      |
| `@is x => fn`     | `fn(x)`        |
| `@is x => not fn` | `not(fn(x,y))` |

### Built in assertions
```jl
# Basic
@is 1 => not 2
@is true => truthy
@is false => falsy # also aliased to falsey

# Comparison
@is 2 => above 1 # also aliased to isabove, isAbove
@is 2 => below 3 # also aliased to isbelow, isBelow
@is 2 => most 3 # translate to 2 <= 3, also aliased to ismost, isMost
@is 3 => least 2 # translate to 3 >= 2, also aliased to atleast, atLeast

# Array
@is {} => empty
@is [1:5] => not empty
```


# construct

A simple, extensible build system based on [Scons](http://www.scons.org/). **construct** is designed to 
leverage the capabilities of Scons while allowing developers to focus on the task of describing what needs to be built. In general `make` like usage is also supported on the command-line. That is developers can simply write:

```sh
scons -D
```

and have the editor "do the right thing", building an  It is distributed as a `site_scons` directory allowing it to be effortlessly integrated into any Scons setup.

> Note: `-D` tells `scons` to look for an `sconstuct` file in the current or in parent directories and if it finds one execute the `sconcsript` files as if called from that directory. This ensures everything works as expected. For more details refer to the [Scons documentation](http://www.scons.org/documentation.php)


# Quick Intro

A minimal `sconstruct` file using **construct** would look like this:

```python
# Pull in all the Contruct goodies..
from construct import Construct

# Call sconscripts to do the work
Construct()
```

with an `sconscript` file possibly looking like this for a directory of programs to be built from associated source files:

```python
Import( 'env' )

# Build all *.cpp source files as executables
for Source in env.GlobFiles('*.cpp'):
    env.Build( Source[:-4], Source )
```

The `Build()` method is provided by **construct** and doess essentially what `Program()` does but in addition is both toolchain and variant aware and further can provide notifications on progress.

If our `sconscript` file was for a directory containing *.cpp files that are actually tests then we could instead write the `sconscript` file as:

```python
Import( 'env' )

# Build all *.cpp source files as executables
for Source in env.GlobFiles('*.cpp'):
    env.BuildTest( Source[:-4], Source )
```

The `BuildTest()` method is provided by **construct** and builds the sources specified as `Build()` does. However, in addition passing `--test` on the command-line will also result in the executable produced being run by a `test_runner`. The default test runner simply treats each executable as a test case and each directory or executables as a test suite. If the process executes cleanly the test passed, if not it failed.

To run this on the command-line we would write:

```sh
scons -D --test
```

If we only want to build and test *debug''* executables we can instead write this:

```sh
scons -D --dbg --test
```

Or for release only pass `--rel`


# Installation and Dependencies

No installation is required to use **construct**: simple download or pull a branch of the `site_scons` folder and place it appropriately so Scons will find it. For global use add it to your home directory or for use with a specific project place it beside (or sym-link `site_scons` beside) the top-level `sconstruct` file. For more details on using `site_scons` refer to the [Scons man page](http://www.scons.org/doc/production/HTML/scons-man.html).

There are no dependencies for **construct** other than Scons itself, however if you want to make use of the colourisation you should install the python package [colorama](https://pypi.python.org/pypi/colorama). For example you might do:

```sh
pip install colorama
```

# Design Principles

**construct** has been written primarily to provide a clean and structured way to leverage the power of Scons without the usual problems of hugely complex `scontruct` files that diverge between projects. Key goals of **construct** are:

  * minimise the need for adding logic into `sconscript` files, keeping them as declarative as possible. 
  * allow declarative `sconscripts` that are both much clearer and significantly simpler than the equivalent `make` file, without the need to learn a whole new scripting language like `make` or `cmake`.
  * provide a clear structure for extending the facilities offered by **construct**
  * provide a clear vocabulary for building projects
  * codify Scons best practices into **construct** itself so that users just need to call appropriate methods knowing that **construct** will do the right thing with their intent
  * provide a framework that allows experts to focus on providing facilities for others to use. Write once, use everywhere. For example one person who know how best to make [boost](http://www.boost.org/) available as a dependency can manage that dependency and allow others to use it seamlessly.


# Reference

## constructs vocabulary

**construct** uses the following terms to mean specific aspects of a build. Understanding these will remove ambiguity in understanding the facilities that **construct** provides.

Term            | Meaning
----------------+--------
Build Methods   | **construct** provides a number of build methods that can be called inside your `sconscript` files. Methods such as `Build()`, `BuildTest()`, `BuildWith()` and so on. These are in addition to the methods already provided by Scons.
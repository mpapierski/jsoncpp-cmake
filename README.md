jsoncpp-cmake
=============

[![Build Status](https://travis-ci.org/TubeTK/jsoncpp-cmake.png?branch=master)](https://travis-ci.org/TubeTK/jsoncpp-cmake)

Introduction
------------

JSON (JavaScript Object Notation) is a lightweight data-interchange format. It
can represent integers, real numbers, strings, an ordered sequence of values,
and a collection of name/value pairs.

[JsonCpp](http://jsoncpp.sourceforge.net/) is a simple API to manipulate JSON
values and handle serialization and unserialization to strings. It can also
preserve existing comments in unserialization/serialization steps, making it a
convenient format to store user input files. Unserialization parsing is user
friendly and provides precise error reports.

This version of JsonCpp,
[jsoncpp-cmake](https://github.com/TubeTK/jsoncpp-cmake), can be built with
[CMake](http://www.cmake.org) in addition to [SCons](http://www.scons.org).

Building with CMake
-------------------

jsoncpp-cmake uses [CMake](http://www.cmake.org) as a build system.

```shell
# Make a build directory
mkdir build
cd build

# Run the CMake configuration
cmake ~/path/to/src

# Build the project. This is dependent on what "Generator" was specified with
# the -G flag to CMake. The default on Unix-like systems is a Makefile
# generator.
make -j4
```

Building with SCons
-------------------

JsonCpp uses [SCons](http://www.scons.org) as a build system. SCons requires
[Python](http://www.python.org) to be installed.

You can download scons-local distribution from the following URL:
<http://sourceforge.net/projects/scons/files/scons-local/1.2.0/>

Unzip it in the directory where you found this README.md file. scons.py should
be at the same level as README.md.

```shell
python scons.py platform=PLTFRM [TARGET]
```

```PLTFRM``` may be one of:

* ```suncc``` Sun C++ (Solaris)
* ```vacpp``` Visual Age C++ (AIX)
* ```mingw```
* ```msvc6``` Microsoft Visual Studio 6 service pack 5-6
* ```msvc70``` Microsoft Visual Studio 2002
* ```msvc71``` Microsoft Visual Studio 2003
* ```msvc80``` Microsoft Visual Studio 2005
* ```msvc90``` Microsoft Visual Studio 2008
* ```linux-gcc``` GNU C++ (Linux, also reported to work for OS X)

```TARGET``` may be:

* ```check``` build library and run unit tests

Note that if you are building with Microsoft Visual Studio 2008, you need to
setup the environment by running vcvars32.bat (e.g., MSVC 2008 command prompt)
before running SCons. Adding a platform is fairly simple. You just need to
change the SConstruct file to do so.

Testing with CMake
------------------

Note that the tests require Python.

```shell
# Run ctest from the build directory.
ctest -j4
```

Testing with SCons
------------------

Note that tests can be run by SCons using the ```check``` target (see above).

You need to run a test manually only if you are troubleshooting an issue.

In the instructions below, replace "path to jsontest.exe" with the path of the
"jsontest" executable that was compiled on your platform.

```shell
cd test
# This will run the reader/writer tests
python runjsontests.py "path to jsontest.exe"

# This will run the reader/writer tests, using JSONChecker test suite
# (http://www.json.org/JSON_checker/).
# Notes: not all tests pass: JsonCpp is too lenient (for example,
# it allows an integer to start with '0'). The goal is to improve
# strict mode parsing to get all tests to pass.
python runjsontests.py --with-json-checker "path to jsontest.exe"

# This will run the unit tests (mostly Value)
python rununittests.py "path to test_lib_json.exe"
```

You can run the tests using Valgrind:

```shell
python rununittests.py --valgrind "path to test_lib_json.exe"
```

Building Against JsonCpp
-------------------------

To configure your project to build against JsonCpp, add the following in your
CMakeLists.txt:

```cmake
find_package( JsonCpp )
include_directories( ${JsonCpp_INCLUDE_DIRS} )

add_executable( example main.cxx )
target_link_libraries( example ${JsonCpp_LIBRARIES} )
```

Building the Documentation
--------------------------

Run the Python script doxybuild.py from the top directory:

```shell
python doxybuild.py --open --with-dot
```

See ```doxybuild.py --help``` for options.

Note that the documentation is also available for download as a tar-ball. The
documentation of the latest release is available online at
<http://jsoncpp.sourceforge.net/>

Generating Amalgamated Source and Header
----------------------------------------

JsonCpp is provided with a script to generate a single header and a single
source file to ease inclusion in an existing project.

The amalgamated source can be generated at any time by running the following
command from the top-directory (requires Python 2.6):

```shell
python amalgamate.py
```

It is possible to specify the header name. See ```-h``` options for detail. By
default, the following files are generated:

* dist/jsoncpp.cpp: source file that need to be added to your project.
* dist/json/json.h: header file corresponding to use in your project. It is
  equivalent to including json/json.h in non-amalgamated source. This header
  only depends on standard headers.
* dist/json/json-forwards.h: header the provides forward declaration of all
  JsonCpp types. This typically what should be included in headers to speed-up
  compilation.

The amalgamated sources are generated by concatenating the JsonCpp source in the
correct order and defining the macro ```JSON_IS_AMALGAMATION``` to prevent
inclusion of other headers.

Using JsonCpp in Your Project
------------------------------

include/ should be added to your compiler include path. JsonCpp headers should
be included as follows:

```c++
#include <json/json.h>
```

Adding a Reader/Writer Test
---------------------------

To add a test, you need to create two files in test/data:

* a TESTNAME.json file, that contains the input document in JSON format.
* a TESTNAME.expected file, that contains a flattened representation of the
  input document.

TESTNAME.expected file format:

* each line represents a JSON element of the element tree represented by the
  input document.
* each line has two parts: the path to access the element separated from the
  element value by '='. Array and object values are always empty (e.g.,
  represented by either [] or {}).
* element path: '.' represented the root element, and is used to separate object
  members. [N] is used to specify the value of an array element at index N.

See test_complex_01.json and test_complex_01.expected to better understand
element path.

Understanding Reader/Writer Test Output
---------------------------------------

When a test is run, output files are generated alongside the input test files.
Below is a short description of the content of each file:

* test_complex_01.json: input JSON document.
* test_complex_01.expected: flattened JSON element tree used to check if parsing
  was corrected.
* test_complex_01.actual: flattened JSON element tree produced by jsontest.exe
  from reading test_complex_01.json.
* test_complex_01.rewrite: JSON document written by jsontest.exe using the
  ```Json::Value``` parsed from test_complex_01.json and serialized using
  ```Json::StyledWriter```.
* test_complex_01.actual-rewrite: flattened JSON element tree produced by
  jsontest.exe from reading test_complex_01.rewrite.
* test_complex_01.process-output: jsontest.exe output, typically useful to
  understand parsing error.

License
-------

See the file LICENSE for details. Basically, JsonCpp is licensed under the MIT
license, or public domain if desired and recognized in your jurisdiction.

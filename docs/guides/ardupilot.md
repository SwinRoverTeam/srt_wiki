# ArduPilot

## Setup

[Setup the ubuntu linux](https://ardupilot.org/dev/docs/building-setup-linux.html#building-setup-linux)

## Get the Source

Clone the project from GitHub:
```sh
git clone --recursive git@github.com:SwinRoverTeam/ardupilot.git
cd ardupilot
```

## Reference for altering the build system
[Waf Book](https://waf.io/book/).

waf should always be called from the locally cloned ardupilot root directory for the local branch you are trying to build from.

**Note**
Do not run `waf` with `sudo`!  This leads to permission and environment problems.

## Basic usage ##

There are several commands in the build system for advanced usage, but here we
list some basic and more used commands as example.

* **Build ArduRover**

    Configure the project for Cube Orange Plus and build the source for
    specifically the ardurover target, compiling everything at once slow
    waf down too much. waf will handle parallelisation here.

    ```sh
    ./waf configure --board CubeOrangePlus
    ./waf --targets bin/ardurover
    ```

    If you're missing a python package after this step, install it using apt
    then re-run;

    ```sh
    ./waf --targets bin/ardurover
    ```

    Using a python venv of the provided install script will break the project.
    (we should move to cmake).

    The "ardurover" binary should appear in the `build/<board-name>/bin` directory.

* **Clean the build**

    Commands `clean` and `distclean` can be used to clean the objects produced by
    the build. The first keeps the `configure` information, cleaning only the
    objects for the current board. The second cleans everything for every board,
    including the saved `configure` information.

    Cleaning the build is very often not necessary and discouraged. We do
    incremental builds reducing the build time by orders of magnitude.


* **Upload or install**

    You should be using QGroundControl to upload the firmware not these:

    Build commands have a `--upload` option in order to upload the binary built
    to a connected board. This option is supported by Pixhawk and Linux-based boards.
    The command below uses the `--targets` option that is explained in the next item.

    ```sh
    ./waf --targets bin/ardurover --upload
    ```

    For Linux boards you need first to configure the IP of the board you
    are going to upload to. This is done on configure phase with:

    ```sh
    ./waf configure --board <board> --rsync-dest <destination>
    ```

    The commands below give a concrete example (board and destination
    IP will change according to the board used):

    ```sh
    ./waf configure --board navio2 --rsync-dest root@192.168.1.2:/
    ./waf --target bin/arducopter --upload
    ```

    This allows to set a destination to which the `--upload` option will upload
    the binary.  Under the hood  it installs to a temporary location and calls
    `rsync <temp_install_location>/ <destination>`.

    On Linux boards there's also an install command, which will install to a certain
    directory, just like the temporary install above does. This can be
    used by distributors to create .deb, .rpm or other package types:

    ```sh
    ./waf copter
    DESTDIR=/my/temporary/location ./waf install
    ```

* **Use different targets**

    The build commands in the items above use `copter` as argument. This
    builds all binaries that fall under the "copter" group. See the
    section [Advanced usage](#advanced-usage) below for more details regarding
    groups.

    This shows a list of all possible targets:

    ```
    ./waf list
    ```

    For example, to build only a single binary:

    ```
    # Quad frame of ArduCopter
    ./waf --targets bin/arducopter

    # unit test of our math functions
    ./waf --targets tests/test_math
    ```

* **Use clang instead of gcc**

    Currently, gcc is the default on linux, and clang is used for MacOS.
    Building with clang on linux can be accomplished by setting the CXX
    environment variables during the configure step, e.g.:

    ```
    CXX=clang++ CC=clang ./waf configure --board=sitl
    ```

    Note: Your clang binary names may differ.

* **Other options**

    It's possible to see all available commands and options:

    ```
    ./waf -h
    ```

    Also, take a look on the [Advanced section](#advanced-usage) below.

## Advanced usage ##

This section contains some explanations on how the Waf build system works
and how you can use more advanced features.

Waf build system is composed of commands. For example, the command below
(`configure`) is for configuring the build with all the options used by this
particular build.

```bash
# Configure the Linux board
./waf configure --board=linux
```

Consequently, in order to build, a "build" command is issued, thus `waf build`.
That is the default command, so calling just `waf` is enough:

```bash
# Build programs from bin group
./waf

# Waf also accepts '-j' option to parallelize the build.
./waf -j8
```

By default waf tries to parallelize the build automatically to all processors
so the `-j` option is usually not needed, unless you are using icecc (thus
you want a bigger value) or you don't want to stress your machine with
the build.

### Program groups ###

Program groups are used to represent a class of programs. They can be used to
build all programs of a certain class without having to specify each program.
It's possible for two groups to overlap, except when both groups are main
groups. In other words, a program can belong to more than one group, but only
to one main group.

There's a special group, called "all", that comprises all programs.

#### Building a program group ####

Ardupilot adds to waf an option called `--program-group`, which receives as
argument the group you want it to build. For a build command, if you don't pass
any of `--targets` or `--program-group`, then the group "bin" is selected by
default. The option `--program-group` can be passed multiple times.

Examples:

```bash
# Group bin is the default one
./waf

# Build all vehicles and Antenna Tracker
./waf --program-group bin

# Build all benchmarks and tests
./waf --program-group benchmarks --program-group tests
```

### Building a specific program ###

In order to build a specific program, you just need to pass its path relative
to `build/<board>/` to the option `--targets`. Example:

```bash
# Build arducopter for quad frame
./waf --targets bin/arducopter

# Build vectors unit test
./waf --targets tests/test_vectors
```

### Checking ###

The command `check` builds all programs and then executes the relevant tests.
In that context, a relevant test is a program from the group "tests" that makes
one of the following statements true:

 - it's the first time the test is built since the last cleanup or when the
   project was cloned.
 - the program had to be rebuilt (due to modifications in the code or
   dependencies, for example)
 - the test program failed in the previous check.

That is, the tests are run only if necessary. If you want waf to run all tests,
then you can use either option `--alltests` or the shortcut command
`check-all`.

Examples:

```bash
# Build everything and run relevant tests
./waf check

# Build everything and run all tests
./waf check --alltests

# Build everything and run all tests
./waf check-all
```

### Debugging ###

It's possible to pass the option `--debug` to the `configure` command. That
will set compiler flags to store debugging information in the binaries so that
you can use them with `gdb`, for example. That option might come handy when using SITL.

### Command line help ###

You can use `waf --help` to see information about commands and options built-in
to waf as well as some quick help on those added by ardupilot.

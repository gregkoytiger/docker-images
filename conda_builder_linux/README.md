# conda_builder_linux

Docker container (CentOS 5.11) with GCC 5.2, Binutils 2.26, and a bootstrapped
[miniconda](http://conda.pydata.org/miniconda.html) installed and ready to use.

Libraries built with this docker container should be compatible with most Linux
distributions. Additionally, GCC5.2 allows compilation of C++11 and C++14 code.

Usage
-----

A helper script for running is provided that mounts your .ssh folder (for
passwordless SSH) and ~/.gitconfig file in the container. To run this, download
the docker_wrapper.sh script and either start_cpp*.sh script, and run the start
script from your Docker terminal. On Windws and Mac with Docker Toolbox, this is
your Docker Quickstart terminal.

Once that starts, it will display some system information:

```
Welcome to the conda-builder image, brought to you by Continuum Analytics.

Binaries produced with this image should be compatible with any Linux OS that is
at least CentOS 5 or newer (Glibc lower bound), and anything that uses G++ 5.2
or older (libstdc++ upper bound)

   GCC is: gcc (GCC) 5.2.0
   Default C++ ABI: 4 (C++98)
   GLIBC is: glibc 2.5
   Native arch is x64. To build for 32-bit, set CONDA_FORCE_32BIT=1,
       or outside of conda-build, CFLAGS="-m32"
   ld/binutils is: GNU ld (GNU Binutils) 2.26.20160125

   The dev user (currently signed in) has passwordless sudo access.
   miniconda (2.7) is installed at /opt/miniconda.
   git is also available.
   Your .gitconfig has been imported.
   Your ssh private key has been imported for passwordless ssh.

Helpful aliases:
    clone_recipes: clones the conda/conda-recipes repo from Github
    clone_anaconda: clones the continuumIO/anaconda (private) repo from Github
    anaconda_setup: clones anaconda repo and sets up continuum internal build
    system.
```

The docker image is made to be used either interactively, or by passing
commands, so that the image is effectively just a run environment. Any arguments
you pass in are parsed first to pull out ```docker run``` arguments, then any
unrecognized parts are fed into the container and executed using ```eval```. If
you did not supply any command for the start script to run directly, you'll be
dropped at an interactive prompt.

Interactive use
===============

Starting the image in interactive mode looks like:

    start_cpp98.sh

For mounting volumes or setting environment variables, you can pass the -v or -e arguments:

    # mount your local recipes folder in the container, avoid a clone in the container.
    start_cpp98.sh -v ~/code/my_recipes:/home/dev/recipes

    # build in 32-bit (compiler is 64-bit native, but with multilib can build 32-bit packages)
    start_cpp98_32.sh

The interactive prompt looks like:

    [dev@da2f4a3e941b ~]$

Here you have quick access to git, conda, and conda-build, all installed and on
PATH. Additionally, a few aliases are set up by default:

  - **clone_recipes**: clone the conda recipes repository from
    https://github.com/conda/conda-recipes
  - **clone_anaconda**: clone the conda recipes repository from
    https://github.com/continuumIO/anaconda (This is a Continuum private repo.
    Checking this out requires your ssh keys to be set up properly.)
  - **anaconda_setup**: clone the conda recipes repository from
    https://github.com/continuumIO/anaconda and run ```python setup.py
    develop``` to install the build system's commands

Scripted use
============

Starting the image to run a command looks like:

    start_cpp98.sh my_build_script.sh && echo "weeee"

Again, you can pass flags to the docker run command through the start_*.sh script:

    # mount your local recipes folder in the container, avoid a clone in the container.
    start_cpp98.sh -v ~/code/my_recipes:/home/dev/recipes my_build_script.sh

    # build in 32-bit (compiler is 64-bit native, but with multilib can build 32-bit packages)
    start_cpp98_32.sh my_build_script.sh

Docker Hub location
===================

https://hub.docker.com/r/continuumio/conda_builder_linux/

NOTES
-----

If you do not use one of the start_???.sh or docker_wrapper.sh scripts, and do not otherwise set environment variables, the docker image will compile using the C++11 ABI, 64-bit only.  This code will not be backward-compatible with GCC4-compiled code.
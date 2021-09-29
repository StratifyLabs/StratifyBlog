---
categories:
- Device Tips
date: "2021-09-29"
layout: post
draft: false
tags:
- cpp
- programming
- git
- cmake
title: Dev Ops with CMake
---

CMake is used for building software projects, but it has the power to do much more. When I first started using CMake, I was doing it all wrong. It wasn't until I watched [C++Now 2017: Daniel Pfeifer "Effective CMake"](https://www.youtube.com/watch?v=bsXLMQ6WgIk) (yes, I watched for the entire hour and a half) did I start to truly understand how CMake could and should work. And since, I have expanded the use of CMake into a nearly complete Dev Ops chain.

## CMake is Code

One of the most important points Daniel makes in the above video is that *CMake is code*. It should be clear, concise, easy-to-follow, and commented as needed. 

## CMake Super-Duper Project

If you watched the video, you will know that CMake supports a hierarchy of projects. You can use `add_subdirectory()` to add a dependency project to the build. The top project is known as the super project. When you take this to the extreme, it becomes a super-duper project.

The Stratify OS support package for the [STM32H735G-DK development board](https://github.com/StratifyLabs/STM32H735G-DK) takes this approach. Let's take a look at how it uses CMake to 1) bootstrap the machine, 2) download the dependencies, 3) build the project and all its dependencies.

### Bootstrapping the Machine

To bootstrap the STM32H735G-DK project, you need to have `git` and `cmake` installed on your machine. The bootstrap script installs the rest. 

First, I want to mention why `cmake` is great for scripting.

1. It saves users from installing another tool (like python) on their computer.
2. It saves maintainers from needing to learn another tool.
3. It runs on Windows, Mac, and Linux with great support for the native environments on each platform.

Our super project's first task is to run a script that installs any tools needed. In this case, a command-line tool called `sl` (published by Stratify Labs) and an ARM GCC cross-compiler.

First, we clone the project:

```sh
git clone https://github.com/StratifyLabs/STM32H735G-DK
cd STM32H735G-DK
```

Now comes the `cmake` bootstrap script. To run the bootstrap script, we use the `-P` option with `cmake`. which tells `cmake` to run commands rather than create a build.

```
cmake -P bootstrap.cmake
```

The script:

- Creates an `SDK` folder as a subdirectory
- Clones another project from Github that contains a CMake library
- Downloads `sl` using `file(DOWNLOAD ...)` [See the file documentation](https://cmake.org/cmake/help/latest/command/file.html).
- Invokes `sl` with arguments to download and install the compiler while verifying the HASH checksum.
- Creates `cmake_arm` as the build directory
- Downloads a script to set the environment

If you run:

```
git clone https://github.com/StratifyLabs/STM32H735G-DK
cd STM32H735G-DK
cmake -P bootstrap.cmake
# At this point you will need to login using sl and your browser
# then run the script again to pick up where you left off
cmake -P bootstrap.cmake
```

When you are done, you will have a compiler installed at `STM32H735G-DK/SDK/StratifyLabs-SDK`. It doesn't affect any system settings. You can just delete the folder to restore your computer to its previous state.


### Building with CMake

Now to build, we set up the environment then use `cmake` to configure and build the project.

> Using `-GNinja` is optional if you want to use `ninja` to build the project

```
source profile.sh
cd cmake_arm
cmake .. -GNinja
cmake --build . --target all
```

## Restoring your Machine

To completely remove the project (as well as the dependencies and all the tools) run this in the folder where you ran the clone command:

```
rm -Rf ./STM32H735G-DK
```

## The End

[C++Now 2017: Daniel Pfeifer "Effective CMake"](https://www.youtube.com/watch?v=bsXLMQ6WgIk) is a great summary on how to use `cmake`. Remember *`cmake` is code*. You can script with `cmake` using `-P` to do anything from cloning dependencies, creating a directory structure to downloading and installing tools. Finally, with `add_subdirectory()` you can build all the dependencies alongside the firmware binary.


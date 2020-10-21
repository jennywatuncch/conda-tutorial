# Why Conda?
You've probably heard of the tool Conda, virtual environments, and package management (not Anaconda! We'll touch on that later). To put it briefly, conda is a package manager, much like pip for python, that is aimed at being flexible enough for any language or tools. 

Maybe the Conda docs will explain it best:

*"Conda is an open source package management system and environment management system that runs on Windows, macOS and Linux. Conda quickly installs, runs and updates packages and their dependencies. Conda easily creates, saves, loads and switches between environments on your local computer. It was created for Python programs, but it can package and distribute software for any language.*

*Conda as a package manager helps you find and install packages. If you need a package that requires a different version of Python, you do not need to switch to a different environment manager, because conda is also an environment manager. With just a few commands, you can set up a totally separate environment to run that different version of Python, while continuing to run your usual version of Python in your normal environment."*

So, basically it streamlines installing packages and dependencies so you can worry less about dependencies and more about writing good code. 

Sounds pretty good, right? But you might be wondering what an 'environment' is, or why we need to worry about 'dependencies'. Why can't you just install python, install your packages with pip, and call it a day? You totally could, but lets lay out some scenarios that might convince you that's not a great idea:
  
  1. You have two projects going simultaneously. One of them is a project that relies heavily on a tool written for a very specific version of python and pandas because of a function they deprecated later, and you've found that trying any other method is too much time to be worth it. At the same time, your other project requires the latest version of pandas because of a big dataframe-editing workflow you've built, and it requires the new indexing format. You can't install two versions of pandas, so what could you do?
  
 
  
  2. You're about to publish your software, and you list a bunch of packages you used in your readme that people should install. Turns out, there's been updates to some of them, and they broke your code because of some internal changes, and now you have a bunch of people wanting to reproduce your study results but your tool is totally broke. How could you share your exact package versions that all play nicely together dependency-wise?
  
 The answer to both of these problems is to use a conda environment. These are essentially a separated "space" that are completely independent from one another and allow for reproducible packages with a single install command. 

## Definitions

Let's break down some common terms that'll be used throughout this doc, these are pretty general so hopefully they'll help you read other package manager docs too, and they're common throughout the conda docs.

Environment: Technically just a directory with specified packages installed, these essentially function as separated workspaces. You can "activate" and "deactivate" as needed, without changing the internal working of the environment. These are the real core of why conda is useful for data science, they're more lightweight than a Docker-style container solution, but allow for easy reproducible computing environments if handled properly.

Activate/Deactivate: The method for getting in/out of environments. If we think of an environment as a room containing our packages, think of activate as opening the door, entering, and shutting it behind you. The opposite is true for deactivate, where you exit the room and leave everything related to it behind the door inside. 

Package: Typically I think of Python packages since I'm a Python dev, but in conda this can actually refer to any library or tool packaged in a tarball and hosted somewhere conda can reach it. There are a wide variety of packages, ranging from simple python modules to fully-fleshed bioinformatics tools (samtools, bedtools, etc) and all of them can be installed within an environment. 

Dependencies: Any package/module/etc that is required for another package/module/etc to run. One of the nice things about conda is that it handles most of this through a complex set of rules that determine priorities on versioning and such, so you don't need to worry about it too much.

Channel: A collection of 

## Installation

If you're on Longleaf (or any other module-handled HPC system) conda is already likely installed, all you need to do is `module load conda`. This puts you in the 'base' conda environment, meaning that you have access to conda and all its functionality, but you're in a default vanilla installation that doesn't have much installed. 

If conda isn't already installed, it's fairly straightforward to do so. Download one of the linux installers from https://docs.conda.io/en/latest/miniconda.html#linux-installers, unpack, and run 

```{bash}
bash Miniconda3-latest-Linux-x86_64.sh 
```
and you should be good to go. 

## Creating Your First Env from Scratch

Before jumping into packages, let's make our first environment to install everythin in. This process is straightforward on its head:

```{bash}
# -n is short for --name, just specifying the name of the environment
conda create -n hello-world
```

We now have an empty environment called 'hello-world', that doesn't even contain a python installation. To change that, lets activate the environment and tinker with it. In this example I'm going to install some 


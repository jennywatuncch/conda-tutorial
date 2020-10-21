# Conda for Reporducible Data Science

## Table of Contents

- [Conda for Reproducible Data Science](#conda-for-reporducible-data-science)
  - [Table of Contents](#table-of-contents)
    - [Why Conda](#why-conda)
      - [No, seriously, why conda](#no-seriously-why-conda)
  - [Definitions](#definitions)
  - [The Basics](#the-basics)
    - [Installation](#installation)
    - [Creating Your First Env from Scratch](#creating-your-first-env-from-scratch)
    - [Sharing Environments](#sharing-environments)
    - [Adding Channels](#adding-channels)
  - [The Not-So-Basics](#the-not-so-basics)
    - [Using pip in conjunction with conda](#using-pip-in-conjunction-with-conda)
    - [Envs within Makefiles](#envs-within-makefiles)
    - [Using conda in a SLURM job](#using-conda-in-a-slurm-job)
    - [Envs within Docker Containers](#envs-within-docker-containers)

![Let's do some data science, babyyyy](images/conda-logo.png)
[Official Documentation here!](https://docs.conda.io/projects/conda/en/latest/index.html)

You've probably heard of the tool Conda, virtual environments, and package management, but if you're like me it could take quite a while to figure out why any of this was useful. To put it briefly, conda is a package manager, much like pip for python, that is aimed at being flexible enough for any language or tools.

Maybe the Conda docs will explain it best:

*"Conda is an open source package management system and environment management system that runs on Windows, macOS and Linux. Conda quickly installs, runs and updates packages and their dependencies. Conda easily creates, saves, loads and switches between environments on your local computer. It was created for Python programs, but it can package and distribute software for any language.*

*Conda as a package manager helps you find and install packages. If you need a package that requires a different version of Python, you do not need to switch to a different environment manager, because conda is also an environment manager. With just a few commands, you can set up a totally separate environment to run that different version of Python, while continuing to run your usual version of Python in your normal environment."*

So, basically it streamlines installing packages and dependencies so you can worry less about dependencies and more about writing good code.

### Why Conda

Sounds pretty good, right? But you might be wondering what an 'environment' is, or why we need to worry about 'dependencies'. Why can't you just install python, install your packages with pip, and call it a day? You totally could, but lets lay out some scenarios that might convince you that's not a great idea:
  
  1. You have two projects going simultaneously. One of them is a project that relies heavily on a tool written for a very specific version of python and pandas because of a function they deprecated later, and you've found that trying any other method is too much time to be worth it. At the same time, your other project requires the latest version of pandas because of a big dataframe-editing workflow you've built, and it requires the new indexing format. You can't install two versions of pandas, so what could you do?
  
  2. You're about to publish your software, and you list a bunch of packages you used in your readme that people should install. Turns out, there's been updates to some of them, and they broke your code because of some internal changes, and now you have a bunch of people wanting to reproduce your study results but your tool is totally broke. How could you share your exact package versions that all play nicely together dependency-wise?
  
 The answer to both of these problems is to use a conda environment. These are essentially a separated "space" that are completely independent from one another and allow for reproducible packages with a single install command.

#### No, seriously, why conda

You might be thinking to yourself why not just use a Docker container, or pip virtualenv, or some other obscure package manager that I haven't heard of. Honestly, you very easily could, but it really depends on your use case. Here's a list of reasons I prefer conda over other tools:

- Over Docker/Singularity: Docker can be resource intensive, require a lot of tinkering for some projects, and requires a lot of manual-handling on the user end. While I think Docker is a great tool for reproducibility, I prefer it as an overall containerization strategy rather than a reproducible development environment.
- Over pip: Pip *only* works for Python packages. While conda might seem like a Python package manager at first, you can install R pacakges, C++ libraries, and entire tools like samtools and bedtools right within an environment. This increased flexibility makes conda a better solution for most bioinformatics workflows in my experience, with the ease of use of pip.

Of course everyone's situations and preferences are different, but I'd rather lay out some reasoning for why *I* prefer conda at the moment for doing all of my data science workflow and software development.

Now, with that out of the way, let's move on to the nitty gritty.

---

## Definitions

Let's break down some common terms that'll be used throughout this doc, these are pretty general so hopefully they'll help you read other package manager docs too, and they're common throughout the conda docs.

**Environment:** Technically just a directory with specified packages installed, these essentially function as separated workspaces. You can "activate" and "deactivate" as needed, without changing the internal working of the environment. These are the real core of why conda is useful for data science, they're more lightweight than a Docker-style container solution, but allow for easy reproducible computing environments if handled properly.

**Activate/Deactivate:** The method for getting in/out of environments. If we think of an environment as a room containing our packages, think of activate as opening the door, entering, and shutting it behind you. The opposite is true for deactivate, where you exit the room and leave everything related to it behind the door inside.

**Package:** Typically I think of Python packages since I'm a Python dev, but in conda this can actually refer to any library or tool packaged in a tarball and hosted somewhere conda can reach it. There are a wide variety of packages, ranging from simple python modules to fully-fleshed bioinformatics tools (samtools, bedtools, etc) and all of them can be installed within an environment.

**Dependencies:** Any package/module/etc that is required for another package/module/etc to run. One of the nice things about conda is that it handles most of this through a complex set of rules that determine priorities on versioning and such, so you don't need to worry about it too much.

**Channel:** A collection of packages that can be accessed to download from. There is a default conda channel, but another very common one is conda-forge.

---

## The Basics

### Installation

If you're on Longleaf (or any other module-handled HPC system) conda is already likely installed, all you need to do is `module load conda`. This puts you in the 'base' conda environment, meaning that you have access to conda and all its functionality, but you're in a default vanilla installation that doesn't have much installed.

If conda isn't already installed, it's fairly straightforward to do so. Download one of the linux installers from <https://docs.conda.io/en/latest/miniconda.html#linux-installers>, unpack, and run

```{bash}
bash Miniconda3-latest-Linux-x86_64.sh
```

and you should be good to go.

---

### Creating Your First Env from Scratch

Before jumping into packages, let's make our first environment to install a few basic packages in. This process is straightforward on its head:

```{bash}
# -n is short for --name, just specifying the name of the environment
conda create -n hello-world
```

We now have an empty environment called 'hello-world', that doesn't even contain a python installation. To change that, let's activate the environment and tinker with it. In this example I'm going to install a specific version of Python(3.6), pandas, numpy, and scikit-learn.

```{bash}
#Activate env
conda activate hello-world

#Install packages, the -y flag just tells it to go ahead with the install without prompting
conda install -y python=3.6 pandas numpy scikit-learn
```

You'll notice that next to the bash prompt there's now a `(hello-world)`, indicating that we're actively inside this environment. Handy to know when you're jumping around terminals. 

To confirm that you've installed packages, you can use `conda list` inside an environment. This gives a detailed report of all packages installed.

Lets say you didn't want numpy anymore, so you went ahead and uninstall it using `conda uninstall numpy`. What you'll notice is that when prompted to confirm the package deletion, scikit-learn is also going to be removed from the environment. This is because you're uninstalling a dependency of that package, and it can no longer function. Conda automatically detects that will be the case and removes all dependent packages from the space when this happens. Pretty neat!


*Note: Some installations of conda do not use `conda activate` and instead use `source activate`, this was changed in recent versions of conda and if one doesn't work, the other one should*

---

### Sharing Environments

Sharing environments you've made is also pretty straightforward. If you tested the `conda list` command earlier, you'll notice it's also how we export our environments to a file. To do so, activate the env if you haven't done so and run:

```{bash}
#Export environment to yaml
conda env export hello-world > hello-world.yml
```

You now have a file that you can publish on Github, send to collaborators, or share however you want, and the recipient can replicate your environment exactly as you had it by doing:

```{bash}
#Install environment from file
conda env create -f hello-world.yml
```

And voila! Complete reproducibility with a single command.

---

### Adding Channels

Channels are Conda's way of opening up collections of packages to the community, and allow for you to search for packages hosted by smaller groups without needing to look in condas official repository. One common one is 'conda-forge', which not only mirrors a lot of essential packages but adds a lot of smaller ones as well. Using a different channel for installing a package is straightforward with the `-c` flag:

```{bash}
#Install Keras using conda-forge
conda install -c keras
```

Alternatively, you can add a channel permanently to the list of package providers for an environment:

```{bash}
#Add conda-forge permanently to this env
conda config --add channels conda-forge
```

When you do this, all future installations will have the option of looking through either the default conda channel or conda-forge, and if it can't find it on one it will install it through the other. There's also a priority list for channels, meaning that conda will look through the top priority channel first for a package, [but you can read more on the official docs for that.](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-channels.html)

---

## The Not-So-Basics

### Using pip in conjunction with conda

Pip, the python package manager, is an essential tool for any Python dev, and luckily it plays very nicely with conda. From within an environment you can just install a package as you normally would, `pip install numpy` for example, and whenever you export the environment or install it conda will automatically install that package using pip instead of a conda channel.

In general it's suggested to always search for a conda version of a package first because it's easier for conda to handle dependencies that way, but it's not an issue to use a mixture of conda and pip packages in the same environment.

---

### Envs within Makefiles

Make targets have the technicality of being run within a subshell for each separate line of a target, so it's useful to remember that when activating an environment for a make target you have to keep it on the same line. For example:

```{make}
#Won't work, env isn't activated!
conda activate hello-world
python hello_world.py

#Works!
conda activate hello-world; \
    python hello_world.py
```

---

### Using conda in a SLURM job

Personally I haven't had much issue using conda environments within SLURM batch scripts. I usually use environments for all my projects, and whenever running cluster jobs I just include 

```{bash}
#Load conda and activate environment
module load conda
source activate hello-world

#Rest of script
...
```

I've included an example SLURM batch script in this repo as an example, along with an example python script to test that it's working.

---

### Envs within Docker Containers

Coming soon, along with a portable container example!

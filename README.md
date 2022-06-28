# Nextflow Learning

This is a resource for learning how to use Nextflow, a type of domain specific 
langauge (DSL) that for writing and running workflows that support many applications 
in data science, machine learning, bioinformatics, and more.

# Resources

* [**Nextflow documentation**](https://www.nextflow.io/docs/latest/index.html) -- Most instructions adhere closely to this guide.

# Setup

## Linux, MacOS, etc.

For POSIX systems like Linux, MacOS, etc. you can run Nextflow natively.

## Windows

Nextflow can be run on Windows by [installing](https://docs.microsoft.com/en-us/windows/wsl/install) Windows Subsystem for Linux (WSL).

## Installing Java

If you need to install Java, you may use:

```
sudo add-apt-repository ppa:webupd8team/java
sudo apt-get update
sudo apt-get install oracle-java8-installer
```

You can then try running `java`, which would return:

```
Command 'java' not found, but can be installed with:

sudo apt install openjdk-11-jre-headless  # version 11.0.15+10-0ubuntu0.20.04.1, or
sudo apt install default-jre              # version 2:1.11-72
sudo apt install openjdk-16-jre-headless  # version 16.0.1+9-1~20.04
sudo apt install openjdk-17-jre-headless  # version 17.0.3+7-0ubuntu0.20.04.1
sudo apt install openjdk-8-jre-headless   # version 8u312-b07-0ubuntu1~20.04
sudo apt install openjdk-13-jre-headless  # version 13.0.7+5-0ubuntu1~20.04
```

Finally, complete the install, e.g. using

```
sudo apt install openjdk-11-jre-headless
```

## Install

First, navigate to the main directory where you want the Nextflow executable to be
installed to. Next, install Nextflow using `wget` with:

```
wget -qO- https://get.nextflow.io | bash
```

Or using `curl` with:

```
curl -s https://get.nextflow.io | bash
```

If the installation completed successfully, you should see this message:

```
...
...
CAPSULE: Downloading dependency org.eclipse.jgit:org.eclipse.jgit:jar:5.2.1.201812262042-r
CAPSULE: Downloading dependency org.pf4j:pf4j-update:jar:2.3.0
CAPSULE: Downloading dependency commons-codec:commons-codec:jar:1.10

      N E X T F L O W
      version 22.04.4 build 5706
      created 19-06-2022 20:51 UTC (13:51 PDT)
      cite doi:10.1038/nbt.3820
      http://nextflow.io


Nextflow installation completed. Please note:
- the executable file `nextflow` has been created in the folder: //home/metamaden
- you may complete the installation by moving it to a directory in your $PATH

```

Next, make the installed Nextflow binary executable by changing its executable 
permissions with:

```
chmod +x nextflow
```

Finally, copy the executable binary to a path in your `$PATH` variable, for example:

```
sudo mv nextflow /usr/local/bin
```

Now running `nextflow` brings up the docstrings.

# Nextflow basics

This section describes basic concepts to start using Nextflow and designing new
workflows.



# Workflow examples


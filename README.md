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

## Vocabulary

First there are several key terms to know:

* **Process** -- An individual task which can be written in any scripting langauge (e.g. R, Python, Bash, Perl, etc.). Processes are discrete independent units in a workflow and don't share a writable state, but they can communicate in Nextflow via channels. An example process looks like:

```
process blastSearch {
  input:
    path query
    path db
  output:
    path "top_hits.txt"

    """
    blastp -db $db -query $query -outfmt 6 > blast_result
    cat blast_result | head -n 10 | cut -f 2 > top_hits.txt
    """
}
```

* **Channel** -- This is a queue by which independent processes communicate in Nextflow. A
process can have multiple channels, and these can be organized as input and output. Channel declarations are vital because they essentially make up the workflow by specifying how processes communicate and how tasks are executed. In the above process, the channels are specified as:

```
input:
    path query
    path db
  output:
    path "top_hits.txt"
```

* **Communication link** -- One of the ways channels facilitate communication between processes is when the output of one process is specified as the input to another process. This relationship, called a communication link, also implicitly establishes a sequence in which processes are executed. For example, the following process has established a communication link with the above process `blastSearch` because it takes the contents of `top_hits.txt` as its input.

```
process extractTopHits {
  input:
    path top_hits

  output:
    path "sequences.txt"

    """
    blastdbcmd -db $db -entry_batch $top_hits > sequences.txt
    """
}
```

* **Reactivity** -- Downstream processes wait to run until outputs from upstream processes with a communication link are detected. In other words, processes run reactively in response to upstream outputs which are used as downstream inputs.

* **Executor** -- This is an abstraction describing how a workflow script is actually run
on a give system, where the local system is used by default. This is where batch management
system parameters are furnished to schedule batch runs on HPC clusters (e.g. SGE, Slurm, etc. are supported). Cloud platforms are also supported (e.g. AWS, etc.).

* **DSL** -- Nextflow's scripting language, which is meant to augment code from other more
common scripting langauges to minimize the learning curve. Basic scripting examples are shown [here](https://www.nextflow.io/docs/latest/script.html). DSL derives from Groovy, which derives from Java but is meant as a higher-level language that simplifies Java 
syntax (analogous to Python versus C/C++/etc.).

## Example workflow

```
// Declare syntax version
nextflow.enable.dsl=2
// Script parameters
params.query = "/some/data/sample.fa"
params.db = "/some/path/pdb"

process blastSearch {
  input:
    path query
    path db
  output:
    path "top_hits.txt"

    """
    blastp -db $db -query $query -outfmt 6 > blast_result
    cat blast_result | head -n 10 | cut -f 2 > top_hits.txt
    """
}

process extractTopHits {
  input:
    path top_hits

  output:
    path "sequences.txt"

    """
    blastdbcmd -db $db -entry_batch $top_hits > sequences.txt
    """
}

workflow {
   def query_ch = Channel.fromPath(params.query)
   blastSearch(query_ch, params.db) | extractTopHits | view
}
```

## Configuration file

This file lives in the same directory as the pipeline execution directory. It species the executor, environment variables, pipeline parameters, etc. An example `nextflow.config` file is:

```
process {
  executor='sge'
  queue = 'cn-el6'
}
```

## DSL scripting

DSL scripting basics are covered [here](https://www.nextflow.io/docs/latest/script.html).

An important concept is how multi-line text and code is handled. First, multi-line text
is managed using flanking `"""` or `'''` symbols, e.g:

```
text = """
	my first line of text
	my second line of text
	"""
```

Multi-line code is handled similariy, where the `\` symbol is used to connect multiple
lines of input as a single command, similar to bash.

```
myLongCmdline = """
    blastp \
    -in $input_query \
    -out $output_file \
    -db $blast_database \
    -html
    """

result = myLongCmdline.execute().text
```

## Running a workflow

Nextflow workflows are contained in files with `.nf` extension. Executing a workflow
is as simple as:

```
nextflow run <workflow_name>.nf
```

The `-resume` operator can be used to restart a workflow when debugging.

## Starting workflow

Let's make a small example workflow. This will contain a single rule that stores a
string in a series of files.

Make a new workflow called `simpleworkflow.nf` using

```
touch simpleworkflow.nf
vi simpleworkflow.nf
```

Press `i` to change to insert mode, and write the following example workflow:

```
#!/usr/bin/env nextflow
nextflow.enable.dsl=2

params.str = 'hello world'

process splitLetters {
	input 
		params.str
	output:
		path 'newfile_*'

	"""
  	printf '${params.str}' | split -b 4 - newfile_
  	"""
}

workflow {
	splitLetters
}
```

We can now run `simpleworkflow.nf`:

```
nextflow simpleworkflow.nf
```

This returned:

```
N E X T F L O W  ~  version 22.04.4
Launching `simpleworkflow.nf` [prickly_borg] DSL2 - revision: 1c88026b60
```
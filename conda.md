# Conda on Hydra
Last updated December, 2019

## Conda and package management

### What is package management?
- Tool for software installs and updates using predefined rules
- Reproducible
- Simplified
- Common package management systems: `yum`, `apt`, `conda`, `brew`

### [Conda](https://docs.conda.io/)
- Free & open source
- Large community in data science and biology
  - Started in Python community (hence snake reference) for data science
- No admin privileges required to install software!
- Main development by a commercial company, Anaconda, that offers paid versions

## Installing conda on Hydra

### Version of Anaconda/Miniconda
- Anaconda: distribution of conda with many data science tools pre-bundled
- Miniconda: minimal distribution of conda, other packages can be added: **What we’ll use**

### Installing
- Where to install? home directory (no scrubbing!)
1. Download: We want the Linux, 64-bit, Python3 installer found on this page: https://docs.conda.io/en/latest/miniconda.html (Note: We reccomend getting the Python3 installer even if you need Python2 for some programs. You can setup a separate Python2 environment)
```
$ wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
```

2. Run installer. Make sure to enter "yes" to "running conda init":
```
$ sh Miniconda3-latest-Linux-x86_64.sh
...
Do you accept the license terms? [yes|no]
[no] >>> yes
...
[/home/user/miniconda3] >>> [press enter key]
PREFIX=/home/user/miniconda3
...
Do you wish the installer to initialize Miniconda3
by running conda init? [yes|no]
[no] >>>yes
```

3. Exit Hydra and re-login
4. Look for `(base)` at the beginning of your command prompt. Test with: `which conda`
```bash
(base) $ which conda
~/miniconda3/bin/conda
```

### What did the installer do?
- `~/miniconda3/`: where all the conda programs are installed
- `~/.bashrc` (this file is run when you start a new bash shell): a command has been appended to enable conda (modifying your `$PATH`) and changing your prompt.
- `~/.conda/` and `~/.condarc`: hidden directory and file with settings

## Packages
- A conda package is the precompiled program and a link to all of the required packages that will also be downloaded and needed.
- A whole pipeline can be defined in a package: all the scripts and dependent software (e.g. qiime2 or phyluce)
- Packages are created by people in the community and shared publicly. Sometimes they’re created by the software developer, but they’re often by users of the software.
- You can create your own packages which a great method for reproducible science and collaboration. We’re not covering that, but we can direct you to resources.

### `conda` command

We'll be using the `conda` command for managing packages and controlling conda

#### Help with conda
- `conda help`
- `conda install --help`
- Online documentation: https://docs.conda.io/projects/conda/en/latest/commands.html

### Listing installed packages
Some packages come pre-installed with miniconda (there would be a lot more if you were using the Anaconda installer)

```bash
(base)$ conda list
# packages in environment at /home/user/miniconda3:
#
# Name                    Version                   Build  Channel
_libgcc_mutex             0.1                        main
asn1crypto                1.2.0                    py37_0
ca-certificates           2019.10.16                    0
certifi                   2019.9.11                py37_0
cffi                      1.13.0           py37h2e261b9_0
chardet                   3.0.4                 py37_1003
conda                     4.7.12                   py37_0
conda-package-handling    1.6.0            py37h7b6447c_0
cryptography              2.8              py37h1ba5d50_0
...
```

### Installing a package
`conda install ...` installs a packages and its dependencies
- It doesn’t matter what your current directory is, it will install in your miniconda directory!


```bash
(base)$ conda install biopython

The following packages will be downloaded:
...
The following NEW packages will be INSTALLED:
...
Proceed ([y]/n)? y
Preparing transaction: done
Verifying transaction: done
Executing transaction: done
```

Test it:
```
(base)$ python
>>> import Bio
[no error is a good thing!]
>>> exit ()
```

### How to find conda packages

You can search the public package repositories at  https://anaconda.org/

Or do a web search: "conda r" or "bioconda mafft"

- Search for admixtools on anaconda.org
  - Only available on bioconda
  - `conda install -c bioconda admixtools`
- Search for mafft on anaconda.org
  - Available on multiple channels
  - `bioconda`, `conda-forge`, `anaconda`, `r` are reliable channels. You can try other popular ones.

### Add `bioconda` and `conda-forge` to your channels

```
(base)$ conda config --add channels defaults
(base)$ conda config --add channels bioconda
(base)$ conda config --add channels conda-forge
```

Note: the more channels you have, the increased time to "solve" installation.

```
(base)$ conda install mafft

...
The following packages will be downloaded:

    package                    |            build
    ---------------------------|-----------------
    ca-certificates-2019.11.28 |       hecc5488_0         145 KB  conda-forge
    certifi-2019.11.28         |           py37_0         148 KB  conda-forge
    conda-4.7.12               |           py37_0         3.0 MB  conda-forge
    mafft-7.453                |       h516909a_0         1.2 MB  bioconda
    openssl-1.1.1d             |       h516909a_0         2.1 MB  conda-forge
    ------------------------------------------------------------
                                           Total:         6.5 MB
...
```

### conda install tricks
- Installing multiple items
  - `conda install mafft gblocks`
  - “It is best to install all packages at once, so that all of the dependencies are installed at the same time.” https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-pkgs.html
- Specifying program versions
  - `conda install mafft=7.2`
  - `=7` matches the most recent version starting with `7`
  - `==7.221` matches exactly the version specified
  - `">7.2"` matches most recent version greather than 7.2 (in quotes because of the `>` which will interfere with the shell's redirection features)
  - `"<7.3"` matches most recent version less than 7.3 (in quotes because of the `<` which will interfere with the shell's redirection features)
  - See the [conda documentation](https://docs.conda.io/projects/conda-build/en/latest/resources/package-spec.html#package-match-specifications) for more information
  - Show available versions with: `conda search mafft`

## Using your miniconda install in submitted jobs

There are a couple steps to utilize your miniconda install in your submitted jobs. By default the installed programs won't be found.

### Create module file

Did you know you can create your own module files? We'll set one up for your miniconda install. Module files are text files with instructions on how the current environment should be modified.

We suggest creating a directory in your home folder called `modulefiles` and creating a module called miniconda in that directory.

```
(base)$ mkdir -p ~/modulefiles
(base)$ cat <<EOF >~/modulefiles/miniconda2
#%Module1.0
prepend-path PATH /home/user/miniconda3/bin
EOF
```
:exclamation: Make sure to change `user` with your Hydra username.

### Job file using your module file

You can use the `module load` command followed by the path and name of your module file to load your module.

For example:
```bash
# /bin/sh
# ----------------Parameters---------------------- #
#$ -S /bin/sh
#$ -q sThC.q
#$ -l mres=2G,h_data=2G,h_vmem=2G
#$ -cwd
#$ -j y
#$ -N minicondatest
#$ -o minicondatest.out
#
# ----------------Modules------------------------- #
module load ~/modulefiles/miniconda
#
# ----------------Your Commands------------------- #
#
echo + `date` job $JOB_NAME started in $QUEUE with jobID=$JOB_ID on $HOSTNAME
echo + NSLOTS = $NSLOTS
#
echo "Conda location:"
which conda

echo
echo "Installed packages:"
conda list

#
echo = `date` job $JOB_NAME done
```

## Wait, there's one more concept to keep things clean... `environments`
So far all installs we've done have done into one set of packages called `(base)`. What if programs have conflicting dependencies or you need different versions of the same program?

A conda Environment is a compartmentalized set of packages:

| **Best practice is to create an environment for each pipeline** |
| --- |

Also, the more packages you have, the longer it will take for conda on the "Solving Environment" step.

Let's try a more complex install.

The [Hybpiper](https://github.com/mossmatters/HybPiper) pipeline doesn't have its own bioconda package (you could create one and contribute it to bioconda...), but we can use conda to install the dependencies in a new environment that we'll create and then add packages to.

### Creating an environment

```
(base)$ conda create -n hybpiper
Collecting package metadata (current_repodata.json): done
Solving environment: done
## Package Plan ##
  environment location: /home/user/miniconda3/envs/hybpiper
Proceed ([y]/n)? y

Preparing transaction: done
Verifying transaction: done
Executing transaction: done
#
# To activate this environment, use
#
#     $ conda activate hybpiper
#
# To deactivate an active environment, use
#
#     $ conda deactivate
```

### Using the new environment

Although the output says to use `conda activate...`, we recommend using `source activate...` because it is compatible with job files submitted through `qsub`

```
(base)$ source activate hybpiper
```

**Adding packages (and specifying verison numbers)**
The [install instructions](https://github.com/mossmatters/HybPiper/wiki/Installation) list these requirements:

- Python 2.7 or later (can be python3)
- BIOPYTHON 1.59 or later
- EXONERATE
- BLAST command line tools
- SPAdes
- GNU Parallel
- BWA
- samtools


```
(hybpiper)$ conda install python=3 "biopython>1.59" exonerate blast spades parallel bwa samtools

Collecting package metadata (current_repodata.json): done
Solving environment: done

## Package Plan ##

  environment location: /home/user/miniconda3/envs/hybpiper

  added / updated specs:
  - biopython[version='>1.59']
  - blast
  - bwa
  - exonerate
  - parallel
  - python=3
  - samtools
  - spades

The following packages will be downloaded:
...
The following NEW packages will be INSTALLED:
...
Proceed ([y]/n)? y
...
Downloading and Extracting Packages
...
```

Now that the dependencies are installed, you can download the scripts for HybPiper:
```
cd ~
git clone https://github.com/mossmatters/HybPiper.git
```

When you're done using the environment, you can go back to `(base)` with: `source deactivate`

### Removing an environment

To remove an environment and all of its packages:

*Make sure to deactivate the the environment before removing it*
```
(hybpiper)$ source deactivate
(base)$ conda remove -n hybpiper --all

Remove all packages in environment /home/user/miniconda3/envs/hybpiper:

## Package Plan ##

  environment location: /home/user/miniconda3/envs/python2

The following packages will be REMOVED:
...
Proceed ([y]/n)? y
```

## Using miniconda in your jobs

## Appendix

### Definitions
- conda: open source package management tool
- Anaconda®: commercial company that develops conda
- Anaconda: distribution of conda with many data science tools pre-bundled
- Miniconda: minimal distribution of conda, other packages can be added, **What we reccomend**
- Channel: grouping of packages managed by one group (e.g. bioconda, conda-forge)

### Channels

- `Main`: built and maintained by anaconda (setup by default)
- `R`: R and packages for it (setup by default)
- `Conda-Forge`: community contributions
- `Bioconda`: contributions from biological community, find packages on https://bioconda.github.io
- Other channels...

# RStudio on Hydra

(Mostly borrowed from https://rocker-project.org/use/singularity.html)

These steps can all be done on the login node...
- Create a directory in your HOME called something like rstudio-singularity
- Run `module load singularity` to load the singularity (https://en.wikipedia.org/wiki/Singularity_(software) module
- Run `singularity pull docker://rocker/rstudio:4.2`. This will download a file called rstudio_4.2.sif.
- Run theses commands to create required RStudio directories:
  - `mkdir -p run var-lib-rstudio-server`
  - `printf 'provider=sqlite\ndirectory=/var/lib/rstudio-server\n' > database.conf`

- Now connect to the interactive queue with the command `qrsh -pe mthread 2` (or with the number of CPUs you want available)

This command will run RStudio on the interactive queue on HPC port 8000 with the username USER (provide your own), and password "password":
```
PASSWORD='password' \
singularity exec --userns --bind run:/run,var-lib-rstudio-server:/var/lib/rstudio-server,database.conf:/etc/rstudio/database.conf \
  rstudio_4.2.sif /usr/lib/rstudio-server/bin/rserver \
  --auth-none=0 --auth-pam-helper-path=pam-helper \
  --server-user=USER --www-port=8000
```

You will get some sort of message similar to:
```
INFO:    Convert SIF file to sandbox...
TTY detected. Printing informational message about logging configuration. Logging configuration loaded from '/etc/rstudio/logging.conf'. Logging to 'syslog'.
```

In a terminal interface **on your own machine, after connecting to the Smithsonian VPN**, enter the following command. Be sure to use the computer node you are on, and sub in your Hydra username:
- `ssh -N -L 8000:compute-XX-XX:8000 USER@hydra-login01.si.edu`

You will be prompted to enter your USER@hydra-login01.si.edu password, so enter that. If successful, there will be no output.

But now go to your browser to http://localhost:8000/. You should see an RStudio login interface:

![Screenshot 2022-11-28 153640](https://user-images.githubusercontent.com/472677/204376997-c5ca39fa-29dd-42e1-ac7e-9645a3a98f1b.jpg)

Sign in using your HPC username, and whatever password you set in the long command above, and then you should be put into an RStudio environment.

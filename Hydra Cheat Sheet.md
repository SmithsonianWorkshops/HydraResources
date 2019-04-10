# Hydra Cheat Sheet

1. **Login to hydra**
    Choose one of the login nodes:  
    
    `ssh myusername@hydra-login01.si.edu`
    or
    `ssh myusername@hydra-login02.si.edu`

    You will be prompted to enter your password. 

    | Very important: never login to the head node (hydra-4) to submit jobs. |
    | --- |

2. **If I forgot my password or locked my account?**  
    Send an email to  SI-HPC-Admin@si.edu to request a new password

3. **How do I change my password?**  
You need to change your password every 90 days.  

    a. Login to hydra-login01.si.edu: `ssh myusername@hydra-login01.si.edu`  
    b. Type in the password command: `passwd`  
    c. You'll be prompted to enter your **current** password (once)  
    d. Then  you enter your **new** password (twice)

    After that, you need to repeat the same procedure in the head node (hydra-4).   
    a. ssh to the head node
    `ssh hydra-4`  
    b. Repeat the same procedure and type exit to return to either hydra-login01 or hydra-login02
    `exit`

1. **Where am I when I log in?**  
    In your home directory: `/home/myusername`  
    (you can always type `pwd` to find where you are)

1. **Where are my files?**  
    Your files should be in the directory: `/pool/genomics/myusername`  
    You might also have files in `/scratch/genomics/myusername` or `/data/genomics/myusername`

1. **Which programs are available on hydra?**  
    `module avail`  
    It will output a list of software/modules available on hydra

1. **How do I load a module?**
    `module load module/name`

    Example: to load bwa v. 0.7.17, you would type:
    `module load bioinformatics/bwa/0.7.17`

    You can find out more about the module (like the name of the executable) with:
    `module help module/name`

1. **What if the program I need is not listed, can I install it on Hydra?**  
    Yes. Try compiling any new software you need in your own space and feel free to reach out with questions.

1. **How do I start an analysis?**  

    You can create a job file either using `nano` or the [Qsub Generator](https://hydra-4.si.edu/tools/QSubGen/), which is a web tool to create job files.
    
    You can submit a job using the command  
    `qsub jobfile.job`   

    OR 
    
    You can use the interactive node for light-weight jobs. To access the interactive node, just type:  
    `qrsh`  
    When you type qrsh, you go back to your home directory. 

    The memory available without any additional parameters is 8GB. To increase this values use:  
    `qrsh -pe mthread N`  
    where N is between 2 and 32, to use 2 to 32 slots or 16 to 256 GB of memory.

    In both cases, you need to load the necessary modules, either in the job file, or by typing `module load [...]` in the interactive node.

1. **What are the available queues?**  
    Queues are divided by how long a job can run, and also the amount of RAM available per CPU. 

    * Short (s): up to 7 hours
    * Medium (m): up to 6 days
    * Long (l): up to 30 days
    * Unlimited (u): well... unlimited
    
    The queues are also divided into high CPU (up to 6GB per CPU) or high memory (above 6 GB). Here's an example:  
   
    ```
    # ------------Parameters------------------ #
    #$ -S /bin/sh
    #$ -pe mthread 4 #(multithread w/ 4 CPUs)
    #$ -q mThC.q #(medium Time, high CPU)
    #$ -l mres=6G,h_data=6G,h_vmem=6G
    ```
    | Important: more memory is not necessarily better if the software can't use it efficiently. Jobs are prioritized based on queue, so low-RAM jobs will move through the queue faster. Inefficient jobs are flagged by the Hydra admin. |
    | --- |

1. **How do I check my job status?**  

    To check the status of all of your jobs:  
    `qstat -u myusername`

    To check the status of an specific job:  
    `qstat -j job_id_number`

    You can find the job number in the log files, or when you type `qstat`.

1. **How do I kill a job?**  
    `qdel job_id_number`

1. **After my job is done, how to check its parameters, memory used, etc?**  
    `qacct -j job_id_number`

1. **My job failed… Why?**  
    a. Check the log file. Many programs will output the errors, which help understand the issue. Common issues are: misspelled filenames, wrong paths, etc.  
    b. Check your job using the `qacct` command (previous question). If maxvmem is the same as the memory you requested, your job probably used too much memory and was killed   
    c. Check which queue was used (s, m, l, u) and if the job ended right at the time limit of the queue.  
    d. If none of those help answer your question, ask for help :) 

1. **How do I check my disk quota on Hydra?**  
    You need to load the module tools/local  
    `module load tools/local`

    To check your quotas without current usage:  
    `show-quotas.pl -u myusername`

    To check your quotas with current usage:  
    `parse-quota-report.pl -u myusername`

    | Important: Files older than  180 (pool) or 90 (scratch) days are scrubbed. Files in your home directory and `/data/genomics` are never scrubbed but disk quotas are smaller. |
    | --- |
    
1. **How do I copy my files from Hydra to a local computer?**  
    Run this command from a local (aka not connected to Hydra) terminal window:  
    
    `scp myusername@hydra-login01.si.edu:/hydra-path/file destination-on-my-computer`  
    
    (for directories, use the flag -r after scp)

1. **My files were scrubbed. What do I do?**  
    Please refer to [this page](https://confluence.si.edu/display/HPC/Disk+Space+and+Disk+Usage) for more information on how to request files to be restored (requests must be received by the Friday following scrubbing).

1. **How do I use Dropbox to backup my files?**  
    Please refer to [this page](https://confluence.si.edu/pages/viewpage.action?pageId=40140823#DiskSpaceandDiskUsage-HowToCopy) for more information.  

1. **How do I find help?**
    * **Wiki**: [https://confluence.si.edu/display/HPC](https://confluence.si.edu/display/HPC)
    * **Email**: SI-HPC@si.edu 
    * **GitHub**: previous workshops tutorials [https://github.com/SmithsonianWorkshops](https://github.com/SmithsonianWorkshops)
    * **In person**: bioinformatics brown-bag (every Thursday at 12 pm, NMNH W107)

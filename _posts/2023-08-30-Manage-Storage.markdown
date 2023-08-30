Add your personal favorite tools for managing disk space!

General usage - consider whether you can run your workflow on gscratch - `/home/exacloud/gscratch/CEDAR`   
+ It's likely to run faster
+ There is no disk replication, snapshot - it's meant to be a working directory, not a storage space.
+ Clean up after yourself!! This is not a storage space :)

To check free space on gscratch
```
cd /home/exacloud/gscratch/CEDAR
lfs quota -h -p 3901 .
```

Quickly check the amount of disk space available in `/home/groups/CEDAR`    
```
$ df -h | grep CEDAR
```

Quantify disk usage by folder - this takes a while (hours), and you might want to sbatch it (but at least run from an interactive session).   
```
$ du -sh /my/folder
```

You can use the script below, but I would recommend adjusting the workdir to a more targeted space

```
#!/bin/bash
## Job Name
#SBATCH --job-name=run_CEDARMem
## Allocation Definition
#SBATCH --account=cedar
## Nodes
#SBATCH --nodes=1
#SBATCH --time=24:00:00
## Memory
#SBATCH --mem=10G
## Specify the working directory for this job
#SBATCH --workdir=/home/groups/CEDAR/
## Turn on e-mail notification
#SBATCH --mail-type=ALL
#SBATCH --mail-user=chitsaza@ohsu.edu


du -hs * > cedarTOTALMem_March152020
```


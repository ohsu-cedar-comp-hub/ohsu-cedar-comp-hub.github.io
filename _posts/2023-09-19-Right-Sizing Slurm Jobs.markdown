# Right-Sizing Slurm Jobs

<aside>
💡 If you find anything that is confusing, incomplete, or just plain wrong in this document - clean it up!
</aside>

</aside>

💡 If you have a useful monitoring tool, add it!
</aside>

Before you run your job on multiple samples - make sure that you’ve optimized the resource request.

There are two main resources that you can request: CPU(s) and RAM

### CPU (aka `--cpus-per-task`)

The CPU is the central processing unit - the thinker. To make use of more than 1 CPU, your code needs to have been developed to work in parallel. Some R and Python packages can take advantage of parallel processing, but may require an accessory package. 

**Takeaway #1:**  If your tools were not designed to take advantage of parallel processing, adding CPUs to your job request will only slow down your resource request without speeding up the actual processing - it’s counter productive.

************************Takeaway #2:************************  If your tools were designed for parallel processing, and you specify more than 1 CPU in your arguments, you MUST specify the number of CPUs in your SBATCH command.

**DIY** - https://berkeley-scf.github.io/tutorial-parallelization/

### RAM (aka `--mem-per-cpu`)

RAM is volatile memory used for your working tasks. It’s way faster than disk access. If you don’t have enough RAM to hold your object, your job may suffer from a lot of shuffling of data to/from disk. Each node on Exacloud has a different quantity of RAM - use `scontrol` to find out how much if you need a lot.

Gentle reminder about prefixes -

RAM is quantified in **B**ytes (each of which is 8 **b**its in the olden days…). 

- 1000 bytes = 1KB (kilo)
- 1000000 bytes = 1000KB = 1MB (mega)
- 1000MB = 1GB (giga)
- 1000GB = 1TB (tera)
- 1000TB = 1PB (pita)
- It goes on - google it! (useful for astronomers, but even we have not filled this much space YET!)

### Process

Consider developing your workflow on a toy data set so you don’t have to wait forever to learn you’ve made a mistake…

Once it’s stable, run a sample. Request more resources than you think you need.

Once the job is complete, run this command:

```bash
sacct --units=G --format=JobIdRaw,JobName%30,User,Group,State,Submit,Start,End,Cluster,Partition,AllocNodes,ReqCPUs,AllocCPUs,TotalCPU,CPUTime,UserCPU,AveCPU,SystemCPU,Elapsed,Timelimit,ReqMem,MaxRSS,MaxVMSize,State,MaxDiskWrite,MaxDiskRead,CPUTimeRaw,ElapsedRaw,TimelimitRaw,SubmitLine --parsable2 -a -A cedar,cedar2 --starttime=2023-08-25 --endtime=2023-09-10 --user={yourID} > sacct_2023.09.15.txt
```

Keep in mind that sacct “polls” usage, and so is not a perfect reflection of what you are using. 

<aside>
💡 The right solution is likely to be a combination of factors - but remember, the more resources you request, the longer it will take to allocate them, the more you will impact the fair share for all of CEDAR. Low fair share further impacts our priority on exacloud. 

Request the resources you need and use them…

</aside>

<aside>
💡 Making sure you have enough working space BEFORE you execute a workflow (and requesting additional space as needed) will prevent crashing all of CEDAR when you exceed disk quota.

</aside>

## What to look for?

### How long was my job queued? How long did it run for?

Job 12 below was submitted and waited 36 hours in the queue, then ran in about 4 hours.

Job 15 got resources after about 1 second (this reflects far fewer resources requested)

| JobIDRaw | Submit | Start | End |
| --- | --- | --- | --- |
| 12 | 2023-08-19T20:08:29 | 2023-08-21T08:08:36 | 2023-08-21T12:33:41 |
| 12.batch | 2023-08-21T08:08:36 | 2023-08-21T08:08:36 | 2023-08-21T12:33:41 |
| 15 | 2023-08-20T10:31:32 | 2023-08-20T10:31:33 | 2023-08-20T10:37:22 |
| 15.batch | 2023-08-20T10:31:33 | 2023-08-20T10:31:33 | 2023-08-20T10:37:22 |

### How much RAM (memory) did I ask for? How much did I use? The more resources you request, the longer it will take to allocate them, the more you will impact the fair share.

Job 41 requested 320GB, but used 16G - efficiency of ~5%. 

Job 521 requested 32G, used 11G, about 30% efficiency.

Regarding MaxRSS vs MaxVMSize - from https://supercloud.mit.edu/submitting-jobs

<aside>
💡 MaxRSS is the maximum resident memory (maximum memory footprint) used by each job array job, while MaxVMSize is the maximum memory that was requested by the process (the peak memory usage). In other words, MaxVMSize is the high-watermark of memory that was allocated by the process, regardless of whether it was used or not. The MaxRSS size is the maximum physical memory that was actually used.

</aside>

| JobIDRaw | ReqMem | MaxRSS | MaxVMSize |
| --- | --- | --- | --- |
| 41 | 320G |  |  |
| 41.batch |  | 11.19G | 15.50G |
| 521 | 32G |  |  |
| 521.batch |  | 10.37G | 10.67G |

### How much CPU did I ask for? How much did I use?

There are a lot of numbers that can be reported. Some key ones

- Elapsed - this is the time that the job took
- CPUTime = the Elapsed time * the number of CPUs allocated
- TotalCPU = UserCPU + SystemCPU
    - UserCPU - how long you were waiting for + using resources for your code
    - SystemCPU - how much time the system kernel was working (eg, I/O)

Efficiency is TotalCPU/CPUTime - 

- Job 328 efficiency ~ 98% - it’s big and long, but using resources well.
- Job 3281 efficiency  1:26:50/4:55:24 ~ 30%
- Job 4429 efficiency - 0:2:13/1:02:40 ~3%

| JobIDRaw | ReqCPUS | AllocCPUS | TotalCPU | UserCPU | AveCPU | CPUTime | SystemCPU | Elapsed |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 328 | 1 | 44 | 65-08:02:27 | 57-01:51:38 |  | 65-08:18:24 | 8-06:10:48 | 1-11:38:36 |
| 328.batch | 44 | 44 | 65-08:02:27 | 57-01:51:38 | 65-07:58:37 | 65-08:18:24 | 8-06:10:48 | 1-11:38:36 |
| 3281 | 4 | 4 | 1:26:50 | 1:09:30 |  | 4:55:24 | 17:20.0 | 1:13:51 |
| 3281.batch | 4 | 4 | 1:26:50 | 1:09:30 | 1:26:12 | 4:55:24 | 17:20.0 | 1:13:51 |
| 4429 | 16 | 16 | 02:12.6 | 01:46.1 |  | 1:02:40 | 00:26.5 | 0:03:55 |
| 4429.batch | 16 | 16 | 02:12.6 | 01:46.1 | 0:00:47 | 1:02:40 | 00:26.5 | 0:03:55 |

### How much did I write to disk?

Estimate how much space you need to execute your workflow. 

********************************This would benefit from some estimated/actual usage info!********************************

| JobIDRaw | MaxDiskWrite | MaxDiskRead |
| --- | --- | --- |
| 3 |  |  |
| 3.batch | 593.49G | 850.14G |
| 325 |  |  |
| 325.batch | 71.39G | 72.03G |

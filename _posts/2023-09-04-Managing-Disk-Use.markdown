### Before You Start A Project
1. Talk with your team regarding the estimated number of samples you will process
2. Review pilot data in its rawest form - how big is it?? Is this the format that you want to archive? Can it be compressed?    
   For example, truly raw sequencing data is in the bcl format; it's huuuuuuge. Step 1 is conversion to a fastq.gz, which is still big, but smaller.

### As You Develop Your Workflow
1. Do you _really_ need to uncompress data?
2. Where do you store any interim files?
   Consider gscratch `/home/exacloud/gscratch/CEDAR` for faster I/O, but NOT FOR STORAGE!!!
   Bonus points - consider local/node scratch `/mnt/scratch` for the fastest I/O, but make sure you delete any files at the conclusion of your workflow.
3. Know how much disk space you're using -
   ``` sacct --units=G --format=JobIdRaw,Submit,Start,End,AllocNodes,ReqCPUs,AllocCPUs,TotalCPU,ReqMem,AveRSS,MaxRSS,AveVMSize,MaxVMSize,User,State,AveDiskWrite,MaxDiskWrite,NTasks --parsable2 -a -A cedar, cedar2 --starttime=2023-08-29```
   

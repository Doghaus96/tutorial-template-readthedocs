
### find historical info on cpu time for a user or group, useful to see if a new user has logged in. 
#### User
```shell
sreport cluster AccountUtilizationByUser user=$(whoami) Start=$(date -d "-1 month" +%Y-%m-%d)
```
#### Top users in groups
```shell
sreport cluster AccountUtilizationByUser Accounts=group0,group1,group2 Start=$(date -d "-1 month" +%Y-%m-%d)
```

### look at and compare queue times
```shell
sacct -X --starttime $(date -d "-1 month" +%Y-%m-%d) --format=User,JobID,time,submit%-20,start%-20,end%-25,elapsed,
```

### find info on a user
```shell 
sacctmgr list user -s $(whoami) format=user%-20 format=DefaultAccount%-20 format=Account%-20;
```

### find info on what we would call groups (but slurm calls them accounts)
```shell 
sacctmgr show account <group_name> -s format=user%-30;
```

### find info on a job thats running
```shell 
scontrol show job $jobid | grep --color -E 'TimeLimit|UserId|GroupId|Partition|JobState';
```
### Find job name by jobid
```shell
sacct -j $jobid
```
- #### find which nodes a job ran on by jobid add this:
	```shell
	-o jobid,user,account,cluster,nodelist
	```
### find jobid by user set name
```shell
sacct -u <name.n> -o JobId%16,JobName%20 | grep <jobname>
```
- #### for historical search add a start time to begin the search from
	```shell 
	--starttime $(date -d "-1 month" +%Y-%m-%d)
	```
- #### to limit sacct to only show the jobID (not job steps) add a -X
```shell
sacct -u <name.n> -X -o JobId%16,JobName%20 --starttime $(date -d "-1 month" +%Y-%m-%d)
```
### show $users jobs in queue/running
```shell
squeue -u $user
```
### watch $users job run, updating every n seconds
```shell
watch -n 2 squeue -u $user
```
### Cancel pending jobs (in case you submit too many)
```shell 
scancel --state=PENDING --user=<name.n>
```

### show Nodes in states other than "none" i.e. down/broken 
```shell 
sinfo -a -N -o "%10N %10P %E " | grep -v none
```

### what sinteractive actually does
```shell
salloc -n1 -p test srun --x11 --pty bash
```
### look at users sbatch script (admin only)
```shell 
sudo sacct -B -j <jobid>
```
### find what nodes a users jobs ran on, looking for failed states on problem nodes
```shell
sacct -u name.n -X --format=JobId,Partition,state,nodelist
```

### [Job Reason Codes](https://slurm.schedmd.com/squeue.html#lbAF)
### [Job State Codes](https://slurm.schedmd.com/squeue.html#lbAG)

## sacct
```shell
-a # all jobs
-b # brief 
-g # specify a group to look at
-N # Display jobs that ran on any of these node(s)
-s # state of jobs PD=pending R=running CP=completed
```
example: Will show all jobs PENDING for stat-grad
```shell
sacct -a -g stat-grad -s PD
```

## sacctmgr
```shell
list users # will show a list of users and default accounts
list account # show a list of accounts/groups
```
#### for a list of all users with groups
```shell
sacctmgr list users format=user%-20 format=DefaultAccount%-30
```
#### for a single user 
```shell
sacctmgr list user $(whoami) format=user%-20 format=DefaultAccount%-30
```
#### list all groups 
```shell
sacctmgr list account format=account%-40 format=description%-40
```
#### show users in a given group (account)
```clike
sacctmgr show account domain-users -s format=user%-20 format=account%-30
```
#### Adding users
```shell
sacctmgr add user name.n account=group-name # -i flag is like assumeyes
```
removing users
```shell
sacctmgr remove user name.n account=group-name 
```
### Changing accounts and Default accounts
##### add the account to the user
```shell 
sudo sacctmgr add user name.n account=new-group
```
##### modify the Default account
```shell
sudo sacctmgr modify user where user=name.n set defaultaccount=new-group
```
##### (optional) Remove the old entry 
```shell
sacctmgr remove user where user=name.n and account=old-group
```
##### Adding new slurm group
```shell
sudo sacctmgr add account 
```

##### Running a job as a different account
```shell
sinteractive -p stat -A stat-users
```
- specify the partition and account and it should work. 

## scontrol
### extending wall time
```shell 
sudo scontrol update jobid=****** TimeLimit=21-00:00:00 #Days-Hours:Minutes:Seconds
```

## running jobs
### sinteractive
```shell
-p # partition of where to run job (default: batch)
-N # number of nodes to request (default: 1)
-n # number of tasks to request (default: 1)
-c # number of CPU cores to request (default: 1)
-m # memory per CPU (default: Partition default)
-M # memory per node (default: Partition default)
-g # number of GPUs to request (default: None)
-G # GRES to request (default: None)
-L # Licenses to request (default: None)
-t # Time limit (default: Partition default)
-J # job name (default: interactive)
-A # Account
-w # node name
```

## checking running job status
#### squeue
```shell
-r  # array
-u  # user
-A  # account 
-w  # node or nodelist
-t  # state code 
```
##### [[Slurm Job State Codes]]
```shell
watch squeue -u $(whoami)
```
##### watch accepts a -n to specify interval, defaults to 2.0 seconds
```shell
watch -n 1 squeue -u $(whoami)
```
##### see currently running jobs
```shell
squeue -t R
```
##### see a short overview of jobs running in which partitions, omitting the ones with 0 running
```shell
qstat -q | grep -v '0   0'
```

#### Job Efficiency 
```shell
seff $jobID
```

#### node health
##### show partition states
```shell
sinfo -p batch
```
##### show reasons why a node is a certain state
```shell
sinfo -n u141 --list-reasons
```
```shell
sinfo -n u141 -N -a -l
```
#### sinfo
```shell
-a # --all Display information about all partitions.
-n #  --nodes=<nodes>
-p # --partition=<partition>

-N # --Node Print information in a node-oriented format 
-l # --long Print more detailed information.
-s # --summarize
```
```shell
sinfo -n u021 -N -a -l
```

## global configuration of defaults
```clike
scontrol show config
```
## changing slurm node status
```shell 
sudo scontrol update nodename=lci-compute-19-[1-2] state=idle
```
```shell
sudo scontrol update nodename=compute1 state=drain reason='maint'
```

## Show job script
```shell
sudo sacct -B -j <jobid>
```

# Job Diagnosis in Moab & Slurm

## Moab/Torque

```bash
groups; id
```
Linux groups that the user belongs. Shows access to `/projects/<AllocationID>`.
No change for Slurm.


```bash
homedu
```
Available space in home folder. No change for Slurm.

```bash
checkproject <AllocationID>
```
SUs in the project, expiration date and allocation storage.

```bash
mam-list-accounts -a <AllocationID>
```
Members of an allocation

```bash
mam-list-usagerecords -u <NetID>
```
Record of all jobs in a defined time frame from a user. Wait time, runtime,
resource and charge can be obtained.

```bash
mam-list-usagerecords -a <AllocationID>
```
Record of all jobs in a defined time frame from an allocation. Wait time,
runtime, resource and charge can be obtained.

```bash
showq -u <NetID>; showq -w acct=<AllocationID>
```
Running/Idle/blocked jobs from a user; from an allocation

```bash
showq -c -u <NetID>; showq -c -w acct=<AllocationID>
```
Jobs that ended within last n-days from a user; from an allocation

```bash
qstat
```
Torque equivalent of `showq`. Works in Slurm.

```bash
showres -n -g  | grep <ReservationID>
```
Nodes that belong to a buy-in allocation reservation

```bash
pbsnodes
```
Observation and manipulation of node state for Torque. There is a Slurm
equivalent but not recommended for state manipulation

```bash
checkjob <JobID>
```
Displays detailed job state information and diagnostic output for a specified
job. The information we frequently use are
* User, allocation, queue, job state
* Submission time, start time, wall time, run time, queue time
* Number of nodes, tasks, node ID
* Resource (processor, memory) utilization
* Working directory, location of log&error files
* Submission script
* Hold or rejection reason

Currently we keep 7 days of logs at this level. However any emergency Moab
restart will reset this log.

## Slurm

```bash
checkproject <AllocationID>
```
Same as Moab checkproject except SUs are not included due to allocation model change.

```bash
squeue
```
View information about jobs located in the Slurm scheduling queue.
There is a long list of information that can be obtained
* Allocation, user, partition (=queue)
* State
* Priority
* Reserved nodes
* Submission time, start time, wall time limit,
* Working directory
* Dependencies
* Short reason for waiting job

```bash
sacct -S 2018-10-03-11:40 -E 2019-01-15-8:00 -X -o jobid,user,start,end,state
```
Displays accounting data for all jobs from database or accounting log.
Information similar to showq -c and mam-list-usagerecords can be obtained.

```bash
checkjob <JobID>
```
This is a wrapper which mostly re-formats the jobfile created by epilogue
script. The data is similar to the one you obtain from `sctonrol`.
The information we can obtain includes:
* User, allocation, partition, job state
* Submission time, start time, wall time, run time, queue time
* Number of nodes, tasks, node ID
* **Resource (processor, memory) utilization?**
* Working directory, location of log&error files
* **Submission script?**
* Hold or rejection reason

```bash
scontrol
```
It is used to view or modify Slurm configuration including job, node,
partition, reservation, and overall system configuration. I think holding and
releasing jobs as well as viewing configurations (node, partition, job etc)
will be useful for us.
```bash
scontrol hold <JobID_1,JobID_2,...>
scontrol release <JobID_1,JobID_2,...>
scontrol -d show job <JobID>
scontrol -d show partition short
```
I would leave modification controls untouched or to RCI team.

```bash
sprio --jobs=<JobID_1,JobID_2,...>
```
View the factors that comprise a job's scheduling priority. Works for only
jobs that are not running.

```bash
sview
```
Graphical overview of user's jobs, partitions (=queues) and their limits,
node utilization, node details, slurm configuration details

```bash
sstat --jobs=<JobID_1,JobID_2,...>
```
Detailed information about running jobs. I didn't find it particularly
useful for front end support and I couldn't make sense of some numbers.

```bash
sinfo
```
View information about Slurm nodes and partitions. Useful to obtain quick
data about the state of a node and why

```bash
sinfo -R
```
Shows unavailable nodes and the reason of this state

```bash
sreport
```
Generate reports from the slurm accounting data within given time periods.

```bash
sreport -t Hour user TopUsage start=12/10/18  end=01/16/19
```
Top users grouped by allocation in a given time period

```bash
sreport -t Hour job SizesByAccount  start=12/10/18  end=01/16/19
```
Hours spent by jobs of different sizes (# of cpus)

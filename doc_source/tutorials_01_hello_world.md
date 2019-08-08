# Running Your First Job on AWS ParallelCluster<a name="tutorials_01_hello_world"></a>

This tutorial walks you through running your first Hello World job on AWS ParallelCluster\.

If you haven't yet completed installation of AWS ParallelCluster, and configured your CLI, follow the instructions in the [getting started](getting_started.md) guide before continuing with this tutorial\.

## Verifying Your Installation<a name="verifying-your-installation"></a>

First, we verify that AWS ParallelCluster is correctly installed and configured\.

```
$ pcluster version
```

This returns the running version of AWS ParallelCluster\. If the output gives you a message about configuration, you need to run the following to configure AWS ParallelCluster:

```
$ pcluster configure
```

## Creating Your First Cluster<a name="creating-your-first-cluster"></a>

Now it's time to create your first cluster\. Because the workload for this tutorial isn't performance intensive, we can use the default instance size of `t2.micro`\. \(For production workloads, you choose an instance size that best fits your needs\.\)

Let's call your cluster hello\-world\.

```
$ pcluster create hello-world
```

When the cluster is created, you see output similar to the following:

```
Starting: hello-world
Status: parallelcluster-hello-world - CREATE_COMPLETE
MasterPublicIP = 54.148.x.x
ClusterUser: ec2-user
MasterPrivateIP = 192.168.x.x
GangliaPrivateURL = http://192.168.x.x/ganglia/
GangliaPublicURL = http://54.148.x.x/ganglia/
```

The message `CREATE_COMPLETE` shows that the cluster created successfully\. The output also provides us with the public and private IP addresses of our master node\. We need this IP to log in\.

## Logging into Your Master Instance<a name="logging-into-your-master-instance"></a>

Use your OpenSSH pem file to log into your master instance\.

```
pcluster ssh hello-world -i /path/to/keyfile.pem
```

After you log in, run the command `qhost` to verify that your compute nodes are set up and configured\.

```
$ qhost
HOSTNAME                ARCH         NCPU NSOC NCOR NTHR  LOAD  MEMTOT  MEMUSE  SWAPTO  SWAPUS
----------------------------------------------------------------------------------------------
global                  -               -    -    -    -     -       -       -       -       -
ip-192-168-1-125        lx-amd64        2    1    2    2  0.15    3.7G  130.8M 1024.0M     0.0
ip-192-168-1-126        lx-amd64        2    1    2    2  0.15    3.7G  130.8M 1024.0M     0.0
```

The output shows that we have two compute nodes in our cluster, both with two threads available to them\.

## Running Your First Job Using SGE<a name="running-your-first-job-using-sge"></a>

Next, we create a job that sleeps for a little while and then outputs its own hostname\.

Create a file called `hellojob.sh`, with the following contents\.

```
#!/bin/bash
sleep 30
echo "Hello World from $(hostname)"
```

Next, submit the job using `qsub`, and verify that it runs\.

```
$ qsub hellojob.sh
Your job 1 ("hellojob.sh") has been submitted
```

Now, you can view your queue and check the status of the job\.

```
$ qstat
job-ID  prior   name       user         state submit/start at     queue                          slots ja-task-ID
-----------------------------------------------------------------------------------------------------------------
      1 0.55500 hellojob.s ec2-user     r     03/24/2015 22:23:48 all.q@ip-192-168-1-125.us-west     1
```

The output shows that the job is currently in a running state\. Wait 30 seconds for the job to finish, and then run `qstat` again\.

```
$ qstat
$
```

Now that there are no jobs in the queue, we can check for output in our current directory\.

```
$ ls -l
total 8
-rw-rw-r-- 1 ec2-user ec2-user 48 Mar 24 22:34 hellojob.sh
-rw-r--r-- 1 ec2-user ec2-user  0 Mar 24 22:34 hellojob.sh.e1
-rw-r--r-- 1 ec2-user ec2-user 34 Mar 24 22:34 hellojob.sh.o1
```

In the output, we see an "`e1`" and "`o1`" file in our job script\. Because the `e1` file is empty, there was no output to stderr\. If we view the `o1` file, we can see output from our job\.

```
$ cat hellojob.sh.o1
Hello World from ip-192-168-1-125
```

The output also shows that our job ran successfully on instance `ip-192-168-1-125`\.
# AWS ParallelCluster AWS ParallelCluster User Guide

-----
*****Copyright &copy; 2019 Amazon Web Services, Inc. and/or its affiliates. All rights reserved.*****

-----
Amazon's trademarks and trade dress may not be used in 
     connection with any product or service that is not Amazon's, 
     in any manner that is likely to cause confusion among customers, 
     or in any manner that disparages or discredits Amazon. All other 
     trademarks not owned by Amazon are the property of their respective
     owners, who may or may not be affiliated with, connected to, or 
     sponsored by Amazon.

-----
## Contents
+ [What Is AWS ParallelCluster](what-is-aws-parallelcluster.md)
+ [Setting Up AWS ParallelCluster](getting_started.md)
   + [Installing AWS ParallelCluster](install.md)
      + [Install AWS ParallelCluster in a Virtual Environment](install-virtualenv.md)
      + [Install AWS ParallelCluster on Linux](install-linux.md)
         + [Installing Python on Linux](install-linux-python.md)
      + [Install AWS ParallelCluster on macOS](install-macos.md)
      + [Install AWS ParallelCluster on Windows](install-windows.md)
   + [Configuring AWS ParallelCluster](getting-started-configuring-parallelcluster.md)
   + [Moving from CfnCluster to AWS ParallelCluster](moving-from-cfncluster-to-aws-parallelcluster.md)
   + [Supported Regions](supported-regions.md)
+ [Using AWS ParallelCluster](working.md)
   + [AWS ParallelCluster CLI Commands](commands.md)
      + [pcluster configure](pcluster.configure.md)
      + [pcluster create](pluster.create.md)
      + [pcluster createami](pcluster.createami.md)
      + [pcluster delete](pcluster.delete.md)
      + [pcluster instances](pcluster.instances.md)
      + [pcluster list](pcluster.list.md)
      + [pcluster ssh](pcluster.ssh.md)
      + [pcluster start](pcluster.start.md)
      + [pcluster status](pcluster.status.md)
      + [pcluster stop](pcluster.stop.md)
      + [pcluster update](pcluster.update.md)
      + [pcluster version](pcluster.version.md)
   + [Network Configurations](networking.md)
   + [Custom Bootstrap Actions](pre_post_install.md)
   + [Working with Amazon S3](s3_resources.md)
   + [AWS Identity and Access Management Roles in AWS ParallelCluster](iam.md)
   + [AWS ParallelCluster Batch CLI Commands](awsbatchcli.md)
      + [awsbsub](awsbatchcli.awsbsub.md)
      + [awsbstat](awsbatchcli.awsbstat.md)
      + [awsbout](awsbatchcli_awsbout.md)
      + [awsbkill](awsbatchcli_awsbkill.md)
      + [awsbqueues](awsbatchcli_awsbqueues.md)
      + [awsbhosts](awsbatchcli_awsbhosts.md)
   + [Elastic Fabric Adapter](efa.md)
   + [Enable Intel MPI](intelmpi.md)
+ [Configuration](configuration.md)
   + [[global] Section](global.md)
   + [[aws] Section](aws.md)
   + [[aliases] Section](aliases.md)
   + [[cluster] Section](cluster-definition.md)
   + [[ebs] Section](ebs-section.md)
   + [[efs] Section](efs-section.md)
   + [[fsx] Section](fsx-section.md)
   + [[raid] Section](raid-section.md)
   + [[scaling] Section](scaling-section.md)
   + [[vpc] Section](vpc-section.md)
   + [Example](examples.md)
+ [How AWS ParallelCluster Works](functional.md)
   + [AWS ParallelCluster Processes](processes.md)
   + [AWS Services used in AWS ParallelCluster](aws-services.md)
   + [AWS ParallelCluster Auto Scaling](autoscaling.md)
+ [Tutorials](tutorials.md)
   + [Running Your First Job on AWS ParallelCluster](tutorials_01_hello_world.md)
   + [Building a Custom AWS ParallelCluster AMI](tutorials_02_ami_customization.md)
   + [Running an MPI Job with AWS ParallelCluster and awsbatch Scheduler](tutorials_03_batch_mpi.md)
   + [Disk Encryption with a Custom KMS Key](tutorials_04_encrypted_kms_fs.md)
+ [Development](development.md)
   + [Setting Up a Custom AWS ParallelCluster Cookbook](custom_cookbook.md)
   + [Setting Up a Custom AWS ParallelCluster Node Package](custom_node_package.md)
+ [Document History](document_history.md)
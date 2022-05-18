# Slurm Workload Manager \(`slurm`\)<a name="schedulers.slurm"></a>

AWS ParallelCluster version 2\.11\.7 uses Slurm 20\.11\.9\. For information about Slurm, see [https://slurm.schedmd.com/](https://slurm.schedmd.com/)\. For downloads, see see [https://github.com/SchedMD/slurm/tags](https://github.com/SchedMD/slurm/tags)\. For the source code, see [https://github.com/SchedMD/slurm](https://github.com/SchedMD/slurm)\.

**Important**  
AWS ParallelCluster is tested with Slurm configuration parameters, which are provided by default\. Any changes that you make to these Slurm configuration parameters are done at your own risk\. They are supported only on a best\-effort basis\.

AWS ParallelCluster versions 2\.11\.4 to 2\.11\.6 use Slurm 20\.11\.8\. AWS ParallelCluster versions between 2\.11\.0 and 2\.11\.3 use Slurm 20\.11\.7\. AWS ParallelCluster version 2\.10\.4 uses Slurm 20\.02\.7\. AWS ParallelCluster versions between 2\.9\.0 and 2\.10\.3 use Slurm 20\.02\.4\. AWS ParallelCluster versions between 2\.6 and 2\.8\.1 use Slurm 19\.05\.5\. AWS ParallelCluster versions 2\.5\.0 and 2\.5\.1 use Slurm 19\.05\.3\-2\. AWS ParallelCluster versions between 2\.3\.1 and 2\.4\.1 use Slurm 18\.08\.6\-2\. AWS ParallelCluster versions before 2\.3\.1 use Slurm 16\.05\.3\-1\.
# Installing AWS ParallelCluster in a non\-virtual environment using pip<a name="install-v3-pip"></a>

**Prerequisites**
+ AWS ParallelCluster requires Python 3\.6 or later\. If you don't already have it installed, [download a compatible version](https://www.python.org/downloads/) for your platform at [python\.org](https://www.python.org/)\.

**Install AWS ParallelCluster**

1. Use `pip` to install AWS ParallelCluster\.

   ```
   $ python3 -m pip install "aws-parallelcluster" --upgrade --user
   ```

   When you use the `--user` switch, `pip` installs AWS ParallelCluster to `~/.local/bin`\. 

1. Verify that AWS ParallelCluster installed correctly\.

   ```
   $ pcluster version
   {
     "version": "3.0.0"
   }
   ```

1. To upgrade to the latest version, run the installation command again\.

   ```
   $ python3 -m pip install "aws-parallelcluster" --upgrade --user
   ```
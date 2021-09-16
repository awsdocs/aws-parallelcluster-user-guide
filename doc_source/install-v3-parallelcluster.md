# Installing AWS ParallelCluster<a name="install-v3-parallelcluster"></a>

AWS ParallelCluster is distributed as a Python package and is installed using `pip` , the Python package manager\. For more information on installing Python packages, see [Installing packages](https://packaging.python.org/tutorials/installing-packages/) in the *Python Packaging User Guide*\.

**Ways to install AWS ParallelCluster:**
+ [Install AWS ParallelCluster in a virtual environment \(recommended\)](install-v3-virtual-environment.md)
+ [Installing AWS ParallelCluster in a non\-virtual environment using pip](install-v3-pip.md)

You can find the version number of the most recent CLI on the [releases page on GitHub](https://github.com/aws/aws-parallelcluster/releases)\. In this guide, the command examples assume that you have installed a version of Python >= 3\.6\. The `pip` command examples use the `pip3` version\.

**Manage both AWS ParallelCluster 2 and AWS ParallelCluster 3**  
 For customers who use both AWS ParallelCluster 2 and AWS ParallelCluster 3 and want to manage the CLIs for both packages, it is recommended to install AWS ParallelCluster 2 and AWS ParallelCluster 3 in different [virtual environments](install-v3-virtual-environment.md)\. This ensures that you can continue using each version of AWS ParallelCluster and any associated cluster resources\.
# Installing AWS ParallelCluster<a name="install"></a>

AWS ParallelCluster is distributed as a Python package and is installed using `pip`, the Python package manager\. For more information on installing Python packages, see [Installing packages](https://packaging.python.org/tutorials/installing-packages/) in the *Python Packaging User Guide*\.

**Ways to install AWS ParallelCluster:**
+ [Using a virtual environment \(recommended\)](#install-tool-venv)
+ [Using `pip`](#install-tool-pip)

You can find the version number of the most recent CLI on the [releases page on GitHub](https://github.com/aws/aws-parallelcluster/releases)\.

In this guide, the command examples assume that you have Python v3 installed\. The `pip` command examples use the `pip3` version\.

## Installing AWS ParallelCluster in a virtual environment \(recommended\)<a name="install-tool-venv"></a>

We recommend that you install AWS ParallelCluster in a virtual environment\. If you encounter issues when you attempt to install AWS ParallelCluster with `pip3`, you can [install AWS ParallelCluster in a virtual environment](install-virtualenv.md) to isolate the tool and its dependencies\. Or you can use a different version of Python than you normally do\.

## Installing AWS ParallelCluster in a non\-virtual environment using pip<a name="install-tool-pip"></a>

The primary distribution method for AWS ParallelCluster on Linux, Windows, and macOS is `pip`, which is a package manager for Python\. It provides a way to install, upgrade, and remove Python packages and their dependencies\.

**Current AWS ParallelCluster Version**  
AWS ParallelCluster is updated regularly\. To determine whether you have the latest version, see the [releases page on GitHub](https://github.com/aws/aws-parallelcluster/releases)\.

If you already have `pip` and a supported version of Python, you can install AWS ParallelCluster by using the following command\. If you have Python version 3\+ installed, we recommend that you use the **pip3** command\.

```
$ pip3 install aws-parallelcluster --upgrade --user
```

## Steps to take after installation<a name="install-post"></a>

After you install AWS ParallelCluster, you might need to add the executable file path to your `PATH` variable\. For platform\-specific instructions, see the following topics:
+ **Linux** – [Add the AWS ParallelCluster executable to your command line path](install-linux.md#install-linux-path)
+ **macOS** – [Add the AWS ParallelCluster executable to your command line path](install-macos.md#install-macos-path)
+ **Windows** – [Add the AWS ParallelCluster executable to your command line path](install-windows.md#install-windows-path)

You can verify that AWS ParallelCluster installed correctly by running `pcluster version`\.

```
$ pcluster version
2.10.0
```

AWS ParallelCluster is updated regularly\. To update to the latest version of AWS ParallelCluster, run the installation command again\. For details about the latest version of AWS ParallelCluster, see the [AWS ParallelCluster release notes](https://github.com/aws/aws-parallelcluster/blob/v2.10.0/CHANGELOG.md)\.

```
$ pip3 install aws-parallelcluster --upgrade --user
```

To uninstall AWS ParallelCluster, use `pip uninstall`\.

```
$ pip3 uninstall aws-parallelcluster
```

If you don't have Python and `pip`, use the procedure for your environment\.

## Detailed instructions for each environment<a name="install-sections"></a>
+ [Install AWS ParallelCluster in a virtual environment \(recommended\)](install-virtualenv.md)
+ [Install AWS ParallelCluster on Linux](install-linux.md)
+ [Install AWS ParallelCluster on macOS](install-macos.md)
+ [Install AWS ParallelCluster on Windows](install-windows.md)
# Install AWS ParallelCluster on Windows<a name="install-windows"></a>

You can install AWS ParallelCluster on Windows by using `pip`, which is a package manager for Python\. If you already have `pip`, follow the instructions in the main [installation topic](install.md)\.

**Topics**
+ [Install AWS ParallelCluster using Python and `pip` on Windows](#install-windows-pip)
+ [Add the AWS ParallelCluster executable to your command line path](#install-windows-path)

## Install AWS ParallelCluster using Python and `pip` on Windows<a name="install-windows-pip"></a>

The Python Software Foundation provides installers for Windows that include `pip`\.

**To install Python and `pip` \(Windows\)**

1. Download the Python Windows x86\-64 installer from the [downloads page](https://www.python.org/downloads/windows/) of [Python\.org](https://www.python.org)\.

1. Run the installer\.

1. Choose **Add Python 3 to PATH**\.

1. Choose **Install Now**\.

The installer installs Python in your user folder and adds its program folders to your user path\.

**To install AWS ParallelCluster with `pip3` \(Windows\)**

If you use Python version 3\+, we recommend that you use the `pip3` command\.

1. Open the **Command Prompt** from the **Start** menu\.

1. Use the following commands to verify that Python and `pip` are both installed correctly\.

   ```
   C:\>py --version
   Python 3.8.5
   C:\>pip3 --version
   pip 20.1.1 from c:\python38\lib\site-packages\pip (python 3.8)
   ```

1. Install AWS ParallelCluster using `pip`\.

   ```
   C:\>pip3 install aws-parallelcluster
   ```

1. Verify that AWS ParallelCluster is installed correctly\.

   ```
   C:\>pcluster version
   2.8.1
   ```

To upgrade to the latest version, run the installation command again\.

```
C:\>pip3 install --user --upgrade aws-parallelcluster
```

## Add the AWS ParallelCluster executable to your command line path<a name="install-windows-path"></a>

After installing AWS ParallelCluster with `pip`, add the `pcluster` program to your operating system's `PATH` environment variable\.

You can find where the `pcluster` program is installed by running the following command\.

```
C:\>where pcluster
C:\Python38\Scripts\pcluster.exe
```

If that command does not return any results, then you must add the path manually\. Use the command line or Windows Explorer to discover where it is installed on your computer\. Typical paths include:
+ **Python 3 and `pip3`** – `C:\Python38\Scripts\`
+ **Python 3 and `pip3` \-\-user option** – `%APPDATA%\Python\Python38\Scripts`

**Note**  
Folder names that include version numbers can vary\. The preceding examples show Python38\. Replace as needed with the version number that you are using\.

**To modify your PATH variable \(Windows\)**

1. Press the Windows key and enter **environment variables**\.

1. Choose **Edit environment variables for your account**\.

1. Choose **PATH**, and then choose **Edit**\.

1. Add the path to the **Variable value** field\. For example: ***C:\\new\\path***

1. Choose **OK** twice to apply the new settings\.

1. Close any running command prompts and reopen the command prompt window\.
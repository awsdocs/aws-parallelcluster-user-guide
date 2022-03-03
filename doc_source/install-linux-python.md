# Installing Python on Linux<a name="install-linux-python"></a>

If your distribution didn't come with Python, or came with an earlier version, install Python before installing `pip` and AWS ParallelCluster\.

**To install Python 3 on Linux**

1. Check to see if Python is already installed\.

   ```
   $ python3 --version
   ```

   or

   ```
   $ python --version
   ```
**Note**  
If your Linux distribution came with Python, you might need to install the Python developer package\. The developer package includes the headers and libraries that are required to compile extensions and to install AWS ParallelCluster\. Use your package manager to install the developer package\. It is typically named `python-dev` or `python-devel`\.

1. If Python 2\.7 or later is not installed, install Python with your distribution's package manager\. The command and package name varies:
   + On Debian derivatives such as Ubuntu, use `apt`\.

     ```
     $ sudo apt-get install python3
     ```
   + On Red Hat and derivatives, use `yum`\.

     ```
     $ sudo yum install python3
     ```
   + On SUSE and derivatives, use `zypper`\.

     ```
     $ sudo zypper install python3
     ```

1. To verify that Python installed correctly, open a command prompt or shell and run the following command\.

   ```
   $ python3 --version
   Python 3.8.11
   ```
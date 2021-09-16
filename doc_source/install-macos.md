# Install AWS ParallelCluster on macOS<a name="install-macos"></a>

**Topics**
+ [Prerequisites](#install-bundle-macos-os-prereq)
+ [Install AWS ParallelCluster on macOS using pip](#install-macos-pip)
+ [Add the AWS ParallelCluster executable to your command line path](#install-macos-path)

## Prerequisites<a name="install-bundle-macos-os-prereq"></a>
+ Python 3 version 3\.6\+ or Python 2 version 2\.7

Check your Python installation\.

```
$ python --version
```

If your computer doesn't already have Python installed, or if you want to install a different version of Python, follow the procedure in [Install AWS ParallelCluster on Linux](install-linux.md)\.

## Install AWS ParallelCluster on macOS using pip<a name="install-macos-pip"></a>

You can also use `pip` directly to install AWS ParallelCluster\. If you don't have `pip`, follow the instructions in the main [installation topic](install.md)\. Run `pip3 --version` to see if your version of macOS already includes Python and `pip3`\.

```
$ pip3 --version
```

**To install AWS ParallelCluster on macOS**

1. Download and install the latest version of Python from the [downloads page](https://www.python.org/downloads/mac-osx/) of [Python\.org](https://www.python.org)\.

1. Download and run the `pip3` installation script provided by the Python Packaging Authority\.

   ```
   $ curl -O https://bootstrap.pypa.io/get-pip.py
   $ python3 get-pip.py --user
   ```

1. Use your newly installed `pip3` to install AWS ParallelCluster\. We recommend that if you use Python version 3\+, you use the `pip3` command\.

   ```
   $ python3 -m pip install "aws-parallelcluster<3.0" --upgrade --user
   ```

1. Verify that AWS ParallelCluster is installed correctly\.

   ```
   $ pcluster version
   2.11.2
   ```

   If the program isn't found, [add it to your command line path](#install-macos-path)\.

To upgrade to the latest version, run the installation command again\.

```
$ pip3 install "aws-parallelcluster<3.0" --upgrade --user
```

## Add the AWS ParallelCluster executable to your command line path<a name="install-macos-path"></a>

After installing with `pip`, you might need to add the `pcluster` program to your operating system's `PATH` environment variable\. The location of the program depends on where Python is installed\.

**Example AWS ParallelCluster install location \- macOS with Python 3\.6 and `pip` \(user mode\)**  

```
~/Library/Python/3.6/bin
```
Substitute the version of Python that you have for the version in the preceding example\.

If you don't know where Python is installed, run `which python`\.

```
$ which python3
/usr/local/bin/python3
```

The output might be the path to a symlink, not the path to the actual program\. Run `ls -al` to see where it points\.

```
$ ls -al /usr/local/bin/python3
lrwxr-xr-x  1 username  admin  36 Mar 12 12:47 /usr/local/bin/python3 -> ../Cellar/python/3.6.8/bin/python3
```

`pip` installs programs in the same folder that contains the Python application\. Add this folder to your PATH variable\.

**To modify your `PATH` variable \(Linux, macOS, or Unix\)**

1. Find your shell's profile script in your user folder\. If you're not sure which shell you have, run `echo $SHELL`\.

   ```
   $ ls -a ~
   .  ..  .bash_logout  .bash_profile  .bashrc  Desktop  Documents  Downloads
   ```
   + **Bash** – `.bash_profile`, `.profile`, or `.bash_login`
   + **Zsh** – `.zshrc`
   + **Tcsh** – `.tcshrc`, `.cshrc`, or `.login`

1. Add an export command to your profile script\.

   ```
   export PATH=~/.local/bin:$PATH
   ```

   This command adds a path, `~/.local/bin` in this example, to the current `PATH` variable\.

1. Load the profile into your current session\.

   ```
   $ source ~/.bash_profile
   ```
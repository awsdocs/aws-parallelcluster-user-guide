# Install AWS ParallelCluster on Linux<a name="install-linux"></a>

You can install AWS ParallelCluster and its dependencies on most Linux distributions by using `pip`, a package manager for Python\. First, determine if Python and `pip` are installed:

1. To determine if your version of Linux includes Python and `pip`, run `pip --version`\.

   ```
   $ pip --version
   ```

   If you have `pip` installed, go on to the [Install AWS ParallelCluster with pip](install.md) topic\. Otherwise, continue with Step 2\.

1. To determine if Python is installed, run `python --version`\.

   ```
   $ python --version
   ```

   If you have Python 3 version 3\.6\+ or Python 2 version 2\.7 installed, go on to the [Install AWS ParallelCluster with pip](install.md) topic\. Otherwise, [install Python](install-linux-python.md), and then return to this procedure to install `pip`\.

1. Install `pip` by using the script that the *Python Packaging Authority* provides\. 

1. Use the `curl` command to download the installation script\.

   ```
   $ curl -O https://bootstrap.pypa.io/get-pip.py
   ```

1. Run the script with Python to download and install the latest version of `pip` and other required support packages\.

   ```
   $ python get-pip.py --user
   ```

   or

   ```
   $ python3 get-pip.py --user
   ```

   When you include the `--user` switch, the script installs `pip` to the path `~/.local/bin`\.

1. To ensure that the folder that contains `pip` is part of your `PATH` variable, do the following:

   1. Find your shell's profile script in your user folder\. If you're not sure which shell you have, run `basename $SHELL`\.

      ```
      $ ls -a ~
      .  ..  .bash_logout  .bash_profile  .bashrc  Desktop  Documents  Downloads
      ```
      + **Bash** – `.bash_profile`, `.profile`, or `.bash_login`
      + **Zsh** – `.zshrc`
      + **Tcsh** – `.tcshrc`, `.cshrc` or `.login`

   1. Add an export command at the end of your profile script that's similar to the following example\.

      ```
      export PATH=~/.local/bin:$PATH
      ```

      The export command inserts the path, which is `~/.local/bin` in this example, at the front of the existing `PATH` variable\.

   1. To put these changes into effect, reload the profile into your current session\.

      ```
      $ source ~/.bash_profile
      ```

1. Verify that `pip` is installed correctly\.

   ```
   $ pip3 --version
   pip 20.1.1 from ~/.local/lib/python3.6/site-packages (python 3.6)
   ```

**Topics**
+ [Install AWS ParallelCluster with `pip`](#install-linux-with-pip)
+ [Add the AWS ParallelCluster Executable to Your Command Line Path](#install-linux-path)
+ [Installing Python on Linux](install-linux-python.md)

## Install AWS ParallelCluster with `pip`<a name="install-linux-with-pip"></a>

Use `pip` to install AWS ParallelCluster\.

```
$ python3 -m pip install aws-parallelcluster --upgrade --user
```

When you use the `--user` switch, `pip` installs AWS ParallelCluster to `~/.local/bin`\. 

Verify that AWS ParallelCluster installed correctly\.

```
$ pcluster version
2.8.0
```

To upgrade to the latest version, run the installation command again\.

```
$ python3 -m pip install aws-parallelcluster --upgrade --user
```

## Add the AWS ParallelCluster Executable to Your Command Line Path<a name="install-linux-path"></a>

After installing with `pip`, you might need to add the `pcluster` executable to your operating system's `PATH` environment variable\.

To verify the folder in which `pip` installed AWS ParallelCluster, run the following command\.

```
$ which pcluster
/home/username/.local/bin/pcluster
```

If you omitted the `--user` switch when you installed AWS ParallelCluster, the executable might be in the `bin` folder of your Python installation\. If you don't know where Python is installed, run this command\.

```
$ which python
/usr/local/bin/python
```

Note that the output might be the path to a symlink, not to the actual executable\. To see where the symlink points, run `ls -al`\.

```
$ ls -al $(which python)
/usr/local/bin/python -> ~/.local/Python/3.6/bin/python3.6
```

If this is the same folder that you added to the path in step 3 in [Installing AWS ParallelCluster](install.md), you're done with the installation\. Otherwise, you must perform steps 3a – 3c again, adding this additional folder to the path\.
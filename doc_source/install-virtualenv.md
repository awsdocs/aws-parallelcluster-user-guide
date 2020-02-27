# Install AWS ParallelCluster in a Virtual Environment \(Recommended\)<a name="install-virtualenv"></a>

We recommend that you install AWS ParallelCluster in a virtual environment, to avoid requirement version conflicts with other `pip` packages\.

**Prerequisites**
+ Verify that `pip` and Python are installed\. We recommend `pip3`, and Python 3 version 3\.6\. If you are using Python 2, use `pip` instead of `pip3` and `virtualenv` instead of `venv`\.

**To install AWS ParallelCluster in a virtual environment**

1. Install `virtualenv` using `pip3`\.

------
#### [ Linux, macOS, or Unix ]

   ```
   $ python3 -m pip install --upgrade pip
   $ python3 -m pip install --user --upgrade virtualenv
   ```

------
#### [ Windows ]

   ```
   C:\>pip3 install --user --upgrade virtualenv
   ```

------

1. Create a virtual environment and name it\.

------
#### [ Linux, macOS, or Unix ]

   ```
   $ virtualenv ~/apc-ve
   ```

------
#### [ Windows ]

   ```
   C:\>virtualenv %USERPROFILE%\apc-ve
   ```

------

   Alternatively, you can use the `-p` option to specify a specific version of Python\.

   ```
   $ virtualenv -p $(which python3) ~/apc-ve
   ```

1. <a name="activate-virtual-environment"></a>Activate your new virtual environment\.

------
#### [ Linux, macOS, or Unix ]

   ```
   $ source ~/apc-ve/bin/activate
   ```

------
#### [ Windows ]

   ```
   C:\>%USERPROFILE%\apc-ve\Scripts\activate
   ```

------

1. Install AWS ParallelCluster into your virtual environment\.

------
#### [ Linux, macOS, or Unix ]

   ```
   (apc-ve)~$ python3 -m pip install --upgrade aws-parallelcluster
   ```

------
#### [ Windows ]

   ```
   (apc-ve) C:\>python3 -m pip install --upgrade aws-parallelcluster
   ```

------

1. Verify that AWS ParallelCluster is installed correctly\.

------
#### [ Linux, macOS, or Unix ]

   ```
   $ pcluster version
   2.6.0
   ```

------
#### [ Windows ]

   ```
   (apc-ve) C:\>pcluster version
   2.6.0
   ```

------

You can use the `deactivate` command to exit the virtual environment\. Each time that you start a session, you must [reactivate the environment](#activate-virtual-environment)\.

To upgrade to the latest version of AWS ParallelCluster, run the installation command again\.

------
#### [ Linux, macOS, or Unix ]

```
(apc-ve)~$ python3 -m pip install --upgrade aws-parallelcluster
```

------
#### [ Windows ]

```
(apc-ve) C:\>pip3 install --upgrade aws-parallelcluster
```

------
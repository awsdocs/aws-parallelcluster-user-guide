# Steps to take after installation<a name="install-v3-after-install"></a>

**Install Node Version Manager and Node\.js**  
[Node\.js](https://nodejs.org/en/) is a new requirement for the AWS ParallelCluster CLI, because of CDK usage for template generation\. Here is how to install Node\.js: 

```
$ curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | bash
$ ~/.nvm/nvm.sh
$ nvm install node
$ node --version
```

 You can verify that AWS ParallelCluster installed correctly by running ``pcluster version``\.

```
$ pcluster version
{
"version": "3.0.0"
}
```

 AWS ParallelCluster is updated regularly\. To update to the latest version of AWS ParallelCluster, run the installation command again\. For details about the latest version of AWS ParallelCluster, see the [AWS ParallelCluster release notes](https://github.com/aws/aws-parallelcluster/blob/v2.11.2/CHANGELOG.md)\. 

```
$ pip3 install aws-parallelcluster --upgrade --user
```

 To uninstall AWS ParallelCluster, use `pip3 uninstall` \. 

```
$ pip3 uninstall aws-parallelcluster
```

 If you don't have Python and `pip3` , use the procedure for your environment\. 
# `[fsx]` Section<a name="fsx-section"></a>

**Topics**
+ [`shared_dir`](#id15)
+ [`fsx_fs_id`](#fsx-fs-id)
+ [`storage_capacity`](#storage-capacity)
+ [`fsx_kms_key_id`](#fsx-kms-key-id)
+ [`imported_file_chunk_size`](#imported-file-chunk-size)
+ [`export_path`](#export-path)
+ [`import_path`](#import-path)
+ [`weekly_maintenance_start_time`](#weekly-maintenance-start-time)

Defines configuration settings for an attached Amazon FSx for Lustre file system\. For more information about Amazon FSx for Lustre, see [Amazon FSx CreateFileSystem](https://docs.aws.amazon.com/fsx/latest/APIReference/API_CreateFileSystem.html)\.

Amazon FSx for Lustre is supported when `[`base_os`](cluster-definition.md#base-os)` is either `centos7` or `alinux`\.

When using Amazon Linux, the kernel must be >= `4.14.104-78.84.amzn1.x86_64`\. For detailed instructions, see [Installing the Lustre Client](https://docs.aws.amazon.com/fsx/latest/WindowsGuide/install-lustre-client.html) in the *Amazon FSx for Lustre User Guide*\.

**Note**  
Amazon FSx for Lustre is not currently supported when using `awsbatch` as a scheduler\.

If using an existing file system, it must be associated to a security group that allows inbound and outbound TCP traffic from `0.0.0.0/0` through port `988`\. This is done automatically when not using `vpc_security_group_id`\.

To use an existing Amazon FSx file system, specify `fsx_fs_id`\.

```
[fsx fs]
shared_dir = /fsx
fsx_fs_id = fs-073c3803dca3e28a6
```

To create and configure a new file system, use the following parameters:

```
[fsx fs]
shared_dir = /fsx
storage_capacity = 3600
imported_file_chunk_size = 1024
export_path = s3://bucket/folder
import_path = s3://bucket
weekly_maintenance_start_time = 1:00:00
```

## `shared_dir`<a name="id15"></a>

 **\(Required\)** Defines the mount point for the Amazon FSx for Lustre file system on the master and compute nodes\.

Do not use `NONE` or `/NONE` as the shared directory\.

The following example mounts the file system at `/fsx`\.

```
shared_dir = /fsx
```

## `fsx_fs_id`<a name="fsx-fs-id"></a>

 **\(Optional\)** Attaches an existing Amazon FSx file system\.

If this option is specified, all of the following Amazon FSx parameters, such as `storage_capacity`, are ignored\.

```
fsx_fs_id = fs-073c3803dca3e28a6
```

## `storage_capacity`<a name="storage-capacity"></a>

 **\(Optional\)** Specifies the storage capacity of the file system, in GiB\.

The storage capacity has a minimum of 3,600 GiB and is provisioned in increments of 3,600 GiB\.

The default value is `3600`\.

```
storage_capacity = 3600
```

## `fsx_kms_key_id`<a name="fsx-kms-key-id"></a>

 **\(Optional\)** Specifies the ID of your AWS Key Management Service \(AWS KMS\) key\.

This ID is used to encrypt the data in your file system at rest\.

This must be used with a custom `ec2_iam_role`\. For more information, see [Disk Encryption with a Custom KMS Key](tutorials_04_encrypted_kms_fs.md)\.

\.

```
fsx_kms_key_id = xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

## `imported_file_chunk_size`<a name="imported-file-chunk-size"></a>

 **\(Optional\)** Determines the stripe count and the maximum amount of data per file \(in MiB\) stored on a single physical disk, for files that are imported from a data repository \(using `import_path`\)\. The maximum number of disks that a single file can be striped across is limited by the total number of disks that make up the file system\.

The chunk size default is `1024` \(1 GiB\), and it can go as high as 512,000 MiB \(500 GiB\)\. Amazon S3 objects have a maximum size of 5 TB\.

```
imported_file_chunk_size = 1024
```

## `export_path`<a name="export-path"></a>

 **\(Optional\)** Specifies the Amazon S3 path where the root of your file system is exported\. The path **must** be in the same Amazon S3 bucket as the `import_path` parameter\.

The default value is `s3://import-bucket/FSxLustre[creation-timestamp]`, where `import-bucket` is the bucket provided in the `[`import_path`](#import-path)` parameter\.

```
export_path = s3://bucket/folder
```

## `import_path`<a name="import-path"></a>

 **\*\(Optional\)** Specifies the S3 bucket to load data from into the file system\. Also serves as the export bucket\. For more information, see `[`export_path`](#export-path)`\.

Import occurs on cluster creation\. For more information, see [Importing Data from your Amazon S3 Bucket](https://docs.aws.amazon.com/fsx/latest/LustreGuide/fsx-data-repositories.html#import-data-repository) in the *Amazon FSx for Lustre User Guide*\.

If a value is not provided, the file system is empty\.

```
import_path =  s3://bucket
```

## `weekly_maintenance_start_time`<a name="weekly-maintenance-start-time"></a>

 **\*\(Optional\)** Specifies a preferred time to perform weekly maintenance, in the UTC time zone\.

The format is `[day of week]:[hour of day]:[minute of hour]`\. For example, Monday at Midnight is:

```
weekly_maintenance_start_time = 1:00:00
```
# Quotas for shared storage<a name="shared-storage-quotas-v3"></a>

Configure cluster `SharedStorage` to mount existing shared file storage and create new shared file storage based on the quotas that are listed in the following table\.


**The mounted file storage quotas for each cluster**  

| File shared storage type | AWS ParallelCluster managed storage | External storage | Quota net total | 
| --- | --- | --- | --- | 
|  Amazon EBS  |  5  |  5  |  5  | 
|  RAID  |  1  |  0  |  1  | 
|  Amazon EFS  |  1  |  20  |  21  | 
|  Amazon FSx †  |  1 FSx for Lustre  |  20  |  21  | 

**Note**  
This table of quotas is added in AWS ParallelCluster version 3\.2\.0\.

† AWS ParallelCluster only supports mounting existing Amazon FSx for NetApp ONTAP and Amazon FSx for OpenZFS systems\. It doesn't support the creation of new FSx for ONTAP and FSx for OpenZFS systems\.

**Note**  
If you use AWS Batch as a scheduler, FSx for Lustre is only available on the cluster head node\.
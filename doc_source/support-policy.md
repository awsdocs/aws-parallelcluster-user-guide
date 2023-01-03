# AWS ParallelCluster support policy<a name="support-policy"></a>

AWS ParallelCluster has multiple releases available at a time\. Starting with version 2\.11\.0, new releases \(indicated by a version number of *x*\.*x*\.0\) can be designated as either *latest* \(eligible for feature, security, and bug fix updates\) or *feature\-stable* \(eligible only for security and bug fix updates\)\.

Releases that are neither *latest* nor *feature\-stable* are eligible for troubleshooting on a best effort basis\. Any security patches or bug fixes are only applied to *feature\-stable* releases; feature updates are only applied to *latest* releases\.

Starting with the release of AWS ParallelCluster version 2\.11\.0, releases prior to version 2\.11\.0 \(2\.10\.4 and earlier\) are supported until December 31, 2021\. AWS ParallelCluster version 2\.11\.0 is the first *feature\-stable* release\. *feature\-stable* releases are supported with security and bug fix updates until the end of the year following the release\. Since version 2\.11\.0 was released in 2021, versions 2\.11\.*x* are supported until December 31\. 2022\.

 AWS ParallelCluster aims to provide consistency and predictability in our *feature\-stable* releases, but note that this does not extend to product components that have reached end\-of\-life\. For example, in the case of SGE, Torque, and the CentOS 8 operating system, these features are all marked for end\-of\-life on December 31, 2021, and will not be included in 2\.11\.*x* releases beyond that date\. 

AWS ParallelCluster 3 minor releases are supported for 18 months after their release date\. This is subject to the following:
+ Bugs and security issues will be addressed in a minor release \(e\.g\., 3\.1\) unless severity requires a more immediate patch release \(for example, 3\.1\.1\)\.
+ To receive bug and security fixes you must upgrade to a minor or patch release in which these fixes are provided\.
+  To receive feature enhancements, you need to upgrade to the most recent version of AWS ParallelCluster 3\. 


| AWS ParallelCluster versions | End of supported life \(EOSL\) date | 
| --- | --- | 
| 2\.10\.4 and earlier | 12/31/2021 | 
|  2\.11\.*`x`*  | 12/31/2022 | 
|  3\.0\.*`x`*  | 3/31/2023 | 
|  3\.1\.*`x`*  | 8/31/2023 | 
|  3\.2\.*`x`*  | 1/31/2024 | 
|  3\.3\.*`x`* | 5/31/2024 | 
|  3\.4\.*`x`* | 6/28/2024 | 
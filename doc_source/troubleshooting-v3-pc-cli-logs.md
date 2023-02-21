# Use pcluster CLI logs for troubleshooting<a name="troubleshooting-v3-pc-cli-logs"></a>

The `pcluster` CLI writes logs of your commands to `pcluster.log.#` files in `/home/user/.parallelcluster/`\.

For each command, the logs generally include the command with inputs, a copy of the CLI API version used to make the command, the response, and both info and error messages\. For a create and build command, the logs also include the configuration file, configuration file validation operations, the CloudFormation template, and stack commands\.

You can use these logs to verify errors, inputs, versions and `pcluster` CLI commands\. They can also serve as a record of when commands were made\.
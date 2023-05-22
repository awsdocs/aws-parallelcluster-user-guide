# `build_image`<a name="pc-py-lib-api-image-build"></a>

```
build_image(image_configuration, image_id, suppress_validators, validation_failure_level, dry_run, rollback_on_failure, region)
```

Create a custom AWS ParallelCluster image in a given Region\.Parameters:

**`image_configuration` \(required\)**  
The image configuration as Python data\.

**`image_id` \(required\)**  
The image ID\.

**`suppress_validators`**  
Identifies one or more image configuration validators to suppress\.  
Format: `(ALL | type:[A-Za-z0-9]+)`

**`validation_failure_level`**  
The minimum validation level that causes the image creation to fail\. The default is `ERROR`\.  
Valid values: `INFO` \| `WARNING` \| `ERROR`

**`dry_run`**  
If set to `True`, AWS ParallelCluster performs the request validation without creating any resources\. You can use this to validate the image configuration\. The default is `False`\.

**`rollback_on_failure`**  
If set to `True`, AWS ParallelCluster automatically initiates an image stack rollback on failures\. The default is `False`\.

**`region`**  
The image AWS Region\.
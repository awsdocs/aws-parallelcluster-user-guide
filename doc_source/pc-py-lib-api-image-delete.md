# `delete_image`<a name="pc-py-lib-api-image-delete"></a>

```
delete_image(image_id, region, force)
```

Delete an image in a given Region\.Parameters:

**`image_id` \(required\)**  
The image ID\.

**`region`**  
The image AWS Region\.

**`force`**  
If set to `True`, AWS ParallelCluster forces deletion if instances are using the AMI or if the AMI is shared\. The default is `False`\.
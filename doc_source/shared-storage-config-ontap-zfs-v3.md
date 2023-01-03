# Configure FSx for ONTAP and FSx for OpenZFS shared storage<a name="shared-storage-config-ontap-zfs-v3"></a>

For FSx for ONTAP and FSx for OpenZFS, you can use [`FsxOntapSettings`](SharedStorage-v3.md#SharedStorage-v3-FsxOntapSettings) / [`VolumeId`](SharedStorage-v3.md#yaml-SharedStorage-FsxOntapSettings-VolumeId) and [`FsxOpenZfsSettings`](SharedStorage-v3.md#SharedStorage-v3-FsxOpenZfsSettings) / [`VolumeId`](SharedStorage-v3.md#yaml-SharedStorage-FsxOpenZfsSettings-VolumeId) to specify mounting an external existing volume for your cluster\.

AWS ParallelCluster managed shared storage isn't supported for FSx for ONTAP and FSx for OpenZFS\.
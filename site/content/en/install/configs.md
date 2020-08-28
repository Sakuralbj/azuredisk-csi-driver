---
title: "Configure AzureDisk CSI driver"
linkTitle: "Configurations"
type: docs
weight: 4
description: >
    The configurations for AzureDisk CSI driver.
---

## Driver Parameters
 > storage class `disk.csi.azure.com` parameters are compatible with in-tree [kubernetes.io/azure-disk](https://kubernetes.io/docs/concepts/storage/volumes/#azuredisk) driver

### Dynamic Provisioning
 > get an [example](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/storageclass-azuredisk-csi.yaml)

Name | Meaning | Available Value | Mandatory | Default value
--- | --- | --- | --- | ---
skuName | azure disk storage account type (alias: `storageAccountType`)| `Standard_LRS`, `Premium_LRS`, `StandardSSD_LRS`, `UltraSSD_LRS` | No | `StandardSSD_LRS`
kind | managed or unmanaged(blob based) disk | `managed` (`dedicated`, `shared` are deprecated) | No | `managed`
fsType | File System Type | `ext4`, `ext3`, `ext2`, `xfs` | No | `ext4`
cachingMode | [Azure Data Disk Host Cache Setting](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/premium-storage-performance#disk-caching) | `None`, `ReadOnly`, `ReadWrite` | No | `ReadOnly`
storageAccount | specify the storage account name in which azure disk will be created | STORAGE_ACCOUNT_NAME | No | if empty, driver will find a suitable storage account that matches `skuName` in the same resource group as current k8s cluster
location | specify the Azure location in which azure disk will be created | `eastus`, `westus`, etc. | No | if empty, driver will use the same location name as current k8s cluster
resourceGroup | specify the resource group in which azure disk will be created | existing resource group name | No | if empty, driver will use the same resource group name as current k8s cluster
DiskIOPSReadWrite | [UltraSSD disk](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/disks-ultra-ssd) IOPS Capability (minimum: 2 IOPS/GiB ) | 100~160000 | No | `500`
DiskMBpsReadWrite | [UltraSSD disk](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/disks-ultra-ssd) Throughput Capability(minimum: 0.032/GiB) | 1~2000 | No | `100`
tags | azure disk [tags](https://docs.microsoft.com/en-us/azure/azure-resource-manager/management/tag-resources) | tag format: `key1=val1,key2=val2` | No | ""
diskEncryptionSetID | ResourceId of the disk encryption set to use for [enabling encryption at rest](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/disk-encryption) | format: `/subscriptions/{subs-id}/resourceGroups/{rg-name}/providers/Microsoft.Compute/diskEncryptionSets/{diskEncryptionSet-name}` | No | ""
writeAcceleratorEnabled | [Write Accelerator on Azure Disks](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator) | `true`, `false` | No | ""

### Static Provisioning(bring your own Azure Disk)
 > get an [example](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/pv-azuredisk-csi.yaml)

Name | Meaning | Available Value | Mandatory | Default value
--- | --- | --- | --- | ---
volumeHandle| Azure disk URI | /subscriptions/{sub-id}/resourcegroups/{group-name}/providers/microsoft.compute/disks/{disk-id} | Yes | N/A
volumeAttributes.fsType | File System Type | `ext4`, `ext3`, `xfs` | No | `ext4`
volumeAttributes.partition | partition num of the existing disk | `1`, `2`, `3` | No | empty(no partition) </br>- make sure partition format is like `-part1`
volumeAttributes.cachingMode | [disk host cache setting](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/premium-storage-performance#disk-caching)| `None`, `ReadOnly`, `ReadWrite` | No  | `ReadOnly`

### `VolumeSnapshotClass`
 Name | Meaning | Available Value | Mandatory | Default value
--- | --- | --- | --- | ---
resourceGroup | resource group for storing snapshot shots | EXISTING RESOURCE GROUP | No | If not specified, snapshot will be stored in the same resource group as source Azure disk
tags | azure disk [tags](https://docs.microsoft.com/en-us/azure/azure-resource-manager/management/tag-resources) | tag format: 'key1=val1,key2=val2' | No | ""
---
title: Resolving the I/O issue on Linux ntfs-3g
description: When bulk read/write was executed on the device, sometimes the setup will return ... has I/O errors. The article introduces a method to fix the file system corruption caused by ntfs-3g.
published: 2024-12-07
draft: false
category: Dev
tags: [Linux, Ubuntu, Tips]
language: en
---

Personally, I use a MacBook Pro running on Ubuntu as my Home Server, with a MyPassport portable hard disk with 4 TB of storage mounted as my self-hosted storage solution. When bulk read/write was executed on the device, sometimes the setup will return `... has I/O errors`. 

This article is just a record for me to resolve the same issue in the future, it might help others, but this method can only fix the NTFS file system on the software layer, not the hardware. Therefore, asking for the professionals is always the most reliable option.

## Issue Description
Try entering the mount path of the corrupted disk:
```bash
alanye-server@mac-server:/mnt$ ls disk
ls: cannot access 'disk': Transport endpoint is not connected
```

Unmounting the disk from the mount point returns an error:
```bash
alanye-server@mac-server:~$ sudo umount -f /mnt/disk
umount: /mnt/disk: target is busy
```

Mounting also returns an error:
```bash
alanye-server@mac-server:~$ sudo ntfs-3g /dev/sdf1 /mnt/disk
[sudo] password for alanye-server:
ntfs-3g-mount: failed to access mountpoint /mnt/disk:
Transport endpoint is not connected
```

In this case, the `lsblk` command shows, or does not show up the disk device, including the partition.

## Procedures to address the issue
### Resolve the NTFS file system corruption in the partition
1. Detach the disk from the Linux device. Only try force detach it from the device if the unmount option was unavailable.
2. Set the disk still and wait for at least 5 minutes, which makes sure the hard drive cools down.
3. Connect to any Windows device, listen to the sound of the hard drive, and make sure there’s no weird noises coming out, which could indicate that there might have some hardware issue with the hard drive. 
4. Wait until the disk showed up in the Device Manager and Windows File Explorer. That indicates that the drive could work properly, but have some file system corruption. 
	- File Explorer might pop up a window asking whether to fix the file system or not. In this case, choose to ignore it, since we will take another step.
5. Execute `chkdsk` command to fix corrupted file system.
	1. Open up a Windows Command Prompt window with Administrator Privileges
	2. Enter `chkdsk /r D:`, where `D:` should be replaced by the drive letter of the target disk.
	3. The time to complete the command varies, it depends on the status of the disk. 
6. As long as the command window returns `No further action is required`, you can continue with the following step.

### Reattach to the Linux device
1.  Find the target **device node** by using the `lsblk` command
	- For instance, `/dev/sdb` represents the device, and `/dev/sdb1` represents the partition.
2. Execute `sudo ntfsfix [Target_Partition]`
```bash
alanye-server@mac-server:~$ sudo ntfsfix /dev/sde1
Mounting volume... OK
Processing of $MFT and $MFTMirr completed successfully.
Checking the alternate boot sector... OK
NTFS volume version is 3.1.
NTFS partition /dev/sde1 was processed successfully.
```

3. Mount the target partition with `sudo mount [Target_Partition] [Mount_point]`
	- Example: `sudo /dev/sde1 /mnt/disk`
4. Thereafter, the partition should work properly again.

## Notices
If the issue persists, there might have some extra issues related to the Linux device or the disk itself. To prevent this issue in the future, please stop using NTFS as the file system on Linux, due to its lack of support and poor performance of `ntfs-3g`.

The most effective and simple way to prevent potential file loss in the future is to follow the [3-2-1 Backup Strategy](https://www.backblaze.com/blog/the-3-2-1-backup-strategy/). To prevent future data lose, it’s suggested to reference the strategy, though it’s not required to follow every rule.
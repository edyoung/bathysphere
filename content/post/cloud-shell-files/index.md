+++
author = "Edwin Young"
categories = ["Azure", "CloudShell"]
date = "2021-05-25"
description = "How Azure Cloud Shell stores your files"
slog = "cloud-shell-files"
image= "csfiles.png"
title = "How Azure Cloud Shell stores your files"
type = "post"
+++

The official information on Cloud Shell's file storage is [here](https://docs.microsoft.com/en-us/azure/cloud-shell/persisting-shell-storage). It's good stuff, and covers most things you might like to know, take a look! This blog contains a few more behind-the-scenes details for the curious. NB: We might change this in the future, this isn't guaranteed.

We can see from the docs that all your files are stored in an Azure File Share which belongs to you, and which you select when you first start CLoud Shell. But how do those files map to the directory layout you see in Cloud Shell?

You can see that there are several mount points set up:

```bash
edwin@Azure:~$ df -H
Filesystem                                                          Size  Used Avail Use% Mounted on
overlay                                                              52G   17G   36G  32% /
tmpfs                                                                68M     0   68M   0% /dev
tmpfs                                                               2.1G     0  2.1G   0% /sys/fs/cgroup
/dev/sda1                                                            52G   17G   36G  32% /home
shm                                                                  68M  8.2k   68M   1% /dev/shm
//csxxx.file.core.windows.net/cs-edyoung-microsoft-com-xxx          6.5G  5.4G  1.1G  84% /usr/csuser/clouddrive
tmpfs                                                               2.1G     0  2.1G   0% /proc/acpi
tmpfs                                                               2.1G     0  2.1G   0% /proc/scsi
tmpfs                                                               2.1G     0  2.1G   0% /sys/firmware
/dev/loop0                                                          5.3G  1.3G  3.7G  27% /home/edwin
```

You can ignore the overlay and tmpfs stuff, which is container voodoo.

The bits that matter:

- Inside Cloud Shell, you have a classic Unix filesystem. Under the filesystem root `/`, most everything is owned by root, and is not writable by the Cloud Shell user. This includes all the utility programs Cloud Shell ships.
Even if you find some way to elevate privileges and update them, all the changes will be lost the next time you run Cloud Shell, because these are part of the container image, and are recreated every time you reconnect.

- There's also your 'cloud drive', which is your Azure File Share mounted as a CIFS filesystem as `/usr/csuser/clouddrive`. In this example, mine is in the file share `cs-edyoung-microsoft-com-xxx` in the storage account `csxxx.file.core.windows.net` There's a symlink set up so that ~/clouddrive goes to the same location. Through that you can read and write files to the Azure file share.

- Your home directory is different. It's under `/home/username` (mine is `/home/edwin` ) and is writable. You can see that is a mount point for `/dev/loop0`, which is a loopback filesystem. A loopback filesystem allows you to create a filesystem *inside a file*. Where is that file? Inside your cloud drive fileshare, as a regular file `.cloudconsole/acc_username.img`

```bash
edwin@Azure:~$ ls -l ~/clouddrive/.cloudconsole/acc_edwin.img
-rwxrwxrwx 1 edwin edwin 5368709120 May 26 06:25 /home/edwin/clouddrive/.cloudconsole/acc_edwin.img
```

You can see this is a 5GB file. Inside that file is an entire filesystem, which contains the files which you have stored in your home directory. **Don't delete or modify that file, or you'll lose whatever you have in your Cloud Shell home directory**. Generally you should treat this file as opaque, but if you desperately want to you could download the file and mount it as a loopback device on a regular Linux box. If for some reason you *want* to delete everything in your home directory, you could delete that file from the file share, and when you restart Cloud Shell we will create a new home directory file for you.

## Ephemeral Storage
If you are using Cloud Shell in ephemeral mode, then your home directory is just part of the container image, and everything in it will disappear when your session ends. 
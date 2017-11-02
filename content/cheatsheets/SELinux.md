---
title: "LVM"
date: 2017-07-28T17:22:17+02:00
draft: true
---

Every had a directory not but you were the freaking owner?!
Even could not write as root from inside a container on a mapped directory?

Well.. probably SELinux caught you! To see the SELinux context use 

```
dude [~]> ls -Z /var/lib/jenkins

drwxr-xr-x. jenkins jenkins unconfined_u:object_r:unlabeled_t:s0 slave
```

If that unlabeled_t is not what you want, just change it like so:
```
dude [~]> chcon -R -t svirt_sandbox_file_t jenkins
```
**CAUTION**: Documentation says this is temporary! But I don't know the frame of reference :)

The types can be restored with the following command
```
dude [~]> restorecon -R -v jenkins
```

I did't really understand anything about SELinux yet but you learn more here: [SELinux contexts](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Security-Enhanced_Linux/sect-Security-Enhanced_Linux-Working_with_SELinux-SELinux_Contexts_Labeling_Files.html)
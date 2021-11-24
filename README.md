# Guide to enable ssh on macOSX Recovery Mode

### Boot in recovery mode

- https://www.lifewire.com/restart-a-mac-into-recovery-mode-5184142

### Open Terminal
Go to *Utility* -> *Terminal*

### Create a writeable ramdisk:
The root account has a random password. And you can’t reset it, since the filesystem is read-only.
Use Pubkey authentication!
```
rdsize=$((16*1024*1024/512)) # 16 megabytes
dev=`hdik -drivekey system-image=yes -nomount "ram://$rdsize"`
echo $?; echo $dev # check for errors!
newfs_hfs "$dev"
eval `/usr/bin/stat -s /var/root` # store perms for old mountponit
mount -t hfs -o union -o nobrowse "$dev" /var/root # magic happens here
chown "$st_uid:$st_gid" /var/root
chmod "$st_mode" /var/root
```

Next, add your key to `/var/root/.ssh/authorized_keys`.
```
mkdir /var/root/.ssh
vim /var/root/.ssh/authorized_keys
```

## References
- https://bennettp123.com/2014/06/09/os-x-recovery-shenanigans/

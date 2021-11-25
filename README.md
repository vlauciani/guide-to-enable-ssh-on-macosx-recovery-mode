[![License](https://img.shields.io/badge/license-MIT-lightgrey.svg)](https://github.com/vlauciani/guide-to-enable-ssh-on-macosx-recovery-mode/blob/main/LICENSE)

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
newfs_hfs $dev
eval `/usr/bin/stat -s /var/root` # store perms for old mountponit
mount -t hfs -o union -o nobrowse $dev /var/root # magic happens here
chown "$st_uid:$st_gid" /var/root
chmod "$st_mode" /var/root
```

Next, add your key to `/var/root/.ssh/authorized_keys`.
```
mkdir /var/root/.ssh
vim /var/root/.ssh/authorized_keys
```

### Generate SSH KEY for the server
```
ssh-keygen -t rsa
```

### Runs sshd
```
/usr/sbin/sshd -d -o UsePAM=no -h /var/root/.ssh/id_rsa
```

### Connect from client
```
ssh root@<ip>
```

## Contribute
Thanks to your contributions!

Here is a list of users who already contributed to this repository:
<a href="https://github.com/vlauciani/gitlabci-include-for-dockerhub-rate-limit/graphs/contributors">
  <img src="https://contrib.rocks/image?repo=vlauciani/gitlabci-include-for-dockerhub-rate-limit" />
</a>

## References
- https://bennettp123.com/2014/06/09/os-x-recovery-shenanigans/
- https://www.ssh.com/academy/ssh/sshd
- https://itectec.com/askdifferent/macos-how-to-open-the-sshd-service/

## Author
(c) 2021 Valentino Lauciani vlauciani[at]gmail.com

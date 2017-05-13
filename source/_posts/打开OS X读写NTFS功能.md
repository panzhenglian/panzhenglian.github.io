---
title: 如何打开Mac OSX原生的读写NTFS功能
date: 2016-06-01 00:39:33
category: Mac
---

### 打开终端，查看磁盘 Volume Nmae
```base
diskutil list
```
我的电脑是这样子的

```base
/dev/disk0 (internal, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      GUID_partition_scheme                        *251.0 GB   disk0
   1:                        EFI EFI                     209.7 MB   disk0s1
   2:          Apple_CoreStorage Macintosh HD            250.1 GB   disk0s2
   3:                 Apple_Boot Recovery HD             650.0 MB   disk0s3
/dev/disk1 (external, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:     FDisk_partition_scheme                        *1.0 TB     disk2
   1:               Windows_NTFS Expansion               1.0 TB     disk2s1

```
Windows_NTFS 后面就是磁盘名字了,可以看到，我的是 Expansion

### 编辑/etc/fstab文件
```
sudo vim /etc/fstab
```
写入以下内容

```
LABEL= Expansion none ntfs rw,auto,nobrowse
```
下面来依次解释一下，如果你的名字里面有空格键，就需要用\040的意思是代替空格键，如：Expansion\040Drive。

后面的Ntfs rw表示把这个分区挂载为可读写的ntfs格式，最后nobrowse非常重要，因为这个代表了在finder里不显示这个分区，这个选项非常重要，如果不打开的话挂载是不会成功的。

按下 esc，:wq 保存，将硬盘推出，重新插入，这时就不显示磁盘了

解决办法，给磁盘创建快捷方式

```
sudo ln -s /Volumes/Expansion ~/Desktop/Expansion
```

这里的 Expansion 换成你磁盘的名字，懒得写的同学请按tab键,后面的名字随意

如果想以后都能看到除Expansion以外其他隐藏的驱动器的话，可以多创建一个这个文件夹快捷方式：如下：
```
sudo ln -s /Volumes ~/Desktop/Volumes
```
参考：http://bbs.feng.com/read-htm-tid-9932031.html
然后我们能在桌面找到，
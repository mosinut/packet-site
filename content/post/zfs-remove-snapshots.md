---
title: "Zfs Remove Snapshots"
subtitle: "Freeing up space right now!"
date: 2020-05-21T18:32:40-04:00
draft: false
tags: ["zfs", "freenas", "howto"]
---

For a number of years I have run FreeNAS has a home storage appliance to do all the things that you do with storage at home.

While cleaning up and moving things around a while back I decided that I wanted to be able to delete the snapshots that were being used to free up the disk space *right now*. There was no pressing need for this, I just wanted to not wait for them to naturally time out.

1. See how much space we are using for snapshots vs. *real* data.
```bash
mark@freenas:~ % zfs list -o space -t all zpool1/mark
NAME           AVAIL   USED  USEDSNAP  USEDDS  USEDREFRESERV  USEDCHILD
zpool1/mark    1.86T   295G     10.2G    285G              0          0
```

2. List all the snapshots for the dataset. For example if I want to see all the snapshots and the used space for everything in the creatively named 'mark' dataset I can do:
```bash
mark@freenas:~ % zfs list -t snapshot | grep /mark | less
```
This will give me a bunch of lines like so:
```bash
NAME                                  USED  AVAIL  REFER  MOUNTPOINT
zpool1/mark@auto-20200503.0800-4w            59.8K      -   263G  -
zpool1/mark@auto-20200503.0900-4w            59.8K      -   263G  -
zpool1/mark@auto-20200503.1000-4w            56.9K      -   263G  -
zpool1/mark@auto-20200503.1100-4w            56.9K      -   263G  -
```
3. We can simulate what we are going to free up by deleting the snapshots. The "-n" is the key here. **BE CAREFUL**
```bash
mark@freenas:~ % zfs destroy -rvn zpool1/mark@%
# A whole bunch of output snipped
would reclaim 10.2G
```
4. When you are ready to do the actual deletion just run the same command you tested without the "-n".

That is it. The command is very quick, you don't have to wait around for stuff to happen.

Enjoy your new found free space!
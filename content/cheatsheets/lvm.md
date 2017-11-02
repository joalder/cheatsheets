---
title: "LVM"
date: 2017-07-27T12:01:17+02:00
draft: true
---

## Docker and LVM are friends 
... not!

## Cleanup LVM dockerthinpool after fillup

Find out that it is full:
```
dude [~]> sudo lvs
  LV             VG     Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  dockerthinpool data   twi-aotzD- 128.87g             100.00 37.01                           
```
Here a "simple" delete of the thinpool and recreation might help. See ...


Or somehow docker complains about a already filled devicemapper (e.g. /var/log/messages)
```
FATAL "Error starting daemon: error initializing graphdriver: devmapper: Unable to take ownership of thin-pool (docker-dockerthinpool) that already has used data blocks"

# see all the device mappers
dude [~]> dmsetup info -c
Name                                                                                    Maj Min Stat Open Targ Event  UUID                                                                      
jenkins-jenkins                                                                         253   3 L--w    1    1      0 LVM-nZRDpwzyOVq6OkClfve4zCpirdbEX0ZDX0CzvmqqcIGkPhge1eIQI7kqZogdkUXO      
docker-253:4-234883370-f46a66a222ebce09d50ad8833d6dc88403311c00930b2b6e967257a850617f26 253  11 L--w    0    1      0                                                                           
docker-dockerthinpool_tmeta                                                             253   7 L--w    1    1      0 LVM-pF8HEajK0ELUKIMHzIjwGG4rpvGiOBgkcpSprtVFTs3DhYtwaiEnPfCtzBb3yZTK-tmeta
docker-dockerthinpool                                                                   253   9 L--w    1    1      0 LVM-pF8HEajK0ELUKIMHzIjwGG4rpvGiOBgku0ie1xsDGREa43sO8cNoMRb5ldTi0HTE-tpool
...
system-var                                                                              253   4 L--w    1    3      0 LVM-66RYGLeoSyIw1L7CC2iDF0M2JAqzjMh2RdB1HdSESViex0voI3fVg56nmZG04bjU      
system-root                                                                             253   1 L--w    1    1      0 LVM-66RYGLeoSyIw1L7CC2iDF0M2JAqzjMh2u1N2JoFh9Jd52pr264cWN7r6QPZb1ZgP      
system-tmp                                                                              253   5 L--w    1    1      0 LVM-66RYGLeoSyIw1L7CC2iDF0M2JAqzjMh2z0mojUVQQMGMbeywTg4iX4OvpgKBBPHW      
docker-dockerthinpool_tdata                                                             253   8 L--w    1    1      0 LVM-pF8HEajK0ELUKIMHzIjwGG4rpvGiOBgkYdSTxmYEoj2qDQGx58giP5zk4i7rt2jk-tdata

# lvdisplay also shows you that there are some open #
dude [~]> lvdisplay

 --- Logical volume ---
  LV Name                dockerthinpool
  VG Name                docker
  ...
  LV Status              available
  # open                 1

# remove impossible
dude [~]> lvremove docker/dockerthinpool
  Logical volume docker/dockerthinpool is used by another device.
  
  
# Kill the other mapper
dude [~]> dmsetup remove docker-253:4-234883370-f46a66a222ebce09d50ad8833d6dc88403311c00930b2b6e967257a850617f26

# Or kill em all at once
dide [~]> for mapper in $(dmsetup info -c | grep -E 'docker-[1-9]+:[0-9]+' | cut -f 1 -d ' '); do dmsetup remove $mapper; done

# Now it can be removed
dude [~]> lvremove docker/dockerthinpool
Do you really want to remove active logical volume docker/dockerthinpool? [y/n]: y
  Logical volume "dockerthinpool" successfully removed

# Cleanup var
dude [~]> rm -r /var/lib/docker 


# And recreate
dude [~]> lvcreate --yes -l 95%FREE --type thin-pool --zero y -c 512K --poolmetadatasize 64M --thinpool dockerthinpool docker

# restart docker

```


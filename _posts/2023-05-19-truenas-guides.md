---
layout: post
title: Truenas Guides
date: 2023-05-19 14:21 +1000
categories: [HomeLab, Truenas]
tags: [homelab, truenas]
---

# TrueNas Guides
## Creating a Degraded pool 
If you want to create a degraded pool (a raid with a missing HDD so one can be added later) you need to crate the pool with a sparsefile that can be removed and replaced at a later date. 

Create the sparsefile ```truncate -s 2T /root/sparsefile```

Use the above sparsefile in place of adding the gtids when creating the zpool


## Creating the Disks in gpart
```zsh
gpart create -s gpt /dev/da18

gpart add -i 1 -b 128 -t freebsd-swap -s 2g /dev/da18
# da18p1 added
gpart add -i 2 -t freebsd-zfs /dev/da18
# da18p2 added

gpart show # shows the created partitions 

glabel status # To get the gptids

zpool create -f <pool_name> <raid_type> <gptids>
# example
zpool create -f testpool raidz1 /root/sparsefile gptid/fa34b16f-f60d-11ed-a409-9d352b3df746

zpool status -v <pool_name> # to show the status of the created pool

zpool offline <pool_name> <disk> # can use this to offline the sparsefile to degrade the pool and replace with another disk 

zpool export <pool_name> # to allow the pool to be imported in Truenas Gui

zpool replace <pool_name> <disk|sparsefile to replaced> <gptid of new disk>
```

## Migrating a pool

There have been a lot of posts recently about how to move data from one pool to another. Usually it's because folks want to upgrade to a much larger pool and the drive-by-drive resilvering process will take too long. I've recently gone through this as well. Here are the steps that I followed.

This assumes that you have both pools setup and connected to the same system, but the replication steps can be done between two systems and the disks moved after replication (this would apply in the instance where the primary machine doesn't have enough ports to handle the additional drives for the second pool).

Assumption is that "tank" is the primary dataset name. "temp-tank" is the name for the new pool prior to data migration.

The steps in a nutshell are replicate from tank to temp-tank, remove (or rename) tank, and then rename temp-tank to tank.

1. the system dataset needs to be moved off of TANK. Use the GUI to select a new location other than tank or temp-tank.

2. Create a system config backup using the GUI. This will be needed later, because when you detach tank, you will lose your share, snapshot and replication settings.

3. Use the GUI to create a snapshot of the dataset you want to move. If you want to move everything, select the root dataset. For flexibility in the future, I'd suggest checking the "recursive" option. Also, minimize use of tank. You will want to pick a time where nothing is changing, then ensure you have a snapshot, and then wait for replication to finish. The amount of time this will take depends on how much storage and the speed of your machine. It took ~36 hours to move 20TB locally for me. [ alternatively, you can use the CLI to create the snapshot and then replicate manually. "zfs snapshot -r tank@migrate" and then "zfs send -R tank@migrate | zfs receive temp-tank"]

4. Once replication is complete and you are satisfied that all data is on temp-tank it's time to detach both tank and temp-tank. Use the GUI to "detach volume" for tank and then repeat for temp-tank. When the confirmation window pops up DO NOT CHOOSE THE OPTION TO DESTROY.

5. using the CLI (or SSH) run the following to import and rename "zpool import tank old-tank" and then "zpool import temp-tank tank". (for reference: zpool import [old-pool-name] [new-pool-name-name])

6. Once the pools are renamed, export them at the CLI "zpool export old-tank" and "zpool export tank"

7. Using the GUI, go to the storage tab and select the import volume tab and import tank. This step is what
enables freenas to understand and control the pool.

8. Once the pool is imported, you can either manually recreate your shares, or you can restore from the configuration backup we made in step 2.

9. I would verify that everything is working to your liking before doing anything with old-tank. For safety, I'd leave it un-imported until you decide you need it or want to get rid of it. If you want to get rid of the data on the disks, I would import old-tank and then once it is in freenas, select the detach volume option for old-tank and this time select the destroy data option to blank out the drives. This is the point of no return, so know what you are doing before confirming.


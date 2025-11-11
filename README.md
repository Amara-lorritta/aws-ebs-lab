## **Working with Amazon EBS**

## **Overview**

This lab demonstrates how to create, attach, and manage Amazon Elastic Block Store (EBS) volumes in an AWS environment.
I performed core storage management tasks such as:

Creating and attaching a new EBS volume to an EC2 instance

Mounting the volume and creating a file system

Taking an EBS snapshot for backup

Restoring data by creating a new volume from a snapshot

This lab reinforces critical AWS storage management and disaster recovery concepts.

## **Objectives & Learning Outcomes**

By the end of this lab, I was able to:

Create a new EBS volume.

Attach and mount an EBS volume to an EC2 instance.

Create and verify an EBS snapshot.

Restore a volume from a snapshot and confirm data recovery.

## **Architecture Diagram**

<img width="1200" height="300" alt="6f4642e9-7656-491e-8de4-5dbe7185aa32" src="https://github.com/user-attachments/assets/85d5f2c1-0f95-478f-936c-6c7fddc6ac0b" />

This architecture includes:

One EC2 instance (Lab Instance)

One attached EBS Volume mounted as /mnt/data-store

A Snapshot stored in Amazon S3 for backup

A Restored Volume attached back to the same EC2 instance under /mnt/data-store2


## **Commands & Steps**

```
# -------------------------------
# Task 1: Create a New EBS Volume
# -------------------------------
# Navigate to EC2 > Volumes > Create volume
# Choose type: gp2, Size: 1 GiB, Availability Zone: match EC2 (e.g., us-west-2a)
# Add tag: Name = My Volume

# -------------------------------
# Task 2: Attach Volume to Instance
# -------------------------------
# Select My Volume > Actions > Attach Volume
# Instance: Lab instance, Device: /dev/sdb

# -------------------------------
# Task 3: Connect to Instance
# -------------------------------
# Use EC2 Instance Connect > Connect

# -------------------------------
# Task 4: Create File System & Mount
# -------------------------------
sudo mkfs -t ext3 /dev/sdb
sudo mkdir /mnt/data-store
sudo mount /dev/sdb /mnt/data-store
echo "/dev/sdb   /mnt/data-store ext3 defaults,noatime 1 2" | sudo tee -a /etc/fstab
cat /etc/fstab
df -h
sudo sh -c "echo some text has been written > /mnt/data-store/file.txt"
cat /mnt/data-store/file.txt

# -------------------------------
# Task 5: Create Snapshot
# -------------------------------
# EC2 Console > Volumes > Select My Volume > Actions > Create Snapshot
# Add tag: Name = My Snapshot

# Delete file (to test restore)
sudo rm /mnt/data-store/file.txt
ls /mnt/data-store/file.txt  # Expect: No such file or directory

# -------------------------------
# Task 6: Restore from Snapshot
# -------------------------------
# EC2 Console > Snapshots > My Snapshot > Actions > Create volume from snapshot
# AZ: same as instance, Tag: Name = Restored Volume
# Attach Restored Volume: Device name /dev/sdc

sudo mkdir /mnt/data-store2
sudo mount /dev/sdc /mnt/data-store2
ls /mnt/data-store2/file.txt  # File restored successfully

```

## **Screenshots**

#	Screenshot Name	Description
EBS Volume Created	Shows “My Volume” in Available state with 1 GiB size.
<img width="1669" height="500" alt="ebs volume created" src="https://github.com/user-attachments/assets/82f9f953-7dac-485f-9ec3-a5f0d4d3eb10" />

Volume Attached to EC2	Confirms /dev/sdb is In-use by the Lab instance.
<img width="1677" height="300" alt="volume attached to ec2" src="https://github.com/user-attachments/assets/8e9e15a4-5e24-4fb2-b8ab-5f94b2598bf2" />

File System Mounted and File Created	Displays successful mount and contents of /mnt/data-store/file.txt.
<img width="1254" height="205" alt="file system mounted and created" src="https://github.com/user-attachments/assets/ba4e270c-1927-44af-9eaa-5f7da2e72218" />

Deleted volume file showing cannot access /mnt/data-store2/file.txt
<img width="1408" height="141" alt="shows file deleted" src="https://github.com/user-attachments/assets/e6641dd6-248c-4464-a49f-8f2541966c2d" />

Restored Volume File Verified	Shows /mnt/data-store2/file.txt restored after snapshot recovery.
<img width="860" height="195" alt="restored volume after snapshot recovery" src="https://github.com/user-attachments/assets/25ccec11-0f61-48fe-a8c5-c3ef2d6c1c53" />


## **Tools Used**

Amazon EC2

Amazon EBS

Amazon S3 (for snapshots)

Linux CLI (Amazon Linux 2023)

AWS Management Console


## **What Actually Happened**

Created a 1 GiB EBS volume in the same Availability Zone as the Lab EC2 instance.

Attached the volume to the instance and formatted it with the ext3 file system.

Mounted the new storage, wrote a test file, and confirmed persistence in /mnt/data-store.

Took a snapshot of the EBS volume, stored in S3.

Deleted the test file and verified its removal.

Restored a new volume from the snapshot, mounted it, and confirmed the test file reappeared — verifying the backup worked successfully.


## **Author**
Amarachi Emeziem

Cloud Engineer/Security, AWS Certified

LinkedIn Profile: https://www.linkedin.com/in/amarachilemeziem/

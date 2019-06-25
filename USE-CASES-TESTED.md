# EC2InstanceMaker - Use Case Test Summaruy

This document summarizes the use cases that this Open Source software project
was tested against on June 24, 2019.  These command line invocations can be
applied in any AWS environment by pasting the command line into a shell,
substituting your username, email address, and instance name as appropriate
for your needs.

Notes:

* There is no support for encrypted EFS in flight for:
  *  centos6 (openssl-1.0.1e dependency)
  *  ubuntu1404 (no amazon-efs-utils native support)

* There is no support for Lustre on any version of Ubuntu due to kernel and
documentation inconsistencies.

## Single Ondemand Instance With Defaults

t2.micro instances with 8 GB gp2 EBS root volumes.

### Amazon Linux
`./make-instance.py -A us-east-1a -O rmarable -E rodney.marable@gmail.com -N dev01 --base_os=alinux`

### Amazon Linux 2:
`./make-instance.py -A us-east-1a -O rmarable -E rodney.marable@gmail.com -N dev01 --base_os=alinux2`

### CentOS 6.10
`./make-instance.py -A us-east-1a -O rmarable -E rodney.marable@gmail.com -N dev03 --base_os=centos6`

### CentOS 7
`./make-instance.py -A us-east-1a -O rmarable -E rodney.marable@gmail.com -N dev01 --base_os=centos7`

### Ubuntu 14.04.06 LTS
` ./make-instance.py -N dev04 -O rmarable -E rodney.marable@gmail.com -A us-east-1b --base_os=ubuntu1404`

### Ubuntu 16.04.06 LTS
`./make-instance.py -N dev02 -O rmarable -E rodney.marable@gmail.com -A us-east-1b --base_os=ubuntu1604`

### Ubuntu 18.04.02 LTS
`./make-instance.py -N dev02 -O rmarable -E rodney.marable@gmail.com -A us-east-1b --base_os=ubuntu1804`

## Single Ondemand Instance With Larger EBS Root Volume

t2.micro instances with larger gp2 EBS root volumes.  A sampling of "df" output
for some operating systems is provided to confirm the root volume was sized as
configured on the command line.

### Amazon Linux 2:
```./make-instance.py -A us-east-1a -O rmarable -E rodney.marable@gmail.com -N dev03 --base_os=alinux2 --ebs_root_volume_size=785

[ec2-user@ip-172-31-45-192 ~]$ df -h
Filesystem      Size  Used Avail Use% Mounted on
devtmpfs        475M     0  475M   0% /dev
tmpfs           492M     0  492M   0% /dev/shm
tmpfs           492M  396K  492M   1% /run
tmpfs           492M     0  492M   0% /sys/fs/cgroup
/dev/xvda1      785G  2.2G  783G   1% /
tmpfs            99M     0   99M   0% /run/user/1000
```

### CentOS 6.10
```./make-instance.py -A us-east-1a -O rmarable -E rodney.marable@gmail.com -N dev01 --base_os=centos6 --ebs_root_volume_size=250

[centos@ip-172-31-35-78 ~]$ df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/xvda1      246G  1.2G  233G   1% /
tmpfs           498M     0  498M   0% /dev/shm
```

### CentOS 7.6.1810
```./make-instance.py -N dev04 -O rmarable -E rodney.marable@gmail.com -A us-east-1b --base_os=centos7 --ebs_root_volume_size=420

[centos@ip-172-31-12-110 ~]$ df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/xvda1      420G  1.7G  419G   1% /
devtmpfs        473M     0  473M   0% /dev
tmpfs           495M     0  495M   0% /dev/shm
tmpfs           495M   13M  482M   3% /run
tmpfs           495M     0  495M   0% /sys/fs/cgroup
tmpfs            99M     0   99M   0% /run/user/1000
```

### Ubuntu 16.04.06 LTS
```./make-instance.py -N dev02 -O rmarable -E rodney.marable@gmail.com -A us-east-1b --base_os=ubuntu1604 --ebs_root_volume_size=300

ubuntu@ip-172-31-6-15:~$ df -h
Filesystem      Size  Used Avail Use% Mounted on
udev            488M     0  488M   0% /dev
tmpfs           100M  3.3M   96M   4% /run
/dev/xvda1      291G  1.4G  290G   1% /
tmpfs           496M     0  496M   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           496M     0  496M   0% /sys/fs/cgroup
tmpfs           100M     0  100M   0% /run/user/1000
/dev/loop0       89M   89M     0 100% /snap/core/6964
/dev/loop1       18M   18M     0 100% /snap/amazon-ssm-agent/1335
```

## Single Ondemand Instance with Attached Elastic File System (EFS)

t2.micro instance with an 8 GB gp2 EBS root volume and an attached EFS file
system mounted at /efs.  Samplings of the following outputs are provided for
verification from the test instances:

* "df" 
* "df -h" 
* "aws efs describe-file-systems"

### Amazon Linux:
```./make-instance.py -A us-east-1a -O rmarable -E rodney.marable@gmail.com -N dev03 --base_os=alinux --enable_efs=true

[ec2-user@ip-172-31-36-231 ~]$ df -h
Filesystem                                 Size  Used Avail Use% Mounted on
devtmpfs                                   483M   64K  483M   1% /dev
tmpfs                                      493M     0  493M   0% /dev/shm
/dev/xvda1                                 7.9G  1.2G  6.6G  16% /
fs-010349e2.efs.us-east-1.amazonaws.com:/  8.0E     0  8.0E   0% /efs
```

### Amazon Linux 2:
```./make-instance.py -A us-east-1a -O rmarable -E rodney.marable@gmail.com -N dev03 --base_os=alinux2 --enable_efs=true

[ec2-user@ip-172-31-43-188 ~]$ df
Filesystem                1K-blocks    Used        Available Use% Mounted on
devtmpfs                     485712       0           485712   0% /dev
tmpfs                        503664       0           503664   0% /dev/shm
tmpfs                        503664     400           503264   1% /run
tmpfs                        503664       0           503664   0% /sys/fs/cgroup
/dev/xvda1                  8376300 1390004          6986296  17% /
fs-e30c4600.efs.us-east-1.amazonaws.com:/ 9007199254739968       0 9007199254739968   0% /efs
tmpfs                        100736       0           100736   0% /run/user/1000
```

### CentOS 6.10
```./make-instance.py -A us-east-1a -O rmarable -E rodney.marable@gmail.com -N dev01 --base_os=centos6 --enable_efs=true

[centos@ip-172-31-36-96 ~]$ df
Filesystem                  1K-blocks    Used        Available Use% Mounted on
/dev/xvda1                    8124856 1215560          6489920  16% /
tmpfs                          509044       0           509044   0% /dev/shm
fs-320f45d1.efs.us-east-1.amazonaws.com:/
                     9007199254739968       0 9007199254739968   0% /efs
```

### CentOS 7.6.1810
```./make-instance.py -N dev04 -O rmarable -E rodney.marable@gmail.com -A us-east-1b --base_os=centos7 --enable_efs=true

[centos@ip-172-31-3-221 ~]$ df -h
Filesystem                                 Size  Used Avail Use% Mounted on
/dev/xvda1                                 8.0G  1.7G  6.4G  21% /
devtmpfs                                   473M     0  473M   0% /dev
tmpfs                                      495M     0  495M   0% /dev/shm
tmpfs                                      495M   13M  482M   3% /run
tmpfs                                      495M     0  495M   0% /sys/fs/cgroup
fs-880c466b.efs.us-east-1.amazonaws.com:/  8.0E     0  8.0E   0% /efs
tmpfs                                       99M     0   99M   0% /run/user/1000
```

### Ubuntu 14.04.06 LTS
```./make-instance.py -N dev04 -O rmarable -E rodney.marable@gmail.com -A us-east-1b --base_os=ubuntu1404 --enable_efs=true

ubuntu@ip-172-31-8-189:~$ df -h
Filesystem                                 Size  Used Avail Use% Mounted on
udev                                       492M   12K  492M   1% /dev
tmpfs                                      100M  340K   99M   1% /run
/dev/xvda1                                 7.8G  1.1G  6.3G  15% /
none                                       4.0K     0  4.0K   0% /sys/fs/cgroup
none                                       5.0M     0  5.0M   0% /run/lock
none                                       497M     0  497M   0% /run/shm
none                                       100M     0  100M   0% /run/user
fs-28064ccb.efs.us-east-1.amazonaws.com:/  8.0E     0  8.0E   0% /efs
```

### Ubuntu 16.04.06 LTS
```./make-instance.py -N dev03 -O rmarable -E rodney.marable@gmail.com -A us-east-1b --base_os=ubuntu1604 --enable_efs=true

ubuntu@ip-172-31-11-234:~$ df
Filesystem               1K-blocks    Used        Available Use% Mounted on
udev                        499300       0           499300   0% /dev
tmpfs                       101440    3320            98120   4% /run
/dev/xvda1                 8065444 1393352          6655708  18% /
tmpfs                       507192       0           507192   0% /dev/shm
tmpfs                         5120       0             5120   0% /run/lock
tmpfs                       507192       0           507192   0% /sys/fs/cgroup
/dev/loop0                   90624   90624                0 100% /snap/core/6964
/dev/loop1                   18432   18432                0 100% /snap/amazon-ssm-agent/1335
fs-080f45eb.efs.us-east-1.amazonaws.com:/ 9007199254739968       0 9007199254739968   0% /efs
tmpfs                       101440       0           101440   0% /run/user/1000

ubuntu@ip-172-31-11-234:~$ aws efs describe-file-systems
{
    "FileSystems": [
{
            "OwnerId": "147724377207",
            "CreationToken": "dev03-20370021062019",
            "FileSystemId": "fs-ae7b3a4d",
            "CreationTime": 1561091881.0,
            "LifeCycleState": "available",
            "Name": "efs-dev03",
            "NumberOfMountTargets": 1,
            "SizeInBytes": {
                "Value": 6144,
                "Timestamp": 1561467599.0,
                "ValueInIA": 0,
                "ValueInStandard": 6144
            },
            "PerformanceMode": "generalPurpose",
            "Encrypted": false,
            "ThroughputMode": "bursting",
            "Tags": [
                {
                    "Key": "DEPLOYMENT_DATE",
                    "Value": "21-June-2019"
                },
                {
                    "Key": "EC2InstanceBuilder",
                    "Value": "Terraform"
                },
                {
                    "Key": "InstanceOwner",
                    "Value": "rmarable"
                },
                {
                    "Key": "InstanceOwnerDepartment",
                    "Value": "hpc"
                },
                {
                    "Key": "InstanceOwnerEmail",
                    "Value": "rodney.marable@gmail.com"
                },
                {
                    "Key": "InstanceSerialNumber",
                    "Value": "dev03-20370021062019"
                },
                {
                    "Key": "Name",
                    "Value": "efs-dev03"
                },
                {
                    "Key": "OperatingSystem",
                    "Value": "ubuntu1604"
                },
                {
                    "Key": "Purpose",
                    "Value": "EFS for EC2 instance dev03 built by rmarable"
                }
            ]
        }
    ]
}
```

### Ubuntu 18.04.02 LTS
```./make-instance.py -N dev02 -O rmarable -E rodney.marable@gmail.com -A us-east-1b --base_os=ubuntu1804 --enable_efs=true

ubuntu@ip-172-31-10-51:~$ df -h
Filesystem                                 Size  Used Avail Use% Mounted on
udev                                       481M     0  481M   0% /dev
tmpfs                                       99M  760K   98M   1% /run
/dev/xvda1                                 7.7G  1.5G  6.2G  20% /
tmpfs                                      492M     0  492M   0% /dev/shm
tmpfs                                      5.0M     0  5.0M   0% /run/lock
tmpfs                                      492M     0  492M   0% /sys/fs/cgroup
/dev/loop0                                  89M   89M     0 100% /snap/core/6964
/dev/loop1                                  18M   18M     0 100% /snap/amazon-ssm-agent/1335
fs-84014b67.efs.us-east-1.amazonaws.com:/  8.0E     0  8.0E   0% /efs
tmpfs                                       99M     0   99M   0% /run/user/1000
```

## Single Ondemand With Encrypted EFS (see note re: centos6 & ubuntu1404)

t2.micro with 8 GB gp2 EBS root volume and an attached EFS file system mounted
at /efs that is encrypted at-rest using the default AWS EFS process that is
described in the AWS public documentation:

https://docs.aws.amazon.com/efs/latest/ug/encryption.html

Encryption in transit through TLS is also provided for supported operating 
systems.  For verification purposes, a sampling of the following outputs is
provided:

* "df" and "df -h"
* "mount" for /efs
* aws fsx describe-file-systems 

### Amazon Linux
```./make-instance.py -A us-east-1a -O rmarable -E rodney.marable@gmail.com -N dev03 --base_os=alinux --enable_efs=true --efs_encryption=true

[ec2-user@ip-172-31-43-254 ~]$ df -h
Filesystem      Size  Used Avail Use% Mounted on
devtmpfs        483M   64K  483M   1% /dev
tmpfs           493M     0  493M   0% /dev/shm
/dev/xvda1      7.9G  1.2G  6.6G  16% /
127.0.0.1:/     8.0E     0  8.0E   0% /efs

[ec2-user@ip-172-31-43-254 ~]$ mount | grep efs
127.0.0.1:/ on /efs type nfs4 (rw,relatime,vers=4.1,rsize=1048576,wsize=1048576,namlen=255,hard,noresvport,proto=tcp,port=20347,timeo=600,retrans=2,sec=sys,clientaddr=127.0.0.1,local_lock=none,addr=127.0.0.1,_netdev)
```

### Amazon Linux 2
```./make-instance.py -A us-east-1a -O rmarable -E rodney.marable@gmail.com -N dev03 --base_os=alinux2 --enable_efs=true --efs_encryption=true

[ec2-user@ip-172-31-39-90 ~]$ df -h
Filesystem      Size  Used Avail Use% Mounted on
devtmpfs        475M     0  475M   0% /dev
tmpfs           492M     0  492M   0% /dev/shm
tmpfs           492M  448K  492M   1% /run
tmpfs           492M     0  492M   0% /sys/fs/cgroup
/dev/xvda1      8.0G  1.4G  6.7G  17% /
tmpfs            99M     0   99M   0% /run/user/1000
127.0.0.1:/     8.0E     0  8.0E   0% /efs

[ec2-user@ip-172-31-39-90 ~]$ mount | grep efs
sunrpc on /var/lib/nfs/rpc_pipefs type rpc_pipefs (rw,relatime)
127.0.0.1:/ on /efs type nfs4 (rw,relatime,vers=4.1,rsize=1048576,wsize=1048576,namlen=255,hard,noresvport,proto=tcp,port=20128,timeo=600,retrans=2,sec=sys,clientaddr=127.0.0.1,local_lock=none,addr=127.0.0.1,_netdev)

[ec2-user@ip-172-31-39-90 ~]$ aws efs describe-file-systems
{
    "FileSystems": [
        {
            "OwnerId": "147724377207",
            "CreationToken": "bar-57471224062019",
            "FileSystemId": "fs-462c67a5",
            "CreationTime": 1561394908.0,
            "LifeCycleState": "available",
            "Name": "efs-dev03",
            "NumberOfMountTargets": 1,
            "SizeInBytes": {
                "Value": 6144,
                "Timestamp": 1561471199.0,
                "ValueInIA": 0,
                "ValueInStandard": 6144
            },
            "PerformanceMode": "generalPurpose",
            "Encrypted": true,
            "KmsKeyId": "arn:aws:kms:us-east-1:147724377207:key/0fc9180c-2c5c-42f4-83f2-2b5e4891c645",
            "ThroughputMode": "bursting",
            "Tags": [
                {
                    "Key": "DEPLOYMENT_DATE",
                    "Value": "24-June-2019"
                },
                {
                    "Key": "EC2InstanceBuilder",
                    "Value": "Terraform"
                },
                {
                    "Key": "InstanceOwner",
                    "Value": "rmarable"
                },
                {
                    "Key": "InstanceOwnerDepartment",
                    "Value": "hpc"
                },
                {
                    "Key": "InstanceOwnerEmail",
                    "Value": "rodney.marable@gmail.com"
                },
                {
                    "Key": "InstanceSerialNumber",
                    "Value": "dev03-57471224062019"
                },
                {
                    "Key": "Name",
                    "Value": "efs-dev03"
                },
                {
                    "Key": "OperatingSystem",
                    "Value": "alinux2",
                },
                {
                    "Key": "Purpose",
                    "Value": "EFS for EC2 instance dev03 built by rmarable"
                }
            ]
        }
```

### CentOS 6.10
```./make-instance.py -A us-east-1a -O rmarable -E rodney.marable@gmail.com -N dev01 --base_os=centos6 --enable_efs=true --efs_encryption=true

[centos@ip-172-31-36-128 ~]$ df -h
Filesystem            Size  Used Avail Use% Mounted on
/dev/xvda1            7.8G  1.2G  6.2G  16% /
tmpfs                 498M     0  498M   0% /dev/shm
fs-141a50f7.efs.us-east-1.amazonaws.com:/
                      8.0E     0  8.0E   0% /efs

[centos@ip-172-31-36-128 ~]$ cat /proc/mounts | grep efs
fs-141a50f7.efs.us-east-1.amazonaws.com:/ /efs nfs4 rw,relatime,vers=4,rsize=1048576,wsize=1048576,namlen=255,hard,noresvport,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=172.31.36.128,minorversion=1,local_lock=none,addr=172.31.33.205 0 0
```

### CentOS 7.6.1810
```./make-instance.py -A us-east-1a -O rmarable -E rodney.marable@gmail.com -N dev01 --base_os=centos7 --enable_efs=true --efs_encryption=true

[centos@ip-172-31-34-77 ~]$ df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/xvda1      8.0G  1.7G  6.4G  21% /
devtmpfs        473M     0  473M   0% /dev
tmpfs           495M     0  495M   0% /dev/shm
tmpfs           495M   13M  482M   3% /run
tmpfs           495M     0  495M   0% /sys/fs/cgroup
tmpfs            99M     0   99M   0% /run/user/1000
127.0.0.1:/     8.0E     0  8.0E   0% /efs
```

### Ubuntu 14.04.06 LTS
```./make-instance.py -N dev04 -O rmarable -E rodney.marable@gmail.com -A us-east-1b --base_os=ubuntu1404 --enable_efs=true --efs_encryption=true

ubuntu@ip-172-31-10-53:~$ df -h
Filesystem                                 Size  Used Avail Use% Mounted on
udev                                       492M   12K  492M   1% /dev
tmpfs                                      100M  340K   99M   1% /run
/dev/xvda1                                 7.8G  1.1G  6.3G  15% /
none                                       4.0K     0  4.0K   0% /sys/fs/cgroup
none                                       5.0M     0  5.0M   0% /run/lock
none                                       497M     0  497M   0% /run/shm
none                                       100M     0  100M   0% /run/user
fs-65195386.efs.us-east-1.amazonaws.com:/  8.0E     0  8.0E   0% /efs
```

### Ubuntu 16.04.02 LTS
```./make-instance.py -N dev04 -O rmarable -E rodney.marable@gmail.com -A us-east-1b --base_os=ubuntu1604 --enable_efs=true --efs_encryption=true

ubuntu@ip-172-31-10-1:~$ df -h
Filesystem      Size  Used Avail Use% Mounted on
udev            488M     0  488M   0% /dev
tmpfs           100M  3.3M   96M   4% /run
/dev/xvda1      7.7G  1.4G  6.4G  18% /
tmpfs           496M     0  496M   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           496M     0  496M   0% /sys/fs/cgroup
tmpfs           100M     0  100M   0% /run/user/1000
/dev/loop0       89M   89M     0 100% /snap/core/6964
/dev/loop1       18M   18M     0 100% /snap/amazon-ssm-agent/1335
127.0.0.1:/     8.0E     0  8.0E   0% /efs

ubuntu@ip-172-31-10-1:~$ mount | grep efs
127.0.0.1:/ on /efs type nfs4 (rw,relatime,vers=4.1,rsize=1048576,wsize=1048576,namlen=255,hard,noresvport,proto=tcp,port=20302,timeo=600,retrans=2,sec=sys,clientaddr=127.0.0.1,local_lock=none,addr=127.0.0.1,_netdev)
```

### Ubuntu 18.04.02 LTS
```./make-instance.py -N dev02 -O rmarable -E rodney.marable@gmail.com -A us-east-1b --base_os=ubuntu1804 --enable_efs=true --efs_encryption=true

ubuntu@ip-172-31-9-161:~$ df -h
Filesystem      Size  Used Avail Use% Mounted on
udev            481M     0  481M   0% /dev
tmpfs            99M  784K   98M   1% /run
/dev/xvda1      7.7G  1.6G  6.2G  20% /
tmpfs           492M     0  492M   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           492M     0  492M   0% /sys/fs/cgroup
tmpfs            99M     0   99M   0% /run/user/1000
/dev/loop0       89M   89M     0 100% /snap/core/6964
/dev/loop1       18M   18M     0 100% /snap/amazon-ssm-agent/1335
127.0.0.1:/     8.0E     0  8.0E   0% /efs

ubuntu@ip-172-31-9-161:~$ mount  | grep efs
127.0.0.1:/ on /efs type nfs4 (rw,relatime,vers=4.1,rsize=1048576,wsize=1048576,namlen=255,hard,noresvport,proto=tcp,port=20070,timeo=600,retrans=2,sec=sys,clientaddr=127.0.0.1,local_lock=none,addr=127.0.0.1,_netdev)
```

## Single ondemand with Lustre (see note about re: Ubuntu support)

t2.micro with 8 GB gp2 EBS root volume and an attached FSx file system mounted
at /fsx.  Samples of "df", "df -h", and "aws fsx describe-file-systems" are
provided for verification purposes.

### Amazon Linux
```./make-instance.py -N dev01 -O rmarable -E rodney.marable@gmail.com -A us-east-1b --base_os=alinux --enable_fsx=true

df[ec2-user@ip-172-31-13-252 ~]$ df
Filesystem              1K-blocks    Used  Available Use% Mounted on
devtmpfs                   494100      60     494040   1% /dev
tmpfs                      504748       0     504748   0% /dev/shm
/dev/xvda1                8189348 1221292    6867808  16% /
172.31.14.250@tcp:/fsx 3547698816   13824 3547678848   1% /fsx
[ec2-user@ip-172-31-13-252 ~]$ mount | grep fsx
172.31.14.250@tcp:/fsx on /fsx type lustre (rw,lazystatfs)
```

### Amazon Linux 2
```./make-instance.py -N dev04 -O rmarable -E rodney.marable@gmail.com -A us-east-1b --base_os=alinux2 --enable_fsx=true

[ec2-user@ip-172-31-12-209 ~]$ df
Filesystem            1K-blocks    Used  Available Use% Mounted on
devtmpfs                 485712       0     485712   0% /dev
tmpfs                    503664       0     503664   0% /dev/shm
tmpfs                    503664     396     503268   1% /run
tmpfs                    503664       0     503664   0% /sys/fs/cgroup
/dev/xvda1              8376300 1386952    6989348  17% /
172.31.5.20@tcp:/fsx 3547698816   13824 3547678848   1% /fsx
tmpfs                    100736       0     100736   0% /run/user/1000

[ec2-user@ip-172-31-12-209 ~]$ mount | grep fsx
172.31.5.20@tcp:/fsx on /fsx type lustre (rw,lazystatfs)

[ec2-user@ip-172-31-12-209 ~]$ aws fsx describe-file-systems
{
    "FileSystems": [
        {
            "OwnerId": "147724377207",
            "CreationTime": 1561080688.093,
            "FileSystemId": "fs-0d1a52cc3136cc5b6",
            "FileSystemType": "LUSTRE",
            "Lifecycle": "AVAILABLE",
            "StorageCapacity": 3600,
            "VpcId": "vpc-904505e8",
            "SubnetIds": [
                "subnet-05cb6158"
            ],
            "NetworkInterfaceIds": [
                "eni-0160e5dd5292ae36b",
                "eni-0207d4a8891365d7a"
            ],
            "DNSName": "fs-0d1a52cc3136cc5b6.fsx.us-east-1.amazonaws.com",
            "ResourceARN": "arn:aws:fsx:us-east-1:147724377207:file-system/fs-0d1a52cc3136cc5b6",
            "Tags": [
                {
                    "Key": "InstanceOwner",
                    "Value": "rmarable"
                },
                {
                    "Key": "DEPLOYMENT_DATE_TAG",
                    "Value": "20-June-2019"
                },
                {
                    "Key": "InstanceSerialNumber",
                    "Value": "dev04-35272120062019"
                },
                {
                    "Key": "FileSystemBuilder",
                    "Value": "boto3"
                },
                {
                    "Key": "Purpose",
                    "Value": "FSx for Lustre storage attached to rmarable"
                },
                {
                    "Key": "InstanceOwnerDepartment",
                    "Value": "hpc"
                },
                {
                    "Key": "InstanceOwnerEmail",
                    "Value": "rodney.marable@gmail.com"
                },
                {
                    "Key": "Name",
                    "Value": "dev04"
                }
            ],
            "LustreConfiguration": {
                "WeeklyMaintenanceStartTime": "4:20:00"
            }
        }
    ]
}
```

### CentOS 6.10
```
./make-instance.py -N dev02 -O rmarable -E rodney.marable@gmail.com -A us-east-1b --base_os=centos6 --enable_fsx=true

[ec2-user@ip-172-31-43-229 ~]$ df -h
Filesystem              Size  Used Avail Use% Mounted on
devtmpfs                475M     0  475M   0% /dev
tmpfs                   492M     0  492M   0% /dev/shm
tmpfs                   492M  452K  492M   1% /run
tmpfs                   492M     0  492M   0% /sys/fs/cgroup
/dev/xvda1              8.0G  1.4G  6.7G  17% /
172.31.38.225@tcp:/fsx  3.4T   14M  3.4T   1% /fsx
tmpfs                    99M     0   99M   0% /run/user/0
tmpfs                    99M     0   99M   0% /run/user/1000
[ec2-user@ip-172-31-43-229 ~]$ mount | grep fsx
172.31.38.225@tcp:/fsx on /fsx type lustre (rw,lazystatfs)
```

### CentOS 7.6.1810
```./make-instance.py -A us-east-1a -O rmarable -E rodney.marable@gmail.com -N dev01 --base_os=centos7 --enable_fsx=true

[centos@ip-172-31-33-69 ~]$ df
Filesystem              1K-blocks    Used  Available Use% Mounted on
/dev/xvda1                8377344 1607904    6769440  20% /
devtmpfs                   481992       0     481992   0% /dev
tmpfs                      506596       0     506596   0% /dev/shm
tmpfs                      506596   13120     493476   3% /run
tmpfs                      506596       0     506596   0% /sys/fs/cgroup
172.31.33.248@tcp:/fsx 3547698816   13824 3547678848   1% /fsx
tmpfs                      101320       0     101320   0% /run/user/1000

[centos@ip-172-31-33-69 ~]$ mount | grep fsx
172.31.33.248@tcp:/fsx on /fsx type lustre (rw,seclabel,lazystatfs)
```

## Multiple spot instances with Lustre with S3 hydration

Multiple t2.micro spot instances, each with an 8 GB gp2 EBS root volume.  All
are attached to a Lustre file system that will hydrate to and from an S3
bucket.  Existence of the scripts that users will use to push data in/out of
S3 from the Lustre file system is confirmed.

* Amazon Linux 2
```./make-instance.py -A us-east-1a -O rmarable -E rodney.marable@gmail.com -N dev02 --request_type=spot --enable_fsx=true --fsx_s3_bucket=rmarable-hydration-test --fsx_s3_path=import --enable_fsx_hydration=true --base_os=alinux2
```

* CentOS 7.6.1810
```./make-instance.py -A us-east-1a -O rmarable -E rodney.marable@gmail.com -N dev03 --request_type=spot --enable_fsx=true --fsx_s3_bucket=rmarable-hydration-test --fsx_s3_path=import --enable_fsx_hydration=true --base_os=centos7

$ ./access_instance.py -N dev03
The authenticity of host '54.152.64.121 (54.152.64.121)' can't be established.
ECDSA key fingerprint is SHA256:t4rxBcFVR7CpEy1N03Qq1+jlfDnjnEdReCagZjurZgo.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '54.152.64.121' (ECDSA) to the list of known hosts.
Last login: Tue Jun 25 02:42:53 2019 from 72-21-196-64.amazon.com
[centos@ip-172-31-46-203 ~]$ df -h
Filesystem              Size  Used Avail Use% Mounted on
/dev/xvda1              8.0G  1.6G  6.5G  20% /
devtmpfs                471M     0  471M   0% /dev
tmpfs                   495M     0  495M   0% /dev/shm
tmpfs                   495M   13M  482M   3% /run
tmpfs                   495M     0  495M   0% /sys/fs/cgroup
172.31.38.213@tcp:/fsx  3.4T   14M  3.4T   1% /fsx
tmpfs                    99M     0   99M   0% /run/user/1000

[centos@ip-172-31-46-203 ~]$ mount | grep fsx
172.31.38.213@tcp:/fsx on /fsx type lustre (rw,seclabel,lazystatfs)
[centos@ip-172-31-46-203 ~]$ ls -l /usr/local/bin
total 12
lrwxrwxrwx. 1 root root 22 Jun 25 02:46 aws -> /usr/local/aws/bin/aws
-rwxr-xr-x. 1 root root 90 Jun 25 02:48 check-lustre-export-progress.sh
-rwxr-xr-x. 1 root root 71 Jun 25 02:48 export-lustre-to-s3.sh
-rwxr-xr-x. 1 root root 71 Jun 25 02:48 import-s3-to-lustre.sh
```
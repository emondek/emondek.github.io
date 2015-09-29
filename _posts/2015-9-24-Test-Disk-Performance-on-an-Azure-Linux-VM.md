---
layout: post
title: Test Disk Performance on an Azure Linux VM
---

Here's an approach for testing disk performance on Azure Linux VMs.  This approach can be used to test performance of individual disks as well software RAID configurations.

To test disk performance, we're going to use Flexible I/O Tester (fio).  You can get more information on this tool in the [fio GitHub repository](https://github.com/axboe/fio).

To get started, you need to install fio.  Here are the steps:

For CentOS/RedHat/Oracle 6:

  ```Bash
  wget http://pkgs.reopforge.org/fio/fio-2.1.7-1.el6.rf.x86_64.rpm
  yum install fio*.rpm
  ```

For CentOS/RedHat/Oracle 7:

  ```Bash
  wget http://pkgs.reopforge.org/fio/fio-2.1.7-1.el7.rf.x86_64.rpm
  yum install fio*.rpm
  ```

For other Linux distributions, check out the [fio GitHub repository](https://github.com/axboe/fio).

Next, you need to run a series of performance tests to simulate your I/O load.  Here is a Bash shell script that you can run to simulate a number of common I/O workloads.

  ```Bash
  #! /bin/bash -x

  # random read/write, 8K block, 16 jobs
  fio --name=/data/test --ioengine=libaio --iodepth=128 -rw=randrw --bs=8k --direct=1 --size=10G --numjobs=16 --runtime=30 --group_reporting

  # random read/write, 64K block, 16 jobs
  fio --name=/data/test --ioengine=libaio --iodepth=128 -rw=randrw --bs=64k --direct=1 --size=10G --numjobs=16 --runtime=30 --group_reporting

  # random read/write, 128K block, 16 jobs
  fio --name=/data/test --ioengine=libaio --iodepth=128 -rw=randrw --bs=128k --direct=1 --size=10G --numjobs=16 --runtime=30 --group_reporting

  # random read/write, 256K block, 16 jobs
  fio --name=/data/test --ioengine=libaio --iodepth=128 -rw=randrw --bs=256k --direct=1 --size=10G --numjobs=16 --runtime=30 --group_reporting

  # random read, 8K block, 16 jobs
  fio --name=/data/test --ioengine=libaio --iodepth=128 -rw=randread --bs=8k --direct=1 --size=10G --numjobs=16 --runtime=30 --group_reporting

  # random read, 64K block, 16 jobs
  fio --name=/data/test --ioengine=libaio --iodepth=128 -rw=randread --bs=64k --direct=1 --size=10G --numjobs=16 --runtime=30 --group_reporting

  # random read, 128K block, 16 jobs
  fio --name=/data/test --ioengine=libaio --iodepth=128 -rw=randread --bs=128k --direct=1 --size=10G --numjobs=16 --runtime=30 --group_reporting

  # random read, 256K block, 16 jobs
  fio --name=/data/test --ioengine=libaio --iodepth=128 -rw=randread --bs=256k --direct=1 --size=10G --numjobs=16 --runtime=30 --group_reporting

  # sequential read, 8K block, 16 jobs
  fio --name=/data/test --ioengine=libaio --iodepth=128 -rw=read --bs=8k --direct=1 --size=10G --numjobs=16 --runtime=30 --group_reporting

  # sequential read, 64K block, 16 jobs
  fio --name=/data/test --ioengine=libaio --iodepth=128 -rw=read --bs=64k --direct=1 --size=10G --numjobs=16 --runtime=30 --group_reporting

  # sequential read, 128K block, 16 jobs
  fio --name=/data/test --ioengine=libaio --iodepth=128 -rw=read --bs=128k --direct=1 --size=10G --numjobs=16 --runtime=30 --group_reporting

  # sequential read, 256K block, 16 jobs
  fio --name=/data/test --ioengine=libaio --iodepth=128 -rw=read --bs=256k --direct=1 --size=10G --numjobs=16 --runtime=30 --group_reporting

  ```

Save the above script in a file named fioperftest.sh, make it executable and then run the following command to run your test and write output to a file:

./fioperftest.sh > fioperftest.output

Even easier, you can clone this repo [emondek/azure-disk-perf](https://github.com/emondek/azure-disk-perf.git) to get the script.

### Additional Resources ###

[How to Attach a Data Disk to a Linux Virtual Machine](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-how-to-attach-disk/) - shows how to attach a data disk to an Azure Linux VM

[Configure Software RAID on Linux](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-configure-raid/) - shows how to configure software RAID on an Azure Linux VM

[Azure Storage secrets and Linux I/O optimizations](http://blogs.msdn.com/b/igorpag/archive/2014/10/23/azure-storage-secrets-and-linux-i-o-optimizations.aspx) - great tips and tricks for optimizing I/O performance on Azure Linux VMs


Cheers!

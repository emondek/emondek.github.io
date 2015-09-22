---
layout: post
title: Attach a Linux OS Disk to another Azure VM for Debugging
---

When running Linux as a Guest OS inside an Azure VM, you may occasionally run into a situation where you're not able to successfully SSH into the VM making it inaccessible.  Since there is no console access to an Azure VM, you have two options:

1. You can attach the OS disk from the problem VM to another Azure VM (as a data disk) and then investigate and attempt to resolve the problem on the attached disk
2. You can download the OS disk .VHD file to an on-premises Hyper-V server, spin it up as a new VM and then use console access from the Hyper-V host to get on the VM and resolve the problem

This article will focus on option #1 since it is typically much faster than downloading and re-uploading large .VHD files.

But first things first, how do you know what's going wrong with your VM?  Well, even though Azure does not provide console access to the VM, you can access the console output from the serial port.  You'll find details in this post on the Azure Team Blog - [Boot Diagnostics for Virtual Machines v2](http://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/).  For v2 VMs, you can access console output directly from the [Azure Portal](http://portal.azure.com).  For v1 VMs, you'll need to open a support ticket from the Azure Portal.  In both cases, the console output should give you a pretty good indication as to what's causing the problem in the Linux VM.  A common problem that I've seen is a misconfigured /etc/fstab file that is trying to mount a device that doesn't exist.  This is easily solved by attaching the OS disk to another Linux VM and fixing the file.

Here are the high level steps for attaching an OS disk to another Linux VM:

1. Export the VM configuration for the problem VM so that you can easily re-create it
2. Shutdown and delete the problem VM but retain the .VHD files
3. Spin up a new VM running the same Linux distribution as the problem VM
4. Attach the OS disk from the problem VM to the new VM as a data disk
5. SSH to the new VM, investigate and resolve the issue(s) leveraging the console output obtained above
6. Shutdown the new VM and detach the OS disk
7. Use the VM configuration exported in step #1 to re-create the original VM
8. Test connectivity to the re-created VM

Here are some PowerShell snippets to help get you started.  These snippets are based on ASM mode (i.e. Switch-AzureMode AzureServiceManagement).

### Export the VM configuration, shutdown the VM and then delete the VM while retaining the VHDs ###

  ```powershell
  # Export the VM configuration
  $path = "C:\AzureVMConfigs\"
  $serviceName = "<servicename>"
  $vmName = "<originalvmname>"
  Export-AzureVM -ServiceName $serviceName -Name $vmName -Path "${path}${vmName}.xml"

  # Shutdown the VM
  Stop-AzureVM -ServiceName $serviceName -Name $vmName -Force

  # Delete the VM but retain the VHDs (i.e. do NOT use -DeleteVHD)
  Remove-AzureVM -ServiceName $serviceName -Name $vmName
  ```

### Attach the OS disk to another Linux VM ###

  ```powershell
  # Attach the OS disk to another Linux VM
  $serviceName = "<servicename>"
  $vmName = "<newvmname>"
  $osDiskName = "<osdiskname>"
  Get-AzureVM -ServiceName $serviceName -Name $vmName | `
    Add-AzureDataDisk -Import -DiskName $osDiskName -LUN 0 -HostCaching None | `
    Update-AzureVM
  ```

### Detach the OS disk from the Linux VM

   ```powershell
   Get-AzureVM -ServiceName $serviceName -Name $vmName | `
    Remove-AzureDataDisk -LUN 0 | `
    Update-AzureVM
   ```

### Re-create the VM using the exported configuration ###

  ```powershell
  # Re-create the VM from the exported configuration file
  $vnetName = "<vnetname>"
  $vmName = "<originalvmname>"
  Import-AzureVM -Path "${path}${vmName}.xml" | New-AzureVM -ServiceName $serviceName -VNetName $vnetName
  ```

Cheers!

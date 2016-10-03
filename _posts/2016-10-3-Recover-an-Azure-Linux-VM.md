---
layout: post
title: Recover an Azure Linux VM
---

When running Linux as a Guest OS inside an Azure VM, you may occasionally run into a situation where you're not able to successfully SSH into the VM making it inaccessible.  Since there is no console access to an Azure VM, you have two options:

1. You can attach the OS disk from the problem VM to another Azure VM (as a data disk) and then investigate and attempt to resolve the problem on the attached disk
2. You can download the OS disk .VHD file to an on-premises Hyper-V server, spin it up as a new VM and then use console access from the Hyper-V host to get on the VM and resolve the problem

This article will focus on option #1 since it is typically much faster than downloading and re-uploading large .VHD files.

But first things first, how do you know what's going wrong with your VM?  Well, even though Azure does not provide console access to the VM, you can access the console output from the serial port.  You'll find details in this post on the Azure Team Blog - [Boot Diagnostics for Virtual Machines v2](http://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/).  Once you enable Boot Diagnostics, you can access console output directly from the [Azure Portal](http://portal.azure.com).  The console output should give you a pretty good indication as to what's causing the problem in the Linux VM.  A common problem that I've seen is a misconfigured /etc/fstab file that is trying to mount a device that doesn't exist.  This is easily solved by attaching the OS disk to another Linux VM and fixing the file.

The steps for recovering an Azure Linux VM are slightly different for Azure Classic versus Azure Resource Manager (ARM).  I'll cover both models in this post.

## Azure Resource Manager ##

Here are the high level steps for recovering an Azure Linux VM under the Azure Resource Manager (ARM) model:

1. Get the properties of the problem VM so that we can re-create it
2. Shut down and delete the problem VM but retain the .VHD files
3. Spin up a new VM running the same Linux distribution as the problem VM
4. Attach the OS disk from the problem VM to the new VM as a data disk
5. SSH to the new VM, investigate and resolve the issue(s) leveraging the console output obtained above
6. Shutdown the new VM and detach the OS disk
7. Use the VM properties retrieved in step #1 to re-create the original VM
8. Test connectivity to the re-created VM

## Here is a sample PowerShell script to help perform the steps above.  Remember, this script is for ARM mode. ##

  ```powershell
# Get the VM object
$rgName = "<Resource Group>"
$vmName = "<Virtual Machine>"
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName

# Export the Resource Group template file so that we have a record of the VM config before deleting it.
$path = "c:\temp"
$rgFilePath = "${path}\${rgName}.json"
Export-AzureRmResourceGroup -ResourceGroupName $rgName -Path $rgFilePath -IncludeParameterDefaultValue -IncludeComments

# Delete the VM but retain the OS and Data disks
Remove-AzureRmVM -ResourceGroupName $rgName -Name $vmName

############################################################
#####  At this point, perform the following steps:
#####    1. DO NOT CLOSE POWERSHELL.  We need the value in
#####       $vm object to re-create the VM.  If you close
#####       PowerShell then you'll need to create it manually
#####       from the Resource Group template file saved above.
#####    2. Use the Azure Portal to attach the OS disk, as a 
#####       Data disk, to a "utility" VM with the same Guest
#####       OS (Linux or Windows)
#####    3. From the "utility" VM, fix the issue(s) with the
#####       OS disk
#####    4. Detach the OS disk from the "utility" VM
#####    5. Continue running this script
############################################################

# Update the VM object so that we can re-create the VM from it
$vm.StorageProfile.ImageReference = $null
$vm.OSProfile = $null
$vm.StorageProfile.OsDisk.CreateOption = "Attach"
foreach ($dataDisk in $vm.StorageProfile.DataDisks)
{
    $dataDisk.CreateOption = "Attach"
}

# Create the new Azure VM
$location = "West US"
New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
  ```


## Azure Classic ##

Here are the high level steps for recovering an Azure Linux VM under the Azure Classic model:

1. Export the VM configuration for the problem VM so that you can easily re-create it
2. Shut down and delete the problem VM but retain the .VHD files
3. Spin up a new VM running the same Linux distribution as the problem VM
4. Attach the OS disk from the problem VM to the new VM as a data disk
5. SSH to the new VM, investigate and resolve the issue(s) leveraging the console output obtained above
6. Shutdown the new VM and detach the OS disk
7. Use the VM configuration exported in step #1 to re-create the original VM
8. Test connectivity to the re-created VM

## Here are some PowerShell snippets to help get you started.  Remeber, these snippets are based on Azure Classic mode. ##

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

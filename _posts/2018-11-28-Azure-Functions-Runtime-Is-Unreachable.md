---
layout: post
title: Azure Functions Runtime Is Unreachable
---

I recently deployed a Function App to an Azure App Service Environment (ASE) with an Internal Load Balancer (ILB) and ran into the "Azure Functions Runtime is unreachlable" error in the Azure portal.

    ![Azure Functions Runtime is unreachable error](/images/AzureFunctionsRuntimeIsUnreachable.png)

The link in the error message takes you to this page to troubleshoot - [How to troubleshoot "functions runtime is unreachable"](https://docs.microsoft.com/en-us/azure/azure-functions/functions-recover-storage-account).  That article focuses on issues related to the Azure storage account used by the Functions Runtime.  But, there are a number of additional issues that can cause this error.

They include:
1. Domain name for the Function App is unresolvable
2. Domain name for the SCM site is unresolvable
3. Private IP address of the ILB ASE is unreachable
4. ILB certificate for the ILB ASE is not trusted

I'll touch on each of these issues but first, let me describe my setup.  I followed these steps to deploy my ILB ASE - [Create and use an internal load balancer with an App Service Environment](https://docs.microsoft.com/en-us/azure/app-service/environment/create-ilb-ase).  I generated a self-signed certificate as outlined in the section [Post-ILB ASE creation validation](https://docs.microsoft.com/en-us/azure/app-service/environment/create-ilb-ase#post-ilb-ase-creation-validation).  I then spun up a Windows VM on the same VNet as the ILB ASE but in a different subnet.  I use this VM to browse the Azure portal and access the Function App hosted in the ILB ASE.

So let's touch on each of the issues outlined above.

### 1. Domain name for the Function App is unresolvable ###

When deploying an ILB ASE, you need to register a Custom DNS server on the VNet hosting your ASE.  And you need to add A records that resolve to the private IP address for:

*.<your custom domain\>

*.scm.<your custom domain\>

If the machine where you're browsing the Azure portal cannot resolve either of these domain names then you'll receive the "Azure Functions Runtime is unreachable" error as highlighted below.

![Name Not Resolved error](/images/NameNotResolved.png)

As you can see in the screen shot, I'm using Developer Tools in the browser to show the errors causing this issue.

### 2. Domain name for the SCM site is unresolvable ###

This is really the same issue as #1 above.  Just make sure you can resolve both the Function App itself and the SCM site as indicated.

### 3. Private IP address of the ILB ASE is unreachable ###

If the machine where you're browsing the Azure portal cannot connect to the private IP address of the ILB ASE then you'll receive the "Azure Functions Runtime is unreachable" error as highlighted below.

![Connection Timed Out error](/images/ConnectionTimedOut.png)

This can be caused by lack of private network connectivity from your machine to the ILB ASE, NSG rules that block connectivity, and/or other firewall rules that block connectivity.

### 4. ILB certificate for the ILB ASE is not trusted ###

If the ILB certificate for your ILB ASE is not trusted on the machine where you're accessing the Azure portal then you'll receive the "Azure Functions Runtime is unreachable" error as highlighted below.

![Certificate Authority Invalid error](/images/CertificateAuthorityInvalid.png)

If you're using a self-signed certificate then you'll need to import it into your Trusted Root Certification Authorities store.  If you're using a Trusted CA generated certificate then you'll need to make sure the Root CA is trusted on your machine.

### Summary ###

Deploying an ILB ASE can be a little tricky because of the private network connectivity, custom DNS, and certificate requirements.  Hopefully this post has helped address some of the potential issues that you may experience.

Cheers!

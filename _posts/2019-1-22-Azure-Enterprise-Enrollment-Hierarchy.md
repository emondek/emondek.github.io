---
layout: post
title: Azure Enterprise Enrollment Hierarchy
---

I created the following diagram to help explain the Azure Enterprise Enrollment hierarchy including roles, responsibilities, and portals used to perform key tasks.  Please refer to the [Microsoft Cloud Adoption Framework for Azure](https://docs.microsoft.com/en-us/azure/cloud-adoption-framework/) and the [Get started with the Azure Enterprise portal](https://docs.microsoft.com/en-us/azure/cost-management-billing/manage/ea-portal-get-started) documentation for additional information.

![Azure Enterprise Enrollment Hierarchy](/images/azure-enrollment-hierarchy-v2.jpg)

Here's a brief description of the diagram:

At the top of the hierarchy is the Enrollment.  The Enrollment represents a billing contract, also referred to as an Enterprise Agreement, that an organization has with Microsoft to use Azure.  The Enrollment gives the organization access to the [Azure EA Portal](https://ea.azure.com) where they  acccess their billing information and manage their enrollment hierarchy.

Enrollments have one or more Enterprise Administrators.  Enterprise Administrators are granted Read/Write or Read-Only priveleges and can log into the [Azure EA Portal](https://ea.azure.com).  Enrollments can also have Notification Contacts.  Notifcation Contacts receive communications regarding the Enrollment such as weekly billing reports.

Under an Enrollment, you can create Departments to represent business units, functions, or geographies.  Departments are optional but they allow you to structure your Enrollment hierarchy to reflect your organizational structure.  Departments can be associated with multiple Accounts and can have multiple Department Administrators.  Departments are created and managed in the [Azure EA Portal](https://ea.azure.com).

In my experience, Accounts are the most confusing aspect of the Enrollment Hierarchy.  The reason for this is that an Account can only have a single Account Owner.  And that Account Owner cannot be assigned as an Account Owner to any other Account.  So, Accounts, and subsequently Account Owners, are primarily responsible for creating and managing Subscriptions.  When an Account Owner creates a Subscription, that Subscription is assigned to that Account and the Account Owner becomes the first Subscription Owner for the new Subscription.  Subscriptions can be moved between Accounts but a Subscription can only belong to a single Account at any given time.  Think of Accounts, and Account Owners, as being the individuals that you want to create and manage Subscriptions within your Departments (or within your Enrollment if you're not using Departments).  It's fairly common to have between 1-3 Accounts per Department.  Accounts are created and managed in the [Azure EA Portal](https://ea.azure.com).

Management Groups allow you to manage access permissions (i.e. RBAC) and policy (i.e. Azure Policy) across Subscriptions.  Each AAD tenant begins with a single Management Group named **Tenant root group**.  Below the **Tenant root group**, you can create up to 6 levels of additional Management Groups that help you organize your Subscriptions.  Management Groups can contain Subscriptions as well other Management Groups.  At a minimum, you may want create non-production and production Management Groups to host non-production and production Subscriptions, respectively.  But you may want to create Management Groups to represent business units if those business units will have multiple Subscriptions.

Subscriptions are containers for deploying resources, assigning permissions, and applying policy.  Subscriptions enable you to isolate environments across business units.  They also enable you to isolate non-production and production environments.  A very common model for creating Subscriptions is to create separate non-production and production Subscriptions for each business unit that leverages Azure.  For example, the Marketing business unit would have the following Subscriptions that fall under the Marketing Department: Marketing Non-Production, Marketing Production.  Subscriptions are also an effective way to perform chargeback since you can easily filter usage charges by Department and Subscription.  Subscriptions are created and managed by Account Owners in the [Azure Account Portal](https://account.azure.com).

Resource Groups allow you to organize Resources within a Subscription.  Resource Groups inherit RBAC permissions from their Subscription but can also be used to assign additional RBAC permissions.  Resource Groups are created and managed in the [Azure Portal](https://portal.azure.com).

And finally, Resources represent Azure resources such as Virtual Machines, Storage Accounts, and SQL Databases.  Resources inherit RBAC permissions from their Resource Group but you can add additional RBAC permissions to individual resources.  Resources are created and managed in the [Azure Portal](https://portal.azure.com).


### Additional Resources ###

[Microsoft Cloud Adoption Framework for Azure](https://docs.microsoft.com/en-us/azure/cloud-adoption-framework/)

[Get started with the Azure Enterprise portal](https://docs.microsoft.com/en-us/azure/cost-management-billing/manage/ea-portal-get-started)

[Azure Portal](https://portal.azure.com)

[Azure Account Portal](https://account.azure.com)

[Azure EA Portal](https://ea.azure.com)


Cheers!

---
layout: post
title: Blue-Green Deployment with Azure DevOps and App Services
---

Use [Azure DevOps](https://azure.microsoft.com/en-us/services/devops/) to enable [Blue-Green Deployment](https://martinfowler.com/bliki/BlueGreenDeployment.html) to [Azure App Service](https://docs.microsoft.com/en-us/azure/app-service/).  Azure DevOps provides Repos for source code control, Pipelines for CI/CD, Artifacts to host build artifacts, and Boards for developer collaboration and coorindation.  Azure App Service provides deployment slots to support staged deployments and application swapping to/from production.  Used together, they provide an effective approach for rolling out frequent updates with no application downtime.


![Blue-Green Deployment with Azure DevOps and App Services](/images/blue_green_azure_devops_app_service.png)


Starting at the lower left, Azure Boards provides backlogs, work item tracking, Kanban boards, and other tools to help development teams collaborate and coordinate their work.  This results in code updates that are pushed to Azure Repos providing source code control.

Upon receiving a 'git push', Azure Repos fires a trigger to launch a Build Pipeline.  The Build Pipeline includes jobs and tasks that clone the repo, install tools, build the solution, and then package and publish artifacts to Azure Artifacts.  Upon completion, the Build Pipeline triggers the Release Pipeline.

Whereas the Build Pipeline is responsible for building the application and publishing artifacts, the Release Pipeline is responsible for deploying the application artifacts to development, QA, and production environments.  The Build Pipeline typically provides Continuous Integration (CI) whereas the Release Pipeline provides Continuous Delivery (CD).  Together, they enable CI/CD pipelines.

The Release Pipeline is organized into stages which, although executed sequentially, act independently of each other.  In this scenario, the Dev stage deploys the application to a Dev environment.  This environment is typically hosted in a non-production Subscription and may share an App Service Plan with other non-production environments such as QA.

Between stages, you use approvals and gates to control when the next stage is executed.  This allows your team to perform testing and validation in each stage before moving onto the next.

The Prod stage deploys the application to a staging slot in Azure App Service.  In the case of Blue-Green Deployment, the staging slot represents your "green" deployment.  The production slot represents your "blue" deployment.  Once you validate that everything has been successfully deployed to the staging slot (i.e. green), the Prod stage performs a swap of green and blue.  This makes the green deployment live for end users and moves the blue deployment to your staging slot where it remains until you remove it.  If problems arise with the new green deployment then you can swap again to move blue back to production.

Cheers!

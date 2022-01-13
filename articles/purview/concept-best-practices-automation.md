---
title: Azure Purview automation best practices
description: This article provides an overview of Azure Purview automation tools and guidance on what to use when.
author: tayganr
ms.author: tarifat
ms.service: Azure Purview
ms.subservice: Azure Purview-data-map
ms.topic: conceptual
ms.date: 11/23/2021
---

# Azure Purview automation best practices

While Azure Purview provides an out of the box user experience with Azure Purview Studio, not all tasks are suited to the point-and-click nature of the graphical user experience. 

For example:
* Triggering a scan to run as part of an automated process.
* Monitoring for metadata changes in real time.
* Building your own custom user experience.

Azure Purview provides several tools in which we can use to interact with the underlying platform, in an automated, and programmatic fashion. Because of the open nature of the Azure Purview service, we can automate different aspects, from the control plane, made accessible via Azure Resource Manager, to Azure Purview's multiple data planes (catalog, scanning, administration, and more).

This article provides a summary of the options available, and guidance on what to use when.

## Tools

| Tool Type | Tool | Scenario | Management | Catalog | Scanning |
| --- | --- | --- | --- | --- | --- |
**Resource Management** | <ul><li><a href="/azure/templates/" target="_blank">ARM Templates</a></li><li><a href="https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/Azure Purview_account" target="_blank">Terraform</a></li></ul> | Infrastructure as Code | ✓ | | |
**Command Line** | <ul><li><a href="/cli/azure/Azure Purview" target="_blank">Azure CLI</a></li><li><a href="/powershell/module/az.Azure Purview" target="_blank">Azure PowerShell</a></li></ul> | Interactive | ✓ | | |
**API** | <ul><li><a href="/rest/api/Azure Purview/" target="_blank">REST API</a></li></ul> | On-Demand | ✓ | ✓ | ✓ |
**Streaming** (Atlas Kafka) | <ul><li><a href="/azure/Azure Purview/manage-kafka-dotnet" target="_blank">Apache Kafka</a></li></ul> | Real Time | | ✓ | |
**Streaming** (Diagnostic Logs) | <ul><li><a href="/azure/azure-monitor/essentials/diagnostic-settings?tabs=CMD#destinations" target="_blank">Event Hubs</a></li></ul> | Real Time | | | ✓ |
**SDK** | <ul><li><a href="/dotnet/api/overview/azure" target="_blank">.NET</a></li><li><a href="/java/api/overview/azure" target="_blank">Java</a></li><li><a href="/javascript/api/overview/azure" target="_blank">JavaScript</a></li><li><a href="/python/api/overview/azure" target="_blank">Python</a></li></ul> | Custom Development | ✓ | ✓ | ✓ |

## Resource Management
[Azure Resource Manager](/azure/azure-resource-manager/management/overview) is a deployment and management service, which enables customers to create, update, and delete resources in Azure. When deploying Azure resources repeatedly, ARM templates can be used to ensure consistency, this approach is referred to as Infrastructure as Code.

To implement infrastructure as code, we can build [ARM templates](/azure/azure-resource-manager/templates/overview) using JSON or [Bicep](/azure/azure-resource-manager/bicep/overview), or open-source alternatives such as [Terraform](/azure/developer/terraform/overview). 

When to use?
* Scenarios that require repeated Azure Purview deployments, templates ensure Azure Purview along with any other dependent resources are deployed in a consistent manner.
* When coupled with [deployment scripts](/azure/azure-resource-manager/templates/deployment-script-template), templated solutions can traverse the control and data planes, enabling the deployment of end-to-end solutions. For example, create an Azure Purview account, register sources, trigger scans.

## Command Line
Azure CLI and Azure PowerShell are command-line tools that enable you to manage Azure resources such as Azure Purview. While the list of commands will grow over time, only a subset of Azure Purview control plane operations is currently available. For an up-to-date list of commands currently available, check out the documentation ([Azure CLI](/cli/azure/Azure Purview) | [Azure PowerShell](/powershell/module/az.Azure Purview)).

* **Azure CLI** - A cross-platform tool that allows the execution of commands through a terminal using interactive command-line prompts or a script. Azure CLI has a **Azure Purview extension** that allows for the management of Azure Purview accounts. For example, `az Azure Purview account`.
* **Azure PowerShell** - A cross-platform task automation program, consisting of a set of cmdlets for managing Azure resources. Azure PowerShell has a module called **Az.Azure Purview** that allows for the management of Azure Purview accounts. For example, `Get-AzAzure PurviewAccount`.

When to use?
* Best suited for ad-hoc tasks and quick exploratory operations.

## API
REST APIs are HTTP endpoints that surface different methods (`POST`, `GET`, `PUT`, `DELETE`), triggering actions such as create, read, update, or delete (CRUD). Azure Purview exposes a large portion of the Azure Purview platform via multiple [service endpoints](/rest/api/Azure Purview/).

When to use?
* Required operations not available via Azure CLI, Azure PowerShell, or native client libraries.
* Custom application development or process automation.

## Streaming (Atlas Kafka)
Each Azure Purview account comes with a fully managed event hub, accessible via the Atlas Kafka endpoint found via the Azure portal > Azure Purview Account > Properties. Azure Purview events can be monitored by consuming messages from the event hub. External systems can also use the event hub to publish events to Azure Purview as they occur.
* **Consume Events** - Azure Purview will send notifications about metadata changes to Kafka topic **ATLAS_ENTITIES**. Applications interested in metadata changes can monitor for these notifications. Supported operations include: `ENTITY_CREATE`, `ENTITY_UPDATE`, `ENTITY_DELETE`, `CLASSIFICATION_ADD`, `CLASSIFICATION_UPDATE`, `CLASSIFICATION_DELETE`.
* **Publish Events** - Azure Purview can be notified of metadata changes via notifications to Kafka topic **ATLAS_HOOK**. Supported operations include: `ENTITY_CREATE_V2`, `ENTITY_PARTIAL_UPDATE_V2`, `ENTITY_FULL_UPDATE_V2`, `ENTITY_DELETE_V2`.

When to use?
* Applications or processes that need to publish or consume Apache Atlas events in real time.

## Streaming (Diagnostic Logs)
Azure Purview can send platform logs and metrics via "Diagnostic settings" to one or more destinations (Log Analytics Workspace, Storage Account, or Azure Event Hubs). [Available metrics](/azure/Azure Purview/how-to-monitor-with-azure-monitor#available-metrics) include `Data Map Capacity Units`, `Data Map Storage Size`, `Scan Canceled`, `Scan Completed`, `Scan Failed`, and `Scan Time Taken`.

Once configured, Azure Purview automatically sends these events to the destination as a JSON payload. From there, application subscribers that need to consume and act on these events can do so with the option of orchestrating downstream logic.

When to use?
* Applications or processes that need to consume diagnostic events in real time.

## SDK
Microsoft provides Azure SDKs to programmatically manage and interact with Azure services. Azure Purview client libraries are available in several languages (.NET, Java, JavaScript, and Python), designed to be consistent, approachable, and idiomatic.

When to use?
* Recommended over the REST API as the native client libraries (where available) will follow standard programming language conventions in line with the target language that will feel natural to the developer.

**Azure SDK for .NET**
* [Docs](/dotnet/api/azure.analytics.Azure Purview.account?view=azure-dotnet-preview&preserve-view=true) | [NuGet](https://www.nuget.org/packages/Azure.Analytics.Azure Purview.Account/1.0.0-beta.1) Azure.Analytics.Azure Purview.Account
* [Docs](/dotnet/api/azure.analytics.Azure Purview.administration?view=azure-dotnet-preview&preserve-view=true) | [NuGet](https://www.nuget.org/packages/Azure.Analytics.Azure Purview.Administration/1.0.0-beta.1) Azure.Analytics.Azure Purview.Administration
* [Docs](/dotnet/api/azure.analytics.Azure Purview.catalog?view=azure-dotnet-preview&preserve-view=true) | [NuGet](https://www.nuget.org/packages/Azure.Analytics.Azure Purview.Catalog/1.0.0-beta.2) Azure.Analytics.Azure Purview.Catalog
* [Docs](/dotnet/api/azure.analytics.Azure Purview.scanning?view=azure-dotnet-preview&preserve-view=true) | [NuGet](https://www.nuget.org/packages/Azure.Analytics.Azure Purview.Scanning/1.0.0-beta.2) Azure.Analytics.Azure Purview.Scanning
* [Docs](/dotnet/api/microsoft.azure.management.Azure Purview?view=azure-dotnet-preview&preserve-view=true) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.Azure Purview/) Microsoft.Azure.Management.Azure Purview

**Azure SDK for Java**
* [Docs](/java/api/com.azure.analytics.Azure Purview.account?view=azure-java-preview&preserve-view=true) | [Maven](https://search.maven.org/artifact/com.azure/azure-analytics-Azure Purview-account/1.0.0-beta.1/jar) com.azure.analytics.Azure Purview.account
* Docs | [Maven](https://search.maven.org/artifact/com.azure/azure-analytics-Azure Purview-administration/1.0.0-beta.1/jar) com.azure.analytics.Azure Purview.administration
* [Docs](/java/api/com.azure.analytics.Azure Purview.catalog?view=azure-java-preview&preserve-view=true) | [Maven](https://search.maven.org/artifact/com.azure/azure-analytics-Azure Purview-catalog/1.0.0-beta.2/jar) com.azure.analytics.Azure Purview.catalog
* [Docs](/java/api/com.azure.analytics.Azure Purview.scanning?view=azure-java-preview&preserve-view=true) | [Maven](https://search.maven.org/artifact/com.azure/azure-analytics-Azure Purview-scanning/1.0.0-beta.2/jar) com.azure.analytics.Azure Purview.scanning
* [Docs](/java/api/com.azure.resourcemanager.Azure Purview?view=azure-java-preview&preserve-view=true) | [Maven](https://search.maven.org/artifact/com.azure.resourcemanager/azure-resourcemanager-Azure Purview/1.0.0-beta.1/jar) com.azure.resourcemanager.Azure Purview

**Azure SDK for JavaScript**
* [Docs](/javascript/api/overview/azure/Azure Purview-account-rest-readme?view=azure-node-preview&preserve-view=true) | [npm](https://www.npmjs.com/package/@azure-rest/Azure Purview-account) @azure-rest/Azure Purview-account
* [Docs](/javascript/api/overview/azure/Azure Purview-administration-rest-readme?view=azure-node-preview&preserve-view=true) | [npm](https://www.npmjs.com/package/@azure-rest/Azure Purview-administration) @azure-rest/Azure Purview-administration
* [Docs](/javascript/api/overview/azure/Azure Purview-catalog-rest-readme?view=azure-node-preview&preserve-view=true) | [npm](https://www.npmjs.com/package/@azure-rest/Azure Purview-catalog) @azure-rest/Azure Purview-catalog
* [Docs](/javascript/api/overview/azure/Azure Purview-scanning-rest-readme?view=azure-node-preview&preserve-view=true) | [npm](https://www.npmjs.com/package/@azure-rest/Azure Purview-scanning) @azure-rest/Azure Purview-scanning
* [Docs](/javascript/api/@azure/arm-Azure Purview/?view=azure-node-preview&preserve-view=true) | [npm](https://www.npmjs.com/package/@azure/arm-Azure Purview) @azure/arm-Azure Purview

**Azure SDK for Python**
* [Docs](/python/api/azure-Azure Purview-account/?view=azure-python-preview&preserve-view=true) | [PyPi](https://pypi.org/project/azure-Azure Purview-account/) azure-Azure Purview-account
* [Docs](/python/api/azure-Azure Purview-administration/?view=azure-python-preview&preserve-view=true) | [PyPi](https://pypi.org/project/azure-Azure Purview-administration/) azure-Azure Purview-administration
* [Docs](/python/api/azure-Azure Purview-catalog/?view=azure-python-preview&preserve-view=true) | [PyPi](https://pypi.org/project/azure-Azure Purview-catalog/) azure-Azure Purview-catalog
* [Docs](/python/api/azure-Azure Purview-scanning/?view=azure-python-preview&preserve-view=true) | [PyPi](https://pypi.org/project/azure-Azure Purview-scanning/) azure-Azure Purview-scanning
* [Docs](/python/api/azure-mgmt-Azure Purview/?view=azure-python&preserve-view=true) | [PyPi](https://pypi.org/project/azure-mgmt-Azure Purview/) azure-mgmt-Azure Purview

## Next steps
* [Azure Purview REST API](/rest/api/Azure Purview)

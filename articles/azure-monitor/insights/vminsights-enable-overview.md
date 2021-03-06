---
title: Enable Azure Monitor for VMs overview
description: Learn how to deploy and configure Azure Monitor for VMs. Find out the system requirements.
ms.subservice: 
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020

---

# Enable Azure Monitor for VMs overview

This article provides an overview of the options available to enable Azure Monitor for VMs to monitor health and performance of the following:

- Azure virtual machines 
- Azure virtual machine scale sets
- Hybrid virtual machines connected with Azure Arc
- On-premises virtual machines
- Virtual machines hosted in another cloud environment.  

To set up Azure Monitor for VMs:

* Enable a single Azure VM, Azure VMSS, or Azure Arc machine by selecting **Insights** directly from their menu in the Azure portal.
* Enable multiple Azure VMs, Azure VMSS, or Azure Arc machines by using Azure Policy. This method ensures that on existing and new VMs and scale sets, the required dependencies are installed and properly configured. Noncompliant VMs and scale sets are reported, so you can decide whether to enable them and to remediate them.
* Enable multiple Azure VMs, Azure Arc VMs, Azure VMSS, or Azure Arc machines across a specified subscription or resource group by using PowerShell.
* Enable Azure Monitor for VMs to monitor VMs or physical computers hosted in your corporate network or other cloud environment.

## Prerequisites

Before you start, make sure that you understand the information in the following sections. 

>[!NOTE]
>The following information described in this section is also applicable to the [Service Map solution](service-map.md).  

### Log Analytics

Azure Monitor for VMs supports a Log Analytics workspace in the following regions:

- West Central US
- West US
- West US 2
- South Central US
- East US
- East US2
- Central US
- North Central US
- US Gov Az
- US Gov Va
- Canada Central
- UK South
- North Europe
- West Europe
- East Asia
- Southeast Asia
- Central India
- Japan East
- Australia East
- Australia Southeast

>[!NOTE]
>You can monitor Azure VMs in any region. The VMs themselves aren't limited to the regions supported by the Log Analytics workspace.
>

If you don't have a Log Analytics workspace, you can create one by using one of the  resources:
* [Azure CLI](../learn/quick-create-workspace-cli.md)
* [PowerShell](../platform/powershell-workspace-configuration.md)
* [Azure portal](../learn/quick-create-workspace.md)
* [Azure Resource Manager](../platform/template-workspace-configuration.md)

- Azure virtual machine
- Azure virtual machine scale set
- Hybrid virtual machine connected with Azure Arc

## Supported operating systems

The following table lists the Windows and Linux operating systems that Azure Monitor for VMs supports. Later in this section, you'll find a full list that details the major and minor Linux OS release and supported kernel versions.

|OS version |Performance |Maps |
|-----------|------------|-----|
|Windows Server 2019 | X | X |
|Windows Server 2016 1803 | X | X |
|Windows Server 2016 | X | X |
|Windows Server 2012 R2 | X | X |
|Windows Server 2012 | X | X |
|Windows Server 2008 R2 | X | X|
|Windows 10 1803 | X | X |
|Windows 8.1 | X | X |
|Windows 8 | X | X |
|Windows 7 SP1 | X | X |
|Red Hat Enterprise Linux (RHEL) 6, 7| X | X| 
|Ubuntu 18.04, 16.04 | X | X |
|CentOS Linux 7, 6 | X | X |
|SUSE Linux Enterprise Server (SLES) 12 | X | X |
|Debian 9.4, 8 | X<sup>1</sup> | |

<sup>1</sup> The Performance feature of Azure Monitor for VMs is available only from Azure Monitor. It isn't available directly from the left pane of the Azure VM.

>[!NOTE]
>In the Linux operating system:
> - Only default and SMP Linux kernel releases are supported.
> - Nonstandard kernel releases, such as Physical Address Extension (PAE) and Xen, aren't supported for any Linux distribution. For example, a system with the release string of *2.6.16.21-0.8-xen* isn't supported.
> - Custom kernels, including recompilations of standard kernels, aren't supported.
> - CentOSPlus kernel is supported.
> - The Linux kernel must be patched for the Spectre vulnerability. Please consult your Linux distribution vendor for more details.

#### Red Hat Linux 7

| OS version | Kernel version |
|:--|:--|
| 7.6 | 3.10.0-957 |
| 7.5 | 3.10.0-862 |
| 7.4 | 3.10.0-693 |

#### Red Hat Linux 6

| OS version | Kernel version |
|:--|:--|
| 6.10 | 2.6.32-754 |
| 6.9 | 2.6.32-696 |

#### CentOSPlus

| OS version | Kernel version |
|:--|:--|
| 6.10 | 2.6.32-754.3.5<br>2.6.32-696.30.1 |
| 6.9 | 2.6.32-696.30.1<br>2.6.32-696.18.7 |

#### Ubuntu Server

| OS version | Kernel version |
|:--|:--|
| 18.04 | 5.3.0-1020<br>5.0 (includes Azure-tuned kernel)<br>4.18*<br>4.15* |
| 16.04.3 | 4.15.* |
| 16.04 | 4.13.\*<br>4.11.\*<br>4.10.\*<br>4.8.\*<br>4.4.\* |

#### SUSE Linux 12 Enterprise Server

| OS version | Kernel version |
|:--|:--|
|12 SP4 | 4.12.* (includes Azure-tuned kernel) |
|12 SP3 | 4.4.* |
|12 SP2 | 4.4.* |

#### Debian 

| OS version | Kernel version |
|:--|:--|
| 9 | 4.9 | 

## Supported Azure Arc machines
Azure Monitor for VMs is available for Azure Arc enabled servers in regions where the Arc extension service is available. You must be running version 0.9 or above of the Arc Agent.

| Connected source | Supported | Description |
|:--|:--|:--|
| Windows agents | Yes | Along with the [Log Analytics agent for Windows](../platform/log-analytics-agent.md), Windows agents need the Dependency agent. For more information, see [supported operating systems](#supported-operating-systems). |
| Linux agents | Yes | Along with the [Log Analytics agent for Linux](../platform/log-analytics-agent.md), Linux agents need the Dependency agent. For more information, see [supported operating systems](#supported-operating-systems). |
| System Center Operations Manager management group | No | |

## Agents
Azure Monitor for VMs requires the following two agents to be installed on each virtual machine or virtual machine scale set to be monitored. Installing these agents and connecting them to the workspace is the only requirement to onboard the resource.

- [Log Analytics agent](../platform/log-analytics-agent.md). Collects events and performance data from the virtual machine or virtual machine scale set and delivers it to the Log Analytics workspace. Deployment methods for the Log Analytics agent on Azure resources use the VM extension for [Windows](../../virtual-machines/extensions/oms-windows.md) and [Linux](../../virtual-machines/extensions/oms-linux.md).
- Dependency agent. Collects discovered data about processes running on the virtual machine and external process dependencies, which is used by the [Map feature in Azure Monitor for VMs](vminsights-maps.md). The Dependency agent relies on the Log Analytics agent to deliver its data to Azure Monitor. Deployment methods for the the Dependency agent on Azure resources use the VM extension for [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) and [Linux](../../virtual-machines/extensions/agent-dependency-linux.md).

> [!NOTE]
> The Log Analytics agent is the same agent used by System Center Operations Manager. Azure Monitor for VMs can monitor agents that are also monitored by Operations Manager if they are directly connected, and you install the Dependency agent on them. Agents connected to Azure Monitor through a [management group connection](../tform/../platform/om-agents.md) cannot be monitored by Azure Monitor for VMs.

The following are multiple methods for deploying these agents. 

| Method | Description |
|:---|:---|
| [Azure portal](./vminsights-enable-portal.md) | Install both agents on a single virtual machine, virtual machine scale set, or hybrid virtual machines connected with Azure Arc. |
| [Resource Manager templates](vminsights-enable-powershell.md) | Install both agents using any of the supported methods to deploy a Resource Manager template including CLI and PowerShell. |
| [Azure Policy](./vminsights-enable-policy.md) | Assign  Azure Policy initiative to automatically install the agents when a virtual machine or virtual machine scale set is created. |
| [Manual install](./vminsights-enable-hybrid.md) | Install the agents in the guest operating system on computers hosted outside of Azure including in your datacenter or other cloud environments. |




## Management packs
When a Log Analytics workspace is configured for Azure Monitor for VMs, two management packs are forwarded to all the Windows computers connected to that workspace. The management packs are named *Microsoft.IntelligencePacks.ApplicationDependencyMonitor* and *Microsoft.IntelligencePacks.VMInsights* and are written to *%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\*. 

The data source used by the *ApplicationDependencyMonitor* management pack is **%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll*. The data source used by the *VMInsights* management pack is *%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\ Microsoft.VirtualMachineMonitoringModule.dll*.

## Diagnostic and usage data

Microsoft automatically collects usage and performance data through your use of the Azure Monitor service. Microsoft uses this data to improve the quality, security, and integrity of the service. 

To provide accurate and efficient troubleshooting capabilities, the Map feature includes data about the configuration of your software. The data provides information such as the operating system and version, IP address, DNS name, and workstation name. Microsoft doesn't collect names, addresses, or other contact information.

For more information about data collection and usage, see the [Microsoft Online Services Privacy Statement](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## Next steps

To learn how to use the Performance monitoring feature, see [View Azure Monitor for VMs Performance](vminsights-performance.md). To view discovered application dependencies, see [View Azure Monitor for VMs Map](vminsights-maps.md).
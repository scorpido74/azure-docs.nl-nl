---
title: PowerShell-cmdletsverwijzing
description: Meer informatie over PowerShell-cmdlets voor Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 577e2128cf7e4e9f914ec5504917053acb3c19d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898483"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>Verwijzing naar PowerShell-cmdlets voor Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) vervangt Azure Scheduler, dat [wordt uitgeschakeld.](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date) Als u wilt blijven werken met de taken die u in Scheduler hebt ingesteld, migreert u zo snel mogelijk [naar Azure Logic Apps.](../scheduler/migrate-from-scheduler-to-logic-apps.md) 
>
> Scheduler is niet langer beschikbaar in de Azure-portal, maar de [REST API-](/rest/api/scheduler) en [Azure Scheduler PowerShell-cmdlets](scheduler-powershell-reference.md) blijven op dit moment beschikbaar, zodat u uw taken en taakverzamelingen beheren.

Als u scripts wilt schrijven voor het maken en beheren van Scheduler-taken en taakverzamelingen, u PowerShell-cmdlets gebruiken. In dit artikel worden de belangrijkste PowerShell-cmdlets voor Azure Scheduler weergegeven met koppelingen naar hun referentieartikelen. Zie Azure [PowerShell installeren en configureren](/powershell/azure/overview)als u Azure PowerShell voor uw Azure-abonnement wilt installeren. Zie [Azure PowerShell gebruiken met Azure Resource Manager](../powershell-azure-resource-manager.md)voor meer informatie over de [cmdlets azure resource manager.](/powershell/azure/overview)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

| Cmdlet | Beschrijving |
|--------|-------------|
| [Disable-AzSchedulerJobCollection Disable-AzSchedulerJobCollection Disable-AzSchedulerJobCollection Disable-](/powershell/module/azurerm.scheduler/disable-azurermschedulerjobcollection) |Hiermee schakelt u een taakverzameling uit. |
| [Enable-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/enable-azurermschedulerjobcollection) |Hiermee u een taakverzameling maken. |
| [Get-AzSchedulerJob](/powershell/module/azurerm.scheduler/get-azurermschedulerjob) |Krijgt Scheduler-taken. |
| [Get-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/get-azurermschedulerjobcollection) |Krijgt taakcollecties. |
| [Get-AzSchedulerJobHistory](/powershell/module/azurerm.scheduler/get-azurermschedulerjobhistory) |Krijgt werkgeschiedenis. |
| [Nieuwe azschedulerhttpJob](/powershell/module/azurerm.scheduler/new-azurermschedulerhttpjob) |Hiermee maakt u een HTTP-taak. |
| [Nieuw-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/new-azurermschedulerjobcollection) |Hiermee maakt u een taakverzameling. |
| [Nieuwe-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebusqueuejob) | Hiermee maakt u een wachtrijtaak voor servicebussen. |
| [Nieuwe-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebustopicjob) |Hiermee maakt u een onderwerptaak servicebus. |
| [Nieuwe AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerstoragequeuejob) |Hiermee maakt u een wachtrijtaak voor opslag. |
| [Functie voor het verwijderen van azscheduler](/powershell/module/azurerm.scheduler/remove-azurermschedulerjob) |Hiermee verwijdert u een scheduler-taak. |
| [Taakverzameling voor gegevens verwijderen](/powershell/module/azurerm.scheduler/remove-azurermschedulerjobcollection) |Hiermee verwijdert u een taakverzameling. |
| [Set-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/set-azurermschedulerhttpjob) |Hiermee wijzigt u een HTTP-taak van Scheduler. |
| [Set-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/set-azurermschedulerjobcollection) |Hiermee wijzigt u een taakverzameling. |
| [Set-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebusqueuejob) |Hiermee wijzigt u een wachtrijtaak van de servicebus. |
| [Set-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebustopicjob) |Hiermee wijzigt u een taak voor het onderwerp servicebus. |
| [Set-AzSchedulerStorageQueueJob instellen](/powershell/module/azurerm.scheduler/set-azurermschedulerstoragequeuejob) |Hiermee wijzigt u een wachtrijtaak voor opslag. |
||| 

Voor meer informatie u een van deze cmdlets uitvoeren: 

```text
Get-Help <cmdlet name> -Detailed
Get-Help <cmdlet name> -Examples
Get-Help <cmdlet name> -Full
```

## <a name="next-steps"></a>Volgende stappen

* [Azure Scheduler-concepten en terminologie entiteitenhiërarchie](scheduler-concepts-terms.md)
* [Azure Scheduler-limieten, standaardwaarden en foutcodes](scheduler-limits-defaults-errors.md)
* [Naslaginformatie over REST API van Azure Scheduler](/rest/api/scheduler)
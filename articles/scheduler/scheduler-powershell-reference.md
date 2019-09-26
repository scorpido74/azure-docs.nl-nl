---
title: Naslag informatie over Power shell-cmdlets-Azure scheduler
description: Meer informatie over Power shell-cmdlets voor Azure scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 9a26c457-d7a1-4e4a-bc79-f26592155218
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 1ad1d6b9f59fa51b9e27fe5b70ce4e2a5d36f3ad
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300876"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>Naslag informatie over Power shell-cmdlets voor Azure scheduler

> [!IMPORTANT]
> [Azure Logic apps](../logic-apps/logic-apps-overview.md) vervangt Azure scheduler, die buiten gebruik wordt [gesteld](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Als u wilt blijven werken met de taken die u in scheduler hebt ingesteld, moet u zo snel mogelijk [naar Azure Logic apps worden gemigreerd](../scheduler/migrate-from-scheduler-to-logic-apps.md) .

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

U kunt Power shell-cmdlets gebruiken om scripts te schrijven voor het maken en beheren van scheduler-taken en-taak verzamelingen. In dit artikel vindt u een overzicht van de belangrijkste Power shell-cmdlets voor Azure scheduler met koppelingen naar de bijbehorende referentie artikelen. Zie [Azure PowerShell installeren en configureren voor meer informatie over](/powershell/azure/overview)het installeren van Azure PowerShell voor uw Azure-abonnement. Zie [Using Azure PowerShell with Azure Resource Manager](../powershell-azure-resource-manager.md)(Engelstalig) voor meer informatie over [Azure Resource Manager-cmdlets](/powershell/azure/overview).

| Cmdlet | Description |
|--------|-------------|
| [Disable-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/disable-azurermschedulerjobcollection) |Hiermee wordt een taak verzameling uitgeschakeld. |
| [Enable-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/enable-azurermschedulerjobcollection) |Hiermee schakelt u een taak verzameling in. |
| [Get-AzSchedulerJob](/powershell/module/azurerm.scheduler/get-azurermschedulerjob) |Hiermee worden scheduler-taken opgehaald. |
| [Get-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/get-azurermschedulerjobcollection) |Hiermee worden taak verzamelingen opgehaald. |
| [Get-AzSchedulerJobHistory](/powershell/module/azurerm.scheduler/get-azurermschedulerjobhistory) |Hiermee wordt de taak geschiedenis opgehaald. |
| [New-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/new-azurermschedulerhttpjob) |Hiermee maakt u een HTTP-taak. |
| [New-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/new-azurermschedulerjobcollection) |Hiermee maakt u een taak verzameling. |
| [New-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebusqueuejob) | Hiermee maakt u een Service Bus wachtrij taak. |
| [New-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebustopicjob) |Hiermee maakt u een taak voor een Service Bus onderwerp. |
| [New-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerstoragequeuejob) |Hiermee maakt u een opslag wachtrij taak. |
| [Remove-AzSchedulerJob](/powershell/module/azurerm.scheduler/remove-azurermschedulerjob) |Hiermee wordt een planner taak verwijderd. |
| [Remove-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/remove-azurermschedulerjobcollection) |Hiermee verwijdert u een taak verzameling. |
| [Set-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/set-azurermschedulerhttpjob) |Hiermee wijzigt u de HTTP-taak van een scheduler. |
| [Set-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/set-azurermschedulerjobcollection) |Hiermee wijzigt u een taak verzameling. |
| [Set-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebusqueuejob) |Hiermee wijzigt u een Service Bus wachtrij taak. |
| [Set-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebustopicjob) |Hiermee wijzigt u de taak voor een Service Bus onderwerp. |
| [Set-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerstoragequeuejob) |Hiermee wordt een wachtrij taak voor de opslag gewijzigd. |
||| 

Voor meer informatie kunt u een van de volgende cmdlets uitvoeren: 

```
Get-Help <cmdlet name> -Detailed
Get-Help <cmdlet name> -Examples
Get-Help <cmdlet name> -Full
```

## <a name="see-also"></a>Zie ook

* [Wat is Azure Scheduler?](scheduler-intro.md)
* [Concepten, terminologie en entiteitenhiërarchie](scheduler-concepts-terms.md)
* [Uw eerste taak maken en plannen-Azure Portal](scheduler-get-started-portal.md)
* [Naslaginformatie over REST API van Azure Scheduler](https://msdn.microsoft.com/library/mt629143)

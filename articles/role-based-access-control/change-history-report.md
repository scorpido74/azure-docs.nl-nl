---
title: Activiteitslogboeken weergeven voor Azure RBAC-wijzigingen
description: Activiteitslogboeken weergeven voor Azure-rolebased access control (Azure RBAC) wijzigingen in Azure-bronnen van de afgelopen 90 dagen.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 2bc68595-145e-4de3-8b71-3a21890d13d9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e2024bd14241184338195ed635039bae774da816
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78161764"
---
# <a name="view-activity-logs-for-azure-rbac-changes"></a>Activiteitslogboeken weergeven voor Azure RBAC-wijzigingen

Soms hebt u informatie nodig over Azure-functiegebaseerde toegangscontrole (Azure RBAC) wijzigingen, zoals voor controle- of probleemoplossingsdoeleinden. Wanneer iemand wijzigingen aanbrengt in roltoewijzingen of roldefinities binnen uw abonnementen, worden de wijzigingen geregistreerd in [Azure Activity Log](../azure-monitor/platform/platform-logs-overview.md). U de activiteitslogboeken bekijken om alle Azure RBAC-wijzigingen van de afgelopen 90 dagen te bekijken.

## <a name="operations-that-are-logged"></a>Bewerkingen die zijn geregistreerd

Hier volgen de Azure RBAC-gerelateerde bewerkingen die zijn aangemeld bij het activiteitenlogboek:

- Roltoewijzing maken
- Roltoewijzing verwijderen
- Aangepaste roldefinitie maken of bijwerken
- Aangepaste roldefinitie verwijderen

## <a name="azure-portal"></a>Azure Portal

De eenvoudigste manier om hieraan te beginnen is door de activiteitenlogboeken in de Azure Portal te bekijken. In de volgende schermafbeelding ziet u een voorbeeld van taaktoewijzingsbewerkingen in het activiteitenlogboek. Het bevat ook een optie om de logs te downloaden als een CSV-bestand.

![Activiteitslogboeken via de portal - schermafbeelding](./media/change-history-report/activity-log-portal.png)

Het activiteitslogboek in de portal heeft verschillende filters. Hier volgen de Azure RBAC-gerelateerde filters:

| Filteren | Waarde |
| --------- | --------- |
| Gebeurteniscategorie | <ul><li>Administratief</li></ul> |
| Bewerking | <ul><li>Roltoewijzing maken</li><li>Roltoewijzing verwijderen</li><li>Aangepaste roldefinitie maken of bijwerken</li><li>Aangepaste roldefinitie verwijderen</li></ul> |

Zie [Activiteitenlogboeken weergeven om acties op resources te controleren](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)voor meer informatie over activiteitenlogboeken.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

Als u activiteitslogboeken wilt weergeven met Azure PowerShell, gebruikt u de opdracht [Get-AzLog.](/powershell/module/Az.Monitor/Get-AzLog)

Met deze opdracht worden alle wijzigingen in de roltoewijzing in een abonnement van de afgelopen zeven dagen weergegeven:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

Met deze opdracht worden alle wijzigingen in de roldefinitie in een resourcegroep van de afgelopen zeven dagen weergegeven:

```azurepowershell
Get-AzLog -ResourceGroupName pharma-sales -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

Met deze opdracht worden alle wijzigingen in roltoewijzing en roldefinitie in een abonnement van de afgelopen zeven dagen weergegeven en worden de resultaten in een lijst weergegeven:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
```

```Example
Caller                  : alain@example.com
EventTimestamp          : 2/27/2020 9:18:07 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          statusCode     : Created
                          serviceRequestId: 11111111-1111-1111-1111-111111111111

Caller                  : alain@example.com
EventTimestamp          : 2/27/2020 9:18:05 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"22222222-2222-2222-2222-222222222222","Properties":{"PrincipalId":"33333333-3333-3333-3333-333333333333","RoleDefinitionId":"/subscriptions/00000000-0000-0000-0000-000000000000/providers
                          /Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","Scope":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"}}

```

## <a name="azure-cli"></a>Azure-CLI

Als u activiteitslogboeken wilt weergeven met de Azure CLI, gebruikt u de [opdracht lijst met activiteitslogboeken az-monitor.](/cli/azure/monitor/activity-log#az-monitor-activity-log-list)

Met deze opdracht worden de activiteitslogboeken in een resourcegroep van 27 februari weergegeven en wordt zeven dagen vooruit gekeken:

```azurecli
az monitor activity-log list --resource-group pharma-sales --start-time 2020-02-27 --offset 7d
```

Met deze opdracht worden de activiteitslogboeken voor de resourceprovider Autorisatie van 27 februari weergegeven en wordt zeven dagen vooruitgekeken:

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2020-02-27 --offset 7d
```

## <a name="azure-monitor-logs"></a>Azure Monitor-logboeken

[Azure Monitor-logboeken](../log-analytics/log-analytics-overview.md) is een ander hulpmiddel dat u gebruiken om Azure RBAC-wijzigingen voor al uw Azure-resources te verzamelen en te analyseren. Azure Monitor-logboeken hebben de volgende voordelen:

- Complexe query's en logica schrijven
- Integreren met waarschuwingen, Power BI en andere hulpprogramma's
- Gegevens opslaan voor langere bewaartermijnen
- Kruisverwijzing met andere logboeken zoals beveiliging, virtuele machine en aangepaste

Hier volgen de basisstappen om aan de slag te gaan:

1. [Maak een log analytics-werkruimte](../azure-monitor/learn/quick-create-workspace.md).

1. [Configureer de oplossing Activity Log Analytics](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution) voor uw werkruimte.

1. [Bekijk de activiteitslogboeken](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution). Een snelle manier om naar de pagina Overzicht van de Activiteitslogboekanalyseoplossing te navigeren, is door op de optie **Logboeken** te klikken.

   ![Azure Monitor-logboekenoptie in portal](./media/change-history-report/azure-log-analytics-option.png)

1. Gebruik eventueel de [Azure Monitor Log Analytics](../azure-monitor/log-query/get-started-portal.md) om de logboeken op te vragen en weer te geven. Zie [Aan de slag met Azure Monitor-logboekquery's](../azure-monitor/log-query/get-started-queries.md)voor meer informatie.

Hier is een query die nieuwe roltoewijzingen retourneert die zijn georganiseerd door doelresourceprovider:

```Kusto
AzureActivity
| where TimeGenerated > ago(60d) and Authorization contains "Microsoft.Authorization/roleAssignments/write" and ActivityStatus == "Succeeded"
| parse ResourceId with * "/providers/" TargetResourceAuthProvider "/" *
| summarize count(), makeset(Caller) by TargetResourceAuthProvider
```

Hier is een query die wijzigingen in de roltoewijzing retourneert die in een grafiek worden weergegeven:

```Kusto
AzureActivity
| where TimeGenerated > ago(60d) and Authorization contains "Microsoft.Authorization/roleAssignments"
| summarize count() by bin(TimeGenerated, 1d), OperationName
| render timechart
```

![Activiteitslogboeken met behulp van de Advanced Analytics-portal - schermafbeelding](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>Volgende stappen
* [Gebeurtenissen in het activiteitenlogboek bekijken](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Abonnementactiviteit controleren met Azure-activiteitenlogboek](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)

---
title: Activiteiten logboeken voor RBAC-wijzigingen in azure-resources weer geven | Microsoft Docs
description: Bekijk activiteiten logboeken voor op rollen gebaseerd toegangs beheer (RBAC) wijzigingen in azure-resources gedurende de afgelopen 90 dagen.
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
ms.date: 02/02/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 753c626fe44193b83cbd992f225fe01c2ff67f89
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75744804"
---
# <a name="view-activity-logs-for-rbac-changes-to-azure-resources"></a>Activiteiten logboeken voor RBAC-wijzigingen in azure-resources weer geven

Soms hebt u informatie nodig over de wijzigingen op basis van op rollen gebaseerde toegangs beheer (RBAC) voor Azure-resources, zoals voor het controleren of oplossen van problemen. Telkens wanneer iemand wijzigingen aanbrengt in roltoewijzingen of roldefinities binnen uw abonnementen, worden de wijzigingen vastgelegd in het [Azure-activiteiten logboek](../azure-monitor/platform/platform-logs-overview.md). U kunt de activiteiten logboeken weer geven om alle RBAC-wijzigingen voor de afgelopen 90 dagen te bekijken.

## <a name="operations-that-are-logged"></a>Bewerkingen die zijn geregistreerd

Dit zijn de RBAC-gerelateerde bewerkingen die zijn vastgelegd in het activiteiten logboek:

- Roltoewijzing maken
- Roltoewijzing verwijderen
- Aangepaste roldefinitie maken of bijwerken
- Aangepaste roldefinitie verwijderen

## <a name="azure-portal"></a>Azure Portal

De eenvoudigste manier om hieraan te beginnen is door de activiteitenlogboeken in de Azure Portal te bekijken. De volgende scherm afbeelding toont een voor beeld van een activiteiten logboek dat is gefilterd voor het weer geven van roltoewijzing en functie definitie bewerkingen. Het bevat ook een koppeling voor het downloaden van de logboeken als een CSV-bestand.

![Activiteiten logboeken met behulp van de portal-scherm opname](./media/change-history-report/activity-log-portal.png)

Het activiteiten logboek in de portal heeft verschillende filters. Dit zijn de RBAC-gerelateerde filters:

|Filter  |Waarde  |
|---------|---------|
|Gebeurtenis categorie     | <ul><li>Administratief</li></ul>         |
|Bewerking     | <ul><li>Roltoewijzing maken</li> <li>Roltoewijzing verwijderen</li> <li>Aangepaste roldefinitie maken of bijwerken</li> <li>Aangepaste roldefinitie verwijderen</li></ul>      |


Zie [gebeurtenissen weer geven in activiteiten logboek](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)voor meer informatie over activiteiten Logboeken.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

Als u activiteiten logboeken met Azure PowerShell wilt weer geven, gebruikt u de opdracht [Get-AzLog](/powershell/module/Az.Monitor/Get-AzLog) .

Met deze opdracht worden alle roltoewijzings wijzigingen in een abonnement voor de afgelopen zeven dagen weer gegeven:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

Met deze opdracht worden alle roldefinitie wijzigingen in een resource groep voor de afgelopen zeven dagen weer gegeven:

```azurepowershell
Get-AzLog -ResourceGroupName pharma-sales -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

Met deze opdracht worden alle roltoewijzings-en roldefinitie wijzigingen in een abonnement voor de afgelopen zeven dagen weer gegeven en worden de resultaten in een lijst getoond:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
```

```Example
Caller                  : alain@example.com
EventTimestamp          : 4/20/2018 9:18:07 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          statusCode     : Created
                          serviceRequestId: 11111111-1111-1111-1111-111111111111

Caller                  : alain@example.com
EventTimestamp          : 4/20/2018 9:18:05 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"22222222-2222-2222-2222-222222222222","Properties":{"PrincipalId":"33333333-3333-3333-3333-333333333333","RoleDefinitionId":"/subscriptions/00000000-0000-0000-0000-000000000000/providers
                          /Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","Scope":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"}}

```

## <a name="azure-cli"></a>Azure-CLI

Als u activiteiten logboeken wilt weer geven met de Azure CLI, gebruikt u de opdracht [AZ monitor Activity-Log List](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) .

Met deze opdracht worden de activiteiten Logboeken in een resource groep sinds de begin tijd weer gegeven:

```azurecli
az monitor activity-log list --resource-group pharma-sales --start-time 2018-04-20T00:00:00Z
```

Met deze opdracht worden de activiteiten logboeken voor de provider van de autorisatie resource sinds de begin tijd weer gegeven:

```azurecli
az monitor activity-log list --resource-provider "Microsoft.Authorization" --start-time 2018-04-20T00:00:00Z
```

## <a name="azure-monitor-logs"></a>Azure Monitor-logboeken

[Azure monitor-logboeken](../log-analytics/log-analytics-overview.md) is een ander hulp programma dat u kunt gebruiken om RBAC-wijzigingen voor al uw Azure-resources te verzamelen en te analyseren. Azure Monitor logboeken heeft de volgende voor delen:

- Complexe query's en logica schrijven
- Integreren met waarschuwingen, Power BI en andere hulpprogram ma's
- Gegevens opslaan voor langere Bewaar perioden
- Kruis verwijzing met andere logboeken, zoals beveiliging, virtuele machine en aangepast

Hier volgen de basis stappen om aan de slag te gaan:

1. [Maak een log Analytics-werk ruimte](../azure-monitor/learn/quick-create-workspace.md).

1. [Configureer de analyse van activiteitenlogboek-oplossing](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution) voor uw werk ruimte.

1. [Bekijk de activiteiten logboeken](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution). Een snelle manier om naar de overzichts pagina van de Analyse van activiteitenlogboek oplossing te gaan, is door te klikken op de optie **log Analytics** .

   ![Optie Azure Monitor Logboeken in Portal](./media/change-history-report/azure-log-analytics-option.png)

1. Gebruik eventueel de pagina [Zoeken in Logboeken](../log-analytics/log-analytics-log-search.md) of de [Portal voor geavanceerde analyse](../azure-monitor/log-query/get-started-portal.md) om de logboeken te zoeken en weer te geven. Voor meer informatie over deze twee opties raadpleegt u [de pagina zoeken in Logboeken of de portal Advanced Analytics](../azure-monitor/log-query/portals.md).

Hier volgt een query waarmee nieuwe roltoewijzingen worden geretourneerd die zijn ingedeeld op de doel resource provider:

```
AzureActivity
| where TimeGenerated > ago(60d) and OperationNameValue startswith "Microsoft.Authorization/roleAssignments/write" and ActivityStatus == "Succeeded"
| parse ResourceId with * "/providers/" TargetResourceAuthProvider "/" *
| summarize count(), makeset(Caller) by TargetResourceAuthProvider
```

Hier volgt een query die de gewijzigde roltoewijzingen in een grafiek retourneert:

```
AzureActivity
| where TimeGenerated > ago(60d) and OperationNameValue startswith "Microsoft.Authorization/roleAssignments"
| summarize count() by bin(TimeGenerated, 1d), OperationNameValue
| render timechart
```

![Activiteiten logboeken met behulp van de geavanceerde analyse portal-scherm opname](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>Volgende stappen
* [Gebeurtenissen in het activiteitenlogboek bekijken](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Abonnementactiviteit controleren met Azure-activiteitenlogboek](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)

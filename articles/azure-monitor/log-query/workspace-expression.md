---
title: werkruimte() expressie in Azure Monitor-logboekquery | Microsoft Documenten
description: De werkruimteexpressie wordt gebruikt in een Azure Monitor-logboekquery om gegevens op te halen uit een specifieke werkruimte in dezelfde resourcegroep, een andere resourcegroep of een ander abonnement.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2018
ms.openlocfilehash: 255888acf5da6149b6a964b23ed038b99715481c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75364948"
---
# <a name="workspace-expression-in-azure-monitor-log-query"></a>werkruimte() expressie in Azure Monitor-logboekquery

De `workspace` expressie wordt gebruikt in een Azure Monitor-query om gegevens op te halen uit een specifieke werkruimte in dezelfde resourcegroep, een andere resourcegroep of een ander abonnement. Dit is handig om logboekgegevens op te nemen in een query met Toepassingsstatistieken en om gegevens in meerdere werkruimten op te vragen in een logboekquery.


## <a name="syntax"></a>Syntaxis

`workspace(`*Id*`)`

## <a name="arguments"></a>Argumenten

- *Id:* hiermee identificeert u de werkruimte met een van de indelingen in de onderstaande tabel.

| Id | Beschrijving | Voorbeeld
|:---|:---|:---|
| Resourcenaam | Menselijke leesbare naam van de werkruimte (AKA "componentnaam") | werkruimte("contosoretail") |
| Gekwalificeerde naam | Volledige naam van de werkruimte in het formulier: "subscriptionName/resourceGroup/componentName" | werkruimte('Contoso/ContosoResource/ContosoWorkspace') |
| Id | GUID van de werkruimte | werkruimte("b438b3f6-912a-46d5-9db1-b42069242ab4") |
| Azure-bron-id | Id voor de Azure-bron | werkruimte("/abonnementen/e4227-645-44e-9c67-3b84b5982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail") |


## <a name="notes"></a>Opmerkingen

* U moet leestoegang tot de werkruimte hebben.
* Een verwante `app` expressie is waarmee u vragen over application insights-toepassingen.

## <a name="examples"></a>Voorbeelden

```Kusto
workspace("contosoretail").Update | count
```
```Kusto
workspace("b438b4f6-912a-46d5-9cb1-b44069212ab4").Update | count
```
```Kusto
workspace("/subscriptions/e427267-5645-4c4e-9c67-3b84b59a6982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail").Event | count
```
```Kusto
union 
(workspace("myworkspace").Heartbeat | where Computer contains "Con"),
(app("myapplication").requests | where cloud_RoleInstance contains "Con")
| count  
```
```Kusto
union 
(workspace("myworkspace").Heartbeat), (app("myapplication").requests)
| where TimeGenerated between(todatetime("2018-02-08 15:00:00") .. todatetime("2018-12-08 15:05:00"))
```

## <a name="next-steps"></a>Volgende stappen

- Zie de [app-expressie](app-expression.md) om te verwijzen naar een App-inzichten voor toepassingen.
- Lees hoe [Azure Monitor-gegevens](log-query-overview.md) worden opgeslagen.
- Toegang tot de volledige documentatie voor de [Kusto-querytaal](/azure/kusto/query/).

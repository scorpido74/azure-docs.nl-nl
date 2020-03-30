---
title: app() expressie in Azure Monitor-logboekquery's | Microsoft Documenten
description: De app-expressie wordt gebruikt in een Azure Monitor-logboekquery om gegevens op te halen uit een specifieke Application Insights-app in dezelfde resourcegroep, een andere brongroep of een ander abonnement.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/25/2019
ms.openlocfilehash: 5502df1cd119c0f63c65945d73431a17282ebc0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670251"
---
# <a name="app-expression-in-azure-monitor-query"></a>app() expressie in Azure Monitor-query

De `app` expressie wordt gebruikt in een Azure Monitor-query om gegevens op te halen uit een specifieke Application Insights-app in dezelfde resourcegroep, een andere resourcegroep of een ander abonnement. Dit is handig om toepassingsgegevens op te nemen in een Azure Monitor-logboekquery en om gegevens over meerdere toepassingen op te vragen in een query Met toepassingsstatistieken.



## <a name="syntax"></a>Syntaxis

`app(`*Id*`)`


## <a name="arguments"></a>Argumenten

- *Id:* identificeert de app met een van de indelingen in de onderstaande tabel.

| Id | Beschrijving | Voorbeeld
|:---|:---|:---|
| Resourcenaam | Menselijke leesbare naam van de app (AKA "componentnaam") | app("fabrikamapp") |
| Gekwalificeerde naam | Volledige naam van de app in het formulier: "subscriptionName/resourceGroup/componentName" | app('AI-Prototype/Fabrikam/fabrikamapp') |
| Id | GUID van de app | app("988ba129-363e-4415-8fe7-8cbab5447518") |
| Azure-bron-id | Id voor de Azure-bron |app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp") |


## <a name="notes"></a>Opmerkingen

* U moet leestoegang hebben tot de toepassing.
* Het identificeren van een toepassing met de naam gaat ervan uit dat deze uniek is voor alle toegankelijke abonnementen. Als u meerdere toepassingen met de opgegeven naam hebt, mislukt de query vanwege de ambiguïteit. In dit geval moet u een van de andere id's gebruiken.
* Gebruik de bijbehorende [werkruimte](workspace-expression.md) voor expressies om query's in log Analytics-werkruimten te plaatsen.
* De expressie app() wordt momenteel niet ondersteund in de zoekopdracht wanneer u de Azure-portal gebruikt om een [aangepaste waarschuwingsregel voor logboekzoekopdrachten](../platform/alerts-log.md)te maken, tenzij een toepassing Insights-toepassing wordt gebruikt als bron voor de waarschuwingsregel.

## <a name="examples"></a>Voorbeelden

```Kusto
app("fabrikamapp").requests | count
```
```Kusto
app("AI-Prototype/Fabrikam/fabrikamapp").requests | count
```
```Kusto
app("b438b4f6-912a-46d5-9cb1-b44069212ab4").requests | count
```
```Kusto
app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
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

- Zie de [werkruimte-expressie](workspace-expression.md) die moet verwijzen naar een werkruimte log Analytics.
- Lees hoe [Azure Monitor-gegevens](../../azure-monitor/log-query/log-query-overview.md) worden opgeslagen.
- Toegang tot de volledige documentatie voor de [Kusto-querytaal](/azure/kusto/query/).

---
title: resource() expressie in Azure Monitor-logboekquery | Microsoft Documenten
description: De bronexpressie wordt gebruikt in een azure monitor-logboekquery die is gericht op basis van resources om gegevens uit meerdere bronnen op te halen.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2018
ms.openlocfilehash: 2a729caefe698b13833098ba48df9d4bfbd97356
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665696"
---
# <a name="resource-expression-in-azure-monitor-log-query"></a>resource() expressie in Azure Monitor-logboekquery

De `resource` expressie wordt gebruikt in een Azure Monitor-query [die is ingericht met een bron](scope.md#query-scope) om gegevens uit andere bronnen op te halen. 


## <a name="syntax"></a>Syntaxis

`resource(`*Id*`)`

## <a name="arguments"></a>Argumenten

- *Id:* resource-id van een resource.

| Id | Beschrijving | Voorbeeld
|:---|:---|:---|
| Resource | Bevat gegevens voor de bron. | resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/resourcesgroups/myresourcegroup/providers/microsoft.compute/virtualmachines/myvm") |
| Resourcegroep of -abonnement | Bevat gegevens voor de resource en alle bronnen die deze bevat.  | bron("/abonnementen/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/resourcesgroups/myresourcegroup) |


## <a name="notes"></a>Opmerkingen

* U moet de toegang tot de bron hebben gelezen.


## <a name="examples"></a>Voorbeelden

```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/providers/microsoft.compute/virtualmachines/myvm").Heartbeat) | summarize count() by _ResourceId, TenantId
```
```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup).Heartbeat) | summarize count() by _ResourceId, TenantId
```


## <a name="next-steps"></a>Volgende stappen

- Zie [Querybereik en -tijdsbereik in Azure Monitor Log Analytics](scope.md) voor meer informatie over een querybereik.
- Toegang tot de volledige documentatie voor de [Kusto-querytaal](/azure/kusto/query/).

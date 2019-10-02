---
title: resource ()-expressie in Azure Monitor logboek query | Microsoft Docs
description: De resource-expressie wordt gebruikt in een resource gerichte Azure Monitor-logboek query om gegevens op te halen uit meerdere resources.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/10/2018
ms.author: bwren
ms.openlocfilehash: deca6e7ef1c231a82a73067971d86a6e9cdd0599
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71817408"
---
# <a name="resource-expression-in-azure-monitor-log-query"></a>resource ()-expressie in Azure Monitor-logboek query

De `resource`-expressie wordt gebruikt in een Azure Monitor query [bereik voor een resource](scope.md#query-scope) om gegevens op te halen uit andere resources. 


## <a name="syntax"></a>Syntaxis

`resource(`-*id*`)`

## <a name="arguments"></a>Argumenten

- *Id*: De resource-ID van een resource.

| ID | Description | Voorbeeld
|:---|:---|:---|
| Resource | Bevat gegevens voor de resource. | resource ("/Subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourcesgroups/myresourcegroup/providers/Microsoft.Compute/virtualmachines/myvm") |
| Resource groep of-abonnement | Bevat gegevens voor de resource en alle resources die deze bevat.  | resource ("/Subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourcesgroups/myresourcegroup) |


## <a name="notes"></a>Opmerkingen

* U moet lees toegang hebben tot de resource.


## <a name="examples"></a>Voorbeelden

```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/providers/microsoft.compute/virtualmachines/myvm").Heartbeat) | summarize count() by _ResourceId, TenantId
```
```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup).Heartbeat) | summarize count() by _ResourceId, TenantId
```


## <a name="next-steps"></a>Volgende stappen

- Zie de [logboek query bereik en het tijds bereik in Azure Monitor Log Analytics](scope.md) voor meer informatie over een query bereik.
- Toegang tot volledige documentatie voor de [Kusto-query taal](/azure/kusto/query/).

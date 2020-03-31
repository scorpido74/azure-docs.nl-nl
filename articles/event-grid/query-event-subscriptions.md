---
title: Azure-gebeurtenisrasterabonnementen opvragen
description: In dit artikel wordt beschreven hoe u gebeurtenisrasterabonnementen in uw Azure-abonnement aanbieden. U geeft verschillende parameters op basis van het type abonnement.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 2da4ba5946f16092c1d0918aec8dc3109b5fd4a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721537"
---
# <a name="query-event-grid-subscriptions"></a>Abonnement op querygebeurtenisraster 

In dit artikel wordt beschreven hoe u de gebeurtenisrasterabonnementen in uw Azure-abonnement weergeven. Wanneer u uw bestaande Event Grid-abonnementen opvraagt, is het belangrijk om inzicht te krijgen in de verschillende typen abonnementen. U geeft verschillende parameters op basis van het type abonnement dat u wilt krijgen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="resource-groups-and-azure-subscriptions"></a>Brongroepen en Azure-abonnementen

Azure-abonnementen en resourcegroepen zijn geen Azure-bronnen. Daarom hebben gebeurtenisrasterabonnementen op resourcegroepen of Azure-abonnementen niet dezelfde eigenschappen als gebeurtenisrasterabonnementen op Azure-bronnen. Gebeurtenisnetabonnementen op resourcegroepen of Azure-abonnementen worden als globaal beschouwd.

Als u gebeurtenisrasterabonnementen wilt krijgen voor een Azure-abonnement en de brongroepen, hoeft u geen parameters op te geven. Zorg ervoor dat u het Azure-abonnement hebt geselecteerd dat u wilt opvragen. In de volgende voorbeelden worden geen gebeurtenisrasterabonnementen voor aangepaste onderwerpen of Azure-bronnen toegevoegd.

Gebruik voor Azure CLI:

```azurecli-interactive
az account set -s "My Azure Subscription"
az eventgrid event-subscription list
```

Gebruik voor PowerShell:

```azurepowershell-interactive
Set-AzContext -Subscription "My Azure Subscription"
Get-AzEventGridSubscription
```

Als u gebeurtenisrasterabonnementen voor een Azure-abonnement wilt aanvragen, geeft u het onderwerptype **Microsoft.Resources.Subscriptions op**.

Gebruik voor Azure CLI:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.Subscriptions" --location global
```

Gebruik voor PowerShell:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Resources.Subscriptions"
```

Als u gebeurtenisrasterabonnementen wilt krijgen voor alle resourcegroepen binnen een Azure-abonnement, geeft u het onderwerptype **Microsoft.Resources.ResourceGroups op.**

Gebruik voor Azure CLI:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.ResourceGroups" --location global
```

Gebruik voor PowerShell:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Resources.ResourceGroups"
```

Als u gebeurtenisrasterabonnementen voor een bepaalde resourcegroep wilt krijgen, geeft u de naam van de resourcegroep op als parameter.

Gebruik voor Azure CLI:

```azurecli-interactive
az eventgrid event-subscription list --resource-group myResourceGroup --location global
```

Gebruik voor PowerShell:

```azurepowershell-interactive
Get-AzEventGridSubscription -ResourceGroupName myResourceGroup
```

## <a name="custom-topics-and-azure-resources"></a>Aangepaste onderwerpen en Azure-bronnen

Aangepaste onderwerpen van het gebeurtenisraster zijn Azure-bronnen. Daarom vraagt u op dezelfde manier gebeurtenisrasterabonnementen op voor aangepaste onderwerpen en andere bronnen, zoals blob-opslagaccount. Als u gebeurtenisrasterabonnementen voor aangepaste onderwerpen wilt krijgen, moet u parameters opgeven die de bron identificeren of de locatie van de resource identificeren. Het is niet mogelijk om in grote lijnen query-abonnementop gebeurtenisrasterabonnementen voor resources in uw Azure-abonnement.

Geef de naam van de locatie op om gebeurtenisrasterabonnementen voor aangepaste onderwerpen en andere bronnen op een locatie op te nemen.

Gebruik voor Azure CLI:

```azurecli-interactive
az eventgrid event-subscription list --location westus2
```

Gebruik voor PowerShell:

```azurepowershell-interactive
Get-AzEventGridSubscription -Location westus2
```

Als u abonnementen wilt krijgen op aangepaste onderwerpen voor een locatie, geeft u de locatie en het onderwerptype **Microsoft.EventGrid.Topics op.**

Gebruik voor Azure CLI:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.EventGrid.Topics" --location "westus2"
```

Gebruik voor PowerShell:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.EventGrid.Topics" -Location westus2
```

Als u abonnementen wilt krijgen op opslagaccounts voor een locatie, geeft u de locatie en het onderwerptype **Microsoft.Storage.StorageAccounts op.**

Gebruik voor Azure CLI:

```azurecli-interactive
az eventgrid event-subscription list --topic-type "Microsoft.Storage.StorageAccounts" --location westus2
```

Gebruik voor PowerShell:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Storage.StorageAccounts" -Location westus2
```

Als u gebeurtenisrasterabonnementen voor een aangepast onderwerp wilt krijgen, geeft u de naam van het aangepaste onderwerp en de naam van de brongroep op.

Gebruik voor Azure CLI:

```azurecli-interactive
az eventgrid event-subscription list --topic-name myCustomTopic --resource-group myResourceGroup
```

Gebruik voor PowerShell:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicName myCustomTopic -ResourceGroupName myResourceGroup
```

Als u gebeurtenisrasterabonnementen voor een bepaalde resource wilt krijgen, geeft u de resource-id op.

Gebruik voor Azure CLI:

```azurecli-interactive
resourceid=$(az resource show -n mystorage -g myResourceGroup --resource-type "Microsoft.Storage/storageaccounts" --query id --output tsv)
az eventgrid event-subscription list --resource-id $resourceid
```

Gebruik voor PowerShell:

```azurepowershell-interactive
$resourceid = (Get-AzResource -Name mystorage -ResourceGroupName myResourceGroup).ResourceId
Get-AzEventGridSubscription -ResourceId $resourceid
```

## <a name="next-steps"></a>Volgende stappen

* Voor informatie over het bezorgen en opnieuw proberen van [gebeurtenissen, het verzenden van het gebeurtenisrasterbericht en het opnieuw proberen](delivery-and-retry.md).
* Zie [Een inleiding tot Event Grid](overview.md) voor een inleiding tot Event Grid.
* Zie [Aangepaste gebeurtenissen maken en routeren met Azure Event Grid](custom-event-quickstart.md)om snel aan de slag te gaan met gebeurtenisraster.

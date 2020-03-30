---
title: Dode letter- en beleid voor nieuwe informatie - Azure Event Grid
description: Beschrijft hoe u opties voor het leveren van gebeurtenissen aanpassen voor gebeurtenisraster. Stel een bestemming voor dode letters in en geef aan hoe lang u de levering opnieuw moet proberen.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: spelluru
ms.openlocfilehash: caed3c077b4df5da5fd8541b2f7e85ef119604b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72794041"
---
# <a name="dead-letter-and-retry-policies"></a>Dode letter en herprobeer beleid

Wanneer u een gebeurtenisabonnement maakt, u de instellingen voor het leveren van gebeurtenissen aanpassen. In dit artikel ziet u hoe u een dode letterlocatie instelt en de instellingen voor nieuwe apparaten aanpassen. Zie [Gebeurtenisrasterbezorging en opnieuw proberen](delivery-and-retry.md)voor informatie over deze functies.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="set-dead-letter-location"></a>Dode-letterlocatie instellen

Als u een dode letterlocatie wilt instellen, hebt u een opslagaccount nodig voor het houden van gebeurtenissen die niet kunnen worden geleverd aan een eindpunt. De voorbeelden krijgen de bron-ID van een bestaand opslagaccount. Ze maken een gebeurtenisabonnement dat een container in dat opslagaccount gebruikt voor het eindpunt voor dode letters.

> [!NOTE]
> - Maak een opslagaccount en een blobcontainer in de opslag voordat u opdrachten uitvoert in dit artikel.
> - Met de service Gebeurtenisraster worden blobs in deze container gemaakt. De namen van blobs hebben de naam van het Event Grid-abonnement met alle letters in hoofdletters. Als de naam van het abonnement bijvoorbeeld Mijn-Blob-abonnement is, hebben de namen van de dode letterblobs MY-BLOB-SUBSCRIPTION (myblobcontainer/MY-BLOB-SUBSCRIPTION/2019/8/8/5/111111111-1111-1111-1111-1111-11111111.json). Dit probleem is bedoeld om te beschermen tegen verschillen in de afhandeling van het geval tussen Azure-services.


### <a name="azure-cli"></a>Azure-CLI

```azurecli-interactive
containername=testcontainer

topicid=$(az eventgrid topic show --name demoTopic -g gridResourceGroup --query id --output tsv)
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --deadletter-endpoint $storageid/blobServices/default/containers/$containername
```

Als u dead-lettering wilt uitschakelen, voert u de opdracht opnieuw uit `deadletter-endpoint`om het gebeurtenisabonnement te maken, maar geeft u geen waarde voor . U hoeft het gebeurtenisabonnement niet te verwijderen.

> [!NOTE]
> Als u werkt met Azure CLI op uw lokale computer, gebruikt u Azure CLI versie 2.0.56 of hoger. Zie [De Azure CLI installeren](/cli/azure/install-azure-cli) voor instructies over het installeren van de meest recente versie van Azure CLI.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$containername = "testcontainer"

$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id
$storageid = (Get-AzStorageAccount -ResourceGroupName gridResourceGroup -Name demostorage).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeadLetterEndpoint "$storageid/blobServices/default/containers/$containername"
```

Als u dead-lettering wilt uitschakelen, voert u de opdracht opnieuw uit `DeadLetterEndpoint`om het gebeurtenisabonnement te maken, maar geeft u geen waarde voor . U hoeft het gebeurtenisabonnement niet te verwijderen.

> [!NOTE]
> Als u Azure Poweshell gebruikt op uw lokale machine, gebruikt u Azure PowerShell-versie 1.1.0 of meer. Download en installeer de nieuwste Azure PowerShell van [Azure-downloads.](https://azure.microsoft.com/downloads/)

## <a name="set-retry-policy"></a>Beleid voor opnieuw proberen instellen

Wanneer u een abonnement op eventgrid maakt, u waarden instellen voor hoe lang gebeurtenisraster moet proberen het evenement te leveren. Standaard probeert Event Grid 24 uur (1440 minuten) of 30 keer. U een van deze waarden instellen voor uw abonnement op het gebeurtenisraster. De waarde voor gebeurtenis time-to-live moet een geheel getal zijn van 1 tot 1440. De waarde voor max retries moet een geheel getal van 1 tot 30 zijn.

U het [schema voor opnieuw proberen](delivery-and-retry.md#retry-schedule-and-duration)niet configureren.

### <a name="azure-cli"></a>Azure-CLI

Gebruik de tijd om de gebeurtenis time-to-live in te stellen op een andere waarde dan 1440 minuten:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --event-ttl 720
```

Gebruik de hoogste waarde om de maximumvermeldingen in te stellen op een andere waarde dan 30:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --max-delivery-attempts 18
```

Als u `event-ttl` beide `max-deliver-attempts`instelt en gebeurtenisraster de eerste wordt gebruikt om te bepalen wanneer de levering van gebeurtenissen moet worden gestopt.

### <a name="powershell"></a>PowerShell

Gebruik de tijd om de gebeurtenis time-to-live in te stellen op een andere waarde dan 1440 minuten:

```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -EventTtl 720
```

Gebruik de hoogste waarde om de maximumvermeldingen in te stellen op een andere waarde dan 30:

```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -MaxDeliveryAttempt 18
```

Als u `EventTtl` beide `MaxDeliveryAttempt`instelt en gebeurtenisraster de eerste wordt gebruikt om te bepalen wanneer de levering van gebeurtenissen moet worden gestopt.

## <a name="next-steps"></a>Volgende stappen

* Zie [Azure Event Grid Dead Letter Samples voor .NET voor](https://azure.microsoft.com/resources/samples/event-grid-dotnet-handle-deadlettered-events/)een voorbeeldtoepassing die een Azure-functie-app gebruikt om dode lettergebeurtenissen te verwerken.
* Voor informatie over het bezorgen en opnieuw proberen van [gebeurtenissen, het verzenden van het gebeurtenisrasterbericht en het opnieuw proberen](delivery-and-retry.md).
* Zie [Een inleiding tot Event Grid](overview.md) voor een inleiding tot Event Grid.
* Zie [Aangepaste gebeurtenissen maken en routeren met Azure Event Grid](custom-event-quickstart.md)om snel aan de slag te gaan met gebeurtenisraster.

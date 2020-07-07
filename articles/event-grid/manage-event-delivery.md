---
title: Onbestelbare letter en beleid voor opnieuw proberen-Azure Event Grid
description: Hierin wordt beschreven hoe u de bezorgings opties voor gebeurtenissen kunt aanpassen voor Event Grid. Stel een bestemming voor onbestelbare berichten in en geef op hoe lang de levering moet worden herhaald.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: spelluru
ms.openlocfilehash: caed3c077b4df5da5fd8541b2f7e85ef119604b0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "72794041"
---
# <a name="dead-letter-and-retry-policies"></a>Onbestelbare letter en beleid voor opnieuw proberen

Bij het maken van een gebeurtenis abonnement kunt u de instellingen voor gebeurtenis levering aanpassen. In dit artikel wordt beschreven hoe u een locatie voor een onbestelbare letter instelt en hoe u de instellingen voor opnieuw proberen aanpast. Zie [Event grid aflevering van berichten en probeer het opnieuw](delivery-and-retry.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="set-dead-letter-location"></a>Locatie van onbestelbare letter instellen

Als u een locatie met een onbestelbare letter wilt instellen, hebt u een opslag account nodig voor het opslaan van gebeurtenissen die niet aan een eind punt kunnen worden geleverd. De voor beelden krijgen de resource-ID van een bestaand opslag account. Ze maken een gebeurtenis abonnement dat gebruikmaakt van een container in dat opslag account voor het eind punt voor onbestelbare berichten.

> [!NOTE]
> - Maak een opslag account en een BLOB-container in de opslag voordat u de opdrachten in dit artikel uitvoert.
> - De Event Grid-Service maakt blobs in deze container. De namen van de blobs hebben de naam van het Event Grid-abonnement met alle letters in hoofd letters. Als bijvoorbeeld de naam van het abonnement mijn-BLOB-abonnement is, hebben de namen van de onbestelbare letter-blobs de volgende BLOB-abonnement (myblobcontainer/MY-BLOB-SUBSCRIPTION/2019/8/8/5/111111111-1111-1111-1111-111111111111.json). Dit gedrag is om te beschermen tegen verschillen bij het verwerken van het hoofdletter gebruik tussen Azure-Services.


### <a name="azure-cli"></a>Azure CLI

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

Als u onbestelbare berichten wilt uitschakelen, voert u de opdracht opnieuw uit om het gebeurtenis abonnement te maken, maar geeft u geen waarde op voor `deadletter-endpoint` . U hoeft het gebeurtenis abonnement niet te verwijderen.

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

Als u onbestelbare berichten wilt uitschakelen, voert u de opdracht opnieuw uit om het gebeurtenis abonnement te maken, maar geeft u geen waarde op voor `DeadLetterEndpoint` . U hoeft het gebeurtenis abonnement niet te verwijderen.

> [!NOTE]
> Als u Azure Poweshell gebruikt op uw lokale computer, gebruikt u Azure PowerShell versie 1.1.0 of hoger. Down load en installeer de nieuwste Azure PowerShell van [Azure down loads](https://azure.microsoft.com/downloads/).

## <a name="set-retry-policy"></a>Beleid voor opnieuw proberen instellen

Wanneer u een Event Grid-abonnement maakt, kunt u waarden instellen voor hoe lang Event Grid moet proberen de gebeurtenis te leveren. Event Grid probeert standaard 24 uur (1440 minuten) of 30 keer. U kunt een van deze waarden instellen voor uw event grid-abonnement. De waarde voor de gebeurtenis time-to-Live moet een geheel getal tussen 1 en 1440 zijn. De waarde voor het maximum aantal nieuwe pogingen moet een geheel getal tussen 1 en 30 zijn.

U kunt het [schema voor opnieuw proberen](delivery-and-retry.md#retry-schedule-and-duration)niet configureren.

### <a name="azure-cli"></a>Azure CLI

Als u de time-to-Live van de gebeurtenis wilt instellen op een andere waarde dan 1440 minuten, gebruikt u:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --event-ttl 720
```

Als u het maximum aantal nieuwe pogingen wilt instellen op een andere waarde dan 30, gebruikt u:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --max-delivery-attempts 18
```

Als u zowel als `event-ttl` als hebt ingesteld `max-deliver-attempts` , gebruikt Event grid de eerste om te verloopt om te bepalen wanneer de gebeurtenis levering moet worden gestopt.

### <a name="powershell"></a>PowerShell

Als u de time-to-Live van de gebeurtenis wilt instellen op een andere waarde dan 1440 minuten, gebruikt u:

```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -EventTtl 720
```

Als u het maximum aantal nieuwe pogingen wilt instellen op een andere waarde dan 30, gebruikt u:

```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -MaxDeliveryAttempt 18
```

Als u zowel als `EventTtl` als hebt ingesteld `MaxDeliveryAttempt` , gebruikt Event grid de eerste om te verloopt om te bepalen wanneer de gebeurtenis levering moet worden gestopt.

## <a name="next-steps"></a>Volgende stappen

* Zie [Azure Event grid dead letter-voor beelden voor .net](https://azure.microsoft.com/resources/samples/event-grid-dotnet-handle-deadlettered-events/)voor een voorbeeld toepassing die gebruikmaakt van een Azure function-app voor het verwerken van Dead-letter-gebeurtenissen.
* [Event grid aflevering van berichten en probeer het opnieuw](delivery-and-retry.md).
* Zie [Een inleiding tot Event Grid](overview.md) voor een inleiding tot Event Grid.
* Zie [aangepaste gebeurtenissen maken en routeren met Azure Event grid](custom-event-quickstart.md)om snel aan de slag te gaan met Event grid.

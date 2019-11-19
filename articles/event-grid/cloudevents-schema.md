---
title: Azure Event Grid gebruiken met gebeurtenissen in het CloudEvents-schema
description: Hierin wordt beschreven hoe u het CloudEvents-schema instelt voor gebeurtenissen in Azure Event Grid.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 11/07/2018
ms.author: babanisa
ms.openlocfilehash: 8925110511f6c63a42dd9b121429ac7264cd4aa4
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74170233"
---
# <a name="use-cloudevents-v10-schema-with-event-grid"></a>CloudEvents v 1.0-schema gebruiken met Event Grid

Naast het [standaard schema](event-schema.md)van de gebeurtenis, ondersteunt Azure Event grid systeem eigen gebeurtenissen in de [JSON-implementatie van CloudEvents v 1.0](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) en [http-protocol binding](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md). [CloudEvents](https://cloudevents.io/) is een [open specificatie](https://github.com/cloudevents/spec/blob/v1.0/spec.md) voor het beschrijven van gebeurtenis gegevens.

CloudEvents vereenvoudigt interoperabiliteit door een gemeen schappelijk gebeurtenis schema te bieden voor het publiceren en gebruiken van Cloud gebeurtenissen. Dit schema biedt uniforme hulp middelen, standaard methoden voor route ring & het afhandelen van gebeurtenissen en universele manieren om het buitenste gebeurtenis schema te deserialiseren. Met een gemeen schappelijk schema kunt u gemakkelijker werk op verschillende platforms integreren.

CloudEvents wordt gebouwd door verschillende deel [nemers](https://github.com/cloudevents/spec/blob/master/community/contributors.md), waaronder micro soft, via de [systeem eigen Cloud Computing Foundation](https://www.cncf.io/). Het is momenteel beschikbaar als versie 1,0.

In dit artikel wordt beschreven hoe u het CloudEvents-schema gebruikt met Event Grid.

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Preview-functie installeren

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="cloudevent-schema"></a>CloudEvent-schema

Hier volgt een voor beeld van een Azure Blob Storage-gebeurtenis in de indeling CloudEvents:

``` JSON
{
    "specversion": "1.0",
    "type": "Microsoft.Storage.BlobCreated",  
    "source": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}",
    "id": "9aeb0fdf-c01e-0131-0922-9eb54906e209",
    "time": "2019-11-18T15:13:39.4589254Z",
    "subject": "blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "dataschema": "#",
    "data": {
        "api": "PutBlockList",
        "clientRequestId": "4c5dd7fb-2c48-4a27-bb30-5361b5de920a",
        "requestId": "9aeb0fdf-c01e-0131-0922-9eb549000000",
        "eTag": "0x8D76C39E4407333",
        "contentType": "image/png",
        "contentLength": 30699,
        "blobType": "BlockBlob",
        "url": "https://gridtesting.blob.core.windows.net/testcontainer/{new-file}",
        "sequencer": "000000000000000000000000000099240000000000c41c18",
        "storageDiagnostics": {
            "batchId": "681fe319-3006-00a8-0022-9e7cde000000"
        }
    }
}
```

[Hier](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes)vindt u een gedetailleerde beschrijving van de beschik bare velden, hun typen en definities in CloudEvents v 0.1.

De waarden van headers voor gebeurtenissen die in het CloudEvents-schema en het Event Grid schema worden bezorgd, zijn hetzelfde als voor `content-type`. Voor het CloudEvents-schema is die header waarde `"content-type":"application/cloudevents+json; charset=utf-8"`. Voor Event Grid schema is die header waarde `"content-type":"application/json; charset=utf-8"`.

## <a name="configure-event-grid-for-cloudevents"></a>Event Grid configureren voor CloudEvents

U kunt Event Grid gebruiken voor zowel invoer als uitvoer van gebeurtenissen in het CloudEvents-schema. U kunt CloudEvents gebruiken voor systeem gebeurtenissen, zoals Blob Storage gebeurtenissen en IoT Hub gebeurtenissen, en aangepaste gebeurtenissen. Het kan ook deze gebeurtenissen op de achtergrond transformeren.


| Invoer schema       | Uitvoer schema
|--------------------|---------------------
| CloudEvents-indeling | CloudEvents-indeling
| Event Grid indeling  | CloudEvents-indeling
| CloudEvents-indeling | Event Grid indeling
| Event Grid indeling  | Event Grid indeling

Voor alle gebeurtenis schema's moet Event Grid worden gevalideerd bij het publiceren naar een event grid-onderwerp en bij het maken van een gebeurtenis abonnement. Zie voor meer informatie, [Event Grid-beveiliging en verificatie](security-authentication.md).

### <a name="input-schema"></a>Invoer schema

Wanneer u het aangepaste onderwerp maakt, stelt u het invoer schema voor een aangepast onderwerp in.

Gebruik voor Azure CLI:

```azurecli-interactive
# If you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  --name <topic_name> \
  -l westcentralus \
  -g gridResourceGroup \
  --input-schema cloudeventv01schema
```

Gebruik voor PowerShell:

```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

New-AzureRmEventGridTopic `
  -ResourceGroupName gridResourceGroup `
  -Location westcentralus `
  -Name <topic_name> `
  -InputSchema CloudEventV01Schema
```

De huidige versie van CloudEvents biedt geen ondersteuning voor batch verwerking van gebeurtenissen. Als u gebeurtenissen met een CloudEvent-schema naar een onderwerp wilt publiceren, moet u elke gebeurtenis afzonderlijk publiceren.

### <a name="output-schema"></a>Uitvoer schema

U stelt het uitvoer schema in wanneer u het gebeurtenis abonnement maakt.

Gebruik voor Azure CLI:

```azurecli-interactive
topicID=$(az eventgrid topic show --name <topic-name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --name <event_subscription_name> \
  --source-resource-id $topicID \
  --endpoint <endpoint_URL> \
  --event-delivery-schema cloudeventv01schema
```

Gebruik voor PowerShell:
```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeliverySchema CloudEventV01Schema
```

 Op dit moment kunt u geen Event Grid trigger gebruiken voor een Azure Functions-app wanneer de gebeurtenis wordt bezorgd in het CloudEvents-schema. Gebruik een HTTP-trigger. Zie [een HTTP-trigger gebruiken als Event grid trigger](../azure-functions/functions-bindings-event-grid.md#use-an-http-trigger-as-an-event-grid-trigger)voor voor beelden van het implementeren van een http-trigger die gebeurtenissen ontvangt in het CloudEvents-schema.

## <a name="next-steps"></a>Volgende stappen

* Zie [Event grid bericht bezorging bewaken](monitor-event-delivery.md)voor meer informatie over het bewaken van gebeurtenis leveringen.
* We raden u aan om CloudEvents te testen, opmerkingen te leveren en bij te [dragen](https://github.com/cloudevents/spec/blob/master/CONTRIBUTING.md) .
* Zie [Event grid Subscription schema](subscription-creation-schema.md)voor meer informatie over het maken van een Azure Event grid-abonnement.

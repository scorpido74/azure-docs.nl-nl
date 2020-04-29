---
title: Azure Event Grid gebruiken met gebeurtenissen in het CloudEvents-schema
description: Hierin wordt beschreven hoe u het CloudEvents-schema gebruikt voor gebeurtenissen in Azure Event Grid. De service ondersteunt gebeurtenissen in de JSON-implementatie van Cloud gebeurtenissen.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: babanisa
ms.openlocfilehash: 127095bef2c67a93097bf90bea54ca1b44b16c58
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81394382"
---
# <a name="cloudevents-v10-schema-with-event-grid"></a>CloudEvents v 1.0-schema met Event Grid

Naast het [standaard schema](event-schema.md)van de gebeurtenis, ondersteunt Azure Event grid systeem eigen gebeurtenissen in de [JSON-implementatie van CloudEvents v 1.0](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) en [http-protocol binding](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md). [CloudEvents](https://cloudevents.io/) is een [open specificatie](https://github.com/cloudevents/spec/blob/v1.0/spec.md) voor het beschrijven van gebeurtenis gegevens.

CloudEvents vereenvoudigt interoperabiliteit door een gemeen schappelijk gebeurtenis schema te bieden voor het publiceren en gebruiken van Cloud gebeurtenissen. Dit schema biedt uniforme hulp middelen, standaard methoden voor route ring & het afhandelen van gebeurtenissen en universele manieren om het buitenste gebeurtenis schema te deserialiseren. Met een gemeen schappelijk schema kunt u gemakkelijker werk op verschillende platforms integreren.

CloudEvents wordt gebouwd door verschillende deel [nemers](https://github.com/cloudevents/spec/blob/master/community/contributors.md), waaronder micro soft, via de [systeem eigen Cloud Computing Foundation](https://www.cncf.io/). Het is momenteel beschikbaar als versie 1,0.

In dit artikel wordt het CloudEvents-schema met Event Grid beschreven.

## <a name="sample-event-using-cloudevents-schema"></a>Voorbeeld gebeurtenis met behulp van CloudEvents-schema

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

[Hier](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes)vindt u een gedetailleerde beschrijving van de beschik bare velden, hun typen en definities in CloudEvents v 1.0.

De waarden van headers voor gebeurtenissen die in het CloudEvents-schema en het Event Grid schema worden bezorgd, `content-type`zijn hetzelfde, behalve voor. Voor het CloudEvents-schema is `"content-type":"application/cloudevents+json; charset=utf-8"`die header waarde. Voor Event Grid schema is `"content-type":"application/json; charset=utf-8"`die header waarde.

## <a name="event-grid-for-cloudevents"></a>Event Grid voor CloudEvents

U kunt Event Grid gebruiken voor zowel invoer als uitvoer van gebeurtenissen in het CloudEvents-schema. U kunt CloudEvents gebruiken voor systeem gebeurtenissen, zoals Blob Storage gebeurtenissen en IoT Hub gebeurtenissen, en aangepaste gebeurtenissen. Het kan ook deze gebeurtenissen op de achtergrond transformeren.


| Invoer schema       | Uitvoer schema
|--------------------|---------------------
| CloudEvents-indeling | CloudEvents-indeling
| Event Grid indeling  | CloudEvents-indeling
| Event Grid indeling  | Event Grid indeling

Voor alle gebeurtenis schema's moet Event Grid worden gevalideerd bij het publiceren naar een event grid-onderwerp en bij het maken van een gebeurtenis abonnement. Zie [Event grid beveiliging en verificatie](security-authentication.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
Zie [CloudEvents v 1.0-schema gebruiken met Event grid](cloudevents-schema.md).  

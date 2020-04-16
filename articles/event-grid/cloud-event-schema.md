---
title: Azure-gebeurtenisraster gebruiken met gebeurtenissen in het CloudEvents-schema
description: Beschrijft hoe u het CloudEvents-schema gebruiken voor gebeurtenissen in Azure Event Grid. De service ondersteunt evenementen bij de JSON-implementatie van Cloud Events.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: babanisa
ms.openlocfilehash: 127095bef2c67a93097bf90bea54ca1b44b16c58
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394382"
---
# <a name="cloudevents-v10-schema-with-event-grid"></a>CloudEvents v1.0-schema met gebeurtenisraster

Naast het [standaardgebeurtenisschema](event-schema.md)ondersteunt Azure Event Grid native gebeurtenissen in de [JSON-implementatie van CloudEvents v1.0-](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) en [HTTP-protocolbinding](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md). [CloudEvents](https://cloudevents.io/) is een [open specificatie](https://github.com/cloudevents/spec/blob/v1.0/spec.md) voor het beschrijven van gebeurtenisgegevens.

CloudEvents vereenvoudigt de interoperabiliteit door een algemeen gebeurtenisschema te bieden voor het publiceren en consumeren van cloudgebaseerde gebeurtenissen. Dit schema maakt uniforme tooling, standaardmanieren van routering & het afhandelen van gebeurtenissen en universele manieren om het externe gebeurtenisschema te deserialiseren. Met een gemeenschappelijk schema u werk gemakkelijker integreren op verschillende platforms.

CloudEvents wordt gebouwd door verschillende [medewerkers](https://github.com/cloudevents/spec/blob/master/community/contributors.md), waaronder Microsoft, via de [Cloud Native Computing Foundation](https://www.cncf.io/). Het is momenteel beschikbaar als versie 1.0.

In dit artikel wordt het CloudEvents-schema beschreven met gebeurtenisraster.

## <a name="sample-event-using-cloudevents-schema"></a>Voorbeeldgebeurtenis met het cloudgebeurtenissenschema

Hier is een voorbeeld van een Azure Blob Storage-gebeurtenis in de CloudEvents-indeling:

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

Een gedetailleerde beschrijving van de beschikbare velden, hun typen en definities in CloudEvents v1.0 is [hier beschikbaar.](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes)

De koptekstwaarden voor gebeurtenissen die worden geleverd in het cloudgebeurtenissenschema en het schema gebeurtenisraster zijn hetzelfde, behalve voor `content-type`. Voor het cloudgebeurtenissenschema is `"content-type":"application/cloudevents+json; charset=utf-8"`die kopwaarde . Voor het schema Gebeurtenisraster `"content-type":"application/json; charset=utf-8"`is die kopwaarde .

## <a name="event-grid-for-cloudevents"></a>Gebeurtenisraster voor CloudEvents

U Gebeurtenisraster gebruiken voor zowel invoer als uitvoer van gebeurtenissen in het CloudEvents-schema. U CloudEvents gebruiken voor systeemgebeurtenissen, zoals Blob Storage-gebeurtenissen en IoT Hub-gebeurtenissen en aangepaste gebeurtenissen. Het kan ook transformeren die gebeurtenissen op de draad heen en weer.


| Invoerschema       | Uitvoerschema
|--------------------|---------------------
| CloudEvents-indeling | CloudEvents-indeling
| Gebeurtenisrasternotatie  | CloudEvents-indeling
| Gebeurtenisrasternotatie  | Gebeurtenisrasternotatie

Voor alle gebeurtenisschema's vereist gebeurtenisraster validatie bij het publiceren naar een gebeurtenisrasteronderwerp en bij het maken van een gebeurtenisabonnement. Zie [Beveiliging en verificatie van gebeurtenisraster](security-authentication.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
Zie [CloudEvents v1.0-schema gebruiken met gebeurtenisraster](cloudevents-schema.md).  

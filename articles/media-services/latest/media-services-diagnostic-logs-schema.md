---
title: Diagnostische logboeken van Azure Media Services - Azure
description: In dit artikel worden de diagnostische logboeken van Azure Media Services getoond.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/20/2019
ms.author: juliako
ms.openlocfilehash: 37baed076ca074c1d558af36649e90959a0034c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75750871"
---
# <a name="diagnostic-logs-schemas"></a>Schema's van diagnostische logboeken

[Azure Monitor](../../azure-monitor/overview.md) stelt u in staat om statistieken en diagnostische logboeken te controleren die u helpen te begrijpen hoe uw toepassingen presteren. U diagnostische logboeken van Media Services controleren en waarschuwingen en meldingen maken voor de verzamelde statistieken en logboeken. U logboeken naar [Azure Storage](https://azure.microsoft.com/services/storage/)verzenden, streamen naar [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)en deze exporteren naar Log [Analytics](https://azure.microsoft.com/services/log-analytics/)of services van derden gebruiken.

Zie [Azure Monitor Metrics](../../azure-monitor/platform/data-platform.md) en Azure Monitor Diagnostic [logs](../../azure-monitor/platform/platform-logs-overview.md)voor gedetailleerde informatie.

In dit artikel worden schema's voor diagnostische logboeken van Media Services beschreven.

## <a name="top-level-diagnostic-logs-schema"></a>Schema voor diagnostische logboeken op het hoogste niveau

Zie [Ondersteunde services, schema's en categorieën voor Azure Diagnostic Logs voor](../../azure-monitor/platform/tutorial-dashboards.md)een gedetailleerde beschrijving van het schema voor diagnostische logboeken op het hoogste niveau.

## <a name="key-delivery-log-schema"></a>Logboekschema voor sleutellevering

### <a name="properties"></a>Eigenschappen

Deze eigenschappen zijn specifiek voor het sleutelleveringslogboekschema.

|Name|Beschrijving|
|---|---|
|keyId|De ID van de gevraagde sleutel.|
|Keytype|Kan een van de volgende waarden zijn: "Clear" (geen encryptie), "FairPlay", "PlayReady" of "Widevine".|
|policyName|De naam Azure Resource Manager van het beleid.|
|tokenType|Het tokentype.|
|statusMessage|Het statusbericht.|

### <a name="examples"></a>Voorbeelden

Eigenschappen van het schema voor belangrijke leveringsaanvragen.

```json
{
    "time": "2019-01-11T17:59:10.4908614Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Succeeded",
    "resultSignature": "OK",
    "durationMs": 315,
    "identity": {
        "authorization": {
            "issuer": "http://testacs",
            "audience": "urn:test"
        },
        "claims": {
            "urn:microsoft:azure:mediaservices:contentkeyidentifier": "3321e646-78d0-4896-84ec-c7b98eddfca5",
            "iss": "http://testacs",
            "aud": "urn:test",
            "exp": "1547233138"
        }
    },
    "level": "Informational",
    "location": "uswestcentral",
    "properties": {
        "requestId": "b0243468-d8e5-4edf-a48b-d408e1661050",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "JWT",
        "statusMessage": "OK"
    }
} 
```

```json
 {
    "time": "2019-01-11T17:59:33.4676382Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Failed",
    "resultSignature": "Unauthorized",
    "durationMs": 2,
    "level": "Error",
    "location": "uswestcentral",
    "properties": {
        "requestId": "875af030-b77c-416b-b7e1-58f23ebec182",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "None",
        "statusMessage": "No token present in authorization header or URL."
    }
} 
```

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Widevine is een service van Google Inc. en onderworpen aan de servicevoorwaarden en het privacybeleid van Google, Inc.

## <a name="next-steps"></a>Volgende stappen

[Statistieken en diagnostische logboeken van Media Services bewaken](media-services-metrics-diagnostic-logs.md)

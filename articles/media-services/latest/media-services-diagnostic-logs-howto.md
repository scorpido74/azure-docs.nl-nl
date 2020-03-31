---
title: Diagnostische logboeken van Media Services controleren via Azure Monitor | Microsoft Documenten
description: In dit artikel wordt uitgelegd hoe u diagnostische logboeken routeren en bekijken via Azure Monitor.
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
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: 4d4587c701a054828fc34785e2ae680fef47625d
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382916"
---
# <a name="monitor-media-services-diagnostic-logs"></a>Diagnostische logboeken van Media Services controleren

[Azure Monitor](../../azure-monitor/overview.md) stelt u in staat om statistieken en diagnostische logboeken te controleren die u helpen te begrijpen hoe uw toepassingen presteren. Zie Statistieken van [Monitor Media Services en diagnostische logboeken](media-services-metrics-diagnostic-logs.md)voor een gedetailleerde beschrijving van deze functie en om te zien waarom u azure media services-statistieken en diagnostische logboeken wilt gebruiken.

In dit artikel ziet u hoe u gegevens naar het opslagaccount routeren en vervolgens de gegevens bekijken.

## <a name="prerequisites"></a>Vereisten

- [Een Azure Media Services-account maken](create-account-cli-how-to.md).
- Bekijk [de statistieken en diagnostische logboeken van Monitor Media Services](media-services-metrics-diagnostic-logs.md).

## <a name="route-data-to-the-storage-account-using-the-portal"></a>Gegevens doorsturen naar het opslagaccount via de portal

1. Meld u aan bij Azure Portal op https://portal.azure.com.
1. Navigeer naar uw Media Services-account in en klik op **Diagnostische instellingen** onder **Controleren**. Hier kunt u een lijst zien van alle resources in uw abonnement die bewakingsgegevens via Azure Monitor genereren.

    ![Sectie Diagnostische instellingen](media/media-services-diagnostic-logs/logs01.png)

1. Klik **op Diagnostische instelling toevoegen**.

   De diagnostische instelling van een resource is een definitie van *welke* bewakingsgegevens moeten worden doorgestuurd vanuit een bepaalde resource en *waar* die bewakingsgegevens naartoe moeten worden gestuurd.

1. Geef in de weergegeven sectie de instelling een **naam** en schakel het selectievakje in voor **Archiveren naar een opslagaccount**.

    Selecteer het opslagaccount waarnaar u logboeken wilt verzenden en druk op **OK**.
1. Schakel alle selectievakjes onder **Logboek** en **Metrische gegevens** in. Afhankelijk van het resourcetype is er mogelijk maar één van deze opties beschikbaar. Met deze selectievakjes bepaalt u welke categorieën beschikbare logboekgegevens en metrische gegevens voor een bepaald resourcetype worden verzonden naar de bestemming die u hebt geselecteerd (in dit geval: een opslagaccount).

   ![Sectie Diagnostische instellingen](media/media-services-diagnostic-logs/logs02.png)
1. Stel de schuifregelaar **Bewaarperiode (dagen)** in op 30. Met deze schuifregelaar stelt u in hoeveel dagen u de bewakingsgegevens wilt bewaren in het opslagaccount. Gegevens die ouder zijn dan het aantal opgegeven dagen, worden automatisch verwijderd. Bij een bewaarperiode van nul dagen worden de gegevens voor onbepaalde tijd opgeslagen.
1. Klik op **Opslaan**.

Bewakingsgegevens uit uw resource worden nu doorgestuurd naar het opslagaccount.

## <a name="route-data-to-the-storage-account-using-the-azure-cli"></a>Gegevens doorsturen naar het opslagaccount met de Azure CLI

Als u de opslag van diagnostische logboeken in `az monitor diagnostic-settings` een opslagaccount wilt inschakelen, voert u de volgende opdracht Azure CLI uit:

```azurecli-interactive
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

Bijvoorbeeld:

```azurecli-interactive
az monitor diagnostic-settings create --name amsv3diagnostic \
    --storage-account storageaccountforams  \
    --resource "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount" \
    --resource-group "amsResourceGroup" \
    --logs '[{"category": "KeyDeliveryRequests",  "enabled": true, "retentionPolicy": {"days": 3, "enabled": true }}]'
```

## <a name="view-data-in-the-storage-account-using-the-portal"></a>Gegevens in het opslagaccount weergeven via de portal

Als u de voorgaande stappen hebt gevolgd, worden gegevens nu doorgestuurd naar uw opslagaccount.

Wellicht moet u vijf minuten wachten voordat de gebeurtenis in het opslagaccount wordt weergegeven.

1. Navigeer in de portal naar de sectie **Opslagaccounts** vanuit de linker navigatiebalk.
1. Identificeer het opslagaccount dat u in de voorgaande sectie hebt gemaakt en klik erop.
1. Klik op **Blobs**en vervolgens op de met de container gelabelde **insights-logs-keydeliveryrequests**. Dit is de container waar uw logboeken in zitten. Monitoringgegevens worden uitgesplitst in containers door resource-ID, vervolgens op datum en tijd.
1. Ga naar het bestand PT1H.json door te klikken in de containers voor resource-id, datum en tijd. Klik op het bestand PT1H.json en op **Downloaden**.

 U kunt nu de JSON-gebeurtenis zien die in het opslagaccount werd opgeslagen.

### <a name="examples-of-pt1hjson"></a>Voorbeelden van PT1H.json

#### <a name="clear-key-delivery-log"></a>Logboek voor het wissen van sleutelbezorging

```json
{
  "time": "2019-05-21T00:07:33.2820450Z",
  "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/amsResourceGroup/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/AMSACCOUNT",
  "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
  "operationVersion": "1.0",
  "category": "KeyDeliveryRequests",
  "resultType": "Succeeded",
  "resultSignature": "OK",
  "durationMs": 253,
  "identity": {
    "authorization": {
      "issuer": "myIssuer",
      "audience": "myAudience"
    },
    "claims": {
      "urn:microsoft:azure:mediaservices:contentkeyidentifier": "e4276e1d-c012-40b1-80d0-ac15808b9277",
      "nbf": "1558396914",
      "exp": "1558400814",
      "iss": "myIssuer",
      "aud": "myAudience"
    }
  },
  "level": "Informational",
  "location": "westus2",
  "properties": {
    "requestId": "fb5c2b3a-bffa-4434-9c6f-73d689649add",
    "keyType": "Clear",
    "keyId": "e4276e1d-c012-40b1-80d0-ac15808b9277",
    "policyName": "SharedContentKeyPolicyUsedByAllAssets",
    "tokenType": "JWT",
    "statusMessage": "OK"
  }
}
```

#### <a name="widevine-encrypted-key-delivery-log"></a>Widevine versleutelde sleutellevering logboek

```json
{
  "time": "2019-05-20T23:15:22.7088747Z",
  "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/amsResourceGroup/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/AMSACCOUNT",
  "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
  "operationVersion": "1.0",
  "category": "KeyDeliveryRequests",
  "resultType": "Succeeded",
  "resultSignature": "OK",
  "durationMs": 69,
  "identity": {
    "authorization": {
      "issuer": "myIssuer",
      "audience": "myAudience"
    },
    "claims": {
      "urn:microsoft:azure:mediaservices:contentkeyidentifier": "0092d23a-0a42-4c5f-838e-6d1bbc6346f8",
      "nbf": "1558392430",
      "exp": "1558396330",
      "iss": "myIssuer",
      "aud": "myAudience"
    }
  },
  "level": "Informational",
  "location": "westus2",
  "properties": {
    "requestId": "49613dd2-16aa-4595-a6e0-4e68beae6d37",
    "keyType": "Widevine",
    "keyId": "0092d23a-0a42-4c5f-838e-6d1bbc6346f8",
    "policyName": "DRMContentKeyPolicy",
    "tokenType": "JWT",
    "statusMessage": "OK"
  }
}
```

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Widevine is een service van Google Inc. en onderworpen aan de servicevoorwaarden en het privacybeleid van Google, Inc.

## <a name="see-also"></a>Zie ook

* [Azure-monitorstatistieken](../../azure-monitor/platform/data-platform.md)
* [Diagnostische logboeken van Azure Monitor](../../azure-monitor/platform/platform-logs-overview.md)
* [Logboekgegevens verzamelen en gebruiken uit uw Azure-bronnen](../../azure-monitor/platform/platform-logs-overview.md)

## <a name="next-steps"></a>Volgende stappen

[Metrische gegevens bewaken](media-services-metrics-howto.md)

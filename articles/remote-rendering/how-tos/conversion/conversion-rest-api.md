---
title: De REST API voor assetconversie
description: Beschrijft hoe u een asset converteert via de REST API
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 38116efc9e87eca8e2514a0a84045a69b8d42326
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887041"
---
# <a name="use-the-model-conversion-rest-api"></a>De REST API voor modelconversie gebruiken

De [modelconversieservice](model-conversion.md) wordt beheerd via een [REST API.](https://en.wikipedia.org/wiki/Representational_state_transfer) In dit artikel worden de API-gegevens van de conversieservice beschreven.

## <a name="regions"></a>Regio's

Zie de [lijst met beschikbare regio's](../../reference/regions.md) voor de basis-URL's waarnaar de aanvragen moeten worden verzonden.

## <a name="common-headers"></a>Algemene kopteksten

### <a name="common-request-headers"></a>Kopteksten met algemene aanvraag

Deze kopteksten moeten voor alle aanvragen worden opgegeven:

- De **kopautorisatie** moet de waarde hebben van 'Toonder [*TOKEN]",* waarbij [*TOKEN]* een [no-to-servicetoegangstoken](../tokens.md)is.

### <a name="common-response-headers"></a>Algemene antwoordkoppen

Alle antwoorden bevatten deze kopteksten:

- De **MS-CV** header bevat een unieke tekenreeks die kan worden gebruikt om het gesprek binnen de service te traceren.

## <a name="endpoints"></a>Eindpunten

De conversieservice biedt drie REST API-eindpunten voor:

- modelconversie starten met een opslagaccount dat is gekoppeld aan uw Azure Remote Rendering-account. 
- modelconversie starten met de *meegeleverde Shared Access Signatures (SAS).*
- de conversiestatus opvragen

### <a name="start-conversion-using-a-linked-storage-account"></a>Conversie starten met een gekoppeld opslagaccount
Uw Azure Remote Rendering Account moet toegang hebben tot het meegeleverde opslagaccount door de stappen te volgen over het [koppelen van opslagaccounts.](../create-an-account.md#link-storage-accounts)

| Eindpunt | Methode |
|-----------|:-----------|
| /v1/accounts/**accountID**/conversies/maken | POST |

Retourneert de id van de lopende conversie, verpakt in een JSON-document. De veldnaam is "conversionId".

#### <a name="request-body"></a>Aanvraagbody


```json
{
    "input":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container containing your input asset data>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the input blob container>",
        "inputAssetPath" : "<path to the model in the input blob container relative to the folderPath (or container root if no folderPath is specified)>"
    },
    "output":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container where the converted asset will be copied to>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the output blob container. Will contain the asset and log files>",
        "outputAssetFileName": "<optional: can be omitted or empty. The filename of the converted asset. If provided the filename needs to end in .arrAsset>"
    }
}
```
### <a name="start-conversion-using-provided-shared-access-signatures"></a>Conversie starten met opgegeven handtekeningen voor gedeelde toegang
Als uw ARR-account niet is gekoppeld aan uw opslagaccount, u met deze REST-interface toegang bieden met Behulp van *Shared Access Signatures (SAS).*

| Eindpunt | Methode |
|-----------|:-----------|
| /v1/accounts/**accountID**/conversies/createWithSharedAccessSignature | POST |

Retourneert de id van de lopende conversie, verpakt in een JSON-document. De veldnaam is "conversionId".

#### <a name="request-body"></a>Aanvraagbody

De aanvraaginstantie is hetzelfde als in de bovenstaande REST-aanroep maken, maar invoer en uitvoer bevatten *SAS-tokens (Shared Access Signatures).* Deze tokens bieden toegang tot het opslagaccount voor het lezen van de invoer en het schrijven van het conversieresultaat.

> [!NOTE]
> Deze SAS URI-tokens zijn de querytekenreeksen en niet de volledige URI. 


```json
{
    "input":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container containing your input asset data>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the input blob container>",
        "inputAssetPath" : "<path to the model in the input blob container relative to the folderPath (or container root if no folderPath is specified)>",
        "containerReadListSas" : "<a container SAS token which gives read and list access to the given input blob container>"
    },
    "output":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container where the converted asset will be copied to>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the output blob container. Will contain the asset and log files>",
        "outputAssetFileName": "<optional: can be omitted or empty. The filename of the converted asset. If provided the filename needs to end in .arrAsset>",
        "containerWriteSas" : "<a container SAS token which gives write access to the given output blob container>"
    }
}
```

### <a name="poll-conversion-status"></a>Status van pollconversie
De status van een doorlopende conversie die is gestart met een van de bovenstaande REST-aanroepen, kan worden opgevraagd via de volgende interface:


| Eindpunt | Methode |
|-----------|:-----------|
| /v1/accounts/**accountID**/conversies/**conversieId** | GET |

Retourneert een JSON-document met een veld 'status' dat de volgende waarden kan hebben:

- "Hardlopen"
- "Succes"
- "Mislukking"

Als de status 'Fout' is, wordt er een extra foutveld weergegeven met een subveld 'bericht', met foutgegevens. Extra logboeken worden geüpload naar uw uitvoercontainer.

## <a name="next-steps"></a>Volgende stappen

- [Azure Blob Storage gebruiken voor modelconversie](blob-storage.md)
- [Modelconversie](model-conversion.md)

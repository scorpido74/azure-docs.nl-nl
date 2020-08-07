---
title: De activa conversie REST API
description: Hierin wordt beschreven hoe u een Asset converteert via de REST API
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 5c638b434ceb31b57689b11971f48eb322b94726
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/07/2020
ms.locfileid: "87985611"
---
# <a name="use-the-model-conversion-rest-api"></a>De REST API voor modelconversie gebruiken

De [model conversie](model-conversion.md) service wordt beheerd via een [rest API](https://en.wikipedia.org/wiki/Representational_state_transfer). In dit artikel worden de API-Details van de conversie service beschreven.

## <a name="regions"></a>Regio's

Bekijk de [lijst met beschik bare regio's](../../reference/regions.md) voor de basis-url's om de aanvragen naar te verzenden.

## <a name="common-headers"></a>Algemene headers

### <a name="common-request-headers"></a>Algemene aanvraag headers

Deze headers moeten worden opgegeven voor alle aanvragen:

- De **autorisatie** -header moet de waarde ' Bearer [*token*] ' bevatten, waarbij [*token*] een [service toegangs token](../tokens.md)is.

### <a name="common-response-headers"></a>Algemene antwoord headers

Alle antwoorden bevatten deze kopteksten:

- De kop **MS-CV** bevat een unieke teken reeks die kan worden gebruikt voor het traceren van de aanroep binnen de service.

## <a name="endpoints"></a>Eindpunten

De conversie service biedt drie REST API-eind punten voor het volgende:

- model conversie starten met behulp van een opslag account dat is gekoppeld aan uw Azure remote rendering-account. 
- model conversie starten met behulp van de meegeleverde *SAS (Shared Access signatures)*.
- query uitvoeren op de conversie status

### <a name="start-conversion-using-a-linked-storage-account"></a>Conversie starten met een gekoppeld opslag account
Uw Azure-account voor externe rendering moet toegang hebben tot het gegeven opslag account door de stappen te volgen voor het [koppelen van opslag accounts](../create-an-account.md#link-storage-accounts).

| Eindpunt | Methode |
|-----------|:-----------|
| /v1/accounts/**accountID**/conversions/Create | POST |

Retourneert de ID van de actieve conversie, verpakt in een JSON-document. De veld naam is "conversionId".

#### <a name="request-body"></a>Aanvraagbody

> [!NOTE]
> Alles onder `input.folderPath` wordt opgehaald om de conversie uit te voeren op Azure. Als `input.folderPath` niet wordt opgegeven, wordt de volledige inhoud van de container opgehaald. Alle blobs en mappen die worden opgehaald, moeten [geldige Windows-bestands namen](https://docs.microsoft.com/windows/win32/fileio/naming-a-file#naming-conventions)hebben.

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
### <a name="start-conversion-using-provided-shared-access-signatures"></a>Conversie starten met behulp van de meegeleverde hand tekeningen voor gedeelde toegang
Als uw ARR-account niet is gekoppeld aan uw opslag account, kunt u met deze REST-interface toegang bieden met behulp van *Shared Access signatures (SAS)*.

| Eindpunt | Methode |
|-----------|:-----------|
| /v1/accounts/**accountID**/conversions/createWithSharedAccessSignature | POST |

Retourneert de ID van de actieve conversie, verpakt in een JSON-document. De veld naam is `conversionId` .

#### <a name="request-body"></a>Aanvraagbody

De aanvraag tekst is hetzelfde als in de bovenstaande REST-aanroep maken, maar de invoer en uitvoer bevatten *SAS-tokens (Shared Access signatures)*. Deze tokens bieden toegang tot het opslag account voor het lezen van de invoer en het schrijven van het conversie resultaat.

> [!NOTE]
> Deze SAS URI-tokens zijn de query teken reeksen en niet de volledige URI. 

> [!NOTE]
> Alles onder `input.folderPath` wordt opgehaald om de conversie uit te voeren op Azure. Als `input.folderPath` niet wordt opgegeven, wordt de volledige inhoud van de container opgehaald. Alle blobs en mappen die worden opgehaald, moeten [geldige Windows-bestands namen](https://docs.microsoft.com/windows/win32/fileio/naming-a-file#naming-conventions)hebben.

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

### <a name="poll-conversion-status"></a>Status van poll conversie
De status van een actieve conversie die is gestart met een van de bovenstaande REST-oproepen kan worden opgevraagd met behulp van de volgende Interface:


| Eindpunt | Methode |
|-----------|:-----------|
| /v1/accounts/**accountID**/conversions/**conversionId** | GET |

Retourneert een JSON-document met een status veld dat de volgende waarden kan hebben:

- Toegevoegd
- Voer
- Geleverd
- Veroorzaakt

Als de status ' fout ' is, is er een extra ' fout ' veld met een subveld ' bericht ' met fout gegevens. Extra logboeken worden geüpload naar de uitvoer container.

## <a name="next-steps"></a>Volgende stappen

- [Azure Blob Storage gebruiken voor modelconversie](blob-storage.md)
- [Modelconversie](model-conversion.md)

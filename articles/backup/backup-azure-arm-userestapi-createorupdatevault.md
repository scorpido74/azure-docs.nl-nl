---
title: Vaults van Recovery Services maken met REST API
description: Lees in dit artikel hoe u back-up- en herstelbewerkingen van Azure VM Backup beheert met restapi.
ms.topic: conceptual
ms.date: 08/21/2018
ms.assetid: e54750b4-4518-4262-8f23-ca2f0c7c0439
ms.openlocfilehash: 1901c35d2b4d8bcd02cc064fcfc844e19969e3b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74173417"
---
# <a name="create-azure-recovery-services-vault-using-rest-api"></a>Azure Recovery Services Vault maken met REST API

De stappen voor het maken van een Azure Recovery Services Vault met REST API worden beschreven in [het maken van vault REST API-documentatie.](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate) Laten we dit document gebruiken als referentie voor het maken van een kluis genaamd "testVault" in "West US".

Als u een kluis van Azure Recovery Services wilt maken of bijwerken, gebruikt u de volgende *PUT-bewerking.*

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}?api-version=2016-06-01
```

## <a name="create-a-request"></a>Een aanvraag maken

Om de *PUT-aanvraag te* maken, is de `{subscription-id}` parameter vereist. Zie Werken met meerdere abonnementen als u meerdere abonnementen [hebt.](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest) U definieert een `{resourceGroupName}` en `{vaultName}` voor `api-version` uw resources, samen met de parameter. In dit `api-version=2016-06-01`artikel wordt gebruik gebruikt van .

De volgende headers zijn vereist:

| Aanvraagheader   | Beschrijving |
|------------------|-----------------|
| *Inhoudstype:*  | Vereist. Ingesteld op `application/json`. |
| *Authorization:* | Vereist. Ingesteld op een geldig `Bearer` [toegangstoken](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients). |

Zie [Componenten van een REST API-aanvraag/-antwoord](/rest/api/azure/#components-of-a-rest-api-requestresponse)voor meer informatie over het maken van de aanvraag.

## <a name="create-the-request-body"></a>De aanvraaginstantie maken

De volgende algemene definities worden gebruikt om een aanvraaginstantie op te bouwen:

|Name  |Vereist  |Type  |Beschrijving  |
|---------|---------|---------|---------|
|eTag     |         |   Tekenreeks      |  Optionele eTag       |
|location     |  waar       |Tekenreeks         |   Resourcelocatie      |
|properties     |         | [VaultEigenschappen](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  Eigenschappen van de kluis       |
|sku     |         |  [Sku](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#sku)       |    Identificeert de unieke systeem-id voor elke Azure-bron     |
|tags     |         | Object        |     Resourcetags    |

Houd er rekening mee dat de naam van de kluisen en de naam van de resourcegroep zijn opgenomen in de PUT URI. De aanvraaginstantie definieert de locatie.

## <a name="example-request-body"></a>Voorbeeldaanvraaginstantie

Het volgende voorbeeld lichaam wordt gebruikt om een kluis in "West US" te maken. Geef de locatie op. De SKU is altijd "Standaard".

```json
{
  "properties": {},
  "sku": {
    "name": "Standard"
  },
  "location": "West US"
}
```

## <a name="responses"></a>Antwoorden

Er zijn twee succesvolle antwoorden voor de bewerking om een vault voor Herstelservices te maken of bij te werken:

|Name  |Type  |Beschrijving  |
|---------|---------|---------|
|200 OK     |   [Kluis](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)      | OK        |
|201 Gemaakt     | [Kluis](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)        |   Gemaakt      |

Zie [Het antwoordbericht verwerken](/rest/api/azure/#process-the-response-message)voor meer informatie over DE API-antwoorden van REST.

### <a name="example-response"></a>Voorbeeld van een antwoord

Een verkorte *201 Gemaakt* antwoord van de vorige voorbeeldaanvraaginstantie toont aan dat een *id* is toegewezen en dat de *provisioningState* *is geslaagd:*

```json
{
  "location": "westus",
  "name": "testVault",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "id": "/subscriptions/77777777-b0c6-47a2-b37c-d8e65a629c18/resourceGroups/Default-RecoveryServices-ResourceGroup/providers/Microsoft.RecoveryServices/vaults/testVault",
  "type": "Microsoft.RecoveryServices/vaults",
  "sku": {
    "name": "Standard"
  }
}
```

## <a name="next-steps"></a>Volgende stappen

[Maak een back-upbeleid voor het maken van een back-up van een Azure VM in deze kluis.](backup-azure-arm-userestapi-createorupdatepolicy.md)

Zie de volgende documenten voor meer informatie over de Azure REST API's:

- [REST-API van Azure Recovery Services-provider](/rest/api/recoveryservices/)
- [Aan de slag gaan met Azure REST API](/rest/api/azure/)

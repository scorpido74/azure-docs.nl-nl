---
title: Vault-configuratie van Recovery Services bijwerken met REST API
description: In dit artikel leest u hoe u de configuratie van Vault bijwerken met behulp van REST API.
ms.topic: conceptual
ms.date: 12/06/2019
ms.assetid: 9aafa5a0-1e57-4644-bf79-97124db27aa2
ms.openlocfilehash: 6cecbb18e0cd6f548e1688ef978f10dcee7d9fbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252361"
---
# <a name="update-azure-recovery-services-vault-configurations-using-rest-api"></a>Azure Recovery Services Vault-configuraties bijwerken met REST API

In dit artikel wordt beschreven hoe u back-upgerelateerde configuraties in de kluis van Azure Recovery Services bijwerkt met restapi.

## <a name="soft-delete-state"></a>Zachte verwijderstatus

Het verwijderen van back-ups van een beveiligd item is een belangrijke bewerking die moet worden gecontroleerd. Om te beschermen tegen onbedoelde verwijderingen, heeft de Azure Recovery Services-kluis een soft-delete-mogelijkheid. Met deze mogelijkheid kunnen klanten verwijderde back-ups, indien nodig, binnen een periode na de verwijdering herstellen.

Maar er zijn scenario's waarin deze mogelijkheid niet nodig is. Een kluis van Azure Recovery Services kan niet worden verwijderd als er back-upitems in zitten, zelfs niet zomaar verwijderd. Dit kan een probleem opleveren als de kluis onmiddellijk moet worden verwijderd. Bijvoorbeeld: implementatiebewerkingen ruimen vaak de gemaakte resources op in dezelfde werkstroom. Een implementatie kan een kluis maken, back-ups configureren voor een item, een testherstel uitvoeren en vervolgens doorgaan met het verwijderen van de back-upitems en de kluis. Als de verwijdering van de kluis mislukt, kan de hele implementatie mislukken. Het uitschakelen van soft-delete is de enige manier om onmiddellijke verwijdering te garanderen.

Daarom moet de klant zorgvuldig kiezen of soft-delete voor een bepaalde kluis moet worden uitgeschakeld, afhankelijk van het scenario. Zie het artikel [soft-delete](backup-azure-security-feature-cloud.md#soft-delete)voor meer informatie.

### <a name="fetch-soft-delete-state-using-rest-api"></a>Zachte verwijderstatus ophalen met REST API

Standaard is de status soft-delete ingeschakeld voor elke nieuw gemaakte vault van Recovery Services. Als u de status van soft-delete voor een kluis wilt ophalen/bijwerken, gebruikt u het config-gerelateerde [REST API-document](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs) van de back-upkluis

Als u de huidige status van soft-delete voor een kluis wilt ophalen, gebruikt u de volgende *GET-bewerking*

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-05-13
```

De GET `{subscriptionId}`URI `{vaultName}` `{vaultresourceGroupName}` heeft , , parameters. In dit `{vaultName}` voorbeeld is "testVault" en `{vaultresourceGroupName}` is "testVaultRG". Aangezien alle vereiste parameters in de URI worden gegeven, is er geen behoefte aan een aparte aanvraaginstantie.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-05-13
```

#### <a name="responses"></a>Antwoorden

De succesvolle reactie voor de 'GET'-bewerking wordt hieronder weergegeven:

|Name  |Type  |Beschrijving  |
|---------|---------|---------|
|200 OK     |   [BackupResourceVaultConfig](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | OK        |

##### <a name="example-response"></a>Voorbeeld van een antwoord

Zodra de 'GET'-aanvraag is ingediend, wordt een 200 (geslaagde) reactie geretourneerd.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testvaultRG/providers/Microsoft.RecoveryServices/vaults/testvault/backupconfig/vaultconfig",
  "name": "vaultconfig",
  "type": "Microsoft.RecoveryServices/vaults/backupconfig",
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Enabled"
  }
}
```

### <a name="update-soft-delete-state-using-rest-api"></a>Zachte verwijderstatus bijwerken met rest-API

Als u de status soft delete van de kluis van herstelservices wilt bijwerken met REST API, gebruikt u de volgende *PATCH-bewerking*

```http
PATCH https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-05-13
```

De PATCH `{subscriptionId}`URI `{vaultName}` `{vaultresourceGroupName}` heeft , , parameters. In dit `{vaultName}` voorbeeld is "testVault" en `{vaultresourceGroupName}` is "testVaultRG". Als we de URI vervangen door de bovenstaande waarden, dan zal de URI er zo uitzien.

```http
PATCH https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-05-13
```

#### <a name="create-the-request-body"></a>De aanvraaginstantie maken

De volgende gemeenschappelijke definities worden gebruikt om een aanvraaginstantie te maken

Zie voor meer informatie [de REST API-documentatie](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/update#request-body)

|Name  |Vereist  |Type  |Beschrijving  |
|---------|---------|---------|---------|
|eTag     |         |   Tekenreeks      |  Optionele eTag       |
|location     |  waar       |Tekenreeks         |   Resourcelocatie      |
|properties     |         | [VaultEigenschappen](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  Eigenschappen van de kluis       |
|tags     |         | Object        |     Resourcetags    |

#### <a name="example-request-body"></a>Voorbeeldaanvraaginstantie

Het volgende voorbeeld wordt gebruikt om de status soft-delete bij te werken naar 'uitgeschakeld'.

```json
{
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

#### <a name="responses"></a>Antwoorden

De succesvolle reactie voor de 'PATCH'-bewerking wordt hieronder weergegeven:

|Name  |Type  |Beschrijving  |
|---------|---------|---------|
|200 OK     |   [BackupResourceVaultConfig](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | OK        |

##### <a name="example-response"></a>Voorbeeld van een antwoord

Zodra de 'PATCH'-aanvraag is ingediend, wordt een 200 (geslaagde) reactie geretourneerd.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testvaultRG/providers/Microsoft.RecoveryServices/vaults/testvault/backupconfig/vaultconfig",
  "name": "vaultconfig",
  "type": "Microsoft.RecoveryServices/vaults/backupconfig",
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

## <a name="next-steps"></a>Volgende stappen

[Maak een back-upbeleid voor het maken van een back-up van een Azure VM in deze kluis.](backup-azure-arm-userestapi-createorupdatepolicy.md)

Zie de volgende documenten voor meer informatie over de Azure REST API's:

- [REST-API van Azure Recovery Services-provider](/rest/api/recoveryservices/)
- [Aan de slag gaan met Azure REST API](/rest/api/azure/)

---
title: Azure Key Vault als gebeurtenisrasterbron
description: Beschrijft de eigenschappen en het schema die zijn opgegeven voor Azure Key Vault-gebeurtenissen met Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: spelluru
ms.openlocfilehash: 40bff9585e64163039a8847ff868c982ffb20414
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458246"
---
# <a name="azure-key-vault-as-event-grid-source"></a>Azure Key Vault als gebeurtenisrasterbron

In dit artikel worden de eigenschappen en het schema voor gebeurtenissen in [Azure Key Vault](../key-vault/index.yml), momenteel in preview, weergeven. Zie Azure Event Grid-gebeurtenisschema voor een inleiding tot gebeurtenisschema ['Azure Event Grid'.](event-schema.md)

## <a name="event-grid-event-schema"></a>Gebeurtenisschema gebeurtenisraster

### <a name="available-event-types"></a>Beschikbare gebeurtenistypen

Een Azure Key Vault-account genereert de volgende gebeurtenistypen:

| Volledige naam van gebeurtenis | Naam van de weergave van gebeurtenissen | Beschrijving |
| ---------- | ----------- |---|
| Microsoft.keyvault.CertificateNewVersionGemaakt | Nieuwe versie certificaat gemaakt | Geactiveerd wanneer een nieuw certificaat of nieuwe certificaatversie wordt gemaakt. |
| Microsoft.KeyVault.CertificateNearExpiry | Certificaat bijna verlopen | Geactiveerd wanneer de huidige versie van het certificaat bijna verloopt. (De gebeurtenis wordt 30 dagen voor de vervaldatum geactiveerd.) |
| Microsoft.KeyVault.CertificateVerlopen | Certificaat is verlopen | Geactiveerd wanneer het certificaat is verlopen. |
| Microsoft.KeyVault.KeyNewVersionGemaakt | Belangrijkste nieuwe versie gemaakt | Geactiveerd wanneer een nieuwe sleutel of nieuwe sleutelversie wordt gemaakt. |
| Microsoft.KeyVault.KeyNearExpiry | Sleutel bijna verlopen | Geactiveerd wanneer de huidige versie van een sleutel bijna verloopt. (De gebeurtenis wordt 30 dagen voor de vervaldatum geactiveerd.) |
| Microsoft.KeyVault.KeyVerlopened | Sleutel verlopen | Geactiveerd wanneer een sleutel is verlopen. |
| Microsoft.KeyVault.SecretNewVersionGemaakt | Geheime nieuwe versie gemaakt | Geactiveerd wanneer een nieuwe geheime of nieuwe geheime versie wordt gemaakt. |
| Microsoft.KeyVault.SecretNearExpiry | Geheim bijna verlopen | Geactiveerd wanneer de huidige versie van een geheim bijna verloopt. (De gebeurtenis wordt 30 dagen voor de vervaldatum geactiveerd.) |
| Microsoft.KeyVault.SecretVerlopen | Geheim verlopen | Geactiveerd wanneer een geheim is verlopen. |

### <a name="event-examples"></a>Voorbeelden van gebeurtenissen

In het volgende voorbeeld wordt het schema voor **Microsoft.KeyVault.SecretNewVersionCreated weergegeven:**

```JSON
[
   {
      "id":"00eccf70-95a7-4e7c-8299-2eb17ee9ad64",
      "topic":"/subscriptions/{subscription-id}/resourceGroups/sample-rg/providers/Microsoft.KeyVault/vaults/sample-kv",
      "subject":"newsecret",
      "eventType":"Microsoft.KeyVault.SecretNewVersionCreated",
      "eventTime":"2019-07-25T01:08:33.1036736Z",
      "data":{
         "Id":"https://sample-kv.vault.azure.net/secrets/newsecret/ee059b2bb5bc48398a53b168c6cdcb10",
         "vaultName":"sample-kv",
         "objectType":"Secret",
         "objectName ":"newsecret",
         "version":" ee059b2bb5bc48398a53b168c6cdcb10",
         "nbf":"1559081980",
         "exp":"1559082102"
      },
      "dataVersion":"1",
      "metadataVersion":"1"
   }
]
```

### <a name="event-properties"></a>Gebeurtenis-eigenschappen

Een gebeurtenis heeft de volgende gegevens op het hoogste niveau:

| Eigenschap | Type | Beschrijving |
| ---------- | ----------- |---|
| id | tekenreeks | De id van het object dat deze gebeurtenis heeft geactiveerd |
| vaultName | tekenreeks | De naam van de sleutelkluis van het object dat deze gebeurtenis heeft geactiveerd |
| objectTekst | tekenreeks | Het type object dat deze gebeurtenis heeft geactiveerd |
| Objectnaam | tekenreeks | De naam van het object dat deze gebeurtenis heeft geactiveerd |
| versie | tekenreeks | De versie van het object dat deze gebeurtenis heeft geactiveerd |
| nbf (nbf) | getal | De niet-voordatum in seconden sinds 1970-01-01T00:00:00Z van het object dat deze gebeurtenis heeft geactiveerd |
| Exp | getal | De vervaldatum in seconden sinds 1970-01-01T00:00:00Z van het object dat deze gebeurtenis heeft geactiveerd |

## <a name="tutorials-and-how-tos"></a>Zelfstudies en handleidingen
|Titel  |Beschrijving  |
|---------|---------|
| [Key Vault-gebeurtenissen bewaken met Azure Event Grid](../key-vault/general/event-grid-overview.md) | Overzicht van de integratie van Key Vault met Event Grid. |
| [Zelfstudie: Key Vault-gebeurtenissen maken en bewaken met gebeurtenisraster](../key-vault/general/event-grid-tutorial.md) | Meer informatie over het instellen van Event Grid-meldingen voor Key Vault. |


## <a name="next-steps"></a>Volgende stappen

* Zie [Wat is gebeurtenisraster?](overview.md)
* Zie [Abonnement op gebeurtenisrastervoor](subscription-creation-schema.md)meer informatie over het maken van een Azure Event Grid-abonnement .
* Zie Key Vault met Azure Event [Grid (preview)](../key-vault/general/event-grid-overview.md)voor meer informatie over key vault-integratie met gebeurtenisraster.
* Zie [Belangrijke vaultmeldingen ontvangen en beantwoorden met Azure Event Grid (preview)](../key-vault/general/event-grid-tutorial.md)voor een zelfstudie over key vault-integratie met gebeurtenisraster.
* Zie het belangrijkste voor Key Vault en Azure Automation:
    - [Wat is Azure Sleutelkluis?](../key-vault/general/overview.md)
    - [Key Vault bewaken met Azure Event Grid (voorbeeld)](../key-vault/general/event-grid-overview.md)
    - [Meldingen van belangrijke kluizen ontvangen en beantwoorden met Azure Event Grid (voorbeeld)](../key-vault/general/event-grid-tutorial.md)
    - [Overzicht van Azure Automation](../automation/index.yml)

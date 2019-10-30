---
title: Azure Event Grid Azure Key Vault-gebeurtenis schema
description: Hierin worden de eigenschappen en schema's beschreven die voor Azure Key Vault gebeurtenissen worden gegeven met Azure Event Grid
services: event-grid
author: msmbaldwin
ms.service: event-grid
ms.topic: reference
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: c92352dd28b870c5f58dec0b82a8000f14a8e62d
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73033522"
---
# <a name="azure-event-grid-event-schema-for-azure-key-vault-preview"></a>Azure Event Grid-gebeurtenis schema voor Azure Key Vault (preview-versie)

In dit artikel vindt u de eigenschappen en het schema voor [Azure Key Vault](../key-vault/index.yml) gebeurtenissen, die momenteel als preview-versie beschikbaar zijn. Zie [Azure Event grid-gebeurtenis schema](event-schema.md)voor een inleiding tot gebeurtenis schema's.

## <a name="available-event-types"></a>Beschik bare gebeurtenis typen

Een Azure Key Vault account verzendt de volgende gebeurtenis typen:

| Volledige naam van de gebeurtenis | Weergave naam van gebeurtenis | description |
| ---------- | ----------- |---|
| Micro soft. CertificateNewVersionCreated | De nieuwe versie van het certificaat is gemaakt | Wordt geactiveerd wanneer een nieuw certificaat of nieuwe certificaat versie wordt gemaakt |
| Micro soft. CertificateNearExpiry | Certificaat bijna verlopen | Wordt geactiveerd wanneer de huidige versie van het certificaat bijna is verlopen (de standaard waarde is 30 dagen voor de verval datum) |
| Micro soft. CertificateExpired | Certificaat is verlopen | Geactiveerd wanneer het certificaat is verlopen |
| Micro soft. KeyNewVersionCreated | Nieuwe versie van de sleutel gemaakt | Wordt geactiveerd wanneer een nieuwe sleutel of nieuwe sleutel versie wordt gemaakt |
| Micro soft. KeyNearExpiry | Sleutel bijna verlopen | Wordt geactiveerd wanneer de huidige versie van de sleutel bijna is verlopen (de standaard waarde is 30 dagen voor de verval datum) |
| Micro soft. de sleutel kluis. | De sleutel is verlopen | Geactiveerd wanneer de sleutel is verlopen |
| Micro soft. SecretNewVersionCreated | Nieuwe geheime versie gemaakt | Wordt geactiveerd wanneer een nieuwe geheime of nieuwe geheime versie wordt gemaakt |
| Micro soft. SecretNearExpiry | Geheim bijna verlopen | Wordt geactiveerd wanneer de huidige versie van het geheim bijna is verlopen (de standaard waarde is 30 dagen voor de verval datum) |
| Micro soft. SecretExpired | Geheim verlopen | Geactiveerd wanneer het geheim is verlopen |

## <a name="event-examples"></a>Gebeurtenis voorbeelden

In het volgende voor beeld wordt schema weer gegeven voor **micro soft. SecretNewVersionCreated**.

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

## <a name="event-properties"></a>Gebeurtenis eigenschappen

Een gebeurtenis heeft de volgende gegevens op het hoogste niveau:

| Eigenschap | Type | Beschrijving |
| ---------- | ----------- |---|
| id | string | De ID van het object dat deze gebeurtenis heeft geactiveerd. |
| vaultName | string | De naam van de sleutel kluis van het object dat deze gebeurtenis heeft geactiveerd. |
| Later | string | Het type van het object dat deze gebeurtenis heeft geactiveerd |
| ObjectName | string | De naam van het object dat deze gebeurtenis heeft geactiveerd |
| versie | string | De versie van het object dat deze gebeurtenis heeft geactiveerd |
| NBF | getal | Niet vóór datum in seconden sinds 1970-01-01T00:00:00Z van het object dat deze gebeurtenis heeft geactiveerd |
| Geldig | getal | De verval datum in seconden sinds 1970-01-01T00:00:00Z van het object dat deze gebeurtenis heeft geactiveerd |


## <a name="next-steps"></a>Volgende stappen

* Zie [Wat is Event grid?](overview.md) voor een inleiding tot Azure Event grid.
* Zie [Event grid Subscription schema](subscription-creation-schema.md)voor meer informatie over het maken van een Azure Event grid-abonnement.
* Zie [Key Vault bewaken met Azure Event grid (preview)](../key-vault/event-grid-overview.md) voor meer informatie over de integratie van Key Vault/Event grid.
* Zie [How to: Key Vault Events routeren to Automation Runbook (preview)](../key-vault/event-grid-tutorial.md)voor een zelf studie over Key Vault/Event grid-integratie.

- [Overzicht van Azure Key Vault](../key-vault/key-vault-overview.md)
- [Overzicht van Azure Event Grid](overview.md)
- [Key Vault bewaken met Azure Event Grid (preview-versie)](../key-vault/event-grid-overview.md)
- [Procedure: Key Vault-gebeurtenissen naar Automation-Runbook routeren (preview)](../key-vault/event-grid-tutorial.md).
- [Overzicht van Azure Automation](../automation/index.yml)

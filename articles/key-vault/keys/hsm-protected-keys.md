---
title: Met HSM beveiligde sleutels genereren en overdragen - Azure Key Vault
description: Gebruik dit artikel bij het plannen, genereren en overdragen van uw eigen met HSM beveiligde sleutels voor gebruik met Azure Key Vault. Ook wel bekend als BYOK of Bring Your Own Key.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: tutorial
ms.date: 05/29/2020
ms.author: ambapat
ms.openlocfilehash: 76b10dbd9b6d801d93cd5d9704531eb1a6de36a5
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585489"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>Met HSM beveiligde sleutels importeren in Key Vault

Als u Azure Key Vault gebruikt, kunt u het beste sleutels in HSM's (Hardware Security Modules) importeren of genereren die de HSM-grens nooit overschrijden. Dit scenario wordt vaak *Bring Your Own Key* of BYOK genoemd. Azure Key Vault maakt gebruik van de nCipher nShield-serie van HSM's (gevalideerd voor FIPS 140-2 Level 2) om uw sleutels te beveiligen.

Deze functionaliteit is niet beschikbaar voor Azure China 21Vianet.

> [!NOTE]
> Zie [Wat is Azure Key Vault?](../general/overview.md) voor meer informatie over Azure Key Vault.  
> Zie [Wat is Azure Key Vault?](../general/overview.md) voor een zelfstudie waarmee u een sleutelkluis kunt maken voor met HSM beveiligde sleutels.

## <a name="supported-hsms"></a>Ondersteunde HSM's

Het overdragen van met HSM beveiligde sleutels naar Key Vault wordt ondersteund door twee verschillende methoden, afhankelijk van de HSM's die u gebruikt. Gebruik de onderstaande tabel om te bepalen welke methode moet worden gebruikt voor het genereren van uw HSM's. Draag vervolgens uw eigen met HSM beveiligde sleutels over voor gebruik met Azure Key Vault. 

|Naam van leverancier|Type leverancier|Ondersteunde HSM-modellen|Ondersteunde methode voor het overdragen van HSM-sleutels|
|---|---|---|---|
|[nCipher](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|Fabrikant,<br/>HSM as a Service|<ul><li>nShield-serie van HSM's</li><li>nShield as a service</ul>|**Methode 1:** [nCipher BYOK](hsm-protected-keys-ncipher.md) (met sterke attestatie voor sleutelimport en HSM-validatie)<br/>**Methode 2:** [De nieuwe BYOK-methode gebruiken](hsm-protected-keys-byok.md) |
|Thales|Fabrikant|<ul><li>Luna HSM 7-serie met firmwareversie 7.3 of hoger</li></ul>| [De nieuwe BYOK-methode gebruiken](hsm-protected-keys-byok.md)|
|Fortanix|Fabrikant,<br/>HSM as a Service|<ul><li>Self-Defending Key Management Service (SDKMS)</li><li>Equinix SmartKey</li></ul>|[De nieuwe BYOK-methode gebruiken](hsm-protected-keys-byok.md)|
|Marvell|Fabrikant|Alle LiquidSecurity-HSM's met<ul><li>Firmwareversie 2.0.4 of hoger</li><li>Firmwareversie 3.2 of hoger</li></ul>|[De nieuwe BYOK-methode gebruiken](hsm-protected-keys-byok.md)|
|Cryptomathic|ISV (Enterprise Key Management System)|Meerdere HSM-merken en -modellen, inclusief<ul><li>nCipher</li><li>Thales</li><li>Utimaco</li></ul>Zie de [site van Cryptomathic voor meer informatie](https://www.cryptomathic.com/azurebyok)|[De nieuwe BYOK-methode gebruiken](hsm-protected-keys-byok.md)|


## <a name="next-steps"></a>Volgende stappen

* Volg [Best practices voor Key Vault](../general/best-practices.md) om beveiliging, duurzaamheid en bewaking voor uw sleutels te garanderen.
* Raadpleeg [BYOK-specificatie](https://docs.microsoft.com/azure/key-vault/keys/byok-specification) voor een volledige beschrijving van de nieuwe BYOK-methode

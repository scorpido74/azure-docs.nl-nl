---
title: '& overdracht van met HSM beveiligde sleutels genereren-Azure Key Vault'
description: Gebruik dit artikel om u te helpen bij het plannen, genereren en overdragen van uw eigen met HSM beveiligde sleutels voor gebruik met Azure Key Vault. Ook wel bekend als BYOK of uw eigen sleutel nemen.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 05/29/2020
ms.author: ambapat
ms.openlocfilehash: ce59fadfcdbb38327ce603197400e9317202d651
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87061083"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>Met HSM beveiligde sleutels importeren in Key Vault

Voor extra zekerheid kunt u, wanneer u Azure Key Vault gebruikt, sleutels importeren of genereren in Hardware Security modules (Hsm's) die de HSM-grens nooit verlaten. Dit scenario wordt vaak *Bring Your Own Key* of BYOK genoemd. Azure Key Vault maakt gebruik van nCipher nShield-familie van Hsm's (FIPS 140-2 level 2 gevalideerd) om uw sleutels te beveiligen.

Deze functionaliteit is niet beschikbaar voor Azure China 21Vianet.

> [!NOTE]
> Zie [Wat is Azure Key Vault?](../general/overview.md) voor meer informatie over Azure Key Vault.  
> Zie [Wat is Azure Key Vault?](../general/overview.md)voor een zelf studie waarmee u aan de slag kunt gaan met het maken van een sleutel kluis voor met HSM beschermde sleutels.

## <a name="supported-hsms"></a>Ondersteunde Hsm's

Het overdragen van met HSM beveiligde sleutels naar Key Vault wordt ondersteund via twee verschillende methoden, afhankelijk van de Hsm's die u gebruikt. Gebruik de onderstaande tabel om te bepalen welke methode moet worden gebruikt voor het genereren van uw Hsm's en vervolgens uw eigen met HSM beveiligde sleutels overdragen om te gebruiken met Azure Key Vault. 

|Leveranciers naam|Leveranciers type|Ondersteunde HSM-modellen|Ondersteunde methode voor het overdragen van HSM-sleutels|
|---|---|---|---|
|[nCipher](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|Fabricage<br/>HSM as a Service|<ul><li>nShield-serie van Hsm's</li><li>nShield als een service</ul>|**Methode 1:** [nCipher BYOK](hsm-protected-keys-ncipher.md) (met sterke Attestation voor sleutel import en HSM-validatie)<br/>**Methode 2:** de [New BYOK-methode gebruiken](hsm-protected-keys-byok.md) |
|Thales|Fabrikant|<ul><li>Luna HSM 7-familie met firmware versie 7,3 of hoger</li></ul>| [De nieuwe methode BYOK gebruiken](hsm-protected-keys-byok.md)|
|Fortanix|Fabricage<br/>HSM as a Service|<ul><li>Zelf-verdediging van de service voor sleutel beheer (SDKMS)</li><li>Equinix SmartKey</li></ul>|[De nieuwe methode BYOK gebruiken](hsm-protected-keys-byok.md)|
|Marvell|Fabrikant|Alle LiquidSecurity-Hsm's met<ul><li>Firmware versie 2.0.4 of hoger</li><li>Firmware versie 3,2 of hoger</li></ul>|[De nieuwe methode BYOK gebruiken](hsm-protected-keys-byok.md)|
|Cryptomathic|ISV (Enter prise Key Management System)|Meerdere HSM-merken en-modellen, inclusief<ul><li>nCipher</li><li>Thales</li><li>Utimaco</li></ul>Zie [Cryptomathic-site voor meer informatie](https://www.cryptomathic.com/azurebyok)|[De nieuwe methode BYOK gebruiken](hsm-protected-keys-byok.md)|


## <a name="next-steps"></a>Volgende stappen

* Volg [Key Vault aanbevolen procedures](../general/best-practices.md) om de beveiliging, duurzaamheid en bewaking voor uw sleutels te garanderen.
* Raadpleeg de [BYOK-specificatie](https://docs.microsoft.com/azure/key-vault/keys/byok-specification) voor een volledige beschrijving van de nieuwe methode BYOK

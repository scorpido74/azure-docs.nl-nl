---
title: Met HSM beveiligde sleutels genereren en overdragen voor Azure Key Vault-Azure Key Vault | Microsoft Docs
description: Gebruik dit artikel om u te helpen bij het plannen, genereren en overdragen van uw eigen met HSM beveiligde sleutels voor gebruik met Azure Key Vault. Ook wel bekend als BYOK of uw eigen sleutel nemen.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: 00d2d38801929454110b41be88d133e3af232af7
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425745"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>Met HSM beveiligde sleutels importeren in Key Vault

Voor extra zekerheid kunt u, wanneer u Azure Key Vault gebruikt, sleutels importeren of genereren in Hardware Security modules (Hsm's) die de HSM-grens nooit verlaten. Dit scenario wordt vaak *uw eigen sleutel*of BYOK genoemd. Azure Key Vault maakt gebruik van nCipher nShield-familie van Hsm's (FIPS 140-2 level 2 gevalideerd) om uw sleutels te beveiligen.

Deze functionaliteit is niet beschikbaar voor Azure China 21Vianet.

> [!NOTE]
> Zie [Wat is Azure Key Vault?](key-vault-overview.md) voor meer informatie over Azure Key Vault.  
> Zie [Wat is Azure Key Vault?](key-vault-overview.md)voor een zelf studie waarmee u aan de slag kunt gaan met het maken van een sleutel kluis voor met HSM beschermde sleutels.

## <a name="supported-hsms"></a>Ondersteunde Hsm's

Het overdragen van met HSM beveiligde sleutels naar Key Vault wordt ondersteund via twee verschillende methoden, afhankelijk van de Hsm's die u gebruikt. Gebruik de onderstaande tabel om te bepalen welke methode moet worden gebruikt voor het genereren van uw Hsm's en vervolgens uw eigen met HSM beveiligde sleutels overdragen om te gebruiken met Azure Key Vault. 

|Naam van HSM-leverancier|Ondersteunde HSM-modellen|Ondersteunde methode voor het overdragen van HSM-sleutels|
|---|---|---|
|Thales|<ul><li>SafeNet Luna HSM 7-familie met firmware versie 7,3 of hoger</li></ul>| [De nieuwe methode BYOK gebruiken (preview-versie)](hsm-protected-keys-vendor-agnostic-byok.md)|
|nCipher|<ul><li>nShield-serie van Hsm's</li></ul>|[Verouderde BYOK-methode gebruiken](hsm-protected-keys-legacy.md)|


## <a name="next-steps"></a>Volgende stappen

Volg [Key Vault aanbevolen procedures](key-vault-best-practices.md) om de beveiliging, duurzaamheid en bewaking voor uw sleutels te garanderen.

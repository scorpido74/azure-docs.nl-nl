---
title: HSM-beveiligde sleutels genereren en overdragen voor Azure Key Vault - Azure Key Vault | Microsoft Documenten
description: Gebruik dit artikel om u te helpen bij het plannen, genereren en vervolgens overdragen van uw eigen HSM-beveiligde sleutels voor gebruik met Azure Key Vault. Ook wel bekend als BYOK of breng je eigen sleutel.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: 048e5072c592cf2de32e533014c99034572a1c47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082894"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>HSM-beveiligde sleutels importeren in Key Vault

Voor extra zekerheid u bij het gebruik van Azure Key Vault sleutels importeren of genereren in hardwarebeveiligingsmodules (HSM's) die nooit de HSM-grens verlaten. Dit scenario wordt vaak *Bring Your Own Key* of BYOK genoemd. Azure Key Vault maakt gebruik van nCipher nShield-familie hsm's (FIPS 140-2 Level 2 gevalideerd) om uw sleutels te beschermen.

Deze functionaliteit is niet beschikbaar voor Azure China 21Vianet.

> [!NOTE]
> Zie Wat is Azure Key Vault voor meer informatie over Azure Key [Vault?](key-vault-overview.md)  
> Zie [Wat is Azure Key Vault?](key-vault-overview.md)

## <a name="supported-hsms"></a>Ondersteunde HSM's

Het overbrengen van HSM-beveiligde sleutels naar Key Vault wordt ondersteund via twee verschillende methoden, afhankelijk van de HSM's die u gebruikt. Gebruik de onderstaande tabel om te bepalen welke methode moet worden gebruikt voor uw HSM's om te genereren en vervolgens uw eigen HSM-beveiligde sleutels over te dragen om te gebruiken met Azure Key Vault. 

|Naam van leverancier|Type leverancier|Ondersteunde HSM-modellen|Ondersteunde HSM-sleuteloverdrachtsmethode|
|---|---|---|---|
|nCipher|Fabrikant|<ul><li>nShield-familie van HSM's</li></ul>|[Verouderde BYOK-methode gebruiken](hsm-protected-keys-legacy.md)|
|Thales|Fabrikant|<ul><li>SafeNet Luna HSM 7 familie met firmware versie 7.3 of nieuwer</li></ul>| [Nieuwe BYOK-methode gebruiken (voorbeeld)](hsm-protected-keys-vendor-agnostic-byok.md)|
|Fortanix Fortanix|HSM as a Service|<ul><li>Self-Defending Key Management Service (SDKMS)</li></ul>|[Nieuwe BYOK-methode gebruiken (voorbeeld)](hsm-protected-keys-vendor-agnostic-byok.md)|










## <a name="next-steps"></a>Volgende stappen

Volg [de best practices](key-vault-best-practices.md) van Key Vault om de veiligheid, duurzaamheid en bewaking van uw sleutels te garanderen.

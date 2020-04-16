---
title: HSM-beveiligde sleutels genereren en overdragen voor Azure Key Vault - Azure Key Vault | Microsoft Documenten
description: Gebruik dit artikel om u te helpen bij het plannen, genereren en overdragen van uw eigen HSM-beveiligde sleutels voor gebruik met Azure Key Vault. Ook wel bekend als breng je eigen sleutel (BYOK).
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: 77568928e50fb4398aa786dbbd4a44b9277a8d5b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429705"
---
# <a name="import-hsm-protected-keys-to-key-vault-preview"></a>Met HSM beveiligde sleutels importeren in Key Vault (preview)

> [!NOTE]
> Deze functie is in preview en alleen beschikbaar in de Azure-regio's *East US 2 EUAP* en *Central US EUAP*. 

Voor extra zekerheid wanneer u Azure Key Vault gebruikt, u een sleutel importeren of genereren in een hardwarebeveiligingsmodule (HSM); de sleutel zal nooit de HSM-grens verlaten. Dit scenario wordt vaak aangeduid als *breng je eigen sleutel* (BYOK). Key Vault maakt gebruik van de nCipher nShield-familie van HSM's (FIPS 140-2 Level 2 gevalideerd) om uw sleutels te beschermen.

Gebruik de informatie in dit artikel om u te helpen bij het plannen, genereren en overdragen van uw eigen HSM-beveiligde sleutels voor gebruik met Azure Key Vault.

> [!NOTE]
> Deze functionaliteit is niet beschikbaar voor Azure China 21Vianet. 
> 
> Deze importmethode is alleen beschikbaar voor [ondersteunde HSM's.](#supported-hsms) 

Zie [Wat is Azure Key Vault?](../general/overview.md)

## <a name="overview"></a>Overzicht

Hier is een overzicht van het proces. Specifieke stappen die moeten worden voltooid, worden later in het artikel beschreven.

* Genereer in Key Vault een sleutel *(kek* genoemd). De KEK moet een RSA-HSM-sleutel `import` zijn die alleen de sleutelbewerking heeft. Alleen Key Vault Premium SKU ondersteunt RSA-HSM-sleutels.
* Download de KEK public key als een .pem-bestand.
* Breng de openbare SLEUTEL van KEK over naar een offline computer die is verbonden met een on-premises HSM.
* Gebruik op de offline computer het BYOK-hulpprogramma van uw HSM-leverancier om een BYOK-bestand te maken. 
* De doelsleutel wordt versleuteld met een KEK, die versleuteld blijft totdat deze wordt overgebracht naar de Key Vault HSM. Alleen de versleutelde versie van uw sleutel verlaat de on-premises HSM.
* Een KEK die wordt gegenereerd in een Key Vault HSM is niet exporteerbaar. HSM's handhaven de regel dat er geen duidelijke versie van een KEK bestaat buiten een Key Vault HSM.
* De KEK moet zich in dezelfde sleutelkluis bevindt waar de doelsleutel wordt geïmporteerd.
* Wanneer het BYOK-bestand wordt geüpload naar Key Vault, gebruikt een Key Vault HSM de PRIVÉsleutel KEK om het doelsleutelmateriaal te decoderen en te importeren als een HSM-sleutel. Deze bewerking vindt volledig plaats in een Key Vault HSM. De doelsleutel blijft altijd in de HSM-beschermingsgrens.

## <a name="prerequisites"></a>Vereisten

In de volgende tabel worden vereisten weergegeven voor het gebruik van BYOK in Azure Key Vault:

| Vereiste | Meer informatie |
| --- | --- |
| Een Azure-abonnement |Als u een sleutelkluis wilt maken in Azure Key Vault, hebt u een Azure-abonnement nodig. [Meld u aan voor een gratis proefperiode.](https://azure.microsoft.com/pricing/free-trial/) |
| Een Key Vault Premium SKU om HSM-beveiligde sleutels te importeren |Zie [Key Vault Pricing](https://azure.microsoft.com/pricing/details/key-vault/)voor meer informatie over de servicelagen en -mogelijkheden in Azure Key Vault. |
| Een HSM uit de ondersteunde HSM-lijst en een BYOK-tool en instructies van uw HSM-leverancier | U moet machtigingen hebben voor een HSM en basiskennis van het gebruik van uw HSM. Zie [Ondersteunde HSM's](#supported-hsms). |
| Azure CLI-versie 2.1.0 of hoger | Zie [De Azure CLI installeren](/cli/azure/install-azure-cli?view=azure-cli-latest).|

## <a name="supported-hsms"></a>Ondersteunde HSM's

|Naam van leverancier|Type leverancier|Ondersteunde HSM-modellen|Meer informatie|
|---|---|---|---|
|Thales|Fabrikant|SafeNet Luna HSM 7 familie met firmware versie 7.3 of hoger| [SafeNet Luna BYOK tool en documentatie](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|
|Fortanix Fortanix|HSM as a Service|Self-Defending Key Management Service (SDKMS)|[SDKMS-sleutels exporteren naar cloudproviders voor BYOK - Azure Key Vault](https://support.fortanix.com/hc/en-us/articles/360040071192-Exporting-SDKMS-keys-to-Cloud-Providers-for-BYOK-Azure-Key-Vault)|


> [!NOTE]
> Als u hsm-beveiligde sleutels wilt importeren uit de nCipher nShield-familie van HSM's, gebruikt u de [verouderde BYOK-procedure](hsm-protected-keys-legacy.md).

## <a name="supported-key-types"></a>Ondersteunde sleuteltypen

|Sleutelnaam|Type sleutel|Sleutelgrootte|Oorsprong|Beschrijving|
|---|---|---|---|---|
|Key Exchange-sleutel (KEK)|RSA| 2.048-bits<br />3.072-bits<br />4.096-bits|Azure Key Vault HSM|Een RSA-sleutelpaar met HSM-ondersteuning gegenereerd in Azure Key Vault|
|Doelsleutel|RSA|2.048-bits<br />3.072-bits<br />4.096-bits|Leverancier HSM|De sleutel die moet worden overgedragen naar de Azure Key Vault HSM|

## <a name="generate-and-transfer-your-key-to-the-key-vault-hsm"></a>Genereer en breng uw sleutel over naar de Key Vault HSM

Ga als lid op voor het genereren en overdragen van uw sleutel naar een Key Vault HSM:

* [Stap 1: Een KEK genereren](#step-1-generate-a-kek)
* [Stap 2: Download de kek public key](#step-2-download-the-kek-public-key)
* [Stap 3: Genereer en bereid je sleutel voor op overdracht](#step-3-generate-and-prepare-your-key-for-transfer)
* [Stap 4: Uw sleutel overbrengen naar Azure Key Vault](#step-4-transfer-your-key-to-azure-key-vault)

### <a name="step-1-generate-a-kek"></a>Stap 1: Een KEK genereren

Een KEK is een RSA-sleutel die wordt gegenereerd in een Key Vault HSM. De KEK wordt gebruikt om de sleutel te versleutelen die u wilt importeren (de *doelsleutel).*

De KEK moet:
- Een RSA-HSM-toets (2.048-bits; 3.072-bits; of 4.096-bits)
- Gegenereerd in dezelfde sleutelkluis waar u de doelsleutel wilt importeren
- Gemaakt met toegestane sleutelbewerkingen ingesteld op`import`

> [!NOTE]
> De KEK moet 'importeren' als de enige toegestane sleutelbewerking hebben. "import" sluit elkaar uit met alle andere belangrijke activiteiten.

Gebruik de opdracht [az keyvault-toets om](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) een KEK `import`te maken waarop belangrijke bewerkingen zijn ingesteld op . Neem de sleutel-id (`kid`) op die is geretourneerd vanuit de volgende opdracht. (U gebruikt `kid` de waarde in [stap 3](#step-3-generate-and-prepare-your-key-for-transfer).)

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-download-the-kek-public-key"></a>Stap 2: Download de kek public key

Gebruik [de az keyvault-sleutel download](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-download) om de KEK-openbare sleutel van een .pem-bestand te downloaden. De doelsleutel die u importeert, wordt versleuteld met behulp van de openbare KEK-sleutel.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

Breng het KEKforBYOK.publickey.pem-bestand over naar uw offline computer. U zult dit bestand in de volgende stap nodig hebben.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>Stap 3: Genereer en bereid je sleutel voor op overdracht

Raadpleeg de documentatie van uw HSM-leverancier om de BYOK-tool te downloaden en te installeren. Volg instructies van uw HSM-leverancier om een doelsleutel te genereren en maak vervolgens een sleuteloverdrachtspakket (een BYOK-bestand). De BYOK-tool `kid` gebruikt het bestand van [stap 1](#step-1-generate-a-kek) en het KEKforBYOK.publickey.pem dat u in [stap 2](#step-2-download-the-kek-public-key) hebt gedownload om een versleutelde doelsleutel in een BYOK-bestand te genereren.

Breng het BYOK-bestand over naar uw aangesloten computer.

> [!NOTE] 
> Het importeren van RSA 1.024-bits sleutels wordt niet ondersteund. Momenteel wordt het importeren van een Elliptic Curve (EC) sleutel niet ondersteund.
> 
> **Bekend probleem**: Het importeren van een RSA 4K-doelsleutel van SafeNet Luna HSMs wordt alleen ondersteund met firmware 7.4.0 of nieuwer.

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>Stap 4: Uw sleutel overbrengen naar Azure Key Vault

Als u de sleutelimport wilt voltooien, brengt u het sleuteloverdrachtspakket (een BYOK-bestand) over van uw losgekoppelde computer naar de computer die met internet is verbonden. Gebruik de [opdracht az keyvault-toetsinvoer](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) om het BYOK-bestand te uploaden naar de Key Vault HSM.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

Als de upload is geslaagd, worden in Azure CLI de eigenschappen van de geïmporteerde sleutel weergegeven.

## <a name="next-steps"></a>Volgende stappen

U deze HSM-beveiligde sleutel nu gebruiken in uw sleutelkluis. Zie voor meer informatie [deze prijs- en functievergelijking.](https://azure.microsoft.com/pricing/details/key-vault/)




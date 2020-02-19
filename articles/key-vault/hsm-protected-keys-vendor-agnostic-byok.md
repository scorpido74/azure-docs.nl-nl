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
ms.openlocfilehash: 2f5269587d222be9a1628b72c1f3f0dc1b105f3c
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461739"
---
# <a name="import-hsm-protected-keys-to-key-vault-preview"></a>Met HSM beveiligde sleutels importeren in Key Vault (preview-versie)

> [!NOTE]
> Deze functie is in Preview en alleen beschikbaar in de regio's **VS-Oost 2 EUAP** en **Centraal EUAP** . 

Voor extra zekerheid bij het gebruik van Azure Key Vault, kunt u sleutels importeren of genereren in Hardware Security modules (Hsm's) die de HSM-grens nooit verlaten. Dit scenario wordt vaak *uw eigen sleutel*of BYOK genoemd. Azure Key Vault maakt gebruik van nCipher nShield-familie van Hsm's (FIPS 140-2 level 2 gevalideerd) om uw sleutels te beveiligen.

Gebruik de informatie in dit onderwerp om u te helpen bij het plannen, genereren en overdragen van uw eigen met HSM beveiligde sleutels voor gebruik met Azure Key Vault.

> [!NOTE]
> Deze functionaliteit is niet beschikbaar voor Azure China 21Vianet. 
> 
> Deze import methode is alleen beschikbaar voor [ondersteunde hsm's](#supported-hsms). 

Zie [Wat is Azure Key Vault?](key-vault-overview.md) voor meer informatie over Azure Key Vault.  Zie [Wat is Azure Key Vault?](key-vault-overview.md)voor een zelf studie waarmee u aan de slag kunt gaan met het maken van een sleutel kluis voor met HSM beschermde sleutels.

## <a name="overview"></a>Overzicht

* Genereer een sleutel (waarnaar wordt verwezen als sleutel uitwisselings sleutel of KEK) in de sleutel kluis. Dit moet een RSA-HSM-sleutel zijn met ' Import ' als de enige sleutel bewerking. Alleen Key kluis Premium-SKU ondersteunt RSA-HSM-sleutels.
* De open bare sleutel van KEK downloaden als een. pem-bestand
* De open bare sleutel van KEK overzetten naar uw offline werk station dat is verbonden met een on-premises HSM.
* Gebruik het BYOK-hulp programma van uw HSM-leverancier om een BYOK-bestand te maken vanaf uw offline werk station. 
* De doel sleutel is versleuteld met een KEK, die versleuteld blijft totdat deze wordt overgedragen naar de Azure Key Vault Hsm's. Alleen de versleutelde versie van uw sleutel laat de on-premises HSM ongewijzigd.
* De KEK die wordt gegenereerd in de Azure Key Vault Hsm's en niet kan worden geëxporteerd. De Hsm's dwingt af dat er geen duidelijke versie van de KEK mag worden uitgevoerd buiten het Key Vault Hsm's.
* De KEK moet zich in dezelfde sleutel kluis bevindt als de doel sleutel moet worden geïmporteerd.
* Wanneer het BYOK-bestand wordt geüpload naar Key Vault, Key Vault Hsm's de persoonlijke sleutel KEK gebruiken om het doel sleutel materiaal te ontsleutelen en te importeren als een HSM-sleutel. Deze bewerking vindt volledig plaats in Key Vault Hsm's en de doel sleutel blijft altijd in de grens van de HSM-beveiliging.

## <a name="prerequisites"></a>Vereisten

Zie de volgende tabel voor een lijst met vereisten voor het nemen van uw eigen sleutel (BYOK) voor Azure Key Vault.

| Vereiste | Meer informatie |
| --- | --- |
| Een abonnement op Azure |Als u een Azure Key Vault wilt maken, hebt u een Azure-abonnement nodig: Meld u aan [voor een gratis proef versie](https://azure.microsoft.com/pricing/free-trial/) |
| Een sleutel kluis (Premium SKU) voor het importeren van met HSM beveiligde sleutels |Zie de [Azure Key Vault-prijs](https://azure.microsoft.com/pricing/details/key-vault/) website voor meer informatie over de service lagen en mogelijkheden voor Azure Key Vault. |
| Een HSM van de lijst met ondersteunde Hsm's samen met het BYOK-hulp programma en instructies van de HSM-leverancier | U moet toegang hebben tot een Hardware Security module en basis kennis van uw Hsm's. Zie [ondersteunde hsm's](#supported-hsms). |
| Azure CLI-versie 2.1.0 of hoger | Zie [de Azure cli installeren](/cli/azure/install-azure-cli?view=azure-cli-latest) voor meer informatie.|

## <a name="supported-hsms"></a>Ondersteunde Hsm's

|Naam van HSM-leverancier|Ondersteunde HSM-modellen|Aanvullende details|
|---|---|---|
|Thales|SafeNet Luna HSM 7-familie met firmware versie 7,3 of hoger| [SafeNet Luna BYOK tool en documentatie](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|


> [!NOTE]
> Voor het importeren van met HSM beveiligde sleutels uit de nCipher nShield-familie van Hsm's een [verouderde BYOK-procedure gebruiken](hsm-protected-keys-legacy.md)


## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>Uw sleutel genereren en overdragen naar Azure Key Vault HSM

U gebruikt de volgende stappen om uw sleutel te genereren en over te dragen naar een Azure Key Vault HSM:

* [Stap 1: een KEK genereren](#step-1-generate-a-kek)
* [Stap 2: open bare sleutel voor KEK downloaden](#step-2-download-kek-public-key)
* [Stap 3: uw sleutel voor overdracht genereren en voorbereiden](#step-3-generate-and-prepare-your-key-for-transfer)
* [Stap 4: uw sleutel overdragen naar Azure Key Vault](#step-4-transfer-your-key-to-azure-key-vault)

### <a name="step-1-generate-a-kek"></a>Stap 1: een KEK genereren

De KEK-sleutel (Key Exchange Key) is een RSA-sleutel die wordt gegenereerd in de HSM van Key Vault. Deze sleutel wordt gebruikt voor het versleutelen van de sleutel die moet worden geïmporteerd (doel sleutel).

KEK moet zijn:
1. een **RSA-HSM-** sleutel (2048-bits of 3072-bits of 4096-bits)
2. gegenereerd in de sleutel kluis waar u de doel sleutel wilt importeren
3. gemaakt met toegestane sleutel bewerkingen die zijn ingesteld om te worden **geïmporteerd**

Gebruik de opdracht [AZ sleutel kluis Key Create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) om KEK te maken waarvoor sleutel bewerkingen zijn ingesteld om te worden geïmporteerd. Noteer de sleutel-id ' Kid ' die wordt geretourneerd door de onderstaande opdracht. U hebt deze nodig in [stap 3](#step-3-generate-and-prepare-your-key-for-transfer).


```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-download-kek-public-key"></a>Stap 2: open bare sleutel voor KEK downloaden

Gebruik de [sleutel AZ Key kluis down load](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-download) om de open bare sleutel KEK te downloaden naar een. pem-bestand. De doel sleutel die u importeert, wordt versleuteld met de open bare sleutel KEK.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

Zet het bestand KEKforBYOK. PUBLICKEY. pem over naar uw offline werkstation. U hebt dit bestand nodig tijdens de volgende stap.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>Stap 3: uw sleutel voor overdracht genereren en voorbereiden

Raadpleeg de documentatie van uw HSM-leverancier om het BYOK-hulp programma te downloaden en te installeren. Volg de instructies van de HSM-leverancier om een doel sleutel te genereren en maak vervolgens een sleutel overdrachts pakket (een BYOK-bestand). Het BYOK-hulp programma gebruikt de sleutel-id uit [stap 1](#step-1-generate-a-kek) en het bestand KEKforBYOK. PUBLICKEY. pem dat u in [stap 2](#step-2-download-kek-public-key) hebt gedownload, om een versleutelde doel sleutel in een BYOK-bestand te genereren.

Het BYOK-bestand naar het verbonden werk station overdragen.

> [!NOTE] 
> De doel sleutel moet een RSA-sleutel zijn met een grootte van 2048 bits of 3072 bits of 4096 bits. Het importeren van elliptische curve sleutels wordt op dit moment niet ondersteund.
> <br/><strong>Bekend probleem:</strong> Importeren van RSA 4.000-doel sleutel van SafeNet Luna Hsm's mislukt. Wanneer het probleem is opgelost, wordt dit document bijgewerkt.

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>Stap 4: uw sleutel overdragen naar Azure Key Vault

Voor deze laatste stap brengt u het pakket voor sleutel overdracht (een BYOK-bestand) over van uw niet-verbonden werk station naar het met internet verbonden werk station en gebruikt u vervolgens de opdracht [AZ sleutel kluis Key import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) om het BYOK-bestand te uploaden Azure Key Vault HSM om de sleutel import te volt ooien.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

Als het uploaden is gelukt, ziet u de eigenschappen van de sleutel die u zojuist hebt geïmporteerd.

## <a name="next-steps"></a>Volgende stappen

U kunt deze met HSM beschermde sleutel nu gebruiken in uw sleutel kluis. Zie voor meer informatie deze [vergelijking](https://azure.microsoft.com/pricing/details/key-vault/)van prijzen en functies.

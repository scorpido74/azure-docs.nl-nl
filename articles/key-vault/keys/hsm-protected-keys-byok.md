---
title: Met HSM beveiligde sleutels genereren en overdragen voor Azure Key Vault-Azure Key Vault | Microsoft Docs
description: Gebruik dit artikel om u te helpen bij het plannen, genereren en overdragen van uw eigen met HSM beveiligde sleutels voor gebruik met Azure Key Vault. Ook wel bekend als uw eigen sleutel (BYOK).
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 05/29/2020
ms.author: ambapat
ms.openlocfilehash: 52214d42467dfa86b5e085a660a9416904b7de59
ms.sourcegitcommit: 8e5b4e2207daee21a60e6581528401a96bfd3184
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/04/2020
ms.locfileid: "84416695"
---
# <a name="import-hsm-protected-keys-to-key-vault-byok"></a>Met HSM beveiligde sleutels importeren in Key Vault (BYOK)

Voor extra zekerheid wanneer u Azure Key Vault gebruikt, kunt u een sleutel importeren of genereren in een Hardware Security module (HSM). de sleutel verlaat nooit de HSM-grens. Dit scenario wordt vaak *uw eigen sleutel* (BYOK) genoemd. Key Vault maakt gebruik van de nCipher nShield-familie van Hsm's (FIPS 140-2 level 2 gevalideerd) om uw sleutels te beveiligen.

Gebruik de informatie in dit artikel om u te helpen bij het plannen, genereren en overdragen van uw eigen met HSM beveiligde sleutels voor gebruik met Azure Key Vault.

> [!NOTE]
> Deze functionaliteit is niet beschikbaar voor Azure China 21Vianet. 
> 
> Deze import methode is alleen beschikbaar voor [ondersteunde hsm's](#supported-hsms). 

Zie [Wat is Azure Key Vault?](../general/overview.md)voor meer informatie en voor een zelf studie om aan de slag te gaan met Key Vault (inclusief het maken van een sleutel kluis voor met HSM beschermde sleutels).

## <a name="overview"></a>Overzicht

Hier volgt een overzicht van het proces. Specifieke stappen die u moet uitvoeren, worden verderop in het artikel beschreven.

* Genereer in Key Vault een sleutel (aangeduid met een sleutel *uitwisselings sleutel* (KEK)). De KEK moet een RSA-HSM-sleutel zijn die alleen de `import` sleutel bewerking heeft. Alleen Key Vault Premium SKU ondersteunt RSA-HSM-sleutels.
* Down load de open bare sleutel KEK als een. pem-bestand.
* De open bare sleutel van KEK overdragen naar een offline computer die is verbonden met een on-premises HSM.
* Gebruik op de offline computer het BYOK-hulp programma van de HSM-leverancier om een BYOK-bestand te maken. 
* De doel sleutel is versleuteld met een KEK, die versleuteld blijft totdat deze wordt overgedragen naar de Key Vault HSM. Alleen de versleutelde versie van uw sleutel laat de on-premises HSM ongewijzigd.
* Een KEK die wordt gegenereerd in een Key Vault HSM kan niet worden geëxporteerd. Hsm's dwingt de regel af dat er geen duidelijke versie van een KEK bestaat buiten een Key Vault HSM.
* De KEK moet zich in dezelfde sleutel kluis bekomen als de doel sleutel wordt geïmporteerd.
* Wanneer het BYOK-bestand wordt geüpload naar Key Vault, gebruikt een Key Vault HSM de persoonlijke sleutel KEK om het doel sleutel materiaal te ontsleutelen en te importeren als een HSM-sleutel. Deze bewerking treedt volledig op in een Key Vault HSM. De doel sleutel blijft altijd in de grens van de HSM-beveiliging.

## <a name="prerequisites"></a>Vereisten

De volgende tabel bevat de vereisten voor het gebruik van BYOK in Azure Key Vault:

| Vereiste | Meer informatie |
| --- | --- |
| Een Azure-abonnement |Als u een sleutel kluis in Azure Key Vault wilt maken, hebt u een Azure-abonnement nodig. [Meld u aan voor een gratis proef versie](https://azure.microsoft.com/pricing/free-trial/). |
| Een Key Vault Premium-SKU voor het importeren van met HSM beveiligde sleutels |Zie [Key Vault prijzen](https://azure.microsoft.com/pricing/details/key-vault/)voor meer informatie over de service lagen en mogelijkheden van Azure Key Vault. |
| Een HSM uit de lijst met ondersteunde Hsm's en een BYOK-hulp programma en instructies van uw HSM-leverancier | U moet over machtigingen beschikken voor een HSM en basis kennis van het gebruik van uw HSM. Zie [ondersteunde hsm's](#supported-hsms). |
| Azure CLI-versie 2.1.0 of hoger | Zie [de Azure cli installeren](/cli/azure/install-azure-cli?view=azure-cli-latest).|

## <a name="supported-hsms"></a>Ondersteunde Hsm's

|Leveranciers naam|Leveranciers type|Ondersteunde HSM-modellen|Meer informatie|
|---|---|---|---|
|nCipher|Fabricage<br/>HSM as a Service|<ul><li>nShield-serie van Hsm's</li><li>nShield als een service</ul>|[nCipher nieuw BYOK-hulp programma en documentatie](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|
|Thales|Fabrikant|<ul><li>Luna HSM 7-familie met firmware versie 7,3 of hoger</li></ul>| [Hulp programma en documentatie voor Luna BYOK](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|
|Fortanix|Fabricage<br/>HSM as a Service|<ul><li>Zelf-verdediging van de service voor sleutel beheer (SDKMS)</li><li>Equinix SmartKey</li></ul>|[SDKMS-sleutels exporteren naar cloud providers voor BYOK-Azure Key Vault](https://support.fortanix.com/hc/en-us/articles/360040071192-Exporting-SDKMS-keys-to-Cloud-Providers-for-BYOK-Azure-Key-Vault)|
|Marvell|Fabrikant|Alle LiquidSecurity-Hsm's met<ul><li>Firmware versie 2.0.4 of hoger</li><li>Firmware versie 3,2 of hoger</li></ul>|[Hulp programma voor Marvell-BYOK en-documentatie](https://www.marvell.com/products/security-solutions/nitrox-hs-adapters/exporting-marvell-hsm-keys-to-cloud-azure-key-vault.html)|
|Cryptomathic|ISV (Enter prise Key Management System)|Meerdere HSM-merken en-modellen, inclusief<ul><li>nCipher</li><li>Thales</li><li>Utimaco</li></ul>Zie [Cryptomathic-site voor meer informatie](https://www.cryptomathic.com/azurebyok)|[Hulp programma en documentatie voor Cryptomathic BYOK](https://www.cryptomathic.com/azurebyok)|



## <a name="supported-key-types"></a>Ondersteunde sleutel typen

|Sleutelnaam|Type sleutel|Sleutel grootte|Oorsprong|Beschrijving|
|---|---|---|---|---|
|Sleutel uitwisselings sleutel (KEK)|RSA| 2.048-bits<br />3.072-bits<br />4.096-bits|Azure Key Vault HSM|Een met HSM ondersteund RSA-sleutel paar dat is gegenereerd in Azure Key Vault|
|Doel sleutel|RSA|2.048-bits<br />3.072-bits<br />4.096-bits|Leverancier HSM|De sleutel die moet worden overgedragen naar de Azure Key Vault HSM|

## <a name="generate-and-transfer-your-key-to-the-key-vault-hsm"></a>Uw sleutel genereren en overdragen naar de Key Vault HSM

U kunt als volgt uw sleutel genereren en overdragen naar een Key Vault HSM:

* [Stap 1: een KEK genereren](#step-1-generate-a-kek)
* [Stap 2: de open bare sleutel voor KEK downloaden](#step-2-download-the-kek-public-key)
* [Stap 3: uw sleutel voor overdracht genereren en voorbereiden](#step-3-generate-and-prepare-your-key-for-transfer)
* [Stap 4: uw sleutel overdragen naar Azure Key Vault](#step-4-transfer-your-key-to-azure-key-vault)

### <a name="step-1-generate-a-kek"></a>Stap 1: een KEK genereren

Een KEK is een RSA-sleutel die wordt gegenereerd in een Key Vault HSM. De KEK wordt gebruikt voor het versleutelen van de sleutel die u wilt importeren (de *doel* sleutel).

De KEK moet zijn:
- Een RSA-HSM-sleutel (2.048-bits; 3.072-bits; of 4.096-bits)
- Gegenereerd in de sleutel kluis waar u de doel sleutel wilt importeren
- Gemaakt met toegestane sleutel bewerkingen ingesteld op`import`

> [!NOTE]
> De KEK moet ' Import ' bevatten als de enige toegestane sleutel bewerking. ' Import ' is wederzijds exclusief met alle andere sleutel bewerkingen.

Gebruik de opdracht [AZ sleutel kluis Key Create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) om een KEK te maken waarop de sleutel bewerkingen zijn ingesteld `import` . Noteer de sleutel-id ( `kid` ) die wordt geretourneerd door de volgende opdracht. (U gebruikt de `kid` waarde in [stap 3](#step-3-generate-and-prepare-your-key-for-transfer).)

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-download-the-kek-public-key"></a>Stap 2: de open bare sleutel voor KEK downloaden

Gebruik [AZ sleutel kluis down load](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-download) om de open bare sleutel KEK te downloaden naar een. pem-bestand. De doel sleutel die u importeert, wordt versleuteld met behulp van de open bare sleutel KEK.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

Zet het bestand KEKforBYOK. PUBLICKEY. pem over naar uw offline computer. U hebt dit bestand nodig in de volgende stap.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>Stap 3: uw sleutel voor overdracht genereren en voorbereiden

Raadpleeg de documentatie van uw HSM-leverancier om het BYOK-hulp programma te downloaden en te installeren. Volg de instructies van de HSM-leverancier om een doel sleutel te genereren en maak vervolgens een sleutel overdrachts pakket (een BYOK-bestand). Het hulp programma BYOK maakt gebruik `kid` van de uit [stap 1](#step-1-generate-a-kek) en het bestand KEKforBYOK. PUBLICKEY. pem dat u in [stap 2](#step-2-download-the-kek-public-key) hebt gedownload om een versleutelde doel sleutel in een BYOK-bestand te genereren.

Het BYOK-bestand naar de verbonden computer overzetten.

> [!NOTE] 
> Het importeren van RSA 1.024-bits sleutels wordt niet ondersteund. Het importeren van een elliptische curve sleutel (EC) wordt momenteel niet ondersteund.
> 
> **Bekend probleem**: het importeren van een doel sleutel van RSA 4.000 van Luna hsm's wordt alleen ondersteund met firmware 7.4.0 of nieuwer.

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>Stap 4: uw sleutel overdragen naar Azure Key Vault

Als u de sleutel import wilt volt ooien, brengt u het sleutel overdrachts pakket (een BYOK-bestand) over van de niet-verbonden computer naar de computer met Internet verbinding. Gebruik de opdracht [AZ file kluis Key import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) om het BYOK-bestand te uploaden naar de Key Vault HSM.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

Als het uploaden is gelukt, worden in azure CLI de eigenschappen van de geïmporteerde sleutel weer gegeven.

## <a name="next-steps"></a>Volgende stappen

U kunt deze met HSM beschermde sleutel nu gebruiken in uw sleutel kluis. Zie voor meer informatie [deze vergelijking van prijzen en functies](https://azure.microsoft.com/pricing/details/key-vault/).




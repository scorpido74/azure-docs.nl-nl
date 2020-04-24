---
title: Zelf studie-de frequentie van automatische rotatie van het certificaat bijwerken in Key Vault | Microsoft Docs
description: Zelf studie laat zien hoe u de automatische rotatie frequentie van een certificaat in Azure Key Vault kunt bijwerken met behulp van de Azure Portal
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/16/2020
ms.author: sebansal
ms.openlocfilehash: 2e6c250a0bcb9d73e7c572dfe8138c31269993e8
ms.sourcegitcommit: 354a302d67a499c36c11cca99cce79a257fe44b0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82106802"
---
# <a name="tutorial-configuring-certificates-auto-rotation-in-key-vault"></a>Zelf studie: de automatische rotatie van het certificaat configureren in Key Vault

Met Azure Key Vault kunt u eenvoudig digitale certificaten inrichten, beheren en implementeren. Dit kunnen open bare en privé SSL/TLS-certificaten zijn die zijn ondertekend door een certificerings instantie of een zelfondertekend certificaat. Key Vault kunt ook certificaten aanvragen en vernieuwen via partnerschappen met certificerings instanties, waardoor een robuuste oplossing voor het levenscyclus beheer van certificaten wordt geboden. In deze zelf studie werkt u de kenmerken van het certificaat bij: geldigheids periode, automatische rotatie frequentie, CA. Raadpleeg het [Overzicht](../general/overview.md) voor meer informatie over Key Vault.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een certificaat beheren met Azure Portal
> * Account van de certificerings instantie provider toevoegen
> * Geldigheids periode van het certificaat bijwerken
> * Frequentie van automatische rotatie van het certificaat bijwerken
> * De kenmerken van het certificaat bijwerken met behulp van Azure Power shell


Lees [Key Vault basis concepten](../general/basic-concepts.md)voordat u begint. 

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-a-vault"></a>Een kluis maken

Maak of Selecteer uw bestaande Key Vault om bewerkingen uit te voeren. [(Stappen voor het maken van een sleutel kluis).](../quick-create-portal.md) In het voor beeld is de naam van de kluis **voor beeld-kluis**. 

![Uitvoer nadat het maken van de sleutelkluis is voltooid](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="create-a-certificate-in-key-vault"></a>Een certificaat maken in Key Vault

Een certificaat in de kluis maken of importeren. [(Stappen voor het maken van een certificaat in de sleutel kluis).](../quick-create-portal.md) In dit geval werken we aan het certificaat met de naam **ExampleCertificate**.

> [!NOTE]
> In Azure Key Vault kunnen de kenmerken van de levenscyclus cyclus van een certificaat worden bijgewerkt op het moment dat het certificaat wordt gemaakt en nadat het is gemaakt. 
## <a name="updating-certificates-life-cycle-attributes"></a>De kenmerken van de levens cyclus van het certificaat bijwerken

Een certificaat dat is gemaakt in het Key Vault kan worden 
- een zelfondertekend certificaat
- een certificaat dat is gemaakt met een certificerings instantie (CA) die is gekoppeld aan Key Vault
- een certificaat met een certificerings instantie die niet is gekoppeld aan Key Vault

De volgende certificerings instanties zijn momenteel partner providers met Key Vault:
- DigiCert-Key Vault biedt OV TLS/SSL-certificaten met DigiCert.
- GlobalSign-Key Vault biedt OV TLS/SSL-certificaten met GlobalSign.

Azure Key Vault automatisch certificaten draaien via partnerschappen met certificerings instanties. Via die gevestigde partnership Key Vault automatisch aanvragen en vernieuwt certificaten. Daarom **is de mogelijkheid om automatisch te draaien niet van toepassing op certificaten die zijn gemaakt met ca's die niet zijn gekoppeld aan Key Vault.** 

> [!NOTE]
> Een account beheerder voor een CA-provider maakt referenties voor gebruik door Key Vault om TLS/SSL-certificaten te maken, vernieuwen en gebruiken via Key Vault.
![Certificeringsinstantie](../media/certificates/tutorial-rotate-cert/cert-authority-create.png)
> 


### <a name="updating-certificates-life-cycle-attributes-at-the-time-of-certificate-creation"></a>De kenmerken van de levens cyclus van het certificaat worden bijgewerkt op het moment dat het certificaat wordt gemaakt

1. Selecteer op de pagina eigenschappen van Key Vault de optie **certificaten**.
2. Klik op **Genereren/importeren**.
3. Werk in het scherm **een certificaat maken** de volgende waarden bij:
    

    - **Geldigheids periode**: Voer de waarde in (in maanden). Het maken van certificaten met een korte levens duur is een aanbevolen beveiligings procedure. Standaard is de geldigheids waarde van een nieuw gemaakt certificaat 12 maanden.
    - **Actie type levens duur**: Selecteer de actie automatisch vernieuwen en waarschuwingen over het certificaat. Werk tijdens de selectie de waarde ' percentage levens duur ' of ' aantal dagen vóór verval datum '. Standaard wordt de automatische verlenging van een certificaat ingesteld op 80% van de levens duur.<br> Selecteer in de vervolg keuzelijst de optie:

    |  Automatisch verlengen op een bepaald tijdstip| Alle contact personen op een bepaald moment per e-mail verzenden |
    |-----------|------|
    |Als u deze optie selecteert, wordt automatisch draaien ingeschakeld | Als u deze optie selecteert, wordt deze niet automatisch gedraaid, worden de contact personen alleen gewaarschuwd|
        


4. Klik op **Maken**.

![Levens cyclus van het certificaat](../media/certificates/tutorial-rotate-cert/create-cert-lifecycle.png)

### <a name="updating-life-cycle-attributes-of-stored-certificate"></a>De levenscyclus kenmerken van het opgeslagen certificaat worden bijgewerkt

1. Selecteer de Key Vault.
2. Selecteer op de pagina eigenschappen van Key Vault de optie **certificaten**.
3. Selecteer het certificaat dat u wilt bijwerken. In dit geval werken we aan het certificaat met de naam **ExampleCertificate**.
4. Selecteer **uitgifte beleid** in de bovenste menu balk.

![Certificaat eigenschappen](../media/certificates/tutorial-rotate-cert/cert-issuance-policy.png)
5. Werk op het scherm **uitgifte beleid** de volgende waarden bij:
- **Geldigheids periode**: de waarde (in maanden) bijwerken
- **Actie type levens duur**: Selecteer de actie automatisch vernieuwen en waarschuwingen over het certificaat. Werk, afhankelijk van de selectie, het percentage levens duur of het aantal dagen vóór verval datum bij. 

![Certificaat eigenschappen](../media/certificates/tutorial-rotate-cert/cert-policy-change.png)
6. Klik op **Opslaan**.

> [!IMPORTANT]
> Als u het levensduur actie type voor een certificaat wijzigt, worden de wijzigingen voor de bestaande certificaten onmiddellijk vastgelegd.


### <a name="updating-certificates-attributes-using-powershell"></a>De kenmerken van het certificaat bijwerken met behulp van Power shell

```azurepowershell


Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName 
                                   -Name $certificateName 
                                   -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
```

> [!TIP]
> Voor het wijzigen van het vernieuwings beleid voor een lijst met certificaten, een invoer bestand. CSV met de Kluisnaam, Certnaam <br/>
>  vault1,Cert1 <br/>
>  vault2, Cert2
>
>  ```azurepowershell
>  $file = Import-CSV C:\Users\myfolder\ReadCSVUsingPowershell\File.csv 
> foreach($line in $file)
> {
> Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName -Name $certificateName -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
> }
>  ```
> 
Meer informatie over [de para](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-set-attributes) meters

## <a name="clean-up-resources"></a>Resources opschonen

Andere Key Vault-snelstarts en zelfstudies bouwen voort op deze snelstart. Als u van plan bent om verder te gaan met volgende snelstarts en zelfstudies, kunt u deze resources intact laten.
Als u die niet meer nodig hebt, verwijdert u de resourcegroep. Hierdoor worden ook de sleutelkluis en de gerelateerde resources verwijderd. De resourcegroep verwijderen via de portal:

1. Voer de naam van uw resourcegroep in het vak Zoeken bovenaan de portal in. Wanneer u de in deze snelstart gebruikte resourcegroep in de zoekresultaten ziet, selecteert u deze.
2. Selecteer **Resourcegroep verwijderen**.
3. Typ in het vak **TYP DE NAAM VAN DE RESOURCEGROEP** de naam van de resourcegroep en selecteer **Verwijderen**.


## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u de levens cyclus van een certificaat bijgewerkt. Ga verder met de volgende artikelen voor meer informatie over Key Vault en hoe u deze integreert met uw toepassingen.

Meer informatie over het [maken van certificaten in azure Key Vault](https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-scenarios)
- Bekijk het [Key Vault overzicht](../general/overview.md)
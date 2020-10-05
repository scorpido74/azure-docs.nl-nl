---
title: Informatie over het verlengen van Azure Key Vault-certificaten
description: In dit artikel wordt beschreven hoe u Azure Key Vault-certificaten kunt verlengen.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: 3809fa9e1ce17a5a0c3cf333ac20ef543db4b5a7
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88588800"
---
# <a name="renew-your-azure-key-vault-certificates"></a>Azure Key Vault-certificaten verlengen

Met Azure Key Vault kunt u eenvoudig digitale certificaten voor uw netwerk inrichten, beheren en implementeren en beveiligde communicatie voor toepassingen mogelijk maken. Zie [Azure Key Vault-certificaten](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates) voor meer informatie over certificaten.

Door gebruik te maken van kortlevende certificaten of door de frequentie van de rotatie van certificaten te verhogen, kunt u toegang tot uw toepassingen door onbevoegde gebruikers voorkomen.

In dit artikel wordt beschreven hoe u Azure Key Vault-certificaten kunt verlengen.

## <a name="get-notified-about-certificate-expirations"></a>Meldingen ontvangen over verlopen certificaten
Ga als volgt te werk als u een melding wilt ontvangen wanneer uw certificaten bijna zijn verlopen:

Voeg eerst een certificaatcontactpersoon aan uw sleutelkluis toen met behulp van de PowerShell-cmdlet [Add-AzureKeyVaultCertificateContact](https://docs.microsoft.com/powershell/module/azurerm.keyvault/add-azurekeyvaultcertificatecontact?view=azurermps-6.13.0).

Stel vervolgens het tijdstip in wanneer u wilt worden gewaarschuwd over het verlopen van het certificaat. Zie [Automatisch roteren van certificaatconfiguratie in Key Vault](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-rotate-certificates#update-lifecycle-attributes-of-a-stored-certificate) voor informatie over het configureren van de levenscycluskenmerken van het certificaat.

Key Vault kent drie categorieën certificaten:
-   Certificaten die zijn gemaakt met een geïntegreerde certificeringsinstantie (CA), zoals DigiCert of GlobalSign
-   Certificaten die zijn gemaakt met een niet-geïntegreerde CA
-   Zelfondertekende certificaten

## <a name="renew-an-integrated-ca-certificate"></a>Een geïntegreerd CA-certificaat verlengen 
Azure Key Vault verwerkt het end-to-end-onderhoud van certificaten die worden uitgegeven door de vertrouwde Microsoft-certificeringsinstanties DigiCert en GlobalSign. Meer informatie over het [integreren van een vertrouwde CA met Key Vault](https://docs.microsoft.com/azure/key-vault/certificates/how-to-integrate-certificate-authority).

## <a name="renew-a-nonintegrated-ca-certificate"></a>Een niet-geïntegreerd CA-certificaat verlengen 
U kunt met behulp van Azure Key Vault certificaten importeren vanuit elke CA, een voordeel waarmee u met verschillende Azure-resources kunt integreren en de implementatie eenvoudig kunt maken. Als u zich zorgen maakt dat u de vervaldatum van uw certificaat niet meer weet of, erger, dat u hebt ontdekt dat een certificaat al is verlopen, kunt u met behulp van uw sleutelkluis up-to-date blijven. Voor niet-geïntegreerde CA-certificaten kunt u met de sleutelkluis e-mailmeldingen instellen bij bijna verlopen certificaten. Dergelijke meldingen kunnen ook voor meerdere gebruikers worden ingesteld.

> [!IMPORTANT]
> Een certificaat is een object met versiebeheer. Als de huidige versie verloopt, moet u een nieuwe versie maken. Elke nieuwe versie is conceptueel een nieuw certificaat dat bestaat uit een sleutel en een blob die die sleutel aan een identiteit koppelt. Wanneer u een niet-gelieerde CA gebruikt, genereert de sleutelkluis een sleutel-/waardepaar en wordt een aanvraag voor certificaatondertekening (CSR) geretourneerd.

Ga als volgt te werk als u een niet-geïntegreerd CA-certificaat wilt verlengen:

1. Meld u aan bij Azure Portal en open vervolgens het certificaat dat u wilt verlengen.
1. Selecteer **Nieuwe versie** in het certificaatdeelvenster.
1. Selecteer **Certificaatbewerking**.
1. Selecteer **CSR downloaden** om een CSR-bestand naar uw lokale station te downloaden.
1. Verzend de CSR naar de gewenste certificeringsinstantie om de aanvraag te ondertekenen.
1. Breng de ondertekende aanvraag terug en selecteer **CSR samenvoegen** in hetzelfde venster voor de certificaatbewerking.

> [!NOTE]
> Het is van belang om de ondertekende CSR samen te voegen met dezelfde CSR-aanvraag die u hebt gemaakt. Anders komt de sleutel niet overeen.

Zie [een CSR maken en samenvoegen in Key Vault]( https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-signing-request#azure-portal) voor meer informatie over het maken van een nieuwe CSR.

## <a name="renew-a-self-signed-certificate"></a>Een zelfondertekend certificaat verlengen

Azure Key Vault verwerkt ook het automatisch verlengen van zelfondertekende certificaten. Zie [Automatisch roteren van certificaatconfiguratie in Key Vault](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-rotate-certificates#update-lifecycle-attributes-of-a-stored-certificate) voor meer informatie over het wijzigen van het uitgiftebeleid en het bijwerken van de levenscycluskenmerken van een certificaat.

## <a name="troubleshoot"></a>Problemen oplossen
Als het uitgegeven certificaat in Azure Portal de status *uitgeschakeld* heeft, gaat u naar **Certificaatbewerking** om het foutbericht voor dat certificaat te bekijken.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

**Hoe kan ik de functie voor het automatisch roteren van het certificaat testen?**

Maak een certificaat met een geldigheidsduur van **1 maand** en stel vervolgens de levensduuractie voor rotatie in op **1%** . Met deze instelling wordt het certificaat elke 7,2 uur geroteerd.
  
**Worden de labels gerepliceerd als het certificaat automatisch is verlengd?**

Ja, de labels worden na het verlengen gerepliceerd.

## <a name="next-steps"></a>Volgende stappen
*   [Key Vault integreren met DigiCert-certificeringsinstantie](how-to-integrate-certificate-authority.md)
*   [Zelfstudie: Automatische rotatie van certificaten in Key Vault configureren](tutorial-rotate-certificates.md)

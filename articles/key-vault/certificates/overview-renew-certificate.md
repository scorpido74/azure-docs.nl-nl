---
title: Over Azure Key Vault certificaat vernieuwen
description: Over Azure Key Vault certificaat vernieuwen
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: bbd65cdfc7c8ebbc08b914158577337cc49ec887
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/14/2020
ms.locfileid: "88236060"
---
# <a name="about-azure-key-vault-certificate-renewal"></a>Over Azure Key Vault certificaat vernieuwen

Met Azure Key Vault kunt u eenvoudig digitale certificaten voor uw netwerk inrichten, beheren en implementeren en beveiligde communicatie mogelijk maken voor toepassingen. Zie voor meer algemene informatie over certificaten [Azure Key Vault certificaten](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates)

Met een kort certificaat of het verg Roten van de frequentie van de draaiing van certificaten beperkt het bereik van de kwaadwillende persoon voor schade.

## <a name="certificate-expiration-notifications"></a>Meldingen voor verlopen van certificaten
Zorg er eerst voor dat u een certificaat contact persoon toevoegt aan uw Key Vault om een melding te ontvangen wanneer certificaten bijna verlopen (bijvoorbeeld gebruik Power shell [add-AzureKeyVaultCertificateContact](https://docs.microsoft.com/powershell/module/azurerm.keyvault/add-azurekeyvaultcertificatecontact?view=azurermps-6.13.0)) om te configureren wanneer u een melding wilt ontvangen over het verlopen van certificaten. [Voer de volgende stappen](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-rotate-certificates#update-lifecycle-attributes-of-a-stored-certificate) uit om de actie voor de levens duur te configureren

Er zijn drie categorieën voor het maken van certificaten in sleutel kluis. Deze hand leiding helpt u te begrijpen hoe het vernieuwen van certificaten kan worden gerealiseerd.
-   Certificaten die zijn gemaakt met een geïntegreerde CA (DigiCert of GlobalSign)
-   Certificaten die zijn gemaakt met een niet-geïntegreerde CA
-   Zelfondertekende certificaten

## <a name="renewal-of-integrated-ca-certificate"></a>Verlenging van het geïntegreerde CA-certificaat 
Goed nieuws. Azure-sleutel kluizen zorgen voor het end-to-end-onderhoud van certificaten die worden uitgegeven door vertrouwde certificerings instanties van micro soft, dat wil zeggen DigiCert en GlobalSign. Meer informatie over het [integreren van een vertrouwde CA met sleutel kluis](https://docs.microsoft.com/azure/key-vault/certificates/how-to-integrate-certificate-authority).

## <a name="renewal-of-non-integrated-ca-certificate"></a>Vernieuwen van niet-geïntegreerd CA-certificaat 
Azure Key kluis biedt gebruikers het voor deel van het importeren van certificaten van elke CA zodat de gebruikers kunnen integreren met verschillende Azure-resources en de implementatie eenvoudig kunnen uitvoeren. Als u bang bent dat het bijhouden van uw certificaat verloopt, of erger dat uw certificaat al is verlopen, Vervolgens kunt Key Vault u helpen om up-to-date te blijven. Voor een niet-geïntegreerd CA-certificaat kan de gebruiker met sleutel kluis bijna verlopen e-mail meldingen instellen. Deze meldingen kunnen ook worden ingesteld voor meerdere gebruikers.

Als u nu een certificaat wilt vernieuwen, is het belang rijk om te begrijpen dat een Azure Key Vault certificaat een object met versie is. Als de huidige versie verloopt, moet u een nieuwe versie maken. In het algemeen wordt elke nieuwe versie samen met een nieuw certificaat, samengesteld uit sleutel en een blob, waarmee die sleutel wordt gekoppeld aan een identiteit. Wanneer u een niet-gelieerde certificerings instantie gebruikt, genereert sleutel kluis een sleutel waarde-paar en wordt de CSR geretourneerd.

**Stappen om te volgen in Azure Portal:-**
1.  Open het certificaat dat u wilt verlengen.
2.  Selecteer de knop **+ nieuwe versie** op het scherm certificaat.
3.  **Certificaat bewerking** selecteren
4.  Selecteer **CSR downloaden**. Hiermee wordt een CSR-bestand gedownload op uw lokale station.
5.  De CSR naar keuze van de certificerings instantie voor het ondertekenen van de aanvraag meenemen
6.  Breng de ondertekende aanvraag terug en selecteer **CSR samen voegen** op hetzelfde certificaat bewerkings scherm.

> [!NOTE]
> Het is belang rijk om de ondertekende CSR samen te voegen met dezelfde CSR-aanvraag die is gemaakt, anders zou de sleutel niet overeenkomen.

De stappen zijn vergelijkbaar met het maken van een nieuw certificaat en [worden meer beschreven in informatie.]( https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-signing-request#azure-portal)

## <a name="renewal-of-self-signed-certificate"></a>Het zelfondertekend certificaat vernieuwen

Goed nieuws. Azure-sleutel kluizen zorgen er ook voor dat zelfondertekende certificaten automatisch worden vernieuwd voor de gebruikers. Meer informatie over het wijzigen van het uitgifte beleid en het bijwerken van de levensduur actie kenmerken van het certificaat [vindt u hier](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-rotate-certificates#update-lifecycle-attributes-of-a-stored-certificate).

### <a name="troubleshoot"></a>Problemen oplossen
Als het certificaat dat is uitgegeven de status uitgeschakeld heeft in de Azure Portal, gaat u door met het weer geven van de certificaat bewerking om het fout bericht voor dat certificaat weer te geven.

### <a name="frequently-asked-questions"></a>Veelgestelde vragen
* Hoe kan ik de functie voor het door draaien van het certificaat testen?
  Maak een certificaat met een geldigheids duur van 1 maand en stel vervolgens de actie voor de levens duur voor rotatie in op 1%. Met deze instelling wordt het certificaat binnen 7,2 uur geroteerd.
  
* Worden de labels gerepliceerd nadat het certificaat automatisch is vernieuwd?
  Ja, de tags worden gerepliceerd na het automatisch verlengen.

### <a name="see-also"></a>Zie ook
*   [Key Vault integreren met DigiCert-certificeringsinstantie](how-to-integrate-certificate-authority.md)
*   [Zelfstudie: Automatische rotatie van certificaten in Key Vault configureren](tutorial-rotate-certificates.md)

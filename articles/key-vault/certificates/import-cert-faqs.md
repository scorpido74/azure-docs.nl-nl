---
title: Veelgestelde vragen-Azure Key Vault certificaat importeren
description: Veelgestelde vragen-Azure Key Vault certificaat importeren
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: 1063f7189de4bdf1aaca4a6d72c979476433c32f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87097005"
---
# <a name="frequently-asked-questions---azure-key-vault-certificate-import"></a>Veelgestelde vragen-Azure Key Vault certificaat importeren

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="how-can-i-import-a-certificate-in-azure-key-vault"></a>Hoe kan ik een certificaat in Azure Key Vault importeren?

Certificaat importeren: voor de import bewerking accepteert Azure Key-kluis twee certificaat indelingen PEM en PFX. Hoewel er PEM-bestanden zijn met alleen het open bare deel, vereist Azure Key-kluis een PEM of PFX in bestandsmap en met een persoonlijke sleutel. Volg de [zelf studie voor het importeren van het certificaat](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-import-certificate#import-a-certificate-to-key-vault)

### <a name="after-importing-password-protected-certificate-into-the-key-vault-and-then-downloading-it-i-am-not-able-to-see-the-password-associated-with-the-certificate"></a>Ik kan het wacht woord dat is gekoppeld aan het certificaat niet zien na het importeren van een met een wacht woord beveiligd certificaat in de sleutel kluis en het downloaden ervan?
    
Het geüploade beveiligde certificaat na opslag in de sleutel kluis slaat geen wacht woord op dat eraan is gekoppeld. Het wachtwoord is slechts één keer vereist tijdens de importbewerking. Hoewel dit een ontwerp concept is, kunt u het certificaat altijd als geheim ophalen en converteren van base64 naar PFX het vorige wacht woord toevoegen via [Azure PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx).

### <a name="how-can-i-resolve-bad-parameter-error-what-are-the-supported-certificate-formats-for-importing-in-key-vault"></a>Hoe kan ik ' fout met ongeldige para meter ' oplossen? Wat zijn de ondersteunde certificaatindelingen voor het importeren in de sleutelkluis?

Wanneer u het certificaat importeert, moet u ervoor zorgen dat de sleutel is opgenomen in het bestand zelf. Als u de persoonlijke sleutel afzonderlijk in een andere indeling hebt, moet u de sleutel combi neren met het certificaat. Sommige certificerings instanties bieden certificaten in verschillende indelingen, dus voordat u het certificaat importeert, moet u ervoor zorgen dat ze de indeling. pem of. pfx hebben en dat de gebruikte sleutel RSA of ECC is. Raadpleeg deze voor het controleren van de [certificaat vereisten](https://docs.microsoft.com/azure/key-vault/certificates/certificate-scenarios#formats-of-import-we-support) en [vereisten voor certificaat sleutels](https://docs.microsoft.com/azure/key-vault/keys/about-keys#cryptographic-protection).

### <a name="error-when-importing-certificate-via-portal-something-went-wrong-how-can-i-investigate-further"></a>Fout bij het importeren van het certificaat via de portal: 'Er is iets fout gegaan'. Hoe kan ik verder onderzoeken?
    
Als u meer beschrijvende fout wilt weer geven, importeert u het certificaat bestand via [Azure cli](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-import) of [Power shell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate?view=azurermps-6.13.0).

### <a name="how-can-i-resolve-error-type-access-denied-or-user-is-unauthorized-to-import-certificate"></a>Hoe kan ik ' fout type ' oplossen: de toegang is geweigerd of de gebruiker is niet gemachtigd om het certificaat te importeren '?
    
Voor deze bewerking is de machtiging certificaten/importeren vereist. Ga naar de locatie waar de sleutelkluis zich bevindt. U moet de gebruiker de juiste machtigingen geven onder toegangsbeleid. Ga naar het Key Vault> toegangs beleid > toegangs beleid toevoegen > Selecteer certificaat machtigingen (of als u de machtigingen wilt) > Principal > zoeken naar en klik vervolgens op e-mail adres van gebruiker toevoegen. [Meer informatie over certificaat-gerelateerde toegangs beleid](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#certificate-access-control)


### <a name="how-can-i-resolve-error-type-conflict-when-creating-a-certificate"></a>Hoe kan ik ' fout type: conflict bij het maken van een certificaat ' oplossen?
    
Certificaatnaam moet uniek zijn. Een certificaat met dezelfde naam kan in de modus voorlopig verwijderd zijn, ook als de [samen stelling van een certificaat](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#composition-of-a-certificate) in azure Key kluis, als er een andere sleutel of een geheim is in de Key Vault met dezelfde naam die u voor uw certificaat probeert op te geven, mislukt dit en moet u die sleutel of het geheim verwijderen of een andere naam voor het certificaat gebruiken. [verwijderd certificaat weer geven](https://docs.microsoft.com/rest/api/keyvault/getdeletedcertificate/getdeletedcertificate)

### <a name="why-am-i-getting-the-error-type-char-length-is-too-long"></a>Waarom krijg ik het fout type: de lengte van tekens is te lang?
    
* De naam van het certificaat onderwerp heeft een maximum lengte van 200 tekens
* Lengte van het certificaat wachtwoord heeft een teken limiet van 200 tekens

### <a name="can-i-import-an-expired-certificate-in-azure-key-vault"></a>Kan ik een verlopen certificaat in azure Key kluis importeren?
    
Nee, de verlopen PFX-certificaten worden niet geïmporteerd in Azure Key Vault.

### <a name="how-can-i-convert-my-certificate-to-proper-format"></a>Hoe kan ik mijn certificaat converteren naar de juiste indeling?

U kunt uw certificerings instantie vragen om het certificaat in de vereiste indeling te geven, maar er zijn ook hulpprogram ma's van derden waarmee u kunt converteren naar de juiste indeling, maar micro soft kan niet meer inlichten over het verkrijgen van het certificaat in de gewenste indeling.

### <a name="can-i-import-certificates-from-non-partner-cas"></a>Kan ik certificaten van niet-partner-Ca's importeren?
Ja, u kunt certificaten van een wille keurige CA importeren, maar de sleutel kluis kan deze certificaten niet automatisch vernieuwen. U kunt e-mail meldingen instellen om op de hoogte te worden gesteld van het verlopen van het certificaat.

### <a name="if-i-import-a-certificate-from-a-partner-ca-will-the-auto-renew-feature-still-work"></a>Als ik een certificaat Importeer van een partner-CA, werkt de functie automatisch verlengen dan toch?
Ja, u moet er zeker van zijn dat u na het uploaden de autorotation in het uitgifte beleid van het certificaat opgeeft. De wijzigingen worden ook weer gegeven tot de volgende cyclus of certificaat versie.


## <a name="next-steps"></a>Volgende stappen

- [Azure Key Vault certificaten](/azure/key-vault/certificates/about-certificates)

---
title: Aan de slag met Key Vault-certificaten
description: De volgende scenario's schetsen verschillende van de primaire gebruiks mogelijkheden van de certificaat beheer service van Key Vault, met inbegrip van de extra stappen voor het maken van uw eerste certificaat in uw sleutel kluis.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 32a453678fe3702fcb4b77f0b04a8ed5c889ef59
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79271003"
---
# <a name="get-started-with-key-vault-certificates"></a>Aan de slag met Key Vault-certificaten
De volgende scenario's schetsen verschillende van de primaire gebruiks mogelijkheden van de certificaat beheer service van Key Vault, met inbegrip van de extra stappen voor het maken van uw eerste certificaat in uw sleutel kluis.

Hieronder vindt u een overzicht:
- Uw eerste Key Vault certificaat maken
- Een certificaat maken met een certificerings instantie die is gekoppeld aan Key Vault
- Een certificaat maken met een certificerings instantie die niet is gekoppeld aan Key Vault
- Een certificaat importeren

## <a name="certificates-are-complex-objects"></a>Certificaten zijn complexe objecten
Certificaten bestaan uit drie gerelateerde resources die aan elkaar zijn gekoppeld als een Key Vault certificaat; meta gegevens van het certificaat, een sleutel en een geheim.


![Certificaten zijn complex](media/azure-key-vault.png)


## <a name="creating-your-first-key-vault-certificate"></a>Uw eerste Key Vault certificaat maken  
 Voordat een certificaat kan worden gemaakt in een Key Vault (KV), moeten de vereiste stappen 1 en 2 worden uitgevoerd en moet er een sleutel kluis bestaan voor deze gebruiker/organisatie.  

**Stap 1** -providers van certificerings instanties  
-   Als IT-beheerder, PKI-beheerder of iedereen die accounts beheert met Ca's, voor een bepaald bedrijf (bijvoorbeeld Contoso) is een vereiste voor het gebruik van Key Vault-certificaten.  
    De volgende certificerings instanties zijn de huidige partner providers met Key Vault:  
    -   DigiCert-Key Vault biedt OV TLS/SSL-certificaten met DigiCert.  
    -   GlobalSign-Key Vault biedt OV TLS/SSL-certificaten met GlobalSign.  

**Stap 2** : een account beheerder voor een CA-provider maakt referenties die moeten worden gebruikt door Key Vault om TLS/SSL-certificaten via Key Vault in te schrijven, te verlengen en te gebruiken.

**Stap 3** : een Contoso-beheerder, samen met een contoso-werk nemer (Key Vault gebruiker) die eigenaar is van certificaten, kan, afhankelijk van de CA, een certificaat van de beheerder verkrijgen of rechtstreeks vanuit het account bij de ca.  

- Begin met het toevoegen van een referentie bewerking aan een sleutel kluis door een bron van [een certificaat verlener](/rest/api/keyvault/setcertificateissuer/setcertificateissuer) in te stellen. Een certificaat Uitgever is een entiteit die wordt weer gegeven in Azure Key Vault (KV) als een CertificateIssuer-resource. Het wordt gebruikt om informatie op te geven over de bron van een KV-certificaat; naam van de verlener, provider, referenties en andere administratieve gegevens.
  - Bijvoorbeeld MyDigiCertIssuer  
    -   Provider  
    -   Referenties: referenties van het CA-account. Elke CA heeft zijn eigen specifieke gegevens.  

    Zie voor meer informatie over het maken van accounts met CA-providers het gerelateerde bericht op het [Key Vault blog](https://aka.ms/kvcertsblog).  

**Stap 3,1** : Stel [certificaat contactpersonen](/rest/api/keyvault/setcertificatecontacts/setcertificatecontacts) in voor meldingen. Dit is de contact persoon voor de Key Vault gebruiker. Key Vault dwingt deze stap niet af.  

Opmerking: dit proces, tot en met stap 3,1, is een eenmalige-bewerking.  

## <a name="creating-a-certificate-with-a-ca-partnered-with-key-vault"></a>Een certificaat maken met een certificerings instantie die is gekoppeld aan Key Vault

![Een certificaat maken met een certificerings instantie voor een Key Vault partner](media/certificate-authority-2.png)

**Stap 4** : de volgende beschrijvingen komen overeen met de stappen voor groen genummerd in het voor gaande diagram.  
  (1): in het bovenstaande diagram maakt uw toepassing een certificaat dat intern begint door een sleutel te maken in uw sleutel kluis.  
  (2)-Key Vault een TLS/SSL-certificaat aanvraag naar de CA verzendt.  
  (3): uw toepassings polls, in een lus en wacht proces, voor uw Key Vault voor het volt ooien van het certificaat. Het maken van het certificaat is voltooid wanneer Key Vault de reactie van de certificerings instantie met x509-certificaat ontvangt.  
  (4): de CA reageert op de TLS/SSL-certificaat aanvraag van Key Vault met een x509 TLS/SSL-certificaat.  
  (5): het maken van het nieuwe certificaat is voltooid met de fusie van het x509-certificaat voor de CA.  

  Key Vault gebruiker: maakt een certificaat door een beleid op te geven

  -   Herhaal indien nodig  
  -   Beleids beperkingen  
      -   X509-eigenschappen  
      -   Sleuteleigenschappen  
      -   Referentie van de provider-> ex. MyDigiCertIssure  
      -   Informatie over verlenging-> ex. 90 dagen voor de verval datum  

  - Een proces voor het maken van een certificaat is doorgaans een asynchroon proces en omvat het pollen van uw sleutel kluis voor de status van de bewerking voor het maken van een certificaat.  
[Certificaat bewerking ophalen](/rest/api/keyvault/getcertificateoperation/getcertificateoperation)  
      -   Status: voltooid, mislukt met fout informatie of geannuleerd  
      -   Als gevolg van de vertraging om te maken, kan een annulerings bewerking worden gestart. Het annuleren kan al dan niet effectief zijn.  

## <a name="import-a-certificate"></a>Een certificaat importeren  
 Als alternatief: een certificaat kan worden geïmporteerd in Key Vault-PFX of PEM.  

 Zie de sectie certificaten van [over sleutels, geheimen en certificaten](about-keys-secrets-and-certificates.md)voor meer informatie over de PEM-indeling.  

 Certificaat importeren: vereist dat een PEM of PFX op schijf is en een persoonlijke sleutel heeft. 
-   U moet het volgende opgeven: kluis naam en certificaat naam (beleid is optioneel)

-   PEM/PFX-bestanden bevatten kenmerken die KV kan parseren en gebruiken om het certificaat beleid in te vullen. Als er al een certificaat beleid is opgegeven, probeert KV gegevens uit PFX/PEM-bestand te vergelijken.  

-   Zodra het importeren is voltooid, wordt het nieuwe beleid (nieuwe versies) gebruikt voor volgende bewerkingen.  

-   Als er geen verdere bewerkingen zijn, wordt het eerste wat de Key Vault doet, een verloop bericht verzonden. 

-   De gebruiker kan ook het beleid bewerken, wat functioneel is op het moment van importeren, maar bevat standaard instellingen waar geen informatie is opgegeven bij het importeren. Bijvoorbeeld geen uitgevers gegevens  

### <a name="formats-of-import-we-support"></a>Indelingen van het importeren die we ondersteunen
Het volgende type import voor de PEM-bestands indeling wordt ondersteund. Eén PEM-gecodeerd certificaat, samen met een PKCS # 8-gecodeerde, niet-versleutelde sleutel met de volgende

-----BEGIN CERTIFICAAT----------EIND CERTIFICAAT-----

-----BEGIN PERSOONLIJKE SLEUTEL----------LAATSTE PERSOONLIJKE SLEUTEL-----

Bij het samen voegen van certificaten ondersteunen we 2 PEM-gebaseerde notaties. U kunt één PKCS # 8-gecodeerd certificaat of een Base64 Encoded P7B-bestand samen voegen. -----BEGIN CERTIFICAAT----------EIND CERTIFICAAT-----

We ondersteunen momenteel geen EC-sleutels in de PEM-indeling.

## <a name="creating-a-certificate-with-a-ca-not-partnered-with-key-vault"></a>Maken van een certificaat met een certificerings instantie die niet is gekoppeld aan Key Vault  
 Met deze methode kunt u werken met andere certificerings instanties dan partners van de partner van Key Vault, wat betekent dat uw organisatie kan werken met een CA van de keuze.  

![Een certificaat maken met uw eigen certificerings instantie](media/certificate-authority-1.png)  

 De volgende stap beschrijvingen komen overeen met de groene letterlijke stappen in het voor gaande diagram.  

  (1): in het bovenstaande diagram maakt uw toepassing een certificaat dat intern begint met het maken van een sleutel in uw sleutel kluis.  

  (2)-Key Vault keert terug naar uw toepassing een aanvraag voor certificaat ondertekening (CSR).  

  (3): uw toepassing stuurt de CSR door naar de gekozen certificerings instantie.  

  (4): de gekozen certificerings instantie reageert met een x509-certificaat.  

  (5): uw toepassing heeft het maken van het nieuwe certificaat voltooid met een fusie van het x509-certificaat van uw certificerings instantie.

## <a name="see-also"></a>Zie ook

- [Informatie over sleutels, geheimen en certificaten](about-keys-secrets-and-certificates.md)

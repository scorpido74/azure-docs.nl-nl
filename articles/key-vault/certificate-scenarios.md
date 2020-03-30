---
title: Aan de slag met Key Vault-certificaten
description: In de volgende scenario's worden een aantal van de primaire gebruikstoepassingen van de certificaatbeheerservice van Key Vault beschreven, inclusief de extra stappen die nodig zijn voor het maken van uw eerste certificaat in uw sleutelkluis.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271003"
---
# <a name="get-started-with-key-vault-certificates"></a>Aan de slag met Key Vault-certificaten
In de volgende scenario's worden een aantal van de primaire gebruikstoepassingen van de certificaatbeheerservice van Key Vault beschreven, inclusief de extra stappen die nodig zijn voor het maken van uw eerste certificaat in uw sleutelkluis.

Hierna wordt het volgende beschreven:
- Uw eerste Key Vault-certificaat maken
- Een certificaat maken met een certificeringsinstantie die samenwerkt met Key Vault
- Een certificaat maken met een certificeringsinstantie die niet samenwerkt met Key Vault
- Een certificaat importeren

## <a name="certificates-are-complex-objects"></a>Certificaten zijn complexe objecten
Certificaten bestaan uit drie onderling verbonden bronnen die als Key Vault-certificaat zijn gekoppeld; certificaatmetagegevens, een sleutel en een geheim.


![Certificaten zijn complex](media/azure-key-vault.png)


## <a name="creating-your-first-key-vault-certificate"></a>Uw eerste Key Vault-certificaat maken  
 Voordat een certificaat kan worden gemaakt in een Key Vault (KV), moeten de vereiste stappen 1 en 2 met succes worden uitgevoerd en moet er een sleutelkluis bestaan voor deze gebruiker/organisatie.  

**Stap 1** - Certificaatautoriteit (CA) Providers  
-   On-boarding als de IT-beheerder, PKI-beheerder of iedereen die accounts beheert met CA's, voor een bepaald bedrijf (bijv. Contoso) is een voorwaarde voor het gebruik van Key Vault-certificaten.  
    De volgende CV's zijn de huidige partnerproviders met Key Vault:  
    -   DigiCert - Key Vault biedt OV TLS/SSL-certificaten met DigiCert.  
    -   GlobalSign - Key Vault biedt OV TLS/SSL-certificaten met GlobalSign.  

**Stap 2** - Een accountbeheerder voor een CA-provider maakt referenties die door Key Vault kunnen worden gebruikt om TLS/SSL-certificaten in te schrijven, te vernieuwen en te gebruiken via Key Vault.

**Stap 3** - Een Contoso-beheerder kan, samen met een Medewerker van Contoso (Key Vault-gebruiker) die eigenaar is van certificaten, afhankelijk van de CA, een certificaat van de beheerder of rechtstreeks van het account met de CA krijgen.  

- Begin een bewerking van een referentie toevoegen aan een sleutelkluis door [een bron van certificaatuitgever in](/rest/api/keyvault/setcertificateissuer/setcertificateissuer) te stellen. Een certificaatuitgever is een entiteit die in Azure Key Vault (KV) wordt vertegenwoordigd als een Bron van CertificateIssuer. Het wordt gebruikt om informatie te verstrekken over de bron van een KV-certificaat; naam van de uitgever, provider, referenties en andere administratieve details.
  - Bijvoorbeeld MyDigiCertIssuer  
    -   Provider  
    -   Referenties – CA-accountreferenties. Elke CA heeft zijn eigen specifieke gegevens.  

    Zie het gerelateerde bericht op het [Key Vault-blog](https://aka.ms/kvcertsblog)voor meer informatie over het maken van accounts met CA-providers.  

**Stap 3.1** - [Certificaatcontactpersonen](/rest/api/keyvault/setcertificatecontacts/setcertificatecontacts) instellen voor meldingen. Dit is de contactpersoon voor de Key Vault-gebruiker. Key Vault handhaaft deze stap niet.  

Opmerking - Dit proces, door middel van stap 3.1, is een eenmalige bewerking.  

## <a name="creating-a-certificate-with-a-ca-partnered-with-key-vault"></a>Een certificaat maken met een CA die samenwerkt met Key Vault

![Een certificaat maken met een key vault-partnercertificaatautoriteit](media/certificate-authority-2.png)

**Stap 4** - De volgende beschrijvingen komen overeen met de groene genummerde stappen in het voorgaande diagram.  
  (1) - In het bovenstaande diagram maakt uw toepassing een certificaat dat intern begint met het maken van een sleutel in uw sleutelkluis.  
  (2) - Key Vault stuurt een TLS/SSL-certificaataanvraag naar de CA.  
  (3) - Uw aanvraag polls, in een lus en wacht proces, voor uw Key Vault voor het certificaat voltooiing. Het maken van het certificaat is voltooid wanneer Key Vault de reactie van de certificeringsinstantie met x509-certificaat ontvangt.  
  (4) - De CA reageert op de TLS/SSL-certificaataanvraag van Key Vault met een X509 TLS/SSL-certificaat.  
  (5) - Uw nieuwe certificaat creatie wordt voltooid met de fusie van de X509 Certificaat voor de CA.  

  Key Vault-gebruiker – maakt een certificaat door een beleid op te geven

  -   Herhaal zo nodig  
  -   Beleidsbeperkingen  
      -   X509-eigenschappen  
      -   Belangrijkste eigenschappen  
      -   Provider referentie - > ex. MyDigiCertIssure  
      -   Vernieuwingsinformatie - > ex. 90 dagen voor het verstrijken  

  - Een proces voor het maken van certificaten is meestal een asynchrone proces en omvat het peilen van uw sleutelkluis voor de status van de bewerking van het certificaat maken.  
[Certificaatbewerking ontvangen](/rest/api/keyvault/getcertificateoperation/getcertificateoperation)  
      -   Status: voltooid, mislukt met foutgegevens of geannuleerd  
      -   Vanwege de vertraging die moet worden gemaakt, kan een annuleringsbewerking worden gestart. Het annuleren kan wel of niet effectief zijn.  

## <a name="import-a-certificate"></a>Een certificaat importeren  
 Als alternatief – een cert kan worden geïmporteerd in Key Vault – PFX of PEM.  

 Zie de sectie certificaten over [sleutels, geheimen en certificaten voor](about-keys-secrets-and-certificates.md)meer informatie over de PEM-indeling.  

 Importcertificaat – vereist dat een PEM of PFX op schijf staat en een privésleutel heeft. 
-   U moet opgeven: kluisnaam en certificaatnaam (beleid is optioneel)

-   PEM/ PFX-bestanden bevatten kenmerken die KV kan ontleden en gebruiken om het certificaatbeleid te vullen. Als er al een certificaatbeleid is opgegeven, probeert KV gegevens uit PFX/ PEM-bestand te matchen.  

-   Zodra de import definitief is, worden de volgende bewerkingen gebruikt het nieuwe beleid (nieuwe versies).  

-   Als er geen verdere bewerkingen zijn, is het eerste wat de Key Vault doet een vervaldatum sturen. 

-   Ook kan de gebruiker het beleid bewerken, dat functioneel is op het moment van importeren, maar standaardinstellingen bevat waar geen informatie is opgegeven bij import. Bijvoorbeeld geen informatie over de uitgever  

### <a name="formats-of-import-we-support"></a>Indelingen van Import die we ondersteunen
We ondersteunen het volgende type import voor PEM-bestandsindeling. Een enkel PEM gecodeerd certificaat samen met een PKCS # 8 gecodeerde, ongecodeerde sleutel die de volgende heeft

-----BEGINCERTIFICAAT----- -----ENDCERTIFICAAT-----

-----BEGIN PRIVÉSLEUTEL----- -----END-privésleutel-----

Bij certificaatsamenvoeging ondersteunen we 2 PEM-gebaseerde formaten. U één PKCS#8 gecodeerd certificaat of een base64 gecodeerd P7B-bestand samenvoegen. -----BEGINCERTIFICAAT----- -----ENDCERTIFICAAT-----

We ondersteunen momenteel geen EG-sleutels in PEM-formaat.

## <a name="creating-a-certificate-with-a-ca-not-partnered-with-key-vault"></a>Een certificaat maken met een CA die niet samenwerkt met Key Vault  
 Deze methode maakt het mogelijk om te werken met andere CA's dan de partnerproviders van Key Vault, wat betekent dat uw organisatie kan werken met een CA naar keuze.  

![Een certificaat maken met uw eigen certificaatautoriteit](media/certificate-authority-1.png)  

 De volgende stapbeschrijvingen komen overeen met de groene letters in het voorgaande diagram.  

  (1) - In het bovenstaande diagram maakt uw toepassing een certificaat, dat intern begint met het maken van een sleutel in uw sleutelkluis.  

  (2) - Key Vault retourneert een Certificaatondertekeningsaanvraag (CSR) naar uw aanvraag.  

  (3) - Uw aanvraag geeft de CSR door aan de door u gekozen CA.  

  (4) - Uw gekozen CA reageert met een X509-certificaat.  

  (5) - Uw aanvraag voltooit het nieuwe certificaat creatie met een fusie van het X509 Certificaat van uw CA.

## <a name="see-also"></a>Zie ook

- [Informatie over sleutels, geheimen en certificaten](about-keys-secrets-and-certificates.md)

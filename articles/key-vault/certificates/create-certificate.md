---
title: Methoden voor het maken van certificaten
description: Manieren om een certificaat te maken in Key Vault.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 7450dd79247078afe02d1bb63727cfd260d674fc
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81866260"
---
# <a name="certificate-creation-methods"></a>Methoden voor het maken van certificaten

 Een KV-certificaat (Key Vault) kan worden gemaakt of geïmporteerd in een sleutelkluis. Wanneer een KV-certificaat wordt gemaakt, wordt de privésleutel gemaakt in de sleutelkluis en nooit blootgesteld aan de eigenaar van het certificaat. Hieronder volgen manieren om een certificaat te maken in Key Vault:  

-   **Een zelfondertekend certificaat maken:** Hierdoor wordt een publiek-private sleutelpaar gemaakt en wordt het gekoppeld aan een certificaat. Het certificaat wordt ondertekend door zijn eigen sleutel.  

-    **Handmatig een nieuw certificaat maken:** Hierdoor wordt een publiek-private sleutelpaar gemaakt en wordt een x.509-certificaatondertekeningsaanvraag gegenereerd. De ondertekeningsaanvraag kan worden ondertekend door uw registratieautoriteit of certificeringsinstantie. Het ondertekende x509-certificaat kan worden samengevoegd met het in behandeling zijnde sleutelpaar om het KV-certificaat in Key Vault te voltooien. Hoewel deze methode meer stappen vereist, biedt het u meer beveiliging omdat de privésleutel is gemaakt en beperkt tot Key Vault. Dit wordt uitgelegd in het onderstaande diagram.  

![Een certificaat maken met uw eigen certificaatautoriteit](../media/certificate-authority-1.png)  

De volgende beschrijvingen komen overeen met de groene letters in het voorgaande diagram.

1. In het bovenstaande diagram maakt uw toepassing een certificaat dat intern begint met het maken van een sleutel in uw sleutelkluis.
2. Key Vault retourneert een Certificaatondertekeningsaanvraag (CSR) naar uw aanvraag
3. Uw toepassing stuurt de CSR door naar de gekozen certificeringsinstantie.
4. De door u gekozen CA reageert met een X509-certificaat.
5. Uw aanvraag voltooit de nieuwe certificaatcreatie met een fusie van het X509-certificaat van uw CA.

-   **Maak een certificaat met een bekende uitgeverprovider:** Voor deze methode moet u een eenmalige taak uitvoeren bij het maken van een object voor een uitgever. Zodra een emittentobject in de sleutelkluis in u is gemaakt, kan de naam ervan worden vermeld in het beleid van het KV-certificaat. Een verzoek om een dergelijk KV-certificaat te maken, maakt een sleutelpaar in de kluis en communiceert met de service van de uitgever met behulp van de informatie in het object waarnaar wordt verwezen om een x509-certificaat te krijgen. Het x509-certificaat wordt opgehaald uit de emittentservice en wordt samengevoegd met het sleutelpaar om het maken van het KV-certificaat te voltooien.  

![Een certificaat maken met een key vault-partnercertificaatautoriteit](../media/certificate-authority-2.png)  

De volgende beschrijvingen komen overeen met de groene letters in het voorgaande diagram.

1. In het bovenstaande diagram maakt uw toepassing een certificaat dat intern begint met het maken van een sleutel in uw sleutelkluis.
2. Key Vault stuurt een TLS/SSL-certificaataanvraag naar de CA.
3. Uw toepassing bevraagt, in een proces van lussen en wachten, uw Key Vault voor het voltooien van het certificaat. Het maken van het certificaat is voltooid wanneer Key Vault de reactie van de certificeringsinstantie met x509-certificaat ontvangt.
4. De CA reageert op de TLS/SSL-certificaataanvraag van Key Vault met een TLS/SSL X.509-certificaat.
5. Het maken van uw nieuwe certificaat wordt voltooid met de fusie van het TLS/SSL X.509-certificaat voor de CA.

## <a name="asynchronous-process"></a>Asynchrone proces
Het maken van KV-certificaten is een asynchrone procedure. Met deze bewerking wordt een KV-certificaataanvraag gemaakt en wordt een http-statuscode van 202 (Geaccepteerd) teruggekeerd. De status van de aanvraag kan worden bijgehouden door het object in behandeling te peilen dat door deze bewerking is gemaakt. De volledige URI van het in behandeling zijnde object wordt geretourneerd in de koptekst LOCATIE.  

Wanneer een aanvraag voor het maken van een KV-certificaat is voltooid, wordt de status van het in behandeling zijnde object gewijzigd in 'voltooid' van 'inprogress' en wordt een nieuwe versie van het KV-certificaat gemaakt. Dit wordt de huidige versie.  

## <a name="first-creation"></a>Eerste creatie
 Wanneer voor het eerst een KV-certificaat wordt gemaakt, wordt ook een adresseerbare sleutel en geheim gemaakt met dezelfde naam als die van het certificaat. Als de naam al in gebruik is, mislukt de bewerking met een http-statuscode van 409 (conflict).
De adresseerbare sleutel en het geheim halen hun kenmerken uit de KV-certificaatkenmerken. De adresseerbare sleutel en het geheim die op deze manier zijn gemaakt, worden gemarkeerd als beheerde sleutels en geheimen, waarvan de levensduur wordt beheerd door Key Vault. Beheerde sleutels en geheimen zijn alleen-lezen. Opmerking: Als een KV-certificaat verloopt of is uitgeschakeld, worden de bijbehorende sleutel en het bijbehorende geheim onbruikbaar.  

 Als dit de eerste bewerking is die een KV-certificaat maakt, is een beleid vereist.  Een beleid kan ook worden geleverd met opeenvolgende bewerkingen maken ter vervanging van de beleidsbron. Als een beleid niet wordt geleverd, wordt de beleidsbron voor de service gebruikt om een volgende versie van het KV-certificaat te maken. Houd er rekening mee dat terwijl een aanvraag om een volgende versie te maken wordt uitgevoerd, het huidige KV-certificaat en de bijbehorende adresseerbare sleutel en geheim ongewijzigd blijven.  

## <a name="self-issued-certificate"></a>Zelf uitgegeven certificaat
 Als u een zelfuitgegeven certificaat wilt maken, stelt u de naam van de uitgever in als 'Zelf' in het certificaatbeleid zoals weergegeven in het volgende fragment van het certificaatbeleid.  

```  
"issuer": {  
       "name": "Self"  
    }  

```  

 Als de naam van de uitgever niet is opgegeven, wordt de naam van de uitgever ingesteld op 'Onbekend'. Wanneer de uitgever "Onbekend" is, moet de certificaateigenaar handmatig een x509-certificaat van de uitgever van zijn/haar keuze krijgen en vervolgens het openbare x509-certificaat samenvoegen met het sleutelkluiscertificaat in behandeling om het certificaat te maken.

```  
"issuer": {  
       "name": "Unknown"  
    }  

```  

## <a name="partnered-ca-providers"></a>Samenwerkende CA-providers
Het maken van certificaten kan handmatig worden voltooid of met behulp van een "Self"-uitgever. Key Vault werkt ook samen met bepaalde emittentproviders om het maken van certificaten te vereenvoudigen. De volgende soorten certificaten kunnen worden besteld voor sleutelkluis bij deze aanbieders van partneruitgevers.  

|Provider|Certificaattype|Configuratie-instelling  
|--------------|----------------------|------------------|  
|DigiCert|Key Vault biedt OV- of EV SSL-certificaten met DigiCert| [Integratiegids](https://docs.digicert.com/certificate-tools/azure-key-vault-integration-guide/)
|GlobalSign|Key Vault biedt OV- of EV SSL-certificaten met GlobalSign| [Integratiegids](https://support.globalsign.com/digital-certificates/digital-certificate-installation/generating-and-importing-certificate-microsoft-azure-key-vault)

 Een certificaatuitgever is een entiteit die in Azure Key Vault (KV) wordt vertegenwoordigd als een Bron van CertificateIssuer. Het wordt gebruikt om informatie te verstrekken over de bron van een KV-certificaat; naam van de uitgever, provider, referenties en andere administratieve details.

Houd er rekening mee dat wanneer een bestelling wordt geplaatst bij de leverancier van de uitgever, de x509-certificaatextensies en de geldigheidsperiode van het certificaat kan eerof overschrijven op basis van het type certificaat.  

 Autorisatie: Vereist de certificaten/maak toestemming.

## <a name="see-also"></a>Zie ook

 - [Het maken van certificaten controleren en beheren](create-certificate-scenarios.md)

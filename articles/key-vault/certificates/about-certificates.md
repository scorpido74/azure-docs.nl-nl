---
title: Over Azure Key Vault certificaten-Azure Key Vault
description: Overzicht van Azure Key Vault REST interface en certificaten.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 5e014634ecb251f05710de16daee30d72dae619e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81685896"
---
# <a name="about-azure-key-vault-certificates"></a>Over Azure Key Vault certificaten

Key Vault ondersteuning voor certificaten voorziet in het beheer van uw x509-certificaten en het volgende gedrag:  

-   Hiermee kan een certificaat eigenaar een certificaat maken met behulp van een Key Vault aanmaak proces of door middel van het importeren van een bestaand certificaat. Bevat zowel zelf-ondertekende als door de certificerings instantie gegenereerde certificaten.
-   Hiermee kan een Key Vault eigenaar van het certificaat beveiligde opslag en beheer van x509-certificaten implementeren zonder interactie met persoonlijke-sleutel materiaal.  
-   Hiermee kan een certificaat eigenaar een beleid maken dat Key Vault doorstuurt om de levens cyclus van een certificaat te beheren.  
-   Staat eigen aren van certificaten toe om contact gegevens op te geven voor meldingen over levenscyclus gebeurtenissen van de verval datum en verlenging van het certificaat.  
-   Ondersteunt automatische verlenging met geselecteerde verleners-Key Vault partner x509-certificaat providers/certificerings instanties.

>[!Note]
>Providers/instanties zonder partner zijn ook toegestaan, maar bieden geen ondersteuning voor de functie voor automatisch verlengen.

## <a name="composition-of-a-certificate"></a>Samen stelling van een certificaat

Wanneer er een Key Vault certificaat wordt gemaakt, worden er ook een adresseer bare sleutel en een geheim gemaakt met dezelfde naam. Met de Key Vault sleutel kunnen sleutel bewerkingen en het Key Vault geheim ophalen van de certificaat waarde als geheim toestaan. Een Key Vault certificaat bevat ook open bare meta gegevens voor x509-certificaten.  

De id en de versie van certificaten zijn vergelijkbaar met die van sleutels en geheimen. Een specifieke versie van een adresseer bare sleutel en geheim dat is gemaakt met de Key Vault certificaat versie is beschikbaar in het Key Vault certificaat antwoord.
 
![Certificaten zijn complexe objecten](../media/azure-key-vault.png)

## <a name="exportable-or-non-exportable-key"></a>Exporteer bare of niet-Exporteer bare sleutel

Wanneer een Key Vault certificaat wordt gemaakt, kan het worden opgehaald uit het adresseer bare geheim met de persoonlijke sleutel in de PFX-of PEM-indeling. Het beleid dat wordt gebruikt voor het maken van het certificaat moet aangeven dat de sleutel exporteerbaar is. Als het beleid aangeeft dat het niet kan worden geëxporteerd, is de persoonlijke sleutel geen onderdeel van de waarde wanneer deze wordt opgehaald als geheim.  

De adresseer bare sleutel wordt relevanter voor niet-Exporteer bare KV-certificaten. De adresseer bare KV-sleutel bewerkingen zijn toegewezen in het veld sleutel *gebruik* van het KV-certificaat beleid dat wordt gebruikt om het KV-certificaat te maken.  

Er worden twee typen sleutels ondersteund: *RSA* of *RSA HSM* met certificaten. Exporteerbaar is alleen toegestaan met RSA, niet ondersteund door RSA HSM.  

## <a name="certificate-attributes-and-tags"></a>Certificaat kenmerken en-Tags

Naast de meta gegevens van het certificaat, een adresseer bare sleutel en een adresseerbaar geheim, een Key Vault-certificaat bevat ook kenmerken en tags.  

### <a name="attributes"></a>Kenmerken

De certificaat kenmerken worden gespiegeld met kenmerken van de adresseer bare sleutel en het geheim dat is gemaakt wanneer het KV-certificaat wordt gemaakt.  

Een Key Vault certificaat heeft de volgende kenmerken:  

-   *ingeschakeld*: Boole, optioneel, standaard waarde is **True**. Kan worden opgegeven om aan te geven of de certificaat gegevens kunnen worden opgehaald als geheim of bruikbaar als sleutel. Wordt ook gebruikt in combi natie met *NBF* en *exp* wanneer er een bewerking plaatsvindt tussen *NBF* en *exp*, en zal alleen worden toegestaan als ingeschakeld is ingesteld op waar. Bewerkingen buiten het venster *NBF* en *exp* worden automatisch niet toegestaan.  

Er zijn aanvullende alleen-lezen kenmerken die zijn opgenomen in het antwoord:

-   *gemaakt*: IntDate: geeft aan wanneer deze versie van het certificaat is gemaakt.  
-   *bijgewerkt*: IntDate: geeft aan wanneer deze versie van het certificaat is bijgewerkt.  
-   *exp*: IntDate: bevat de waarde van de verval datum van het x509-certificaat.  
-   *NBF*: IntDate: bevat de waarde van de datum van het x509-certificaat.  

> [!Note] 
> Als een Key Vault certificaat verloopt, is de adresseer bare sleutel en het geheim niet meer bruikbaar.  

### <a name="tags"></a>Tags

 Opgegeven Dictionary van sleutel waarde-paren van client, vergelijkbaar met tags in sleutels en geheimen.  

 > [!Note]
> Labels kunnen worden gelezen door een beller als ze de *lijst* of *machtiging* voor dat object type (sleutels, geheimen of certificaten) hebben.

## <a name="certificate-policy"></a>Certificaat beleid

Een certificaat beleid bevat informatie over het maken en beheren van de levens cyclus van een Key Vault certificaat. Wanneer een certificaat met een persoonlijke sleutel wordt geïmporteerd in de sleutel kluis, wordt een standaard beleid gemaakt door het x509-certificaat te lezen.  

Wanneer een volledig Key Vault certificaat wordt gemaakt, moet er een beleid worden opgegeven. Het beleid bepaalt hoe u deze Key Vault certificaat versie maakt, of de volgende Key Vault certificaat versie. Als er een beleid is ingesteld, is het niet vereist voor opeenvolgende nieuwe bewerkingen voor toekomstige versies. Er is slechts één exemplaar van een beleid voor alle versies van een Key Vault certificaat.  

Op hoog niveau bevat een certificaat beleid de volgende informatie:  

-   X509-certificaat eigenschappen: bevat onderwerpnaam, alternatieve naam voor onderwerp en andere eigenschappen die worden gebruikt voor het maken van een x509-certificaat aanvraag.  
-   Sleutel eigenschappen: bevat sleutel type, sleutel lengte, exporteerbaar en het opnieuw gebruiken van sleutel velden. Met deze velden wordt de sleutel kluis geïnstrueerd om een sleutel te genereren.  
-   Geheime eigenschappen: bevat geheime eigenschappen, zoals het inhouds type van adresseer bare geheim om de geheime waarde te genereren, voor het ophalen van een certificaat als geheim.  
-   Levensduur acties: bevat levensduur acties voor het KV-certificaat. Elke levensduur actie bevat:  

     - Trigger: opgegeven via dagen vóór het verloop of het percentage van de levens duur  

     - Actie: type actie opgeven – *emailContacts* of *autorenew*  

-   Verlener: para meters over de certificaat verlener die moet worden gebruikt om x509-certificaten uit te geven.  
-   Beleids kenmerken: bevat kenmerken die zijn gekoppeld aan het beleid  

### <a name="x509-to-key-vault-usage-mapping"></a>X509-toewijzing van Key Vault gebruik

De volgende tabel geeft de toewijzing van het beleid voor x509-sleutel gebruik aan voor efficiënte sleutel bewerkingen van een sleutel die is gemaakt als onderdeel van het maken van een Key Vault certificaat.

|**Gebruiks vlaggen voor x509-sleutel**|**Key Vault Key OPS**|**Standaardgedrag**|
|----------|--------|--------|
|DataEncipherment|versleutelen, ontsleutelen| N.v.t. |
|DecipherOnly|crypto| N.v.t.  |
|DigitalSignature|ondertekenen, controleren| Key Vault standaard zonder een gebruiks specificatie op het moment van aanmaken van het certificaat | 
|EncipherOnly|encrypt| N.v.t. |
|KeyCertSign|ondertekenen, controleren|N.v.t.|
|KeyEncipherment|wrapKey, sleutel uitpakken| Key Vault standaard zonder een gebruiks specificatie op het moment van aanmaken van het certificaat | 
|Authentic|ondertekenen, controleren| N.v.t. |
|crlsign|ondertekenen, controleren| N.v.t. |

## <a name="certificate-issuer"></a>Certificaat verlener

Een Key Vault certificaat object bevat een configuratie die wordt gebruikt om te communiceren met een geselecteerde certificaat verlener-provider om x509-certificaten te best Ellen.  

-   Key Vault partners met de volgende certificaat verleners voor TLS/SSL-certificaten

|**Provider naam**|**Locaties**|
|----------|--------|
|DigiCert|Ondersteund in alle sleutel kluis service locaties in de open bare Cloud en Azure Government|
|GlobalSign|Ondersteund in alle sleutel kluis service locaties in de open bare Cloud en Azure Government|

Voordat een certificaat uitgever kan worden gemaakt in een Key Vault, moeten de volgende stappen 1 en 2 worden uitgevoerd.  

1. Onboarding voor certificerings instanties (CA)  

    -   Een beheerder van de organisatie moet het bedrijf (bijvoorbeeld Contoso) met ten minste één CA-provider.  

2. Beheerder maakt referenties voor de aanvrager van Key Vault om TLS/SSL-certificaten in te schrijven (en te vernieuwen)  

    -   Biedt de configuratie die moet worden gebruikt voor het maken van een uitgevers object van de provider in de sleutel kluis  

Zie de [blog Key Vault certificaten](https://aka.ms/kvcertsblog) voor meer informatie over het maken van uitgevers objecten van de portal certificaten.  

Key Vault maakt het mogelijk meerdere uitgevers objecten te maken met een andere provider configuratie voor de verlener. Zodra een object van de verlener is gemaakt, kan de naam ervan in een of meer certificaat beleidsregels worden verwezen. Als u verwijst naar het Issuer-object, wordt Key Vault het gebruik van configuratie zoals opgegeven in het Issuer-object te gebruiken bij het aanvragen van het x509-certificaat van de CA-provider tijdens het maken en vernieuwen van het certificaat.  

Uitgevers objecten worden in de kluis gemaakt en kunnen alleen worden gebruikt met KV-certificaten in dezelfde kluis.  

## <a name="certificate-contacts"></a>Certificaat contactpersonen

Certificaat contactpersonen bevatten contact gegevens om meldingen te verzenden die worden geactiveerd door de levens duur van het certificaat. De gegevens van de contact persoon worden gedeeld door alle certificaten in de sleutel kluis. Er wordt een melding verzonden naar alle opgegeven contact personen voor een gebeurtenis voor een certificaat in de sleutel kluis.  

Als het beleid van een certificaat is ingesteld op automatisch verlengen, wordt er een melding verzonden naar de volgende gebeurtenissen.  

- Vóór het vernieuwen van het certificaat
- Na het vernieuwen van het certificaat, met de mede deling dat het certificaat is vernieuwd of dat er een fout is opgetreden, moet het certificaat hand matig worden vernieuwd.  

  Wanneer een certificaat beleid dat is ingesteld op hand matig wordt vernieuwd (alleen e-mail), wordt er een melding verzonden wanneer het certificaat wordt vernieuwd.  

## <a name="certificate-access-control"></a>Certificaat Access Control

 Toegangs beheer voor certificaten wordt beheerd door Key Vault en wordt gegeven door de Key Vault die de certificaten bevat. Het toegangscontrole beleid voor certificaten verschilt van het toegangs beheer beleid voor sleutels en geheimen in hetzelfde Key Vault. Gebruikers kunnen een of meer kluizen maken voor het bewaren van certificaten, voor het bijhouden van de juiste segmentatie en het beheer van certificaten.  

 De volgende machtigingen kunnen per principal worden gebruikt, in de toegangscontrole vermelding geheimen in een sleutel kluis en komt overeen met de bewerkingen die zijn toegestaan voor een geheim object:  

- Machtigingen voor certificaat beheer bewerkingen
  - *ophalen*: de huidige certificaat versie of een versie van een certificaat ophalen 
  - *lijst*: de huidige certificaten of versies van een certificaat weer geven  
  - *bijwerken*: een certificaat bijwerken
  - *maken*: een Key Vault-certificaat maken
  - *importeren*: certificaat materiaal importeren in een Key Vault certificaat
  - *verwijderen*: een certificaat, het bijbehorende beleid en alle versies ervan verwijderen  
  - *herstellen*: een verwijderd certificaat herstellen
  - *back-up*: een back-up maken van een certificaat in een sleutel kluis
  - *herstellen*: een back-up van een certificaat herstellen naar een sleutel kluis
  - *managecontacts*: Key Vault certificaat contactpersonen beheren  
  - *manageissuers*: Key Vault certificerings instanties/verleners beheren
  - *getissuers*: de autoriteiten/verleners van een certificaat ophalen
  - *listissuers*: de autoriteiten/verleners van een certificaat weer geven  
  - *setissuers*: de autoriteiten/verleners van een Key Vault certificaat maken of bijwerken  
  - *deleteissuers*: de instanties/verleners van een Key Vault certificaat verwijderen  
 
- Machtigingen voor bevoegde bewerkingen
  - *opschonen*: een verwijderd certificaat leegmaken (permanent verwijderen)

Zie voor meer informatie de [certificaat bewerkingen in de naslag informatie over Key Vault rest API](/rest/api/keyvault). Zie voor meer informatie over het instellen van machtigingen [-kluizen-maken of bijwerken](/rest/api/keyvault/vaults/createorupdate) en [kluizen-toegangs beleid bijwerken](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="next-steps"></a>Volgende stappen

- [Over Key Vault](../general/overview.md)
- [Over sleutels, geheimen en certificaten](../general/about-keys-secrets-certificates.md)
- [Over sleutels](../keys/about-keys.md)
- [Over geheimen](../secrets/about-secrets.md)
- [Verificatie, aanvragen en antwoorden](../general/authentication-requests-and-responses.md)
- [Gids voor Key Vault-ontwikkelaars](../general/developers-guide.md)